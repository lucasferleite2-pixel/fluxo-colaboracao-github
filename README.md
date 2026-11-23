# Colaboração no GitHub — fluxo por PR

Skill para colaborar em repositórios GitHub: branch por issue, sincronização da
`main`, abertura de pull request, revisão, rebase da branch antes do merge,
stash para trocar de tarefa e o fluxo de fork para projetos de terceiros.

## O que cobre

- **Checklist do PR (8 passos):** sincronizar a `main`, branch por issue,
  commits referenciando a issue, push, PR mergeável sem conflito, revisão na
  mesma branch, rebase antes do merge e limpeza da branch depois.
- **Qual fluxo usar:** Fork Workflow (sem escrita no repositório) vs. Single
  Repository Workflow (equipe, com escrita) — e como a regra do rebase muda
  entre eles.
- **Doutrina do rebase:** rebase na branch ANTES do PR, merge commit NO PR,
  nunca rebase em branch compartilhada ou publicada, nunca reescrever a `main`.
- **PR defasado:** no fork, nova branch + novo PR (nunca rebase + force-push na
  branch do PR); na equipe, `pull --rebase` + `--force-with-lease`.
- **Revisão e proteção de branch:** 3 formas de revisão, proteção contra
  force-push, exigência de revisão aprovada, o autor não ser o único revisor.
- **Stash:** pilha, `push`/`pop`/`apply`/`drop`, `-u` e `--keep-index`, conflito
  ao trazer de volta, por que `git stash save` está depreciado.
- **Defasagem:** `master` → `main`, senha → PAT/SSH + 2FA (senha para push por
  HTTPS morreu em 13/08/2021), `git config user.name` não é autenticação.

## O que NÃO cobre

Fundamentos de Git (`add`, `commit`, `log`, `merge` básico, `checkout`) — a
skill aponta para [git-scm.com](https://git-scm.com/doc) em vez de reensinar.
Nada de CI/CD, Actions, GitHub Pages ou administração de organizações.

## Avisos de datação (regras da skill)

- **Branch principal é `main`.** Se o repositório ainda chama a principal de
  `master`, troque o nome nos comandos — o fluxo é o mesmo.
- **Senha não autentica push por HTTPS desde 13/08/2021.** Use PAT (escopo
  `repo`) ou SSH, com 2FA.
- **`git stash save` está depreciado.** A forma atual é `git stash push`.
- **Nomes de menu do GitHub** (Settings → Branches, Collaborators) podem mudar
  com o tempo; o fluxo não muda.

## Como usar

Aponte o agente para `SKILL.md`. A skill define o fluxo, a doutrina do rebase
e as regras duras; os detalhes vivem em `references/`:

| Arquivo | Conteúdo |
|---|---|
| `SKILL.md` | Princípio, checklist, qual fluxo, doutrina do rebase, revisão, stash, defasagem, racionalizações proibidas, red flags |
| `references/checklist-colaboracao.md` | Passo a passo dos dois fluxos, ciclo de revisão, sincronizar a branch, merge e limpeza, variantes |
| `references/rebase-vs-merge.md` | O que cada um faz, a doutrina em 3 frases, regra de ouro, PR defasado por fluxo, rebase interativo, erros comuns |
| `references/stash-fork.md` | Conceito e comandos do stash, fluxo de troca de tarefa, conflito no retorno, fluxo completo de fork |
| `references/defasagem.md` | O que mudou desde 2021: `main`, PAT/SSH + 2FA, `user.name`, sintaxe de stash, terminologia |

## Método

Construída pelo método Superpowers `writing-skills` (TDD aplicado a
skills): 7 cenários de teste (baseline 0/7 PASS — 3 FAIL, 3 FAIL parcial,
1 QUASE PASS → com skill 7/7 PASS) + 2 micro-testes de pressão (T1/T2, 2/2
PASS).

## Licença

MIT — ver `LICENSE`.
