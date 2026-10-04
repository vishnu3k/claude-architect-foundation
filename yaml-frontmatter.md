# 📘 YAML Frontmatter

> **Claude Architect Certification – Foundation** · Study Note

**One-liner:** YAML frontmatter is a block of `key: value` metadata at the very top of a text file, fenced by two `---` lines. It tells *tools* about the file; the content below is for *humans/the model*.

---

## 🧠 Concept in 60 seconds

```markdown
---
key: value
---

Everything below is the normal file body.
```

| Rule | Detail |
|---|---|
| Position | Must start on **line 1**. Nothing above it (no blank line, no space, no BOM issues) |
| Fences | Opening `---` and closing `---`, each on its own line |
| Syntax | Valid YAML: `key: value`, lists, nested maps |
| Indentation | **Spaces only**, never tabs |
| Purpose | Machine-readable metadata (name, description, tools, model, tags, dates) |
| Body | Plain Markdown after the closing fence |

---

## 🧪 Examples

### 1. Blog post (Jekyll / Hugo / GitHub Pages)
```yaml
---
title: "Intro to Prompt Caching"
date: 2026-10-04
tags: [claude, caching, cost]
draft: false
---
```

### 2. Claude Skill (`SKILL.md`)
```yaml
---
name: pdf-helper
description: Extract text and tables from PDFs. Use when the user uploads or mentions a PDF.
---
```
The `description` is what Claude reads to decide **whether to load the skill**. The body is only loaded after that.

### 3. Claude Code subagent (`.claude/agents/reviewer.md`)
```yaml
---
name: code-reviewer
description: Reviews diffs for bugs and style. Use proactively after code changes.
tools: Read, Grep, Glob
model: sonnet
---
You are a senior code reviewer. Focus on correctness first...
```

### 4. Claude Code slash command (`.claude/commands/commit.md`)
```yaml
---
description: Create a conventional commit message
argument-hint: [scope]
allowed-tools: Bash(git diff:*), Bash(git commit:*)
---
Write a commit message for the staged changes. Scope: $ARGUMENTS
```

> ⚠️ Exact supported keys vary by feature and version. Verify against the current official docs before the exam.

---

## 🔍 Troubleshooting Breakdown

**Scenario:** A `SKILL.md` sits in the right folder, but Claude never uses it.

### 1. The symptom
- The skill is present on disk but **never triggers** (or never appears as available).
- No obvious error; Claude just behaves as if the skill doesn't exist.

### 2. What is NOT the problem
- ❌ The quality or length of the instructions in the **body** (never read if the skill isn't loaded)
- ❌ The model's capability or version
- ❌ The user's prompt wording
- ❌ Folder location (it's correct in this scenario)

### 3. The actual culprit (quoted verbatim)
The broken file, exactly as written:

```text
 ---
name: pdf-helper
description: Use when: the user mentions PDFs
---
```

Two defects:
1. **Leading space** before the opening `---`, so the frontmatter is not on line 1 and isn't recognized.
2. **`Use when: the user...`** has an unquoted `: ` inside a plain scalar, which is invalid YAML (`mapping values are not allowed here`).

> I used the faulty file itself as the "verbatim" evidence, since I can't verify a word-for-word quote from official docs without the source. If your course material has a specific quoted line, paste it here and I'll swap it in.

### 4. The implicit fix category
**Configuration / metadata syntax fix**, not prompt engineering, not model change, not adding more instructions.

```yaml
---
name: pdf-helper
description: "Use when the user mentions PDFs: extract text and tables"
---
```

### 5. Traps
| Trap | Why it's wrong |
|---|---|
| "Rewrite the instructions to be clearer" | Body is never reached if frontmatter fails |
| "Switch to a larger model" | Parsing failure is model-independent |
| "Add more examples to the body" | Same: wrong layer |
| "A blank line before `---` is harmless" | Frontmatter must begin at line 1 |
| "Tabs are fine for indentation" | YAML forbids tabs |
| "Colons in descriptions are fine" | Quote any value containing `: ` or starting with special chars (`*`, `&`, `#`, `@`) |
| "The body can hold the trigger conditions" | Trigger info belongs in `description` |

---

## ⚡ Quick Review

### Cheat sheet
- Starts at **line 1**, fenced by `---` / `---`
- Valid **YAML**, spaces not tabs
- **Quote** values with `:` or `#`
- `description` = **routing signal** (when to use)
- Body = **content** (how to do it)
- Broken frontmatter → silent non-loading, **fix config, not prompts**

### 🃏 Flashcards (click to reveal)

<details>
<summary><b>Q1.</b> Where must frontmatter appear in a file?</summary>

At the very top, starting on line 1, delimited by `---` lines.
</details>

<details>
<summary><b>Q2.</b> A skill never triggers and the body is well written. What do you check first?</summary>

The frontmatter: position, delimiters, YAML validity, and the `description` field.
</details>

<details>
<summary><b>Q3.</b> Why does <code>description: Use when: X</code> fail?</summary>

An unquoted `: ` inside a plain scalar is a YAML parse error. Wrap the value in quotes.
</details>

<details>
<summary><b>Q4.</b> Which part does Claude use to decide whether to load a skill?</summary>

The `description` in the frontmatter.
</details>

<details>
<summary><b>Q5.</b> Can you indent frontmatter with tabs?</summary>

No. YAML requires spaces.
</details>

### 📝 Practice question

> A team's custom skill is in the correct directory but is never invoked. The instructions are detailed and accurate. What is the **most likely** cause?
>
> - A. The model is too small
> - B. The instructions need more examples
> - C. The YAML frontmatter is malformed
> - D. The user prompt is too short

<details>
<summary>Show answer</summary>

**C.** Malformed frontmatter prevents the skill from being registered. A, B, D all blame the wrong layer.
</details>

### ✅ Self-check
- [ ] I can write valid frontmatter from memory
- [ ] I can name 3 ways it breaks (leading space, tabs, unquoted colon)
- [ ] I know `description` drives triggering
- [ ] I can classify this failure as a **config fix**
- [ ] I can spot "wrong-layer" distractors

### 🧩 Pattern for exam stems
**"Works on disk, never used / not recognized"** → suspect **metadata/config**, not content or model.

---

*Next topic: send it over and I'll add it as the next note in the same format.*
