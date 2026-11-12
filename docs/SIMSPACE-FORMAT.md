# SimSpace authoring cheat-sheet

Distilled from the normative specs in [`dockersamples/simspace`](https://github.com/dockersamples/simspace)
(`spec/labspace.md`, `spec/simulator.md`, `spec/slidedeck.md`, `spec/catalog.md`).
One lab/deck = one directory `labs/<id>/`. `labs.json` is generated — never hand-write it.
Validate with `docker compose run --rm validate`.

## labspace.yaml (lab)
```yaml
kind: lab                 # optional; "lab" (default) or "slides"
title: "…"                # optional
description: "…"          # optional
version: "1.0.0"          # optional; bump when steps change (invalidates saved progress)
simulator: simulator.yaml # REQUIRED for a lab
catalog:                  # optional card for the landing page (shown when >= 2 entries)
  icon: deployed_code     #   Material Symbols name
  tags: ["sandbox"]
  estimatedMinutes: 20
  order: 2                #   ascending sort on the landing page
terminals:                # optional; default one "Terminal". Multiple share ONE simulator/VFS
  - id: host
    title: Terminal
    icon: terminal
files:                    # optional seed VFS: path -> full contents (built-in ls/cat read these)
  shiplog/.env: |
    GITHUB_TOKEN=sbx-proxy-placeholder-9f3a2c
sections:                 # ordered instruction pages
  - title: "…"            #   REQUIRED per section
    contentPath: 00-x.md  #   optional
    steps:                #   optional milestones; completed by a scenario's `completes:`
      - id: scaffold
        title: "…"
variables:                # optional $$name$$ substitutions
  app: ShipLog
```

## labspace.yaml (slides) — `kind: slides`
Same format as a lab, three differences: `slides:` replaces `sections:`; `simulator:` is
optional (only for live demo terminals); markdown splits into slides on `---`.
```yaml
kind: slides
title: "…"
theme: dark               # deck default surface: light | dark | tint
brand:
  logo: assets/logo.svg   # entry-relative
  eyebrow: "ShipLog"
  source: "ContainerDays · 2026"
slides:
  - title: Opening
    contentPath: 00-opening.md
```

### Slide markdown
- Slide break = a line of **exactly three dashes** `---`. Use `----` (4+) for a real `<hr>`.
  Never put `---` content in a bare paragraph you don't want split.
- Per-slide config = an HTML comment that OPENS the slide:
  `<!-- layout: split\ntheme: dark -->`
- **Valid layouts (7):** `default` · `title` · `section` · `split` · `stats` · `quote` · `image`.
- **Valid themes (3):** `light` · `dark` · `tint`.
- Speaker notes: a line starting `Note:` (to end of slide).
- Fragments: `:::fragment` … `:::` (incremental reveal).
- Split columns: divide a `layout: split` slide with `<!-- region -->` markers.
- Components: `:::stat{value="20B+" label="…" accent=green}…:::`, `:::card{…}…:::`,
  inline `:tag[Before]{accent=red}`. Accents: blue/green/red/amber/neutral.
- Demo terminal: ```` ```bash terminal-id=demo ```` then `::terminal{id=demo height=300}`.
  On a slide a fence is runnable **only** with `terminal-id=`.

## simulator.yaml (schema 2.0)
```yaml
version: "2.0"            # REQUIRED
metadata: { id: contain, title: "…", summary: "…" }
state: { scaffolded: false }   # optional initial state
settings:
  pace: { pull: 900 }    # named delays; built-ins always available: short=250 medium=700 long=1400
defaults:
  unmatched: { stderr: ["…"], exit: 127 }   # when nothing matches
scenarios:               # REQUIRED; first full match wins; scenarios beat built-ins
  - id: sbx-run
    completes: scaffold  # optional: marks the labspace step done when this fires
    when:
      command: sbx run   # prefix: "sbx run" or [sbx, run]
      args:              # optional
        0: { any: true } #   positional capture -> {{ args.0 }}
        --tag: { any: true }   # flag capture -> {{ args.tag }} (dashes stripped)
        -d: true         #   flag present
      state: { scaffolded: false }   # optional equality guard
    then:                # applied files -> state -> output -> exit
      state: { scaffolded: true }
      output:
        - "plain line"
        - { text: "delayed line", delay: short }
        - { delay: long }            # pure pause, prints nothing
      exit: 0
```
- Agent scenarios: `when: { agent: true, promptContains: [health] }` (or exact `prompt:`).
- Templating in output/stderr/file content: `{{ args.N }}`, `{{ args.name }}`, `{{ state.path }}`.
  **Every `{{ args.X }}` needs a capture** (equals / `{any:true}` / `{oneOf:[…]}`).
- VFS ops under `then.files:` — `create`/`append`/`replace`(needs `find`)/`delete`/`copy`/`mkdir`.
- Built-ins `ls [path]` and `cat <file>` work off `files:` with no scenario.

## Markdown directives (labs)
- Runnable fences: `bash`/`sh`/`console`/`prompt` get Run + Copy buttons by default.
- Fence meta: `terminal-id=`, `save-as=path`, `highlight=1-2`, `no-run-button`, `no-copy-button`,
  `filename="…"`.
- `:filelink[label]{path="app/x"}` — cats the file in a terminal (target must exist in `files:`).
- `:tablink[…]`, `:variabledefinition`, `:variablesetbutton`, `:conditionaldisplay`.
- `$$name$$` substitutes a variable at render time.

## Gotchas (what validate-lab enforces)
- **Every fenced code block in a lab section is reconciled** against the simulator — a fence
  whose content isn't a handled command/built-in is an ERROR, regardless of language tag.
  → Put ASCII diagrams / non-commands in blockquotes or inline code, NOT in a code fence.
- Milestone `completes:` must name a real step; a step with no completing scenario = warning.
- `{{ args.X }}` without a capture = error; `delay:` naming an undeclared pace profile = error.
- `replace` without `find` = error. `command`+`agent` and `input`+`session` are mutually exclusive.
- Unknown slide `layout:`/`theme:` = error. `image` layout needs `image:`.
- Validate regenerates `labs.json` and exits 1 if no labs found.
- Icons are [Material Symbols](https://fonts.google.com/icons) names.
