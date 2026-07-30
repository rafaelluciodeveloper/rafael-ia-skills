# rafael-skill

Pacote enxuto de **skills** e **agents** focados em **desenvolvimento de software**, extraído de `claude-skills-main`.

Inclui arquitetura, frontend/backend/fullstack, QA, DevOps, segurança, cloud, dados/ML, CI/CD, revisão de código e padrões de engenharia. Não inclui marketing, finanças, C-level, compliance regulatório nem research acadêmico.

## Conteúdo

| Pasta | O que é | Quantidade (aprox.) |
|-------|---------|---------------------|
| `engineering-team/` | Skills core de papéis de engenharia | ~50 `SKILL.md` |
| `engineering/` | Skills avançadas (CI/CD, agents, DevOps, etc.) | ~80 `SKILL.md` |
| `agents/` | Agents orquestradores (`cs-*`) | 9 agents |
| `commands/` | Slash commands de engenharia | 23 commands |
| `standards/` | Padrões (git, quality, security, documentation) | 4 áreas |

### Agents disponíveis

| Agent | Uso típico |
|-------|------------|
| `cs-senior-engineer` | Arquitetura, code review, CI/CD |
| `cs-frontend-engineer` | Frontend (a11y, performance, design system) |
| `cs-backend-engineer` | Backend (API, DB, SLO, observabilidade) |
| `cs-fullstack-engineer` | Fluxo fullstack completo |
| `cs-engineering-lead` | Coordenação de time e incidentes |
| `cs-karpathy-reviewer` | Review no estilo Karpathy |
| `cs-wiki-ingestor` / `cs-wiki-librarian` / `cs-wiki-linter` | Wiki técnica do projeto |

### Skills core (exemplos)

- `senior-architect`, `senior-frontend`, `senior-backend`, `senior-fullstack`
- `senior-qa`, `senior-devops`, `senior-secops`, `code-reviewer`
- `tdd-guide`, `playwright-pro`, `a11y-audit`
- `ci-cd-pipeline-builder`, `docker-development`, `terraform-patterns`
- `pr-review-expert`, `tech-debt-tracker`, `zero-hallucination-coder`

Cada skill é uma pasta com `SKILL.md` (+ opcionalmente `scripts/`, `references/`, `assets/`).

---

## Claude Code / Claude

### Opção A — Marketplace (se usar o repo original completo)

No Claude Code:

```text
/plugin marketplace add alirezarezvani/claude-skills
/plugin install engineering-skills@claude-code-skills
/plugin install engineering-advanced-skills@claude-code-skills
```

### Opção B — Instalação manual deste pacote (`rafael-skill`)

```bash
# Skills pessoais (valem em todos os projetos)
mkdir -p ~/.claude/skills
cp -r engineering-team/skills/* ~/.claude/skills/
# Skills avançadas que ficam em engineering/skills/
cp -r engineering/skills/* ~/.claude/skills/

# Agents
mkdir -p ~/.claude/agents
cp agents/engineering/*.md ~/.claude/agents/
cp agents/engineering-team/*.md ~/.claude/agents/

# Commands (slash)
mkdir -p ~/.claude/commands
cp commands/*.md ~/.claude/commands/
```

Ou, por projeto:

```bash
# Na raiz do seu repositório
mkdir -p .claude/skills .claude/agents .claude/commands
cp -r /caminho/para/rafael-skill/engineering-team/skills/* .claude/skills/
cp -r /caminho/para/rafael-skill/engineering/skills/* .claude/skills/
cp /caminho/para/rafael-skill/agents/engineering/*.md .claude/agents/
cp /caminho/para/rafael-skill/agents/engineering-team/*.md .claude/agents/
cp /caminho/para/rafael-skill/commands/*.md .claude/commands/
```

### Como usar no Claude

1. Peça a skill pelo nome, por exemplo: *"use a skill senior-architect para desenhar este serviço"*.
2. Invoque um agent: *"rode como cs-backend-engineer neste PR"*.
3. Use slash commands quando disponíveis: `/tdd`, `/tech-debt`, `/cs-frontend-review`, etc.

Skills com scripts Python (stdlib only) podem ser executadas assim:

```bash
python3 engineering/skills/ci-cd-pipeline-builder/scripts/*.py --help
```

---

## Gemini CLI

### Instalação

```bash
cd /caminho/para/rafael-skill

# Índice / symlinks no padrão Gemini
mkdir -p .gemini/skills

# Linkar skills core
for d in engineering-team/skills/*/; do
  name=$(basename "$d")
  ln -sfn "$(pwd)/$d" ".gemini/skills/$name"
done

# Linkar skills avançadas
for d in engineering/skills/*/; do
  name=$(basename "$d")
  ln -sfn "$(pwd)/$d" ".gemini/skills/$name"
done

# Linkar pacotes top-level que têm SKILL.md aninhado (ex.: docker-development)
for d in engineering/*/skills/*/; do
  [ -f "${d}SKILL.md" ] || continue
  name=$(basename "$d")
  ln -sfn "$(pwd)/$d" ".gemini/skills/$name"
done
```

Se você tiver o script do repositório original:

```bash
# A partir de claude-skills-main (repo completo)
./scripts/gemini-install.sh
```

