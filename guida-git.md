# Guida completa a Git, Gitflow e GitHub

Versione: 1.0  
Autore: Stefanoo94

## Scopo
Questa guida copre in dettaglio l'uso di Git (concetti e comandi), il workflow Gitflow (con comandi pratici) e le pratiche consigliate su GitHub: repository remoti, pull request, protezione branch, CI/CD, release e gestione collaborativa. È pensata per sviluppatori che vogliono lavorare in team in modo professionale e sicuro.

---

## Indice
- Prerequisiti e installazione
- Configurazione iniziale
- Concetti chiave di Git
- Comandi di base
- Lavorare con i branch
- Merge vs Rebase
- Risolvere conflitti
- Stash, Cherry-pick, Reflog, Reset
- Tag e release
- Gitflow: cos'è e perché usarlo
  - Comandi principali Gitflow
  - Esempio pratico
- GitHub: concetti e funzionalità principali
  - Forks e remotes
  - Pull Request (PR) workflow
  - Protezioni branch e regole d'accesso
  - Issues, Projects e Milestones
  - Releases, Packages e artefatti
  - GitHub Actions (CI/CD) panoramica
  - Pages / Wiki
- Best practice e convenzioni
  - Commit messages
  - Branch naming
  - PR review checklist
- Sicurezza: SSH, PAT, segreti
- Strumenti utili ed estensioni
- Troubleshooting comune
- Cheat sheet rapido
- Risorse

---

## Prerequisiti e installazione

