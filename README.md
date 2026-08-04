# Teste Técnico — AI Solution Engineer Jr.

Bem-vindo(a)!

Se você chegou até aqui, é porque avançamos com sua candidatura para a vaga de **AI Solution Engineer Jr.** na Runflow AI. Esse desafio foi pensado para simular o tipo de trabalho que você faria no dia a dia do time de Professional Services: implementar um agente de IA de ponta a ponta, com LLM, ferramentas e integração com um sistema externo.

Antes de começar, duas coisas importantes:

> **Estamos mais interessados em quanto você quer aprender e correr atrás pra aprender do que no que você sabe hoje.** Se tem coisa nova nesse desafio, ótimo — queremos ver como você encara.

> **Você pode usar IA** (Claude, ChatGPT, Cursor, etc) durante o desenvolvimento — é parte natural do nosso dia a dia também. Mas fica o aviso: **na próxima fase da entrevista vamos fazer perguntas técnicas sobre o que você entregou.** Então, mais importante do que terminar rápido é entender o que você está fazendo.

---

## 📋 Índice

- [Prazo](#-prazo)
- [Contexto](#-contexto)
- [Escopo do desafio](#-escopo-do-desafio)
- [Requisitos técnicos](#️-requisitos-técnicos)
- [Testes de aceite](#-testes-de-aceite)
- [O que entregar](#-o-que-entregar)
- [O que vamos avaliar](#-o-que-vamos-avaliar)
- [Dicas](#-dicas)
- [Dúvidas](#-dúvidas)

---

## ⏱ Prazo

Você tem **5 dias corridos** a partir do momento em que recebe o desafio.

Preferimos uma solução simples e bem compreendida a uma complexa e incompleta.

---

## 🛒 Contexto

Um cliente contratou a Runflow para automatizar parte do atendimento da sua loja online. O objetivo é ter um agente conversacional em que o usuário final possa consultar informações sobre produtos e pedidos, e também criar um pedido novo — tudo em linguagem natural.

Seu papel é construir o agente que vai atender esse caso de uso. Você vai implementar do zero:

- O loop do agente
- As ferramentas (tools) que ele pode chamar
- A integração com a "API do cliente" (que você vai mockar/implementar)
- Uma interface simples para conversar com ele

---

## 🎯 Escopo do desafio

### 1. API do cliente (mock)

Implemente um servidor HTTP simples em Node.js (Express, Fastify, Hono — sua escolha) expondo os seguintes endpoints:

| Método | Endpoint        | Descrição                                                                                 |
| ------ | --------------- | ----------------------------------------------------------------------------------------- |
| `GET`  | `/products`     | Lista produtos disponíveis (id, nome, preço, estoque).                                    |
| `GET`  | `/products/:id` | Detalhes de um produto específico.                                                        |
| `GET`  | `/orders/:id`   | Busca um pedido pelo ID (status, itens, total).                                           |
| `POST` | `/orders`       | Cria um pedido novo. Recebe `{ productId, quantity }[]` e retorna o pedido criado com ID. |

Os dados podem ficar em memória mesmo (array/objeto), não precisa banco de dados. Popule com 5–10 produtos e alguns pedidos de exemplo.

### 2. O agente

Construa um agente conversacional em Node.js/TypeScript que:

- Usa um **LLM** (qualquer provedor — documente sua escolha)
- Tem acesso a **4 ferramentas (tools)** que chamam a sua API mock:
  - `list_products` — lista produtos disponíveis
  - `get_product` — consulta detalhes de um produto
  - `get_order_status` — consulta status de um pedido pelo ID
  - `create_order` — cria um pedido novo
- Implementa um **loop de tool calling** que chama as ferramentas quando necessário e continua a conversa com o resultado
- Mantém **contexto/histórico** ao longo da conversa (memória da sessão em memória mesmo)
- Tem uma **system prompt clara** definindo o comportamento do agente (persona, limites, como lidar com ambiguidade)

### 3. Interface de conversa

Você escolhe como expor o agente — pode ser um endpoint HTTP, um script que aceita entrada, um chat em terminal, ou qualquer outra forma que permita testar múltiplos turnos de conversa.

O importante é conseguir conversar com o agente e manter contexto ao longo da sessão.

**Exemplo de interação esperada:**

```
Usuário: Oi, quais produtos vocês têm?
Agente:  Temos os seguintes produtos disponíveis: [...lista]

Usuário: Me conta mais sobre o produto 3.
Agente:  O produto 3 é [...detalhes]

Usuário: Quero comprar 2 unidades dele.
Agente:  Pedido criado! ID: 1042. Total: R$ XX,XX.

Usuário: Qual o status do pedido 1042?
Agente:  Seu pedido 1042 está em [...status].
```

---

## 🛠️ Requisitos técnicos

- **Linguagem:** Node.js ou TypeScript (TS preferido)
- **Frameworks de agente:** você pode usar frameworks (LangChain, Vercel AI SDK, LlamaIndex, Mastra, OpenAI Agents SDK) **OU** implementar o loop de tool calling na mão — ambos são aceitos. Documente sua escolha.
- **Organização:** separe responsabilidades (API mock, tools, agente, interface)
- **Variáveis sensíveis** (API keys) em `.env` — inclua um `.env.example`
- **Tratamento de erros básico** (o que acontece se o usuário pedir um produto que não existe? se tentar criar pedido sem estoque?)

### LLMs com tier gratuito

Se você não tiver um provedor preferido, seguem opções **gratuitas e sem cartão de crédito**:

| Provedor                                | Destaque                                                           | Link                                               |
| --------------------------------------- | ------------------------------------------------------------------ | -------------------------------------------------- |
| **Google AI Studio** (Gemini 2.5 Flash) | Tier gratuito generoso, frontier-class, tool calling nativo        | [aistudio.google.com](https://aistudio.google.com) |
| **Groq**                                | Inferência rápida em Llama 3.3 70B, endpoint compatível com OpenAI | [console.groq.com](https://console.groq.com)       |
| **OpenRouter**                          | Acesso unificado a vários modelos com sufixo `:free`               | [openrouter.ai](https://openrouter.ai)             |
| **Cerebras**                            | Gratuito, inferência em alta velocidade                            | [cloud.cerebras.ai](https://cloud.cerebras.ai)     |

---

## ✅ Testes de aceite

Antes de entregar, garanta que seu agente responde bem aos cenários abaixo. **Inclua no README a transcrição ou prints dos testes rodando.**

### Fluxo 1 — Listar produtos

- [ ] `"Quais produtos vocês têm disponíveis?"` → agente chama `list_products` e responde com a lista
- [ ] `"Me mostra só os produtos que estão em estoque."` → agente consegue filtrar (via raciocínio ou tool)

### Fluxo 2 — Detalhes de produto

- [ ] `"Me conta mais sobre o produto 3."` → agente chama `get_product` com o ID correto
- [ ] `"E quanto custa o produto 2?"` → agente mantém contexto e responde sobre outro produto

### Fluxo 3 — Criar pedido

- [ ] `"Quero comprar 2 unidades do produto 1 e 1 unidade do produto 4."` → agente chama `create_order` com a estrutura correta e confirma o pedido com ID e total
- [ ] `"Adiciona também 3 unidades do último que você me mostrou."` → agente usa contexto da conversa pra identificar o produto certo

### Fluxo 4 — Consultar pedido

- [ ] `"Qual o status do pedido 1042?"` → agente chama `get_order_status` e responde com o status
- [ ] `"E o que eu pedi nele mesmo?"` → agente responde com os itens do pedido sem precisar perguntar o ID de novo

### Fluxo 5 — Tratamento de erros

- [ ] `"Me fala do produto 999."` (ID inexistente) → agente responde de forma útil, sem quebrar nem inventar dados
- [ ] `"Quero comprar 1000 unidades do produto 1."` (sem estoque suficiente) → agente comunica a limitação e não cria um pedido inválido
- [ ] `"Qual o status do pedido 9999?"` (pedido inexistente) → agente responde apropriadamente

### Fluxo 6 — Limites do agente

- [ ] `"Qual a previsão do tempo em SP?"` (fora do escopo) → agente reconhece que não é o papel dele e orienta de volta ao contexto
- [ ] `"Cancela meu pedido 1042."` (operação não implementada) → agente comunica que a operação não é suportada, sem "fingir" que executou

---

## 📦 O que entregar

Um repositório Git (GitHub, GitLab) contendo:

- **Código-fonte completo**
- **README** com:
  - Como rodar o projeto (passo a passo, incluindo dependências)
  - Decisões técnicas que você tomou e por quê (escolha do LLM, do framework ou não, como estruturou o agente, o que deixou de fora de propósito)
  - O que você faria diferente / melhoraria se tivesse mais tempo
  - Exemplos de conversas testadas (pode ser print ou bloco de texto)
- **`.env.example`** com as variáveis necessárias

### Como entregar

1. Se o repositório for **privado**, compartilhe acesso com o usuário [`jacksonsilvadev`](https://github.com/jacksonsilvadev) no GitHub.
2. Envie o link do repositório por e-mail para **[jackson.silva@runflow.ai](mailto:jackson.silva@runflow.ai)** com o assunto:
   ```
   Teste Técnico - AI Solution Engineer Jr. - [Seu Nome]
   ```

---

## 🔍 O que vamos avaliar

- **Funcionamento de ponta a ponta** — o agente resolve os 4 fluxos (listar, detalhar, consultar pedido, criar pedido)?
- **Qualidade do código** — legibilidade, organização, separação de responsabilidades
- **Qualidade da system prompt e do design das tools** — descrições claras, parâmetros bem definidos
- **Tratamento de casos de erro e edge cases**
- **Qualidade do README e das decisões documentadas** — queremos entender como você pensa, não só o que você entrega
- **Pragmatismo** — preferimos uma solução simples e bem feita a uma complexa e incompleta

---

## 💡 Dicas

- **Não é um concurso de engenharia.** Resista à tentação de adicionar microserviços, Docker, testes E2E etc. Foque no fluxo principal.
- **Se ficar travado** em alguma decisão, documente no README e siga em frente — a decisão documentada vale mais que a decisão perfeita.
- **Teste os fluxos** antes de entregar. Converse com seu agente como se fosse um cliente real.

---

## ❓ Dúvidas

Qualquer dúvida sobre o escopo, mande um e-mail para **[jackson.silva@runflow.ai](mailto:jackson.silva@runflow.ai)**.

Boa sorte e boa construção! 🚀
