# Equipe de Agentes — Guia de Uso

## Visão geral
Esta pasta contém os prompts de sistema dos 6 agentes da equipe de desenvolvimento web.

## Os agentes

| Arquivo | Agente | Papel |
|---------|--------|-------|
| `01_analista_requisitos.md` | Analista | Conversa com você, levanta requisitos, gera `requisitos.md` e `etapas_dev.md` |
| `02_dev_backend.md` | Dev Back-end | Implementa APIs, banco de dados, autenticação |
| `03_dev_frontend.md` | Dev Front-end | Implementa interface React, integra com a API |
| `04_devops.md` | DevOps | Configura ambiente, sobe serviços (tmux), valida CORS, gera `devops_report.md` |
| `05_tester_caixa_branca.md` | Tester (branca) | Testa o código interno do back-end, segurança, cobertura |
| `06_tester_caixa_preta.md` | Tester (preta) | Testa o sistema pela interface, fluxos E2E, UX |

## Como usar no Claude Code

### 1. Configurar cada agente
No Claude Code, ao invocar um subagente via ferramenta `Task`, passe o conteúdo do `.md` correspondente como `system_prompt`.

### 2. Ordem de execução
```
Analista → Back-end + Front-end (paralelo) → DevOps (sobe o ambiente) → Testers (paralelo) → Loop de correção
```

### 3. Canal de comunicação
Os agentes se comunicam via arquivos. Estrutura esperada no projeto:

```
/seu-projeto/
  requisitos.md          ← Analista escreve
  etapas_dev.md          ← Analista escreve
  api_contract.md        ← Back-end escreve
  backend_spec.md        ← Back-end escreve
  frontend_spec.md       ← Front-end escreve
  devops_report.md       ← DevOps escreve (URLs, portas, status)
  test_report_white.md   ← Tester Branca escreve (PASS/FAIL)
  test_report_black.md   ← Tester Preta escreve (PASS/FAIL)
  /src/backend/
  /src/frontend/
```

### 4. Loop de revisão (pseudocódigo)
```python
MAX_ITERACOES = 3

for i in range(MAX_ITERACOES):
    run_agent("backend", context=["requisitos.md", "etapas_dev.md"])
    run_agent("frontend", context=["requisitos.md", "api_contract.md"])

    # DevOps sobe o ambiente antes dos testes
    run_agent("devops", context=["requisitos.md", "backend_spec.md", "frontend_spec.md"])

    white = run_agent("tester_branca", context=["backend_spec.md", "/src/backend/"])
    black = run_agent("tester_preta", context=["frontend_spec.md", "/src/frontend/"])

    if white.status == "PASS" and black.status == "PASS":
        break  # ✅ Concluído

    # Passa os relatórios para correção
    if white.status == "FAIL":
        run_agent("backend", context=["test_report_white.md"])
    if black.status == "FAIL":
        run_agent("frontend", context=["test_report_black.md"])
```

## Pontos de confirmação
Todos os agentes têm **autonomia média** — eles pausam e confirmam com você em decisões importantes:
- Analista: após entendimento geral, antes de recomendar stack, antes de finalizar docs
- Back-end: antes de implementar (apresenta plano), antes de corrigir bugs
- Front-end: antes de implementar (apresenta plano), antes de corrigir bugs
- DevOps: antes de subir/derrubar serviços, se encontrar erro de ambiente desconhecido
- Testers: antes de criar novos testes, se encontrar falha crítica

## Stack configurada
- **Front-end:** React 18 + Vite + TailwindCSS + shadcn/ui
- **Back-end:** Node.js + Express + Prisma
- **Auth:** JWT + bcrypt
- **Testes:** Vitest (unit) + Playwright (E2E)
- **Banco:** PostgreSQL (local)
- **Infra/DevOps:** tmux (serviços em background) + Prisma (migrations)
