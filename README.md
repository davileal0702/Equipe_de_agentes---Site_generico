# 🤖 Equipe de Agentes — Gerador de Sites com IA

Uma equipe de 6 agentes especializados que colaboram de forma autônoma para planejar, desenvolver, configurar e testar aplicações web completas usando Claude Code.

---

## 💡 Intuito

A ideia surgiu da necessidade de ter um time de desenvolvimento totalmente autônomo, onde cada agente tem uma responsabilidade clara e bem delimitada — e mais importante: onde os agentes se **avaliam entre si e corrigem os próprios erros** antes de considerar qualquer entrega concluída.

O objetivo não é substituir desenvolvedores humanos, mas criar uma estrutura onde um único humano pode orquestrar um projeto completo com qualidade e rastreabilidade, aprovando decisões nos pontos críticos e deixando a execução para os agentes.

---

## 🧭 Fluxo de funcionamento

```
┌─────────────────────────────────────────────────────────────────┐
│                        HUMANO                                   │
│           (aprova em pontos de confirmação)                     │
└───────────────────────────┬─────────────────────────────────────┘
                            │
                            ▼
                   ┌─────────────────┐
                   │   Orquestrador  │  coordena tudo, decide o fluxo
                   └────────┬────────┘
                            │
              ┌─────────────▼─────────────┐
              │      1. Analista          │  levanta requisitos com o humano
              └─────────────┬─────────────┘
                            │ requisitos.md + etapas_dev.md
              ┌─────────────▼─────────────┐
              │  2. Dev Back-end          │  API, banco, autenticação
              │  3. Dev Front-end         │  interface, rotas, integração
              └─────────────┬─────────────┘
                            │ código + api_contract.md + specs
              ┌─────────────▼─────────────┐
              │      4. DevOps            │  ambiente, migrations, tmux, CORS
              └─────────────┬─────────────┘
                            │ devops_report.md (ambiente no ar)
              ┌─────────────▼─────────────┐
              │  5. Tester Caixa Branca   │  código interno, segurança, cobertura
              │  6. Tester Caixa Preta    │  UI, fluxos E2E, acessibilidade
              └─────────────┬─────────────┘
                            │
                    ┌───────▼────────┐
                    │   PASS / FAIL  │
                    └───────┬────────┘
                            │
               FAIL ────────┘──────── PASS
                │                       │
                ▼                       ▼
        Devs corrigem            Projeto concluído
        (até 3 iterações)
```

---

## 👥 Os 6 agentes

### 📋 1. Analista de Requisitos
A fundação de tudo. Conduz uma conversa estruturada com o humano em 4 fases — entendimento geral, levantamento funcional, requisitos não-funcionais e design. Recomenda a stack com justificativa e só avança após confirmação explícita do humano.

**Gera:** `requisitos.md` (RFs numerados, critérios de aceite, escopo) e `etapas_dev.md` (plano dividido por responsável com checklists).

---

### ⚙️ 2. Desenvolvedor Back-end
Implementa a API, o banco de dados e toda a lógica de servidor. Apresenta um plano antes de codar, segue padrões rígidos de segurança (Helmet, CORS, rate limit, Zod, bcrypt) e documenta tudo que o front-end precisa saber.

**Gera:** código em `/src/backend/`, `api_contract.md` (contrato da API) e `backend_spec.md`.

**Opera em dois modos:** IMPLEMENTAÇÃO (primeira vez) e CORREÇÃO (após relatório de falhas do tester).

---

### 🎨 3. Desenvolvedor Front-end
Constrói a interface React seguindo o design system definido nos requisitos. Integra com a API via Axios com interceptors, implementa os 4 estados obrigatórios de UI (loading, erro, vazio, dados) e garante acessibilidade básica.

**Gera:** código em `/src/frontend/` e `frontend_spec.md`.

**Opera em dois modos:** IMPLEMENTAÇÃO e CORREÇÃO.

---

### 🛠️ 4. DevOps
Responsável por deixar o ambiente 100% funcional antes dos testes. Configura banco de dados, variáveis de ambiente, roda migrations e sobe os serviços em background via tmux. Só emite o relatório quando todos os serviços estão respondendo.

**Gera:** `devops_report.md` com URLs, status de cada serviço e o que foi corrigido.

---

### 🔬 5. Tester Caixa Branca
Analisa o código do back-end por dentro. Verifica cobertura de testes (mínimo 80%), segurança (senhas, JWT, validações, CORS), conformidade com o `api_contract.md` e todas as regras de negócio dos requisitos. Uma falha de segurança é FAIL automático.

