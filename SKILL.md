---
name: fluxo-colaboracao-github
description: Use when collaborating on GitHub: branching per issue, syncing main, opening a pull request, reviewing, rebasing your branch before merge, stashing work in progress, or working through a fork.
---

# Colaboração no GitHub — fluxo por PR

## Princípio

Toda mudança entra por **branch por issue → pull request → revisão → merge commit**.
A `main` nunca recebe commit direto, e o histórico do projeto não é reescrito depois de
publicado. O fluxo tem duas variantes — fork e repositório único — e a regra do rebase muda
entre elas. Confundir as variantes é o erro mais comum.

## O checklist (siga nesta ordem)

1. **Sincronize a `main`.** `git checkout main && git pull` (no fork: `git pull upstream main`
   e `git push origin main`). Nunca crie a branch a partir de uma `main` desatualizada.
2. **Crie uma branch por issue, com o nome da issue.** `git checkout -b issue-<número>`
   (ex.: `issue-2`). Nunca use a `main` para o PR — ela fica sincronizada com o upstream.
3. **Trabalhe e commite referenciando a issue.** `git commit -m "... (refs #<número>)"`.
   O merge do PR pode fechar a issue na mensagem ("fecha a issue #2").
4. **Envie a branch.** `git push -u origin issue-<número>` (no fork: `origin` é o seu fork).
5. **Abra o pull request.** Base = `main` do repositório original. O PR precisa estar
   mergeável sem conflito — o dono do repositório não resolve conflito que você gerou.
6. **Revisão: trabalhe NA MESMA branch.** Pediram mudanças → novos commits na mesma branch
   + push. O PR atualiza sozinho (o GitHub rastreia a branch, não o PR). Não abra PR novo
   nem faça merge local.
7. **Rebase na branch antes do merge; merge commit no PR.** Ver a seção abaixo — a forma
   do rebase depende do fluxo.
8. **Limpeza.** Depois do merge, apague a branch (botão delete branch no PR;
   `git branch -d issue-<número>` local e `git push origin -D issue-<número>` remoto).

Detalhes e comandos: [checklist-colaboracao.md](references/checklist-colaboracao.md).

## Qual fluxo? (decida pelo acesso de escrita)

| Você tem escrita no repositório? | Fluxo | Clone de |
|---|---|---|
| Não (projeto de terceiros, público) | **Fork Workflow** | seu fork (`origin`); original é `upstream` |
| Sim (equipe, você é colaborador) | **Single Repository Workflow** | repositório central (`origin`) |

Fork: fork no GitHub → clone do SEU fork → `git remote add upstream <url-do-original>` →
passos 1–8 acima. Nunca clone o original e tente push nele.
Equipe: clone direto do central → passos 1–8. O dono adiciona você como colaborador
(aba Collaborators) — sem isso o push é recusado.

## A doutrina do rebase (leia antes de responder "rebase ou merge?")

- **Rebase na branch ANTES do PR; merge commit NO PR.** O merge do PR é feito no GitHub
  como merge commit (não "Rebase and merge", que some com o ponto do PR no histórico).
- **Nunca "rebase sempre".** Rebase serve para atualizar a SUA branch com a `main` antes
  do PR e para limpar o histórico ANTES de publicar. Não é um substituto geral do merge.
- **Nunca rebase em branch compartilhada ou publicada.** Rebase reescreve hashes; quem já
  baixou a branch quebra. É a regra de ouro.
- **Nunca reescreva a `main`.** Nada de rebase na `main`, nada de `push -f` na `main`
  (a proteção de branch bloqueia force-push nela).

### O PR defasou — o que fazer depende do fluxo

| Fluxo | PR defasado (main avançou, conflito) |
|---|---|
| **Fork** (público) | NÃO faça rebase + force-push na branch do PR. Crie uma NOVA branch a partir da antiga, faça o rebase NELA (ainda não publicada), FECHE o PR antigo avisando e abra um NOVO PR referenciando o antigo ("continua o PR #6"). |
| **Equipe** (repositório único) | `git pull --rebase` na branch do PR, resolva, `git push --force-with-lease` (só na branch do PR). Aceito quando a equipe trabalha assim — nunca `--force` seco. |

