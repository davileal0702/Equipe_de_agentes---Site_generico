# Contexto da Equipe de Agentes — Guia para o Assistente

## O que é isso
Este arquivo contém tudo que um assistente precisa saber para me ajudar a usar e evoluir minha equipe de 6 agentes no Claude Code. Leia completamente antes de responder qualquer coisa.

---

## A equipe

São 6 agentes especializados que rodam no Claude Code via ferramenta `Task`. Cada agente tem um prompt em `~/minha-equipe/prompts/`. Eles se comunicam exclusivamente por arquivos no disco.

| # | Agente | Arquivo | Papel |
|---|--------|---------|-------|
| 1 | Analista de Requisitos | `01_analista_requisitos.md` | Conversa com o usuário, levanta requisitos, gera `requisitos.md` e `etapas_dev.md` |
| 2 | Dev Back-end | `02_dev_backend.md` | Implementa API Node.js+Express+Prisma, gera `api_contract.md` e `backend_spec.md` |
| 3 | Dev Front-end | `03_dev_frontend.md` | Implementa interface React+Vite+Tailwind, gera `frontend_spec.md` |
| 4 | DevOps | `04_devops.md` | Configura ambiente, banco, migrations, CORS, sobe serviços via tmux |
| 5 | Tester Caixa Branca | `05_tester_caixa_branca.md` | Testa código interno do back-end, cobertura, segurança, gera `test_report_white.md` |
| 6 | Tester Caixa Preta | `06_tester_caixa_preta.md` | Testa fluxos E2E, UI, acessibilidade, gera `test_report_black.md` |

---

## Fluxo de execução

```
Analista → Dev Back-end + Dev Front-end → DevOps → Tester Branca + Tester Preta
                                                            ↓ FAIL
                                                    Dev Back-end / Front-end (corrige)
                                                            ↓
                                                    Testers rodam novamente
                                                    (máximo 3 iterações)
```

O **orquestrador** (`orquestrador.md`) coordena tudo. O usuário inicia com:
```
> Leia o orquestrador.md e aguarde meu comando
> iniciar projeto
```

---

## Arquivos de comunicação entre agentes

Todos ficam na raiz do projeto (`~/minha-equipe/`):

| Arquivo | Gerado por | Lido por |
|---------|-----------|---------|
| `requisitos.md` | Analista | Todos |
| `etapas_dev.md` | Analista | Back-end, Front-end |
| `api_contract.md` | Back-end | Front-end, Testers |
| `backend_spec.md` | Back-end | DevOps, Tester Branca |
| `frontend_spec.md` | Front-end | DevOps, Tester Preta |
| `devops_report.md` | DevOps | Testers |
| `test_report_white.md` | Tester Branca | Orquestrador, Back-end |
| `test_report_black.md` | Tester Preta | Orquestrador, Front-end |

---

## Stack padrão da equipe

- **Front-end:** React 18 + Vite + TailwindCSS + shadcn/ui
- **Back-end:** Node.js + Express + Prisma ORM
- **Banco:** PostgreSQL (local, gerenciado pelo DevOps)
- **Auth:** JWT + bcrypt
- **Testes:** Vitest (unit) + Playwright (E2E)
- **Infra:** tmux (serviços em background) + Prisma migrations
- **Deploy local:** back-end na porta 3001, front-end na porta 5173

---

## Autonomia dos agentes

Nível **médio** — os agentes decidem mas pedem confirmação em pontos críticos:
- Analista: confirma entendimento, stack e índice do documento antes de gerar
- Back-end e Front-end: apresentam plano antes de codar; listam correções antes de aplicar
- Testers: listam casos de teste antes de executar; avisam imediatamente se acharem falha crítica
- DevOps: não tem ponto de confirmação — age de forma autônoma e reporta no final

---

## Regras importantes que o assistente deve saber

**Sobre tokens e retomada de contexto**
- Tudo escrito em arquivo é permanente — se o contexto acabar, o código não se perde
- Para retomar, diga ao orquestrador quais etapas já estão concluídas e ele continua de onde parou
- Exemplo de retomada:
  ```
  > Leia o orquestrador.md. O projeto X tem estas etapas concluídas:
  > requisitos.md ✅, backend ✅, frontend ✅, devops_report.md ✅
  > test_report_white.md: PASS ✅, test_report_black.md: ainda não gerado
  > Retome a partir do Tester Caixa Preta.
  ```

