# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

A small Express 4 REST API (users + health check) used as the starter project for the Claude Code course.

## Commands

- `npm run dev` — start the API on http://localhost:3000 with `node --watch` (port via `PORT`)
- `npm test` — run all tests with the built-in `node --test` runner (not Jest/Mocha)
- `node --test tests/users.test.js` — run a single test file
- `npm run lint` — ESLint (`eslint:recommended`); CI runs lint, then test, on Node 22

## Conventions

- Do not change app code (`server.js`, `routes/`, `db/`) unless explicitly asked — this project is about Claude setup, not features.
- New code must be TypeScript with no `any`. Do not convert existing JS files without being asked.
- Every new or changed endpoint gets a Supertest case in `tests/`, using `node:test` + `node:assert`.
- Run `npm run lint && npm test` before proposing a commit.
- Never commit or push on your own — always ask first.

## Architecture

- `server.js` builds the Express app, mounts one router per resource from `routes/`, and exports `app`. It only calls `listen()` when `require.main === module`, so tests can import `app` without opening a port — keep that guard.
- Route handlers never touch data directly; all data access goes through `db/store.js`.
- `db/store.js` is an in-memory array: data resets on restart, and state persists across tests in the same run (don't assume a fixed user count).
- Never read or commit `.env`; `.env.example` documents the config.
