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

## Working in this repo right now

Since there is no code yet, treat any task here as either:
1. **Documentation/planning work** (like this file) — safe to do directly.
2. **Scaffolding work** (creating `package.json`, build config, first components) — this is a significant architectural decision (build tool, monorepo vs single package, testing framework, framework-interop packaging strategy). Confirm the approach with the user before generating substantial project structure, rather than assuming defaults.

Once real source code, configs, and tests exist in this repo, this file should be updated with:
- Actual install/build/lint/test commands (and how to run a single test)
- The real directory structure and where components, stories/docs, and tests live
- How components are packaged/published and how framework-specific wrappers (if any) are organized
- Any established conventions for naming, props/attributes, events, and styling APIs across components