Com este pacote filtrado, prefira os symlinks acima.

### Como usar no Gemini

No Gemini CLI, ative a skill pelo nome da pasta:

```javascript
activate_skill(name="senior-architect")
activate_skill(name="senior-frontend")
activate_skill(name="ci-cd-pipeline-builder")
activate_skill(name="cs-backend-engineer")
```

Coloque um `GEMINI.md` na raiz do projeto apontando para esta pasta, se quiser contexto permanente:

```markdown
# Engenharia

Skills em `rafael-skill/`. Preferir papéis senior-* e agents cs-*-engineer.
```

---

## Cursor Agent (este agente)

No Cursor, skills seguem o padrão Agent Skills (`SKILL.md` + frontmatter YAML).

### Onde instalar

| Escopo | Caminho |
|--------|---------|
| Pessoal (todos os projetos) | `~/.cursor/skills/<nome-da-skill>/` |
| Por projeto (compartilhável no repo) | `.cursor/skills/<nome-da-skill>/` |

**Não** use `~/.cursor/skills-cursor/` — essa pasta é reservada às skills internas do Cursor.

### Instalação rápida (projeto)

```bash
PROJ=/caminho/do/seu/projeto
SRC=/caminho/para/rafael-skill

mkdir -p "$PROJ/.cursor/skills"

# Core
for d in "$SRC"/engineering-team/skills/*/; do
  name=$(basename "$d")
  cp -a "$d" "$PROJ/.cursor/skills/$name"
done

# Avançadas
for d in "$SRC"/engineering/skills/*/; do
  name=$(basename "$d")
  cp -a "$d" "$PROJ/.cursor/skills/$name"
done
```

### Instalação pessoal

```bash
mkdir -p ~/.cursor/skills
# mesmo loop acima, trocando o destino para ~/.cursor/skills
```

### Agents no Cursor

Copie os `.md` de `agents/` para regras ou skills de orquestração:

```bash
mkdir -p .cursor/rules
# Exemplo: transformar um agent em regra de projeto
cp agents/engineering/cs-senior-engineer.md .cursor/rules/cs-senior-engineer.mdc
```

Ou peça explicitamente no chat:

> Leia `rafael-skill/agents/engineering/cs-frontend-engineer.md` e siga esse fluxo neste projeto.

### Como eu (Cursor Agent) uso isto

1. **Automático**: se a skill estiver em `.cursor/skills/` ou `~/.cursor/skills/` com `description` clara no frontmatter, posso invocá-la quando o pedido bater com a descrição.
2. **Explícito**: diga o nome da skill/agent, por exemplo:
   - *"Usa a skill `tdd-guide` nesta feature"*
   - *"Age como `cs-fullstack-engineer` e revisa o PR"*
   - *"Segue `standards/git` e `standards/quality` neste commit"*
3. **Referência pontual**: aponte o caminho do `SKILL.md` ou do agent — eu leio e sigo as instruções.

Exemplo de pedido eficaz:

```text
Use rafael-skill/engineering-team/skills/senior-backend e
rafael-skill/agents/engineering/cs-backend-engineer.md
para desenhar a API de pagamentos deste repo.
```

### Standards (Cursor / Claude / Gemini)

Os padrões em `standards/` não são skills invocáveis isoladas; use-os como regras de projeto:

```bash
# Exemplo: incluir no AGENTS.md ou .cursor/rules
cat standards/git/*.md
cat standards/quality/*.md
cat standards/security/*.md
```

---

## Mapa rápido: o que pedir quando

| Objetivo | Skill / Agent sugerido |
|----------|------------------------|
| Desenhar arquitetura | `senior-architect` / `cs-senior-engineer` |
| Implementar frontend | `senior-frontend` / `cs-frontend-engineer` |
| Implementar backend | `senior-backend` / `cs-backend-engineer` |
| Fullstack ponta a ponta | `senior-fullstack` / `cs-fullstack-engineer` |
| Testes / TDD | `tdd-guide`, `senior-qa`, `playwright-pro` |
| DevOps / pipelines | `senior-devops`, `ci-cd-pipeline-builder`, `docker-development` |
| Segurança | `senior-security`, `skill-security-auditor`, `ai-security` |
| Code review | `code-reviewer`, `pr-review-expert`, `cs-karpathy-reviewer` |
| Dívida técnica | `tech-debt-tracker` + command `tech-debt` |
| Acessibilidade | `a11y-audit` |

---

## Estrutura de pastas

```text
rafael-skill/
├── README.md                 ← este arquivo
├── ORCHESTRATION.md          ← como combinar agents + skills
├── engineering-team/         ← skills core por papel
│   └── skills/
├── engineering/              ← skills avançadas
│   └── skills/ (+ pacotes top-level)
├── agents/
│   ├── engineering/
│   └── engineering-team/
├── commands/                 ← slash commands de engenharia
└── standards/
    ├── git/
    ├── quality/
    ├── security/
    └── documentation/
```

## Origem e licença

Derivado de [alirezarezvani/claude-skills](https://github.com/alirezarezvani/claude-skills) (MIT). Este pacote é um recorte local apenas de conteúdo relacionado a desenvolvimento de software.
