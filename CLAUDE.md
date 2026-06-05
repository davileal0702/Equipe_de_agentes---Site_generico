# Equipe de Agentes — Configuração

## Sobre este projeto
Este diretório contém uma equipe de 6 agentes especializados para desenvolvimento web com Node.js + React.

## Como invocar os agentes
Use a ferramenta Task com o conteúdo do prompt correspondente em `./prompts/`.

## Agentes disponíveis e seus prompts
| Agente | Arquivo do prompt | Quando usar |
|--------|-------------------|-------------|
| Analista | ./prompts/01_analista_requisitos.md | Início de qualquer projeto novo |
| Dev Back-end | ./prompts/02_dev_backend.md | Após requisitos aprovados |
| Dev Front-end | ./prompts/03_dev_frontend.md | Após requisitos aprovados |
| DevOps | ./prompts/04_devops.md | Após back-end e front-end implementados, antes dos testes |
| Tester Branca | ./prompts/05_tester_caixa_branca.md | Após back-end implementado e ambiente no ar |
| Tester Preta | ./prompts/06_tester_caixa_preta.md | Após front-end implementado e ambiente no ar |

## Arquivos de comunicação entre agentes
Os agentes se comunicam via estes arquivos (criados durante o trabalho):
- `requisitos.md` — gerado pelo Analista
- `etapas_dev.md` — gerado pelo Analista
- `api_contract.md` — gerado pelo Back-end
- `backend_spec.md` — gerado pelo Back-end
- `frontend_spec.md` — gerado pelo Front-end
- `devops_report.md` — gerado pelo DevOps
- `test_report_white.md` — gerado pelo Tester Branca
- `test_report_black.md` — gerado pelo Tester Preta

## Regras globais da equipe
- Todo agente deve ler os arquivos de contexto antes de agir
- Nunca pular a etapa de confirmação com o usuário
- Em caso de ambiguidade, parar e perguntar
- Relatórios de teste com FAIL bloqueiam o avanço
