# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project status

This repository is currently a bare skeleton: it contains only a `LICENSE` file (GPLv3). No source code, package manifest, build tooling, or tests exist yet. There is nothing to build, lint, or run.

**Do not fabricate or assume the presence of `package.json`, npm scripts, config files, or a directory layout.** Before referencing a command or path, verify it actually exists in the working tree.

## Project vision

`juki-components` is intended to become a **framework-agnostic UI component library built with native Web Components**. The goal is for each component to be a standard custom element (built on the Custom Elements / Shadow DOM platform APIs) that can later be dropped into consuming applications built with **Vue 3** or **React**, without those frameworks needing component-specific wrapper code to render or interoperate with them.

Implications this places on future work in this repo:

- **Framework independence is the core constraint.** Components must not depend on Vue, React, or any other UI framework at runtime. Any framework-specific integration code (e.g. Vue wrapper components, React type defs for custom elements) belongs in a separate integration layer/package, not mixed into the core component implementations.
- **Public APIs should use platform-native conventions** — HTML attributes/properties for input, DOM events (`CustomEvent`) for output — since these are what both Vue and React can bind to via their native custom-element interop (Vue's `v-bind`/`v-on` on custom elements, React's `ref` + `addEventListener` or `createElement` prop passthrough), rather than framework-idiomatic patterns (like React props-as-objects or Vue-specific directives).
- **Shadow DOM encapsulation** should be used deliberately, keeping in mind that consuming apps will need to style/theme components from outside (e.g. via CSS custom properties/parts), since global stylesheets from the host app won't pierce the shadow boundary by default.
- The recommended toolchain for implementing this, per current project direction, is **[Lit](https://lit.dev/)** — chosen for its small runtime, strong DX, and straightforward compilation down to standard custom elements. This is a direction, not yet an implemented decision — confirm with the user before scaffolding actual tooling.

## Styling constraints

Components ship with a **default design token set** (colors, spacing) baked in, but every token must be **overridable by the consuming app via CSS custom properties**:

- Colors and spacing are never hardcoded as literal values inside component styles. Each one is defined as a CSS custom property with a sensible default (e.g. `background-color: var(--juki-color-primary, #2563eb);`, `padding: var(--juki-spacing-md, 1rem);`), so a value always applies out of the box, but a consuming app can override it from outside the shadow boundary simply by setting that custom property (custom properties inherit through shadow DOM even though other styles don't).
- Token names should follow a consistent, namespaced convention (e.g. `--juki-color-*`, `--juki-spacing-*`) shared across all components, so overriding a token once (e.g. at `:root` in the host app) themes every component consistently rather than requiring per-component overrides.
- This is the styling API surface of the library — treat the set of exposed custom properties with the same care as component attributes/events, since consuming apps depend on them for theming.

## Planned folder structure (atomic design)

Components are intended to be organized under `src/components/` following **atomic design**, ordered by increasing composition:

```
src/
└── components/
    ├── atoms/          # Smallest indivisible UI elements — button, input, icon, label, badge
    │   └── <atom-name>/
    │       ├── <atom-name>.ts       # Custom element implementation (e.g. Lit)
    │       ├── <atom-name>.styles.ts
    │       └── <atom-name>.test.ts
    ├── molecules/       # Simple combinations of atoms — form-field (label + input), search-bar
    │   └── <molecule-name>/...
    ├── organisms/       # Complex, self-contained sections composed of molecules/atoms — nav-bar, card-list, data-table
    │   └── <organism-name>/...
    └── templates/       # Layout-level composition of organisms, defining structure without real content
        └── <template-name>/...
```

Notes on applying atomic design here:
- **No "pages" tier.** Pages are a concept of the consuming application (the Vue/React app assembling these components), not of this library — this repo stops at `templates`.
- Each component folder is self-contained (implementation, styles, tests, and any local docs/examples) so components can be authored, tested, and packaged independently.
- Classification of a given component (atom vs. molecule vs. organism) should be judged by composition, not visual size: does it wrap other library components (molecule/organism), or is it a leaf element with no sub-components (atom)?
- This structure is **planned, not yet created** — it should be scaffolded when actual component implementation work begins, and this section updated to match reality once real folders exist.

## Working in this repo right now

Since there is no code yet, treat any task here as either:
1. **Documentation/planning work** (like this file) — safe to do directly.
2. **Scaffolding work** (creating `package.json`, build config, first components) — this is a significant architectural decision (build tool, monorepo vs single package, testing framework, framework-interop packaging strategy). Confirm the approach with the user before generating substantial project structure, rather than assuming defaults.

Once real source code, configs, and tests exist in this repo, this file should be updated with:
- Actual install/build/lint/test commands (and how to run a single test)
- The real directory structure and where components, stories/docs, and tests live
- How components are packaged/published and how framework-specific wrappers (if any) are organized
- Any established conventions for naming, props/attributes, events, and styling APIs across components
