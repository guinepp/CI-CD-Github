# CI/CD Github

[![CI/CD](https://github.com/guinepp/CI-CD-Github/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/guinepp/CI-CD-Github/actions/workflows/ci.yml)

Prática baseada no tutorial Construção de Esteira CI/CD, de Faber Henrique
Zacarias Xavier (PUC Minas), e no [projeto original](https://github.com/faberhenrique/ex1DataOps).
O histórico de origem foi preservado.

## Fluxo final

```text
push em feature/esteira ou main
  -> validate (Ubuntu + Python 3.11)
     -> checkout -> dependências -> flake8 -> mypy -> bandit -> pytest
     -> executar pipeline -> publicar sales-summary
  -> deploy (somente main, após validate)
     -> baixar sales-summary da mesma execução
     -> conferir arquivos -> entregar production-summary
```

O deploy é uma entrega didática de CSVs ao ambiente production do GitHub
Actions. Não há servidor externo no enunciado: o job promove os mesmos arquivos
aprovados, sem recalculá-los. Os artefatos ficam disponíveis por 30 dias.

## Etapas do PDF

| Etapa | Implementação | Validador |
| --- | --- | --- |
| Inicial | Script e testes locais; corrigidos dados e relatório anual | 4 testes e geração do CSV |
| I | Primeiro gatilho exclusivo em feature/esteira | Push inicia execução |
| II | Job validate, ubuntu-latest | Job concluído |
| III | Checkout e Python 3.11 | Steps concluídas nos logs |
| IV | pip install e flake8 app tests | Lint aprovado |
| V | Pytest, mypy e bandit | Testes, tipagem e segurança aprovados |
| VI | Geração e upload de output/summary.csv | Artefato sales-summary |
| VII | deploy com needs: validate e condição em main | Feature pula deploy; main entrega artefato |

Na etapa VII, main é acrescentada ao gatilho. Manter apenas feature/esteira
impediria o deploy após o merge. Os validadores são executados antes de avançar;
os commits registram a evolução.

## Executar localmente

Requer Python 3.11; a validação local também funciona com Python 3.12.

```bash
python -m venv .venv
```

Ative com `.venv\Scripts\Activate.ps1` no PowerShell ou
`source .venv/bin/activate` em Linux/macOS. Depois:

```bash
python -m pip install -r requirements.txt
python -m flake8 app tests
python -m mypy app
python -m bandit -r app
python -m pytest -q
python app/pipeline.py
```

Resultado de output/summary.csv para o CSV de exemplo:

```csv
total_sales,avg_sales,total_orders
700.0,175.0,4
```

Também é gerado output/annual_summary.csv. Os dados são exemplos fictícios.
O CSV recebeu date para compatibilidade com o relatório anual da base.
A contagem anual usa order_id, com teste para pedidos de mesmo valor.
pandas-stubs foi fixado na versão compatível com pandas 2.2.3.

## Consultar as entregas

Abra [Actions](https://github.com/guinepp/CI-CD-Github/actions), selecione uma
execução bem-sucedida e baixe sales-summary. Em main, também há
production-summary e o registro do ambiente production.
O job falha se um arquivo esperado estiver ausente. Testes e análises bloqueiam
a geração e a entrega em caso de erro; não há continue-on-error.

Para repetir, faça mudanças na branch feature/esteira, envie um push, confira
validate e promova por pull request para main.

## Reflexão técnica

1. **CI e CD:** CI prepara o runtime, instala dependências, verifica estilo,
   tipagem, segurança e comportamento, e gera um artefato aprovado. CD recebe
   esse artefato e o entrega ao ambiente final apenas em main.
2. **Script e esteira:** o script transforma os dados; a esteira automatiza
   quando, onde e sob quais critérios esse script executa e entrega resultados.
   O script também funciona fora do GitHub.
3. **Staging:** entraria após validate e antes de deploy, consumindo o mesmo
   artefato. Testes de integração em staging e, se desejado, aprovação de
   ambiente seriam condições para promover à produção.

## Escopo

As regras de risco adicionais da origem foram preservadas. Radon permanece
como ferramenta exploratória; a régua adicional obrigatória usa mypy e bandit.
O exemplo legado de regras de risco tem alta complexidade; não foi imposto um
limite de radon fora do escopo. Não são necessários secrets nem APIs externas.
