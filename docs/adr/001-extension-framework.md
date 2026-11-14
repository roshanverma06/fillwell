# ADR-001: Chrome MV3 extension built with WXT + TypeScript + React

**Status:** Accepted · **Date:** 2026-11-14

## Context
Fillwell must read and fill forms inside job sites. Chrome requires Manifest V3, which uses service workers instead of persistent background pages and doesn't allow loading remote code. We need a side panel UI, content scripts per site, hot reload during development, and type safety shared with the backend schemas.

## Decision
Build the extension with **WXT** (an extension framework built on Vite), **TypeScript** throughout, **React** for the side panel, and **Zod** to validate every message and API response.

## Alternatives considered
- **Plain Vite + CRXJS plugin:** popular, but slower to maintain; WXT gives manifest generation, entry points and cross-browser builds out of the box.
- **Plasmo:** similar features; WXT is lighter and closer to standard Vite.
- **No framework (hand-written manifest + webpack):** maximum control, too much setup time.

## Consequences
- **Benefit:** Fast dev loop (hot reload), typed entry points, easy Edge/Firefox builds later.
- **Benefit:** A shared `packages/shared-schemas` gives the same types on both sides.
- **Trade-off:** The team has to learn WXT conventions; fewer Stack Overflow answers than plain webpack.
