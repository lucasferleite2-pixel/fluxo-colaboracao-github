# Defasagem — o que mudou desde 2021

O fluxo desta skill usa os padrões atuais. O padrão antigo (branch `master` e senha
para push, de 2021) está defasado nos pontos abaixo. As seções marcadas (SUPLEMENTO)
registram padrões atuais posteriores a 2021.

## 1. `master` → `main` (SUPLEMENTO)

- O padrão antigo usava `master` como branch principal.
- O padrão atual é `main` (o GitHub usa `main` para repositórios novos desde outubro de
  2020). Todos os comandos desta skill usam `main`.
- Se o repositório que você está usando ainda chama a principal de `master`, troque o
  nome nos comandos — o fluxo é o mesmo.

## 2. Senha → PAT / SSH + 2FA (SUPLEMENTO)

- O padrão antigo autenticava o push com usuário e senha. **Senha para push por HTTPS
  morreu no GitHub em 13/08/2021** — mesmo com a senha certa, o push é recusado.
- O que usar no lugar:
  - **PAT (Personal Access Token):** GitHub → Settings → Developer settings → Personal
    access tokens → Generate new token (classic), escopo `repo`. No push, usuário = seu
    login, senha = o token (começa com `ghp_`, aparece uma só vez). Limpe a credencial
    errada salva: `printf "protocol=https\nhost=github.com\n" | git credential reject`.
    Guarde com `git config --global credential.helper store` (Linux) / `osxkeychain`
    (macOS) / `manager` (Windows).
  - **SSH (recomendado):** `ssh-keygen -t ed25519 -C "seu@email.com"` → adicione a chave
    pública em Settings → SSH and GPG keys → teste com `ssh -T git@github.com` → troque o
    remote: `git remote set-url origin git@github.com:USUARIO/REPO.git`.
  - **2FA:** com autenticação de dois fatores ativa, a senha sozinha nunca basta — PAT ou
    SSH é o caminho.
- Sem permissão de escrita no repositório, nem o token válido resolve: faça fork ou peça
  acesso como colaborador.

## 3. `git config user.name` NÃO é autenticação

- `git config user.name` / `user.email` definem **quem aparece como autor do commit** —
  o nome registrado no histórico. Não autenticam nada e não concedem permissão de push.
- Configurar `user.name`/`user.email` por repositório (sem `--global`) ao criar
  o projeto é configuração de identidade, não conserto de push.
- Se alguém disser que configurar o `user.name` resolveu o push, foi coincidência (um
  token já salvo, ou a troca para SSH junto, sem perceber).

## 4. Sintaxe antiga de stash (SUPLEMENTO)

- A forma antiga `git stash save -m "msg"` está depreciada. A forma atual é
  `git stash push -m "msg"`. O comportamento é o mesmo.

## 5. Terminologia correta

- **Feature Branch Strategy**: uma branch por feature/issue, a partir da `main`.
- **branches**, **rebase** e **push** — grafias que às vezes aparecem erradas em
  fontes antigas ("brands", "rebates", "punch").

## 6. O que NÃO mudou

- A doutrina de rebase/merge continua válida: rebase na branch antes do PR,
  merge commit no PR, nunca rebase em branch compartilhada.
- O fluxo de PR, review e limpeza pós-merge continua o mesmo.

## Fontes

- Padrões atuais: nome `main` (GitHub, desde outubro de 2020), autenticação PAT/SSH +
  2FA (GitHub, agosto de 2021), sintaxe `git stash push`.
- Fundamentos de Git fora do escopo desta skill: [git-scm.com](https://git-scm.com/doc).