**Gera:** `test_report_white.md` com status PASS ou FAIL, evidências e ações necessárias.

---

### 🧪 6. Tester Caixa Preta
Testa o sistema como um usuário real — sem ver o código. Valida cada requisito funcional, executa fluxos E2E com Playwright, verifica responsividade em 3 larguras (375px, 768px, 1280px) e acessibilidade com axe-core.

**Gera:** `test_report_black.md` com status PASS ou FAIL, passos para reproduzir cada falha e evidências.

---

## 📁 Como os agentes se comunicam

Os agentes não se chamam diretamente. Toda a comunicação acontece via **arquivos no disco**, gerenciados pelo orquestrador:

```
requisitos.md          ← Analista escreve → todos leem
etapas_dev.md          ← Analista escreve → Back-end e Front-end leem
api_contract.md        ← Back-end escreve → Front-end e Testers leem
backend_spec.md        ← Back-end escreve → DevOps e Tester Branca leem
frontend_spec.md       ← Front-end escreve → DevOps e Tester Preta leem
devops_report.md       ← DevOps escreve → Testers leem
test_report_white.md   ← Tester Branca escreve → Orquestrador e Back-end leem
test_report_black.md   ← Tester Preta escreve → Orquestrador e Front-end leem
```

---

## 🔄 O loop de auto-avaliação

O diferencial da equipe é o ciclo de revisão automático. Após os testes:

- Se **ambos PASS** → projeto concluído
- Se **qualquer FAIL** → orquestrador envia o relatório de falhas para o dev responsável, que corrige de forma cirúrgica (sem adicionar features) e os testers rodam novamente
- **Máximo de 3 iterações** — se não passar, o orquestrador para e reporta ao humano para decisão

Na prática, isso significa que os agentes se responsabilizam pela qualidade da entrega antes de chegar ao humano.

---

## 🛠️ Stack padrão

| Camada | Tecnologia |
|--------|-----------|
| Front-end | React 18 + Vite + TailwindCSS + shadcn/ui |
| Back-end | Node.js + Express |
| ORM | Prisma |
| Banco de dados | PostgreSQL |
| Autenticação | JWT + bcrypt |
| Validação | Zod |
| Testes unitários | Vitest |
| Testes E2E | Playwright |
| Acessibilidade | axe-core |
| Infra | tmux + Prisma migrations |

---

## ✅ Escopo

- Desenvolvimento de **aplicações web completas** (front-end + back-end + banco)
- Projetos com **autenticação, CRUD, regras de negócio e integração** entre camadas
- Ciclo completo: **requisitos → código → ambiente → testes → correção**
- Autonomia média: agentes decidem mas **confirmam pontos críticos** com o humano
- Rastreabilidade total via **arquivos de comunicação** entre agentes

---

## ❌ Fora do escopo

- Aplicações mobile nativas (iOS/Android)
- Projetos que exijam stacks fora do padrão sem ajuste nos prompts
- Deploy em produção (a equipe entrega o projeto funcionando localmente)
- Gerenciamento de infraestrutura de longo prazo (CI/CD, monitoramento, escalabilidade)
- Projetos sem requisitos — o Analista precisa de uma conversa real com o humano

---

## 🧪 Projeto de referência — TaskFlow

Para validar a equipe, foi desenvolvido um **gerenciador de tarefas colaborativo** (estilo anti-Jira) com:

- Autenticação com e-mail/senha e JWT
- Projetos com convite por link e dois papéis (dono e membro)
- Kanban com drag-and-drop (@dnd-kit) e 3 colunas
- Tarefas com título, descrição, responsável, prioridade e prazo
- Indicador in-app de tarefas atribuídas

**Resultado:** PASS em ambos os testers na iteração 2/3. A única falha foi de contraste de cores (WCAG AA), corrigida cirurgicamente pelo Front-end sem tocar em nenhuma outra parte do código.

---

## 📐 Autonomia dos agentes

Todos os agentes operam em **nível médio de autonomia** — tomam decisões técnicas de forma independente mas pausam em pontos críticos:

| Agente | Quando confirma |
|--------|----------------|
| Analista | Resumo do entendimento, escolha de stack, índice do documento |
| Back-end | Plano antes de codar, lista de correções antes de aplicar |
| Front-end | Plano antes de codar, lista de correções antes de aplicar |
| DevOps | Não confirma — age de forma autônoma e reporta no final |
| Tester Branca | Lista de testes novos, falhas críticas imediatamente |
| Tester Preta | Lista de casos de teste, falhas críticas imediatamente |

---

## 📄 Licença

MIT — use, adapte e evolua à vontade.