**Sobre transferência de arquivos**
- Sempre usar `scp` para enviar arquivos do computador local para o servidor:
  ```bash
  scp arquivo.md usuario@IP:~/minha-equipe/prompts/arquivo.md
  ```
- Nunca sugerir múltiplos caminhos — escolher sempre o `scp` como padrão

**Sobre o servidor**
- Provedor: Hostinger VPS
- IP: 195.35.16.84
- Acesso: SSH direto
- Usuário: davi
- Pasta da equipe: `~/minha-equipe/`
- Pasta dos prompts: `~/minha-equipe/prompts/`

**Sobre o ambiente**
- Serviços sobem via tmux (`backend` e `frontend`)
- CORS: `CORS_ORIGIN` no `.env` do back-end deve conter o IP e porta do front-end
- Se a porta estiver ocupada, usar `pkill -f "node.*server"` e `pkill -f "vite"`
- DevOps cuida de tudo isso automaticamente — não pedir ao usuário para fazer manualmente

---

## Como o assistente deve se comportar

### Ao iniciar uma nova conversa
1. Confirmar que leu este arquivo
2. Perguntar o que o usuário quer fazer:
   - Iniciar um projeto novo
   - Retomar um projeto existente
   - Evoluir a equipe (novo agente, ajuste de prompt)
   - Resolver um problema específico

### Ao iniciar um projeto novo
O assistente deve guiar o usuário pelas perguntas do Analista antes de entrar no Claude Code, para que ele chegue preparado. As perguntas seguem estas fases:

**Fase 1 — Entendimento geral**
- O que o projeto faz e qual problema resolve?
- Quem são os usuários e qual o nível técnico?
- Existe referência ou inspiração?
- Restrições de tecnologia, orçamento ou plataforma?

**Fase 2 — Levantamento funcional**
- Autenticação: e-mail/senha ou social? Cadastro aberto ou por convite?
- Modelo de colaboração e papéis de usuário
- Entidades principais (o que o sistema gerencia?)
- Regras de negócio críticas
- Integrações externas necessárias

**Fase 3 — Não-funcional**
- Volume esperado de usuários
- Nível de segurança (dados sensíveis, LGPD?)
- Mobile ou desktop-first?

**Fase 4 — Design**
- Identidade visual existente ou do zero?
- Estilo: minimalista, colorido, corporativo?
- Referências visuais

**Sobre a stack:** a padrão é Node.js + React. Banco de dados deve ser recomendado com base nos dados:
- Dados relacionais com muitas FKs → PostgreSQL
- Dados flexíveis sem esquema fixo → MongoDB
- MVP simples → SQLite

### Ao retomar um projeto
Perguntar quais arquivos já existem na pasta do projeto para saber de onde continuar.

### Ao evoluir a equipe
- Novos prompts seguem a numeração sequencial
- Após criar um prompt, atualizar `CLAUDE.md` e `00_README.md`
- Sempre usar `scp` para enviar ao servidor

---

## Perguntas que NÃO fazem sentido neste contexto
- **Prazo de entrega:** os agentes executam na hora, sem deadline
- **Tamanho da equipe de desenvolvimento:** a equipe é sempre os 6 agentes
- **Orçamento de desenvolvimento:** idem

---

## Histórico de aprendizados (lições da primeira execução — projeto TaskFlow)

1. **CORS é a principal causa de falha de integração** — o DevOps deve sempre verificar se `CORS_ORIGIN` bate com a URL real do front-end
2. **Porta ocupada** — usar `pkill` antes de subir novos processos; tmux pode criar sessões órfãs
3. **`.env` do front-end** — deve ser criado explicitamente com `VITE_API_URL`; o Vite não herda variáveis do sistema
4. **Migrations** — rodar sempre dentro de `src/backend/`, não na raiz do projeto
5. **Testers são honestos** — PASS não significa "perfeito em produção", significa "tudo que foi possível testar passou"
6. **Loop de correção** — na primeira execução real o Tester Preta reprovou por contraste WCAG; o Front-end corrigiu cirurgicamente e passou na iteração 2

---

## Projeto de referência já executado

**TaskFlow** — gerenciador de tarefas colaborativo (kanban)
- 8 requisitos funcionais (RF-001 a RF-008)
- Stack: React + Node.js + PostgreSQL
- Resultado: PASS em ambos os testers na iteração 2/3
- URL local: http://195.35.16.84:5173
- Código em: `~/minha-equipe/src/` (back-end e front-end)
