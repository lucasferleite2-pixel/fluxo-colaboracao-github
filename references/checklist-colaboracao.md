# Checklist de colaboração (fluxo por PR)

## Antes de começar: qual fluxo?

| Você tem escrita no repositório? | Fluxo | `origin` | `upstream` |
|---|---|---|---|
| Não (terceiros, público) | Fork Workflow | seu fork | repositório original |
| Sim (equipe) | Single Repository Workflow | repositório central | — |

## Fork Workflow — passo a passo

```bash
# 1. Fork no GitHub (botão Fork) — cria a cópia na sua conta
# 2. Clone do SEU fork (nunca do original)
git clone https://github.com/SEU-USUARIO/REPO.git
cd REPO

# 3. Adicione o original como upstream
git remote add upstream https://github.com/DONO-ORIGINAL/REPO.git
git remote -v   # origin = seu fork; upstream = original

# 4. Sincronize a main com o upstream
git checkout main
git pull upstream main
git push origin main

# 5. Branch por issue (nome = número da issue)
git checkout -b issue-2

# 6. Trabalhe, commite referenciando a issue
git add <arquivos>
git commit -m "Melhora a dica 1 (refs #2)"

# 7. Envie a branch para o SEU fork
git push -u origin issue-2

# 8. Abra o PR no GitHub: base = DONO-ORIGINAL/REPO:main,
#    comparação = SEU-USUARIO/REPO:issue-2
#    O PR precisa estar mergeável sem conflito.
```

## Single Repository Workflow — passo a passo

```bash
# 1. Clone direto do central (você é colaborador; sem isso o push é recusado)
git clone https://github.com/DONO/REPO.git
cd REPO

# 2. Sincronize a main
git checkout main
git pull origin main

# 3. Branch por issue
git checkout -b issue-5

# 4. Trabalhe, commite referenciando a issue
git add <arquivos>
git commit -m "Adiciona dica 5 (refs #5)"

# 5. Envie a branch
git push -u origin issue-5

# 6. Abra o PR: base = main, comparação = issue-5
```

## Revisão — o ciclo

1. Leia os comentários: separe mudança de código / dúvida / sugestão com a qual discorda
   (responda explicando; não ignore em silêncio).
2. Vá para a branch do PR: `git checkout issue-<número>` (+ `git pull` se outro colaborador
   mexeu nela — `git checkout <branch>` cria o tracking automático da remota de mesmo nome).
3. Faça as mudanças pedidas em NOVOS commits (um por mudança lógica — facilita a revisão).
4. `git push origin issue-<número>` — o PR atualiza sozinho. Não abra PR novo.
5. Responda cada comentário ("Feito em <commit>") e peça nova revisão ("Re-request review").

Formas de comentar no PR: conversa geral; comentário numa linha de código; revisão formal
(comentar / pedir alterações / aprovar). Sem revisão aprovada, não há merge.

## Sincronizar a branch antes do PR (main avançou)

```bash
git fetch origin        # ou: git fetch upstream (fork)
git checkout issue-<número>
git rebase origin/main  # ou: git rebase upstream/main (fork)
# conflito? resolva, git add <arquivo>, git rebase --continue
# desistir: git rebase --abort
```

Depois do rebase, o histórico mudou — o push precisa de force, com a regra do fluxo
(ver `rebase-vs-merge.md`): equipe → `git push --force-with-lease`;
fork → NÃO force-push: nova branch + novo PR.

## Merge e limpeza

- O merge do PR é feito no GitHub como **merge commit** (não "Rebase and merge").
  A mensagem do merge pode fechar a issue ("fecha a issue #2") — a issue fecha sozinha.
- Depois do merge, apague a branch: botão delete branch no PR; local
  `git checkout main && git branch -d issue-<número>`; remoto
  `git push origin -D issue-<número>`.
- Nunca delete a branch em que você está — faça checkout na `main` antes.
- Sincronize a `main` local depois do merge: `git checkout main && git pull`.

## Variantes (o fluxo admite, sem mudar a doutrina)

- **Branch `deploy`:** em vez de PR direto para a `main`, o PR vai para uma branch `deploy`
  (testes lá) e só depois há merge para a `main`. Continua no mesmo repositório.
- **Mudar a branch padrão:** o dono pode definir outra branch como padrão (Settings →
  Branches) — o PR passa a mirar nela em vez da `main`.
- **Proteger a `main`:** Settings → Branches → proteção: bloqueia force-push, exige PR com
  revisão, invalida revisão se o PR desatualizar, define code owners. Administradores
  podem ficar isentos — não deixe isentos sem motivo.
