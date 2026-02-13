# CLAUDE.md - 淵 & 妍 記帳本 (YYMoney)

## Project Overview

A shared expense tracking web app for two people (淵/Yuan and 妍/Yen). Single-page HTML application with no build tools or backend — all data is persisted via `window.storage` (platform-provided key-value storage).

## Architecture

**Single file**: `index.html` contains all HTML, CSS, and JavaScript inline. No frameworks, no dependencies, no build step.

### Tech Stack
- **HTML5** + inline CSS + vanilla JavaScript
- **Fonts**: Google Fonts — Noto Sans TC (UI text), JetBrains Mono (numeric values)
- **Storage**: `window.storage.get()` / `window.storage.set()` — async key-value API provided by the hosting platform
- **Storage key**: `expenses-data` — JSON stringified array of expense objects

### Data Model

Each expense record:
```json
{
  "id": 1707849600000,
  "date": "2026-02-13T12:00:00.000Z",
  "amount": 150.00,
  "currency": "TWD",
  "payer": "淵",
  "item": "午餐",
  "paymentMethod": "刷卡"
}
```

Fields:
- `id` — `Date.now()` timestamp (unique identifier)
- `currency` — one of `TWD`, `JPY`, `USD`
- `payer` — `淵` or `妍`
- `paymentMethod` — `刷卡`, `現金`, or `轉帳`

### UI Structure (3 tabs)

1. **記帳 (Add Expense)** — Form to add new expense entries
2. **分帳總額 (Summary)** — Per-person totals by currency, settlement calculation, TWD conversion with configurable exchange rates
3. **月份記錄 (History)** — Filterable monthly expense table with delete functionality

### Settlement Logic

Settlement calculates `淵's total - 妍's total` per currency:
- Positive = 妍 owes 淵
- Negative = 淵 owes 妍
- The TWD total conversion uses user-configurable exchange rates (defaults: JPY×0.23, USD×31)

## Language

- All UI text is in **Traditional Chinese (zh-TW)**
- Variable names and code comments are in a mix of English and Chinese
- The two users are referred to as 淵 (yuan) and 妍 (yen) in both code and UI

## CSS Design System

CSS custom properties defined in `:root`:
- `--primary` / `--primary-light` — green tones (#2d5a4f / #4a8b7a)
- `--secondary` / `--accent` — warm orange tones (#d4956c / #e8b89a)
- `--yuan` — green (#5b8e7d) for 淵's color coding
- `--yen` — orange (#c97d5d) for 妍's color coding
- Responsive: mobile breakpoint at 768px

## Development Notes

- No build tools, linters, or test framework — open `index.html` directly in a browser
- All state lives in the `expenses` global array; persisted on every add/delete
- The app uses `alert()` for user feedback (success/error)
- DOM rendering uses template literals with `innerHTML` — be mindful of XSS if adding user-generated content beyond the current fields
