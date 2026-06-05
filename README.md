Opção A — Deixar o orquestrador conduzir tudo
> Leia o orquestrador.md e inicie o projeto
O Claude Code vai ler o arquivo, entender o fluxo e começar invocando o Analista via Task automaticamente.
Opção B — Invocar agentes manualmente um por um
Útil para testar ou retomar de onde parou:
> Leia ./prompts/01_analista_requisitos.md e aja como esse agente
> Leia ./prompts/02_dev_backend.md, leia também requisitos.md e etapas_dev.md, depois execute sua etapa

Resumo do que você vai ter no servidor
~/minha-equipe/
  CLAUDE.md                         ← lido automaticamente pelo Claude Code
  orquestrador.md                   ← prompt para iniciar tudo
  prompts/
    01_analista_requisitos.md
    02_dev_backend.md
    03_dev_frontend.md
    04_tester_caixa_branca.md
    05_tester_caixa_preta.md



