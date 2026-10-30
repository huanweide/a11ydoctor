**中文** · [README.md](./README.md)

<!-- badges -->
[![Node](https://img.shields.io/badge/Node.js-supported-339933?logo=node.js&logoColor=white)](https://nodejs.org)
[![Zero-Deps](https://img.shields.io/badge/dependencies-zero-0f9d58?logo=npm&logoColor=white)](https://www.npmjs.com/)
[![License](https://img.shields.io/github/license/huanweide/a11ydoctor)](LICENSE)
<!-- /badges -->

# a11ydoctor

A zero-dependency, single-file **Web accessibility static-scan CLI**. It scans HTML / JSX / Vue / Svelte templates for semantic a11y anti-patterns and reports severity, a weighted health score, and a CI gate. Pure Node stdlib — **zero dependency, zero config, offline-capable**.

> Part of the "code-health family" front-end slice: devdoctor (dependency check-up) + testlite (test check-up) + debtlens (tech-debt density) + a11ydoctor (front-end accessibility).

## Who should use it / Who shouldn't

- **Use it if** you write HTML / JSX / Vue / Svelte, individually or as a team; WCAG / ADA / national-standard accessibility compliance is a legal and enterprise requirement; or you want a purely static a11y gate in CI.
- **Skip it if** you already run axe-core + a browser integration for full a11y coverage (this tool doesn't check runtime items like color contrast); or you're pure back-end with no front-end templates.

## Why not oxlint / axe

- **oxlint**: a Rust zero-config linter, but its jsx-a11y rules need ESLint + React config to enable, and it focuses on JSX rather than plain HTML.
- **axe-core**: needs a browser DOM runtime, so it can't run as a pure static check in CI.
- **a11ydoctor**: pure HTML / template-layer static scan, no framework config needed, deterministic, runs out of the box, and drops into CI via `--json` + `--fail-on`.

## Install / Use

```bash
# Run directly (no npm install)
node index.js ./src
node index.js index.html --json
a11ydoctor . --fail-on any --max 0
```

Global install (optional):

```bash
npm i -g a11ydoctor
a11ydoctor ./src
```

## Four quick questions

1. **What it solves**: hard-to-spot semantic accessibility defects in front-end / Web projects (missing alt, form controls without labels, buttons without names, missing lang, skipped heading levels, tables without headers, empty links, keyboard-inaccessible elements, etc.).
2. **Audience**: any team or individual writing HTML / JSX / Vue / Svelte; WCAG / ADA / national-standard accessibility compliance is a legal and enterprise must-have.
3. **Alternatives**: oxlint (needs React/ESLint config), axe (needs a browser), html-validate (needs install + config). a11ydoctor is zero-dependency, purely static, and deterministic.
4. **Architecture**: a single-file Node CLI. A lightweight HTML tokenizer (state machine skips `>` inside quotes, void elements don't push to the stack, `<script>/<style>` aren't recursed to avoid false positives) + a context-aware rule engine (ancestor stack for label wrapping, subtree for table/th, global for heading order) + weighted health score + CI gate.

## Detection rules

| Rule | Severity | Description |
|---|---|---|
| `img-alt` | high | `<img>` missing alt attribute (use `alt=""` explicitly for decorative images) |
| `html-lang` | high | `<html>` missing lang attribute |
| `label-association` | medium | Form control missing label association (for/id, wrapping label, or aria-label) |
| `accessible-name` | medium | `<a>`/`<button>` missing an accessible name (text / aria-label / title) |
| `heading-order` | medium | Skipped heading level (e.g. h1 directly to h3) |
| `table-structure` | medium | `<table>` missing `<th>` or `<caption>` |
| `link-purpose` | medium | `<a>` with unclear purpose (empty anchor `#` / `javascript:`) |
| `interactive-role` | medium | `<div onclick>` missing role/tabindex, keyboard-inaccessible |
| `deprecated-element` | low | Uses deprecated elements (blink/marquee/center/font) |
| `media-alternative` | low | `<video>/<audio>` should provide a `<track>` caption |
| `title-present` | low | Document missing `<title>` |
| `doctype` | low | HTML file missing `<!DOCTYPE>` (only .html/.htm) |

## Health score

Each issue is weighted by severity (high×3 / medium×2 / low×1). Every 1000 lines tolerates 1 weighted issue for free; beyond that, each costs 25, floored at 0.

```bash
$ node index.js demo.html
demo.html:3  [high] img-alt — <img> missing alt attribute
demo.html:8  [medium] label-association — <input> missing accessible label

summary: files 1 · issues 2 (high1/med1/low0) · health 50/100
```

## CI gate

```bash
# Fail on any high-severity issue (default)
a11ydoctor . --fail-on high

# Fail on any issue at all
a11ydoctor . --fail-on any

# Cap total issue count (with --max)
a11ydoctor . --max 5

# JSON report (for gate platforms to consume)
a11ydoctor . --json
```

Exit codes: `0` pass; `1` gate failed; `2` argument / path error.

## Known limitations

- Accessible-name detection is based on `aria-*` / `title` / `alt` attributes or the element's *direct* text; for complex nested text, add an explicit `aria-label` to the interactive element.
- Color contrast is a runtime computation and is out of scope (needs axe + a browser).
- The estimation metrics are for check-up reference only, not an authoritative compliance certification.

## Roadmap

Rule set is stable. Possible future additions: ARIA-role validity checks and a `--fail-on` severity preset. No breaking changes planned.

## Contributing

Issues and pull requests are welcome. Keep it single-file and zero-dependency.

## ⭐ Star

If a11ydoctor ever caught a "missing alt / unlabeled form" accessibility defect for you, a Star ⭐ helps others find it: https://github.com/huanweide/a11ydoctor

## Author

[huanweide](https://github.com/huanweide)

## License

MIT