Detalhes: [rebase-vs-merge.md](references/rebase-vs-merge.md).

## Revisão e proteção de branch

- Revisão tem 3 formas: conversa no PR, comentário em linha, revisão formal (comentar /
  pedir alterações / aprovar). Sem revisão aprovada não há merge.
- Proteção de branch (dono do repositório, Settings → Branches): bloqueia force-push,
  exige PR com revisão antes do merge, invalida revisão quando o PR fica desatualizado.
- O autor do PR não deve ser o único revisor: se você pode abrir o PR e mergeá-lo
  sozinho, o PR não está servindo para nada.

## Stash — trocar de tarefa no meio do trabalho

- Trabalho inacabado e precisa trocar de branch: `git stash` → troca → resolve o outro
  problema → `git stash pop` (apply + drop) ou `git stash apply` (mantém na pilha, depois
  `git stash drop`).
- A pilha: `git stash list`, `git stash show -p` (ver o diff), `git stash drop <código>`
  (sem código = o do topo). `git stash push -m "msg"` para nomear (a forma antiga
  `git stash save -m` está depreciada).
- `--keep-index`: só o que NÃO está na stage vai para o stash; o staged fica.
- `-u`: inclui arquivos untracked — sem `-u`, untracked NÃO entra no stash.
- Conflito ao trazer de volta: o Git para e não aplica. Commite ou descarte o trabalho
  local primeiro; com um commit anterior salvo, dá para resolver o conflito e commitar.
- Não use commit WIP + reset como substituto do stash: é o improviso cansativo que o
  stash existe para substituir.
- Detalhes: [stash-fork.md](references/stash-fork.md).

## Defasagem — o que mudou desde 2021

O fluxo abaixo usa `main` e autenticação por PAT/SSH + 2FA. O padrão antigo usava
`master` e senha — os dois estão defasados. Regras: [defasagem.md](references/defasagem.md).
Resumo: senha para push por HTTPS morreu em **13/08/2021**; `git config user.name` é só a
identidade do commit — não autentica nada e não concede permissão de push.

## Fora do escopo

Esta skill não ensina fundamentos de Git (`add`, `commit`, `log`, `merge` básico,
`checkout`): [git-scm.com](https://git-scm.com/doc). Se a pergunta é sobre o básico,
aponte para lá em vez de reensinar.

## Racionalizações proibidas

| Racionalização (vista no baseline) | Contraponto |
|---|---|
| "É urgente, commito direto na main" | Nunca. Urgência não muda o fluxo: branch por issue + PR. A proteção de branch existe para impedir exatamente isso. |
| "Merge ou rebase, tanto faz, depende do time" | Não é tanto faz: rebase na branch antes do PR, merge commit no PR. O merge na branch polui o histórico. |
| "Rebase + force-push é o preferido em projetos públicos" | Falso no fluxo Fork: branch de PR publicada não se rebaseia — nova branch + novo PR. |
| "WIP commit é equivalente ao stash" | Não: o commit WIP + reset é o improviso que o stash substitui. Use `git stash`. |
| "Vou configurar o `user.name` que o push resolve" | `user.name` é identidade do commit, não credencial. Push pede PAT ou SSH (+ 2FA). |
| "O autor pode mergear o próprio PR" | Pode tecnicamente; o PR sem revisão independente não serve para nada. Exija revisão. |
| "Rebase sempre, histórico sempre linear" | Nunca "rebase sempre": rebase só na sua branch, antes de publicar. Branch compartilhada não se rebaseia. |

## Red flags (a resposta está errada se...)

- Manda commitar direto na `main`, mesmo "só dessa vez" ou "só um arquivo".
- Apresenta merge e rebase como opções intercambiáveis sem dizer qual o fluxo usa.
- Ensina `git push --force` (seco) ou force-push na `main`.
- Ensina rebase + force-push na branch de um PR aberto num fork.
- Sugere `git config user.name` para resolver erro de autenticação.
- Usa `master` como nome da branch principal sem avisar que o padrão atual é `main`.
- Ensina `git stash save` sem notar que a forma atual é `git stash push`.
