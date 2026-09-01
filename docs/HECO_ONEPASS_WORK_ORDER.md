# HECO ONE-PASS WORK ORDER
## Hermes System Archify — install → verify → smoke test → close

### Mission

Install the strengthened `hermes-system-archify` skill for Heco/Codex, verify the installed instructions, execute one real architecture-analysis smoke test, validate the SVG, and close the task in the same run.

Do not stop after cloning or installation. Do not ask for intermediate approval when the required access already exists.

### Safety boundary

Production inspection is read-only.

Do **not** modify application code, containers, services, systemd units, nginx, DNS, firewall, databases, volumes, provider configuration, or credentials during the smoke test.

Never print, save, commit, or embed secrets. Redact API keys, tokens, passwords, private keys, cookies, Authorization headers, session values, and credential-bearing URLs.

Never infer runtime state from repository state, public exposure from a local socket alone, or active provider/model from source defaults alone.

## PHASE 0 — PREFLIGHT

1. Record host, user, working directory, local/UTC timestamp.
2. Record Heco/Codex version if available.
3. Confirm `git` exists.
4. Confirm `https://github.com/icerain-cmd/explain_me.git` is reachable.
5. Determine effective Codex skill directory; preferred: `~/.codex/skills/hermes-system-archify`.
6. If an old installation exists, inspect origin and `git status`; preserve local changes and do not overwrite blindly.

**Gate:** installation target known; no destructive overwrite required.

## PHASE 1 — INSTALL

Preferred:

```bash
git clone https://github.com/icerain-cmd/explain_me.git ~/.codex/skills/hermes-system-archify
```

If already installed from the same repository:

```bash
cd ~/.codex/skills/hermes-system-archify
git status --short
git remote -v
git pull --ff-only
```

Do not use `reset --hard` or forced checkout to discard local work.

Verify `SKILL.md` exists, YAML front matter is readable, the skill is discoverable by Heco/Codex, and installed files contain no credentials.

**Gate:** Heco/Codex can discover and read the effective installed skill.

## PHASE 2 — INSTRUCTION INTEGRITY

Read the installed `SKILL.md` completely and confirm the effective installed copy contains:

- evidence first;
- declared / intended / observed separation;
- repository / runtime / network-exposure / persistent-data / external-dependency evidence layers;
- DECLARED / INTENDED / OBSERVED / INFERRED / UNKNOWN;
- runtime never inferred from repository alone;
- network/public exposure verification;
- provider/model runtime verification;
- secret redaction;
- PASS/PARTIAL completion gate.

**Gate:** controls exist in the installed copy, not merely on GitHub.

## PHASE 3 — REAL SMOKE TEST

Analyze one real repository available to Heco. Prefer the active Hermes repository or another currently deployed service for which read-only runtime access already exists. Invoke the skill to produce a one-page SVG.

### Repository evidence

Inspect applicable repository instructions, application entrypoints, Dockerfile/Compose/deployment manifests, ports, mounts and volumes, environment-variable names, jobs/timers/schedulers, databases/caches/queues, external APIs and AI providers.

### Runtime evidence

Attempt where authorized:

```bash
docker ps
docker inspect <relevant-container>
docker compose ps
ss -lntp
nginx -T
systemctl --type=service --state=running
systemctl list-timers
```

Also inspect relevant cron, persistent mounts/volumes, service health endpoints, effective entry command, active AI provider/model identifiers, and narrowly scoped recent logs needed to prove an edge or mismatch. Redact secrets before retaining evidence.

### Reconciliation ledger

Create:

| component/edge | declared | observed | mismatch | evidence | consequence |
|---|---|---|---|---|---|

Do not hide UNKNOWN items.

**Gate:** at least one real system analyzed; repository and runtime evidence remain distinct.

## PHASE 4 — SVG QUALITY & VALIDATION

The SVG must contain title + concise ELI5 analogy, dominant architecture flow, evidence-state distinction where material, data responsibility, operations/observability, strengths 3–5, risks 3–5 as `evidence -> consequence`, and exactly three ordered next moves.

Validate XML with an available parser. Check clipped labels, overlapping text, misleading arrow crossings, readable font size, duplicate IDs, external resources, embedded secrets, desktop readability, and mobile readability if rendering permits. If mobile inspection is impossible, mark only that item PARTIAL.

**Gate:** valid XML, readable SVG, evidence-backed architecture.

## PHASE 5 — SAFETY / REGRESSION CHECK

Confirm the task did not mutate production: no application container/service intentionally restarted, nginx not rewritten, no DB migration/data mutation, no DNS/firewall/provider configuration change, and skill installation isolated to the user skill directory.

**Gate:** no production mutation caused by this task.

## PHASE 6 — FINAL REPORT

Return exactly these seven sections:

1. **INSTALLATION** — PASS/PARTIAL/FAIL, installed path, source repository, source commit/ref.
2. **DISCOVERY** — PASS/PARTIAL/FAIL and evidence Heco/Codex discovered/read the skill.
3. **SMOKE TEST** — target, repository evidence, runtime evidence, unavailable evidence.
4. **ARTIFACT** — SVG path/link, XML validation, desktop inspection, mobile inspection.
5. **MISMATCHES FOUND** — only evidence-backed declared-vs-observed differences.
6. **SAFETY** — production mutation and secret-redaction checks.
7. **FINAL VERDICT** — PASS only if every mandatory gate passed. If runtime verification was applicable but unavailable: `PARTIAL — missing runtime evidence: <exact items>`.

Never convert UNKNOWN into PASS.

## Definition of Done

`installed + discovered + effective instructions verified + real smoke test executed + SVG generated + SVG validated + production unchanged + final verdict issued`

Installation alone is **not** completion.
