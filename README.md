# 🌤️ Bot de Clima no Telegram com N8N

Chatbot no Telegram que informa a temperatura atual de qualquer cidade do Brasil, utilizando N8N como orquestrador e a API gratuita do OpenWeather.

---

## 📋 Descrição

O usuário envia o nome de uma cidade no formato `Cidade,UF,BR` para o bot no Telegram e recebe de volta uma mensagem amigável com a temperatura atual em graus Celsius.

**Exemplo:**
- Entrada: `São Paulo,SP,BR`
- Saída: `🌤️ A temperatura em São Paulo é de 18°C.`

Em caso de cidade não encontrada:
- Saída: `❌ Cidade não encontrada. Use o formato Cidade,UF,BR (ex.: São Paulo,SP,BR).`

---

## 🔧 Estrutura do Workflow

```
Telegram Trigger → Edit Fields → HTTP Request → IF → Send a text message (sucesso)
                                                   ↘ Send a text message (erro)
```

1. **Telegram Trigger** — recebe mensagens de texto enviadas ao bot
2. **Edit Fields** — captura o texto e normaliza (trim, lowercase, remove acentos) na variável `queue`
3. **HTTP Request** — consulta a API OpenWeather com o parâmetro `q` (cidade formatada)
4. **IF** — verifica se o campo `cod` da resposta é igual a `200` (sucesso)
5. **Send a text message (true)** — envia a temperatura formatada ao usuário
6. **Send a text message (false)** — envia mensagem de erro ao usuário

---

## 📦 Importando o Workflow no N8N

1. Acesse seu N8N (`http://localhost:5678` ou sua URL de produção)
2. Clique em **New Workflow**
3. Clique nos **3 pontinhos** (⋯) no canto superior direito
4. Selecione **Import from file**
5. Selecione o arquivo `workflow-chatbot-telegram.json`
6. Clique em **Save**

---

## 🔑 Credenciais necessárias

### Variáveis esperadas

| Variável | Descrição |
|----------|-----------|
| `TELEGRAM_BOT_TOKEN` | Token do bot gerado pelo @BotFather no Telegram |
| `OPENWEATHER_API_KEY` | Chave de API obtida em https://openweathermap.org/api |

### Como inserir as credenciais no N8N

**Telegram:**
1. No N8N acesse **Settings → Credentials**
2. Clique em **Add Credential**
3. Busque por **Telegram**
4. Insira o `TELEGRAM_BOT_TOKEN` no campo **Access Token**
5. Salve e vincule ao nó **Telegram Trigger** e ao nó **Send a text message**

**OpenWeather:**
1. No nó **HTTP Request** do workflow
2. Localize o parâmetro `appid` em **Query Parameters**
3. Substitua o valor pelo seu `OPENWEATHER_API_KEY`

---

## 🚀 Como executar o chatbot

### Pré-requisitos
- N8N instalado (`npm install -g n8n`)
- Conta no [OpenWeather](https://openweathermap.org/api) com API Key ativa
- Bot criado no Telegram via [@BotFather](https://t.me/BotFather)
- [ngrok](https://ngrok.com) instalado para expor o N8N localmente (se rodar em ambiente local)

### Passos

1. **Suba o N8N com a URL do ngrok:**
```bash
# Terminal 1 — ngrok
ngrok http 5678

# Terminal 2 — N8N (Windows PowerShell)
$env:WEBHOOK_URL="https://SUA_URL.ngrok-free.app"; n8n start

# Terminal 2 — N8N (Linux/Mac)
WEBHOOK_URL="https://SUA_URL.ngrok-free.app" n8n start
```

2. **Importe o workflow** conforme instruções acima

3. **Configure as credenciais** do Telegram e OpenWeather

4. **Ative o workflow** clicando no toggle no canto superior direito

5. **Teste enviando uma cidade** para o bot no Telegram:
```
São Paulo,SP,BR
Belo Horizonte,MG,BR
Rio de Janeiro,RJ,BR
```

---

## ✅ Testes realizados

| Cidade | Resultado |
|--------|-----------|
| São Paulo,SP,BR | ✅ Temperatura retornada corretamente |
| Belo Horizonte,MG,BR | ✅ Temperatura retornada corretamente |
| Rio de Janeiro,RJ,BR | ✅ Temperatura retornada corretamente |
| cidadeinexistente,XX,BR | ✅ Mensagem de erro retornada corretamente |

---

## ⚠️ Segurança

- **Nunca suba tokens ou API Keys no repositório**
- O arquivo `workflow-chatbot-telegram.json` exportado não contém credenciais reais
- Substitua sempre os valores sensíveis diretamente nas configurações do N8N

---

## 📁 Arquivos do repositório

```
chatbot-telegram/
├── workflow-chatbot-telegram.json   # Workflow exportado do N8N
└── README.md                        # Este arquivo
```

---

## 🛠️ Tecnologias utilizadas

- [N8N](https://n8n.io) — orquestrador de automações
- [Telegram Bot API](https://core.telegram.org/bots/api) — interface do chatbot
- [OpenWeather API](https://openweathermap.org/api) — dados de clima em tempo real
