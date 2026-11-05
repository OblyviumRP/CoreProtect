# Spec de arquitetura: CoreProtect

Proposta, sem código. Base: `oblyvium-26.3` (`9557be6`), CoreProtect CE 24.1 com alvo Paper 26.3, lida em 2026-11-05. Cada fase vira um PR separado.

## Objetivo

Manter o fork fino. O nosso trabalho aqui é um commit ("Target Paper 26.3") sobre o CoreProtect CE. Aplicar SOLID e padrões em 43 mil linhas de terceiro só tornaria caro trazer cada nova versão, justamente num plugin de auditoria e rollback, que não pode falhar.

Fora de escopo: qualquer refatoração do código do CoreProtect.

## Estado medido

- 43 mil linhas, sem testes no repo. `CoreProtectAPI` 1174, `EntityUtil` 815, `CopperGolemChestListener` 803.
- Branch padrão `master`, em uso `oblyvium-26.3`. Regra da org pede a versão em uso na `main`, com exceções listadas, e este repo não está entre elas.
- `build.yml` usa `ubuntu-latest`, contra a regra do runner. Sem execução registrada na branch em uso. Sem autoupdate, jar posto à mão.

## SOLID

| Princípio | Onde | Mudança |
|---|---|---|
| S, O, L, I, D | Código do upstream | Não aplicar. |
| D | Integração nossa | Outros plugins só usam `CoreProtectAPI`. Regra para código nosso futuro. |

## Padrões pedidos

Chain of Responsibility, Método da fábrica e Decorador não têm caso **nosso** aqui: não temos código próprio além do alvo de build. Colocar qualquer um deles seria alterar o upstream.

Se um plugin da org precisar registrar no CoreProtect, o padrão certo é **Adapter** no plugin dele, em volta de `CoreProtectAPI`, nunca patch aqui.

## Fases

1. Regra de branch: pôr `oblyvium-26.3` como `main`, ou registrar a exceção no `CLAUDE.md` e no catálogo. Aceite: catálogo e GitHub dizem o mesmo.
2. Workflow com `runs-on: [self-hosted, ptero]` construindo o jar. Aceite: execução verde e jar igual ao instalado.
3. Registrar neste repo como trazer uma versão nova do CE. Aceite: atualizar uma vez seguindo o texto.
4. Decidir autoupdate com o dev. A regra pede perguntar antes. Aceite: resposta registrada.

## Riscos

- **Dados:** rollback depende do banco. Qualquer atualização passa pelo servidor de teste antes.
- **Thread:** o CE já tem caminhos próprios para Folia. Conferir no boot do ShreddedPaper a cada versão.

## Não fazer

- Refatorar, migrar para `.mb` ou mudar pacote.
- Criar API paralela.
