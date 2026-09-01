---
name: svg-eli5-archify
description: Analyze a codebase or infrastructure repository and produce an evidence-backed, SVG-first one-page architecture explainer using simple ELI5 analogies and layered system mapping. Use for requests to understand, review, or present a repository or deployed system visually; do not use for generic charts, implementation-only work, or text-only summaries.
---

# SVG ELI5 Archify

Turn a repository into one page that answers four questions at a glance:

1. What is the system?
2. How does work and data move through it?
3. Which decisions are strong, and where can it fail silently?
4. What should happen next?

The SVG is the explanation, not decoration. Put the architecture, evidence state,
findings, and next actions inside the visual. Keep prose outside the visual to at
most one short conclusion.

## Evidence first

Inspect the repository before drawing. Prefer local source and configuration over
assumptions.

- Read applicable repository instructions and the top-level documentation.
- Inventory deployable units, services, jobs, data stores, queues, caches,
  observability resources, external dependencies, and exposure paths.
- Trace dependencies from configuration, environment variables, ports, mounts,
  selectors, imports, and entry commands. Check the application entrypoint before
  treating a script as a running service.
- Calculate exact counts and capacity when they materially explain the system.
  For Kubernetes, include replicas when totaling pods and resource requests or
  limits.
- Compare three states explicitly: **declared** in deployable configuration,
  **intended** in comments or design docs, and **observed** at runtime. Never show
  intended behavior as deployed. If runtime inspection is unavailable, say so.
- Check adjacent code only to confirm an architectural edge or missing runtime
  role; do not expand into an unrelated full-repository review.

Keep an internal evidence map from every important visual claim to a file and
line or parsed resource. Show citations only when the user requests them or when
the claim would otherwise be hard to verify.

## Archify the system

Organize the page in layers rather than listing files:

- **Context:** users, external systems, the current public path, and the system
  boundary.
- **Workloads:** APIs, workers, scheduled jobs, consumers, replicas, and role
  boundaries.
- **Flow:** request direction, work assignment, event transport, replay, and
  fan-out.
- **Data responsibility:** distinguish source of truth, durable operational
  state, transport, cache, and rebuildable derived views.
- **Operations:** metrics, dashboards, alerts, SLOs, scaling constraints, and
  failure containment.

Use solid connectors for declared relationships. Use clearly labeled dashed
connectors or nodes for intended-but-undeclared behavior. Show transitional
coupling such as local paths, manual bootstrapping, or an old production route
instead of hiding it.

## Explain like five without becoming inaccurate

Use one coherent analogy that preserves system semantics. Good mappings include:

- source of truth → warehouse or archive;
- task queue → ticket desk;
- event log → conveyor belt or shipping ledger;
- cache → short-lived sticky note;
- search index → catalog that can be rebuilt;
- observability → CCTV, gauges, and alarms.

Put the analogy near the title, then use precise technical labels in the diagram.
Do not force an analogy onto components where it would distort ownership,
durability, ordering, or failure behavior.

## Adapt to the host without weakening the result

Use the richest visual surface the current host provides, but keep the SVG as the
portable source of truth.

- If the host provides a visualization, artifact, canvas, or browser-preview
  capability, read its instructions before creating the visual and use it for the
  preview.
- Otherwise create a self-contained `.svg` file in an authorized workspace
  location. Add a minimal HTML preview only when it helps inspection; do not move
  substantive explanation into the wrapper.
- Never make completion depend on a Codex-, Claude-, or Gemini-specific tool. If a
  native preview is unavailable, return the SVG path and state which visual checks
  could not be performed.

## Make the SVG carry the page

- Put the title and analogy, architecture map, evidence legend, strengths, risks,
  and prioritized next moves inside SVG. Outside prose should be no more than a
  short handoff or conclusion.
- Use native SVG shapes, connectors, labels, badges, and grouped regions rather
  than embedding a screenshot or substituting a long Markdown explanation. The
  architecture flow should be the visually dominant region.
- When the host supports responsive inline output, compose a desktop view around
  736px and a separate mobile view around 360px. For standalone output, use a
  responsive `viewBox`; create a mobile companion SVG when one composition cannot
  remain readable at both widths.
- Keep visible text at least 11 screen pixels. Wrap text with explicit `tspan`
  lines and reserve room for the longest label.
- Use host theme tokens when available. For standalone output, define a
  self-contained light/dark palette inside the SVG. Pair color with labels, line
  styles, and shapes so meaning never depends on color alone.
- Give each SVG a concise `title` and `desc`. Keep IDs unique across multiple
  compositions.
- Prefer direct labels over legends. Use a small legend only for durable semantic
  categories such as source, transport, cache, derived view, and missing desired
  state.
- Do not invent maturity scores. Report concrete evidence, consequences, and
  next actions.

## Findings and prioritization

The one page should normally contain:

- three to five strengths that explain sound boundaries or operational choices;
- three to five risks stated as **evidence → consequence**;
- three ordered next moves that close the most important gaps.

Prioritize missing desired state, data-loss paths, silent monitoring failures,
external exposure, and reproducibility before cosmetic cleanup. Calibrate the
assessment to the stated environment: a deliberate single-node home lab is not a
failed high-availability cluster, but it must still be labeled as single-node.

## Verification

Before responding:

- render the available artifact and inspect it at desktop and mobile widths;
- inspect both light and dark themes when the renderer supports them; otherwise
  check both palettes for contrast;
- fix clipped labels, crossing arrows that change meaning, unreadably small text,
  low contrast, duplicate IDs, and unsupported external resources;
- validate standalone SVG as XML and keep it free of external runtime dependencies;
- apply any size or wrapper limits required by the current host's artifact surface;
- re-check the top findings against source evidence;
- include the visualization or a link to the generated SVG in the same turn.

# Hermes Runtime Extension

When authorized runtime or infrastructure access exists, repository evidence alone is not sufficient for claims about a deployed system. Build five evidence layers separately before producing the architecture:

1. **Repository state** — source, compose/manifests, config, entrypoints, migrations, jobs, and declared dependencies.
2. **Runtime state** — running processes/containers, images, health, restart state, active systemd units/timers, cron jobs, and effective entry commands.
3. **Network/exposure state** — listening sockets, reverse proxies, TLS termination, hostnames, ingress, public/private bindings, and upstream/downstream routes.
4. **Persistent-data state** — mounted volumes, database endpoints, durable queues, backups when observable, and rebuildable versus non-rebuildable data.
5. **External-dependency state** — model providers, SaaS APIs, object storage, DNS/CDN, authentication services, and any dependency whose failure crosses the system boundary.

Never infer one layer from another. A service present in source or compose is **not OBSERVED running** until runtime evidence confirms it. A local listening port is **not publicly exposed** until the reverse-proxy/network path confirms it. A configured provider/model is **not the active provider/model** until runtime configuration, effective process state, or logs confirm it.

## Preferred read-only runtime probes

Use only probes authorized by the user and available in the host. Prefer read-only commands:

- `docker ps`, `docker inspect`, `docker compose ps`;
- container health, image, mounts, restart policy, effective command;
- `ss -lntp` or equivalent;
- `nginx -T` or effective reverse-proxy configuration;
- `systemctl --type=service --state=running`;
- `systemctl list-timers`;
- user/system cron listings where relevant;
- mount and volume inspection;
- service-native health/status endpoints;
- effective provider/model identifiers with all credentials redacted;
- recent logs only when needed to prove an architectural edge or failure state.

Never print or retain API keys, tokens, passwords, private keys, cookies, Authorization headers, session material, or credential-bearing URLs.

## Evidence status model

Use these evidence states consistently:

- **DECLARED** — deployable source/configuration says it exists.
- **INTENDED** — docs/comments/design say it should exist, but it is not deployably declared.
- **OBSERVED** — confirmed in effective runtime.
- **INFERRED** — strongly implied by multiple evidence points but not directly observed; use sparingly.
- **UNKNOWN** — material state could not be verified.

`OBSERVED` must never be assigned from repository evidence alone.

## Topology reconciliation

Before drawing the final SVG, reconcile the layers in a ledger with:

`component/edge | declared | observed | mismatch | evidence | consequence`

Prioritize mismatches such as stale or shadow containers, old reverse-proxy routes, duplicated schedulers, orphaned persistent volumes, repository/runtime provider or model drift, local-only assumptions presented as public, public exposure absent from repository configuration, and desired state documented but not deployed.

## AI provider-path verification

For systems using LLM/AI providers, record the effective path without secrets:

`caller -> application service -> provider adapter/router -> provider -> model`

Distinguish configured default, requested provider/model, and actually observed provider/model. If runtime evidence contradicts repository defaults, show runtime as OBSERVED and source defaults as DECLARED. A retry/fallback path is not a stable architecture edge unless configuration or repeated runtime evidence proves that it is.

## Completion gate

Do not report PASS until all applicable checks pass:

- repository instructions read;
- declared architecture inventoried;
- runtime inspection attempted when authorized and available;
- DECLARED / INTENDED / OBSERVED / INFERRED / UNKNOWN kept distinct;
- network/exposure path checked when relevant;
- persistent-data responsibility classified;
- active provider/model path checked when relevant;
- secrets redacted;
- SVG validates as XML;
- SVG has no external runtime dependency;
- desktop readability inspected;
- mobile readability inspected or explicitly marked unverified;
- strengths 3–5 included;
- risks 3–5 written as evidence -> consequence;
- exactly three ordered next moves included;
- evidence ledger provided or embedded;
- unresolved unknowns that could change the conclusion listed explicitly.

A partial result is acceptable; a false PASS is not. If required evidence cannot be obtained, return `PARTIAL` and name the missing evidence exactly.
