# 🤖 Chat IA Generativa com Azure OpenAI + Streamlit

Aplicação web desenvolvida em **Python + Streamlit** que implementa um chat interativo utilizando modelos hospedados no **Azure OpenAI**, com suporte a streaming de respostas em tempo real.

O projeto demonstra:

* Integração com Azure OpenAI via SDK oficial `openai>=1.x`
* Uso de modelos modernos (ex: `gpt-5.x`)
* Tratamento de erros da API
* Gerenciamento de estado de conversa com `st.session_state`
* Interface organizada com sidebar de configurações
* Streaming de respostas token a token

---

# 📌 Objetivo do Projeto

Criar um **chat educacional interativo**, onde o modelo assume o papel de uma professora técnica de tecnologia, respondendo:

* De forma clara e didática
* Em português do Brasil
* Com exemplos práticos

O projeto também serve como base para:

* Apps corporativos com Azure OpenAI
* Assistentes educacionais
* Prototipagem de IA generativa

---

# 🏗️ Arquitetura da Aplicação

## 🔹 Frontend

* **Streamlit**
* Layout em duas áreas:

  * Sidebar → Configurações e status
  * Área principal → Histórico + input do chat

## 🔹 Backend

* SDK `openai` versão 1.x
* Cliente `AzureOpenAI`
* Comunicação via endpoint configurado no Azure
* Streaming via `chat.completions.create(..., stream=True)`

---

# ⚙️ Configuração de Ambiente

## 1️⃣ Variáveis de Ambiente (.env)

```env
AZURE_OPENAI_ENDPOINT=https://SEU-ENDPOINT.openai.azure.com/
AZURE_OPENAI_KEY=SUA_CHAVE_AQUI
MODEL_DEPLOY_NAME=gpt-5.2-chat
```

---

## 2️⃣ Dependências

Recomendado:

```txt
streamlit>=1.38.0
openai>=1.51.0
python-dotenv>=1.0.1
```

Instalação:

```bash
pip install -r requirements.txt
```

---

# 🚀 Execução

```bash
streamlit run app.py
```

---

# 🧠 Funcionamento Técnico

## 1️⃣ Inicialização do Cliente

Uso do SDK moderno:

```python
from openai import AzureOpenAI

client = AzureOpenAI(
    azure_endpoint=AZURE_OPENAI_ENDPOINT,
    api_key=AZURE_OPENAI_KEY,
    api_version="2024-12-01-preview"
)
```

O cliente é cacheado com:

```python
@st.cache_resource
```

Evita recriação a cada interação.

---

## 2️⃣ Gerenciamento do Histórico

O histórico é armazenado em:

```python
st.session_state.messages
```

Formato padrão:

```python
{
    "role": "user" | "assistant" | "system",
    "content": "texto"
}
```

O histórico é:

* Renderizado na área principal
* Enviado completo para a API a cada nova mensagem

---

## 3️⃣ Streaming de Resposta

A chamada é feita com:

```python
response = client.chat.completions.create(
    model=MODEL_DEPLOY_NAME,
    messages=messages_for_api,
    max_completion_tokens=max_tokens,
    stream=True
)
```

E processada assim:

```python
for chunk in response:
    if chunk.choices and chunk.choices[0].delta.content:
        content = chunk.choices[0].delta.content
```

Isso permite:

* Resposta token a token
* Experiência semelhante ao ChatGPT
* Melhor percepção de desempenho

---

# 🧩 Problemas Enfrentados e Soluções

Durante o desenvolvimento, ocorreram alguns erros importantes.

---

## ❌ 1. Conflito de dependências (`proxies`)

Erro:

```
Client.init() got an unexpected keyword argument 'proxies'
```

### 🔍 Causa

Conflito entre:

* `azure-ai-projects`
* versões antigas de `httpx`
* SDK `openai` 1.x

### ✅ Solução

Remover bibliotecas conflitantes e manter apenas:

```txt
openai>=1.51.0
```

---

## ❌ 2. Parâmetro `max_tokens` não suportado

Erro:

```
Unsupported parameter: 'max_tokens'
```

### 🔍 Causa

Modelos modernos (GPT-5.x) não usam mais `max_tokens`.

### ✅ Correção

Substituir:

```python
max_tokens=...
```

Por:

```python
max_completion_tokens=...
```

---

## ❌ 3. `temperature` não suportada

Erro:

```
Unsupported value: 'temperature'
```

### 🔍 Causa

Modelos GPT-5.x possuem temperatura fixa (1.0).

### ✅ Solução

Remover:

```python
temperature
top_p
```

Da chamada da API.

---

# 🎨 Estrutura Final de Interface

## Sidebar

* Temperatura (se modelo permitir)
* Máximo de tokens
* Botão limpar conversa
* Status da conexão
* Informações do modelo

## Área Principal

* Histórico do chat
* Streaming da resposta
* Input fixo na parte inferior

---

# 🔐 Tratamento de Erros

O sistema trata:

* 401 → Erro de autenticação
* 404 → Modelo não encontrado
* 429 → Rate limit
* Erros de conexão
* Exibição parcial da mensagem de erro para debugging

---

# 📊 Boas Práticas Aplicadas

* Uso de variáveis de ambiente
* Separação clara entre UI e lógica de API
* Cache do cliente
* Streaming
* Validação de cliente antes da chamada
* Reset controlado de sessão

---

# 🔮 Próximos Passos Possíveis

* Persistência de histórico em banco (SQLite/Postgres)
* Controle de tokens consumidos
* Upload de documentos (RAG)
* Autenticação via Azure Entra ID
* Deploy no Azure App Service
* Containerização com Docker

---

Projeto desenvolvido em aula do curso Desenvolvimento de soluções em inteligência artificial - Microsoft AI-102 Senai

