# 📚 Study Bot — Assistente de Estudos com IA

Assistente de estudos pessoal construído com **n8n**, **IA local** e **Telegram**, rodando 100% na minha máquina e com **custo zero**.

Este projeto faz parte da minha jornada de aprendizado de n8n do zero, documentada em público no [LinkedIn](https://www.linkedin.com/in/vinicius-trajano-3266a4204).

---

## 💡 A ideia

Ler o material de uma matéria é fácil, mas lembrar dele uma semana depois é outra história. O Study Bot resolve isso automatizando o ciclo de estudo:

1. **Envio o conteúdo** de uma matéria (texto ou PDF) para um bot no Telegram
2. **A IA gera** um resumo e flashcards de pergunta e resposta
3. **Os flashcards são salvos** no banco de dados
4. **Todo dia o bot me envia perguntas de revisão**, usando repetição espaçada para fixar o conteúdo

## 🛠️ Stack

| Camada         | Tecnologia                                                    |
| -------------- | ------------------------------------------------------------- |
| Orquestração   | [n8n](https://n8n.io) (self-hosted)                           |
| IA             | [Ollama](https://ollama.com) com modelo local (`llama3.2:3b`) |
| Banco de dados | PostgreSQL 16                                                 |
| Interface      | Bot do Telegram                                               |
| Infraestrutura | Docker / Docker Compose                                       |

## 🏗️ Arquitetura

```
Telegram  ⇄  n8n (Docker)  ⇄  PostgreSQL (Docker)
                 ⇅
         Ollama (IA local, Windows + GPU)
```

- O **n8n** orquestra os fluxos: recebe as mensagens, chama a IA, salva os dados e agenda as revisões
- O **PostgreSQL** guarda os dados internos do n8n e, em um banco separado, os flashcards e o progresso
- O **Ollama** roda o modelo de IA localmente, sem custo de API e sem enviar dados para terceiros

## 🗺️ Roadmap

- [x] Ambiente com n8n + PostgreSQL via Docker Compose
- [x] Primeiro workflow: envio de flashcard para o Telegram
- [ ] Bot recebendo mensagens do usuário
- [ ] Integração com IA local (Ollama) para gerar resumos
- [ ] Geração e persistência de flashcards no PostgreSQL
- [ ] Revisão diária com repetição espaçada
- [ ] Suporte a PDFs e comandos (`/materias`, `/progresso`, `/quiz`)
- [ ] Tratamento de erros, backup e demo final

## 🚀 Como rodar

### Pré-requisitos

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) (com WSL2 no Windows)
- Um bot do Telegram criado via [@BotFather](https://t.me/BotFather)

### Passos

1. Clone o repositório e crie um arquivo `.env` na raiz:

   ```env
   POSTGRES_USER=n8n
   POSTGRES_PASSWORD=sua_senha_forte
   POSTGRES_DB=n8n
   N8N_ENCRYPTION_KEY=gere_com_openssl_rand_hex_32
   ```

   Para gerar a chave de criptografia:

   ```bash
   openssl rand -hex 32
   ```

2. Suba os containers:

   ```bash
   docker compose up -d
   ```

3. Acesse o n8n em [http://localhost:5678](http://localhost:5678) e crie sua conta.

4. Importe os workflows da pasta `workflows/` e configure a credencial do Telegram.

### Comandos úteis

| Comando                      | O que faz                                     |
| ---------------------------- | --------------------------------------------- |
| `docker compose up -d`       | Sobe os containers                            |
| `docker compose down`        | Desliga os containers (os dados são mantidos) |
| `docker compose logs -f n8n` | Mostra os logs do n8n                         |

> ⚠️ **Nunca** use `docker compose down -v`: o `-v` apaga os volumes e todos os workflows e dados.

## 🔒 Segurança

- Segredos ficam no `.env`, que está no `.gitignore` e nunca é versionado
- Credenciais (como o token do Telegram) ficam criptografadas no banco com a `N8N_ENCRYPTION_KEY`
- As portas do n8n e do PostgreSQL ficam acessíveis apenas em `127.0.0.1`
- Guarde sua `N8N_ENCRYPTION_KEY` com segurança: sem ela, as credenciais salvas não podem ser descriptografadas

## 👤 Autor

**Vinicius Trajano**: desenvolvedor backend

- GitHub: [@trajanovinicius](https://github.com/trajanovinicius)
- LinkedIn: [Vinicius Trajano](https://www.linkedin.com/in/vinicius-trajano-3266a4204)
- Portfólio: [viniciustrajano.netlify.app](https://viniciustrajano.netlify.app)
