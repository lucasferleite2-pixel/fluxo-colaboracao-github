# Rebase vs. merge — a doutrina exata

## O que cada um faz

- **Merge**: integra uma branch na outra criando um commit de merge. Não destrói nada:
  o histórico mostra a ramificação — o que aconteceu antes e depois fica visível.
  Repetido muitas vezes na mesma branch, polui o histórico e dificulta rastrear mudanças.
- **Rebase**: pega os commits da sua branch, guarda numa área temporária, move a base da
  branch para a frente da `main` e reinsere os commits por cima — linear, sem commit de
  merge. Os commits ganham **novos hashes**. Por isso rebase é reescrita de histórico.

## A doutrina (3 frases)

1. **Rebase na branch ANTES do PR** — para atualizar a branch com a `main` e deixar o
   histórico limpo antes de pedir revisão.
2. **Merge commit NO PR** — o merge do PR no GitHub é feito como merge commit, para que
   o ponto do PR fique visível no histórico. O modo "Rebase and merge" do GitHub é
   rejeitado: ele some com a ramificação do PR.
3. **Nunca "rebase sempre"** — rebase não é substituto geral do merge. Ele serve para a
   sua branch, antes de publicar.

## A regra de ouro

**Nunca faça rebase numa branch compartilhada ou publicada.** Quem já baixou a branch
quebra, porque os hashes mudaram. A documentação do Git diz o mesmo — e a proteção de
branch do GitHub bloqueia o force-push na `main` justamente por isso.

## O PR defasou — dois caminhos, por fluxo

### Fluxo Fork (projeto público, você não tem escrita)

A branch do PR já está publicada e compartilhada com o PR aberto. Rebase + force-push
nela dessincroniza o que o dono do repositório já viu.

```bash
# 1. Crie uma NOVA branch a partir da branch do PR (a nova ainda não foi publicada)
git checkout issue-5
git checkout -b issue-5-v2

# 2. Rebase da nova branch com a main atual do upstream
git fetch upstream
git rebase upstream/main
# conflito? resolva, git add <arquivo>, git rebase --continue

# 3. Envie a nova branch e abra um NOVO PR
git push -u origin issue-5-v2
# No novo PR, escreva: "Continua o processo iniciado no PR #6"

# 4. Feche o PR antigo avisando que vai submeter um novo pedido com os ajustes
```

O PR antigo fica fechado sem merge, mas rastreável: o novo PR referencia o número dele.

### Fluxo de equipe (repositório único, você tem escrita)

Aqui o rebase na branch do PR + force-push é aceito — desde que seja a convenção da
equipe e o push seja com `--force-with-lease`, nunca `--force` seco.

```bash
git checkout issue-5
git fetch origin
git pull --rebase origin/main
# conflito? resolva, git add <arquivo>, git rebase --continue
git push --force-with-lease origin issue-5
```

`--force-with-lease` aborta se alguém atualizou a branch remota depois do seu último
fetch — `--force` seco sobrescreveria o trabalho dela sem avisar.

## Rebase interativo (limpar o histórico ANTES de publicar)

Só em branch local, nunca publicada:

```bash
git rebase -i <ponto-de-partida>   # ex.: git rebase -i HEAD~3
```

No editor: `pick` (manter), `reword` / `r` (trocar a mensagem), `squash` / `s`
(juntar com o commit de cima), reordenar as linhas para mudar a ordem dos commits.
Salvar e fechar aplica. Conflito no meio: resolva, `git add`, `git rebase --continue`;
para pular o commit em conflito: `git rebase --skip`; para desistir: `git rebase --abort`.

## Erros comuns

| Erro | Por que está errado |
|---|---|
| `git rebase` na `main` | Reescreve a branch de todos. A `main` se atualiza com `pull`, nunca com rebase. |
| `git push --force` na branch do PR (fork) | A branch já é pública/compartilhada — dessincroniza o PR. Use nova branch + novo PR. |
| `git push --force` seco | Pode apagar commits de outra pessoa. Use `--force-with-lease`. |
| "Rebase and merge" no botão do PR | Some com o ponto do PR no histórico. Use merge commit. |
| Fazer merge da `main` na branch do PR "para atualizar" | Polui o histórico com um commit de merge que não diz nada. Rebase a branch. |
| Rebasear e depois abrir PR novo sem referenciar o antigo | Perde o rastreio da discussão. O novo PR referencia o antigo. |
