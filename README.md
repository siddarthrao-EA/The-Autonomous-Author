# The Autonomous Author

A first-draft documentation pipeline with visible human gates, designed for **Tessellate Networks**, a fictional data-center networking vendor.

It reads a merged pull request or a feature spec, writes a first draft as a **DITA 1.3** topic and an **MDX** page, checks it against the house style, and then stops. A writer reviews the draft in the app (gate 1), and a code owner approves the pull request in Git (gate 2). Nothing publishes on its own, and the run log records who decided what.

[Design document](./index.html) · [Where the humans are](./index.html#s7-hitl) · [Simulation](./index.html#s8) · [ADRs](./index.html#s10) · [Status](#status)

---

## The problem, in one sentence

Tessellate's writers start each feature days after the code merges, rebuild context by hand, and catch style and precision problems late, so docs land after GA. Any AI that speeds this up must leave a clear record of the human decisions, or the team cannot trust what it publishes.

## How it works

Six single-purpose agents, two code emitters, two human gates:

```
PR or spec
  └─▶ A-01 Intake ─▶ A-02 Research ─▶ A-03 Draft ─▶ A-04 Ambiguity (specs only)
        ─▶ A-05 Compliance ─▶ A-06 Review Prep
              └─▶ GATE 1 · writer decides every HIGH flag
                    └─▶ emit DITA 1.3 + MDX + run log ─▶ pull request
                          └─▶ CI: Vale · DITA-OT · Docusaurus · links · run-log check
                                └─▶ GATE 2 · code owner approves ─▶ publish
```

- **Every agent explains itself.** Each one emits a reasoning card (understood, decided, why, uncertain) before the next one runs.
- **Missing facts stay visible.** The draft writes `[REQUIRES INPUT: reason]` instead of guessing, and export stays locked until a person resolves it.
- **Rules are data.** House-style rules live in a versioned `rules.json`, and the same rule IDs run in Vale on every pull request.
- **One draft, two formats.** The model writes structured JSON; code turns it into a DITA task, concept or reference topic and an MDX page.
- **The record travels with the content.** Each approved run commits `.taa/runs/<id>.json` beside the topic, and CI fails a pull request if any HIGH flag lacks a human decision.

## Key design decisions

Ten ADRs, each with a rejected alternative. Full detail in [§10](./index.html#s10).

| ADR | Decision | Rejected because |
|---|---|---|
| 01 | Client-side app, no backend | A proxy server would hold unreleased product content |
| 02 | Groq behind a provider adapter | v1 hard-coded a model ID that is no longer listed |
| 03 | Six single-purpose agents | One large prompt hides the stage-by-stage reasoning |
| 04 | Versioned rules file | Retrieval over the whole style guide gives citations a writer cannot trust |
| 05 | Fixed graph | An autonomous loop could skip compliance |
| 06 | Ambiguity Detector as a separate critic | Self-review defends the draft it just wrote |
| 07 | One structured draft, two emitters | Model-written XML can be invalid; DITA-to-MDX conversion is lossy |
| 08 | Same rule IDs in the app and in Vale | App-only checks miss edits made after export |
| 09 | Writer opens the PR; app holds no Git credentials | A browser-stored GitHub token widens the risk to repo write access |
| 10 | Two gates: writer in the app, code owner in Git | A single in-app approval does not prove an SME agreed |

## Status

| | |
|---|---|
| Design document | ✅ v2.0, 14 sections, 10 ADRs, scripted simulation, stakeholder deck |
| Working application | ⏳ Not started. Roadmap in [§13](./index.html#s13) |
| Simulation | Scripted and animated. No model call and no API key in the page |

## Changelog

- **v2.0 (16 Oct 2026).** Rebuilt as a single design document. Re-domained to a networking vendor (EVPN/VXLAN, RFC 7432, 8365, 9136). Changed the client from one writer to a documentation team. Added DITA 1.3 and MDX emitters, Vale in CI, CODEOWNERS as gate 2, and the run log in Git. Updated the model from Llama 3.1 70B to `llama-3.3-70b-versatile` behind an adapter. Corrected the CSP approach (GitHub Pages cannot set response headers, so the policy uses a meta tag). Removed unsourced performance claims.
- **v1.0 (2026).** Nine-page design for a single writer (persona "Maya Chen") with Markdown and HTML export.

## Disclaimer

Tessellate Networks, its products (TN-7300, TessOS, FlowSentry), the `tessctl` command line, and every named person in this repository are fictional, built for a portfolio piece. The standards cited are real: IETF RFC 7432, RFC 8365 and RFC 9136; OASIS DITA 1.3; the Google developer documentation style guide; Vale; and GitHub CODEOWNERS. Figures marked *illustrative* are modelled, with the method shown in the design document.

## Author and license

Siddharth Rao Potukuchi · [MIT](./LICENSE)