Installare Git:
- Windows: Git per Windows (https://git-scm.com/download/win)
- macOS: `brew install git` oppure installer su git-scm.com
- Linux: `sudo apt install git` (Debian/Ubuntu) o `sudo yum install git` (RHEL/CentOS)

Installare git-flow (opzionale, per usare Gitflow):
- macOS: `brew install git-flow-avh`
- Ubuntu: `apt install git-flow` (o usare script git-flow-avh)

Verifiche:
```bash
git --version
git config --list
```

---

## Configurazione iniziale

Configura identità e strumenti:
```bash
git config --global user.name "Tuo Nome"
git config --global user.email "tuo@esempio.com"
git config --global core.editor "code --wait"       # VSCode come editor
git config --global pull.rebase false               # preferenza default (vedi sotto)
git config --global init.defaultBranch main         # branch iniziale main
```

Consigli utili:
- Usa SSH per autenticazione a GitHub (più sicuro) o PAT se usi HTTPS con 2FA.
- Abilita credential helper per non reinserire password: `git config --global credential.helper manager-core` (Windows/Mac) o `cache` su Linux.

---

## Concetti chiave di Git

- Repository: cartella .git con storia (local repo).
- Working directory: file attuali modificabili.
- Staging area (index): file pronti per il commit (`git add`).
- Commit: snapshot immutabile con metadati.
- Branch: puntatore a un commit (linea di sviluppo).
- Remote: repository remoto (es. origin su GitHub).
- Fast-forward vs non-fast-forward: differenze nel push/merge.

---

## Comandi di base

Inizializzare:
```bash
git init                      # nuovo repo
git clone git@github.com:org/repo.git   # clonare repo
```

Stato, staging e commit:
```bash
git status
git add file.txt
git add -p                       # stage interattivo
git commit -m "Messaggio chiaro"
git commit --amend               # modifica ultimo commit (non pubblicato)
```

Lavorare con remote:
```bash
git remote add origin <url>
git fetch origin
git pull origin main              # aggiorna branch locale (merge)
git push -u origin main           # primo push e set upstream
git push                           # push successivi
```

Visualizzare la storia:
```bash
git log --oneline --graph --decorate -n 50
git show <commit>
git blame file.txt
```

---

## Lavorare con i branch

Creare e cambiare branch:
```bash
git checkout -b feature/nome-feature
# o con Git >=2.23
git switch -c feature/nome-feature
```

Listare e cancellare:
```bash
git branch
git branch -d feature/old         # cancella branch locale (se già mergeato)
git branch -D feature/old         # forza cancellazione
git push origin --delete feature/old  # rimuove remote branch
```

Buona pratica:
- Ogni feature su suo branch
- Branch corti-lived (scope ridotto e PR rapidi)

---

## Merge vs Rebase

Merge (crea commit di merge, preserva storia):
```bash
git checkout main
git merge feature/x
```

Rebase (riscrive storia, linea più pulita):
```bash
git checkout feature/x
git rebase main
# risolve conflitti se appaiono, poi:
git checkout main
git merge --ff-only feature/x   # fast-forward merge
```

Quando usare:
- Rebase per pulire commit locali prima del merge in shared branch.
- Evitare rebase su branch già pubblicati condivisi con altri senza coordinazione.

---

## Risolvere conflitti

1. `git status` per vedere file in conflitto.
2. Aprire file, cercare marker `<<<<<<<`, `=======`, `>>>>>>>`.
3. Risolvere i conflitti manualmente, poi:
```bash
git add file-risolto
git rebase --continue   # se eri in rebase
# o
git commit              # se eri in merge (se git non auto-crea commit)
git push
```

Se vuoi abortire:
```bash
git rebase --abort
git merge --abort
```

---

## Stash, Cherry-pick, Reflog, Reset

Stash (salvare lavoro temporaneo):
```bash
git stash save "WIP: layout header"
git stash list
git stash pop      # applica e rimuove stash
git stash apply    # applica ma lascia nello stash
```

Cherry-pick (applicare commit specifico su altro branch):
```bash
git checkout main
git cherry-pick <commit_hash>
```

Reflog (recuperare commit persi):
```bash
git reflog
git checkout <reflog_hash>   # ispeziona commit "perso"
```

Reset:
- Soft: conserva cambi staged nel working
```bash
git reset --soft HEAD~1
```
- Mixed (default): mantiene modifiche in working dir
```bash
git reset HEAD~1
```
- Hard: PERICOLO, cancella modifiche locali
```bash
git reset --hard HEAD~1
```

---

## Tag e release

Tag lightweight vs annotated:
```bash
git tag v1.0.0              # lightweight
git tag -a v1.0.0 -m "Release v1.0.0"   # annotated
git push origin v1.0.0
```

Creare release GitHub:
- Su GitHub UI: Releases → Draft a new release (linka tag)
- Automatizza con CI per pubblicare artefatti.

---

## Gitflow: cos'è e perché usarlo

Gitflow è un workflow strutturato creato da Vincent Driessen:
- Branch principali: `main` (o `master`) e `develop`
- Branch temporanei: `feature/*`, `release/*`, `hotfix/*`
- Usato per progetti con release regolari, processi di QA.
- Vantaggi:
- Chiarezza tra sviluppo (develop) e produzione (main)
- Procedure standard per release e hotfix

Svantaggi:
- Può essere sovraccarico per team very small o deploy continui (CI/CD + trunk-based dev preferite in quelle realtà).

---

### Comandi principali Gitflow (git-flow AVH)

Inizializza:
```bash
git flow init
# rispondi a default: branch principale = main, development = develop (puoi adattare)
```

Feature:
```bash
git flow feature start nome-feature
# lavora, commit...
git flow feature finish nome-feature   # merge in develop e rimozione branch
```

Release:
```bash
git flow release start 1.2.0
# aggiorna version, changelog
git flow release finish '1.2.0'   # merge in main e develop, crea tag
git push origin --all
git push origin --tags
```

Hotfix:
```bash
git flow hotfix start bugfix-1
# correzione
git flow hotfix finish bugfix-1
# merge su main e develop e tag
```

Nota: git-flow automatizza molte operazioni, ma puoi anche eseguire manualmente gli step equivalent.

---

## Esempio pratico con Gitflow

1. Inizializza repo:
```bash
git init
git checkout -b main
git checkout -b develop
git push -u origin main
git push -u origin develop
```

2. Start feature:
```bash
git checkout develop
git checkout -b feature/login
# sviluppo...
git add .
git commit -m "feat(login): add login form"
git checkout develop
git merge --no-ff feature/login -m "Merge feature/login"
git push origin develop
```

3. Release:
```bash
git checkout develop
git checkout -b release/1.0.0
# bump version, test, change log
git commit -am "chore(release): bump 1.0.0"
git checkout main
git merge --no-ff release/1.0.0
git tag -a v1.0.0 -m "Release 1.0.0"
git checkout develop
git merge --no-ff release/1.0.0
git push origin main develop --tags
```

---

## GitHub: concetti e funzionalità principali

### Repository remoto, fork e remotes
- Fork: copia del repo sotto il tuo account (usato per contributi esterni).
- Remotes: `origin` è di solito il tuo fork/clone remoto; puoi avere `upstream` per il repo originale:
```bash
git remote add upstream git@github.com:original/repo.git
git fetch upstream
git merge upstream/main
```

### Pull Request (PR) workflow
1. Crea branch feature locale
2. Push al tuo remote (`origin/feature/...`)
3. Apri PR su GitHub: target branch (es. develop o main)
4. PR review: reviewer commentano, richiedono changes
5. Merge approvato: usare squash, rebase o merge commit a seconda delle policy

Consigli:
- PR piccoli e mirati
- Descrizione chiara: cosa cambia, perché, how to test
- Link a issue correlati: `Fixes #123`
- Aggiungi checklist e aspettative di testing

### Protezione branch e regole
- Abilita branch protection per `main` / `develop`:
  - Require pull request reviews before merging
  - Require status checks (CI) to pass
  - Require signed commits (opzionale)
  - Restrict who can push

### Issues, Projects e Milestones
- Usa Issues per bug/task
- Projects / GitHub Projects (Kanban) per pianificazione
- Milestones per raggruppare issue/PR verso una release

### Releases, Packages e artefatti
- Releases: tag + changelog + binary assets
- Packages: GitHub Packages per npm, docker images etc.

### GitHub Actions (CI/CD)
- Workflow YAML in `.github/workflows/*`
- Azioni per test, lint, build, deploy, pubblicare release
- Esempio semplice workflow per Node:
```yaml
name: CI
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: node-version: '18'
      - run: npm ci
      - run: npm test
```

### GitHub Pages / Wiki / Discussions
- Pages: hosting statico (branch `gh-pages` o directory `docs/`)
- Wiki: documentazione collaborativa
- Discussions: forum di progetto

---

## Best practice e convenzioni

### Commit messages (convenzionali)
Segui Conventional Commits (opzionale ma utile):
```
<type>(scope?): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```
Esempi:
```
feat(auth): add JWT refresh token
fix(api): handle 500 on /users
chore(deps): bump react to 18.2.0
```

### Branch naming
- feature/..., fix/..., hotfix/..., release/..., chore/...
- Evita spazi e caratteri speciali

### Pull Request checklist
- [ ] Tests pass (CI)
- [ ] Lint OK
- [ ] Documentazione aggiornata
- [ ] Nessun segreto nel diff
- [ ] Changelog o nota di release

---

## Sicurezza: SSH, PAT, segreti

- Usa chiavi SSH per autenticazione a GitHub, non le password.
  - Genera chiave: `ssh-keygen -t ed25519 -C "tuo@esempio.com"`
  - Aggiungi a GitHub: Settings → SSH and GPG keys
- Personal Access Token (PAT): per HTTPS o API; usa scope minimi e durata limitata.
- Segreti in CI: non committare `.env` o segreti; usa GitHub Secrets.
- Signed commits: `git config --global commit.gpgsign true` con GPG o S/MIME.

---

## Strumenti utili ed estensioni

- GUI: GitKraken, SourceTree, GitHub Desktop
- VSCode: estensione GitLens
- Pre-commit hooks: husky, pre-commit
- Linting: `eslint`, `stylelint`
- Git LFS: per file binari grandi (`git lfs install`, `git lfs track "*.psd"`)

---

## Troubleshooting comune

- "Updates were rejected..." → `git pull --rebase origin main` poi risolvi conflitti
- Ho perso un commit: `git reflog` e `git checkout <hash>`
- Ho forzato e ho rotto la storia remota → `git push --force-with-lease` per sicurezza
- Conflitti di rebase su file aggiunti da entrambi (both added) → esamina `:2:file` e `:3:file` con `git show :2:...` e `git show :3:...`

---

## Cheat sheet rapido

Creare branch e lavorare:
```bash
git checkout -b feature/x
git add .
git commit -m "feat: message"
git push -u origin feature/x
# apri PR
```

Sincronizzare:
```bash
git fetch origin
git pull --rebase origin main   # tieni storia lineare
```

Backout rapido:
```bash
git revert <commit>    # crea commit che annulla
git reset --hard <sha> # PERICOLO: riscrive la storia locale
```

Recover:
```bash
git reflog
git checkout -b recover <sha>
```

---

## Risorse
- Pro Git book: https://git-scm.com/book/it/v2  
- GitHub Docs: https://docs.github.com  
- Gitflow Cheatsheet: https://nvie.com/posts/a-successful-git-branching-model/

---

## Checklist di adozione (per team)
- [ ] Configurare branch protections (main/develop)
- [ ] Definire branching strategy (Gitflow o trunk-based)
- [ ] Abilitare CI checks obbligatori
- [ ] Documentare convenzioni commit/branch/PR
- [ ] Fornire template PR e issue
- [ ] Gestire segreti con GitHub Secrets
- [ ] Abilitare scansione vulnerabilità e Dependabot

---

## Se vuoi, posso:
- generare questo file `guida-git.md` nella root del repository per te,
- convertire la guida in PDF (pandoc) e aggiungerla al repo,
- creare template di PR/ISSUE e file di configurazione per GitHub Actions/branch protection.
Dimmi quale operazione vuoi che esegua e te la preparo con i comandi pronti.