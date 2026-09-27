# Evidências da prática

Validado localmente em Python 3.12 e no GitHub em Python 3.11.
Execução local: 4 testes aprovados, flake8 sem erros, mypy sem erros
em 4 arquivos e bandit sem achados. Resumo: total 700, média 175, 4 pedidos.
Relatório anual: 350 em 2023 e 350 em 2024, com 2 pedidos em cada ano.

Cada validador abaixo concluiu com sucesso antes de avançar para a etapa seguinte:

| Etapa | Execução |
| --- | --- |
| I e II: trigger e runner | [36348649831](https://github.com/guinepp/CI-CD-Github/actions/runs/36348649831) |
| III: checkout e Python | [36348680805](https://github.com/guinepp/CI-CD-Github/actions/runs/36348680805) |
| IV: dependências e lint | [36348811960](https://github.com/guinepp/CI-CD-Github/actions/runs/36348811960) |
| V: testes, mypy e bandit | [36348911972](https://github.com/guinepp/CI-CD-Github/actions/runs/36348911972) |
| VI: geração e upload | [36348979367](https://github.com/guinepp/CI-CD-Github/actions/runs/36348979367) |

A etapa VI publicou `sales-summary`, contendo os dois CSVs.
A etapa VII acrescenta a branch main ao gatilho e o job deploy condicionado
a main, dependente de validate. Consulte as execuções finais em
[Actions](https://github.com/guinepp/CI-CD-Github/actions).

O primeiro push da base para main registrou uma falha de configuração:
o arquivo ci.yml original estava vazio. As execuções incrementais substituem
esse arquivo por um workflow válido; o histórico de falha foi preservado.
