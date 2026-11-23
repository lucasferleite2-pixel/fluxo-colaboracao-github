# Stash e fork — referência

## Stash: o conceito

O stash guarda trabalho inacabado numa pilha temporária para você trocar de tarefa sem
perder nada — e sem precisar criar branch, commitar e desfazer o commit depois (o
improviso que o stash substitui). A pilha aceita vários trabalhos empilhados; cada um é
recuperado pelo código (`stash@{0}` = o do topo).

## Comandos

```bash
git stash push -m "descrição do trabalho"   # guarda tudo (staged + unstaged rastreado)
git stash push -u -m "msg"                  # inclui arquivos untracked (-u)
git stash push --keep-index -m "msg"        # só o NÃO-staged vai; o staged fica

git stash list                              # pilha: stash@{0}, stash@{1}, ...
git stash show -p                           # diff do stash do topo
git stash show -p stash@{1}                 # diff de um stash específico

git stash pop                               # traz de volta E remove da pilha
git stash apply                             # traz de volta e MANTÉM na pilha
git stash apply stash@{1}                   # traz um stash específico
git stash drop                              # remove o do topo
git stash drop stash@{1}                    # remove um específico
```

Regras que o baseline erra ou omite:

- `apply` **não** remove da pilha — precisa de `drop` depois. `pop` = apply + drop.
- Sem código, todo comando opera no **topo** (`stash@{0}`).
- Sem `-u`, arquivos **untracked não entram** no stash.
- A forma antiga `git stash save -m "msg"` está depreciada — use `git stash push -m`.
- O stash não é branch: ele não se integra com merge no projeto; serve para trabalho
  em progresso que ainda não está pronto para commit.

## O fluxo completo (trocar de tarefa)

```bash
git status                     # veja o que está pendente
git stash push -m "wip: capítulo 2"   # guarda o trabalho inacabado
git status                     # área limpa — pode trocar de branch
git switch <outra-branch>      # resolva o outro problema, commite
git switch <branch-original>   # volte
git stash pop                  # traz o trabalho de volta (e remove da pilha)
```

## Conflito ao trazer o stash de volta

O Git **para e não aplica** — não faz merge automático. Se o working tree tem alterações
que seriam sobrescritas, o `pop`/`apply` falha e avisa:

1. Commite o trabalho local atual (aí dá para resolver o conflito e commitar de novo), ou
2. Descarte as alterações locais (`git checkout -- <arquivo>`) e traga o stash.

Com um commit anterior salvando o trabalho local, o conflito pode ser resolvido
normalmente e commitado — o `git log` mostra todo o histórico.

## Fork: o fluxo completo (referência rápida)

```bash
# No GitHub: botão Fork no repositório original
git clone https://github.com/SEU-USUARIO/REPO.git
cd REPO
git remote add upstream https://github.com/DONO-ORIGINAL/REPO.git
git remote -v
# origin = seu fork; upstream = original

# Sincronizar o fork com o original (a cópia é uma foto do momento do fork)
git checkout main
git pull upstream main
git push origin main

# Contribuir: branch por issue a partir da main sincronizada
git checkout -b issue-2
# ... trabalhar, commitar referenciando a issue ...
git push -u origin issue-2
# Abrir o PR no GitHub: base = original:main, comparação = seu-fork:issue-2

# Depois do merge: sincronizar de novo e limpar
git checkout main
git pull upstream main
git push origin main
git branch -d issue-2
git push origin --delete issue-2
```

Pontos que o baseline erra:

- **Nunca trabalhe direto na `main` do fork** — ela fica sincronizada com o upstream;
  toda alteração vai numa branch separada.
- **Nunca clone o original e tente push nele** — sem escrita, o push é recusado; o
  caminho é o fork.
- **PR defasado no fork: nada de rebase + force-push na branch do PR.** Nova branch a
  partir da antiga, rebase nela, fecha o PR antigo, abre novo PR referenciando o antigo
  (ver `rebase-vs-merge.md`).
- O dono do repositório não resolve conflito que você gerou: o PR precisa estar
  mergeável sem conflito.
