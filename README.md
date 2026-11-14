# UniDesk — Offline University Student Desktop Workspace

> **Day 1 Milestone: Foundational Architecture, UI Shell, Local Persistence & macOS .dmg Build**

UniDesk is an offline-first desktop productivity application engineered specifically for university students. Built with **Electron**, **pure HTML5**, **pure Vanilla CSS** (no Tailwind, no Bootstrap), and **pure Vanilla JavaScript (ES6+)**, UniDesk prioritizes security, speed, and privacy with zero reliance on cloud APIs or external CSS/JS frameworks.

---

## 🏛️ Architectural Pillars

1. **Strictly Pure Vanilla Frontend**
   - Zero CSS frameworks (no Tailwind, Bootstrap, Sass).
   - Zero JavaScript frameworks (no React, Vue, Svelte, Angular).
   - Lightweight, responsive layout powered by pure CSS Flexbox, Grid, and Custom Properties (CSS variables).
   - Client-side Single Page Application (SPA) routing powered by a custom vanilla hash router (`router.js`).
2. **Zero Native C++ Database Modules**
   - No `sqlite3`, `better-sqlite3`, or node-gyp bindings that complicate cross-compilation.
   - Offline data persistence is powered entirely by Node.js's built-in `fs` module, reading and writing to an atomic `database.json` store located in Electron's standard user data directory (`app.getPath('userData')`).
3. **Hardened Security Architecture**
   - `contextIsolation: true`
   - `nodeIntegration: false`
   - `sandbox: true`
   - Strict Content Security Policy (CSP) blocking unauthorized remote scripts and inline code execution.
   - Clean IPC communication bridging Main and Renderer processes via Electron's `contextBridge`.
4. **macOS Apple Silicon (.dmg) Packaging**
   - Production packaging configured via `electron-builder`.
   - Supports Apple Silicon (`arm64`).
   - Configured with `"identity": null` under `mac` so unsigned local development builds succeed cleanly without requiring an Apple Developer Certificate.

---

## 📁 Complete File Tree

```
UniDesk/
├── .gitignore
├── README.md
├── package.json
├── assets/
│   └── icon.png
└── src/
    ├── main/
    │   ├── ipc-handlers.js
    │   ├── main.js
    │   └── storage.js
    ├── preload/
    │   └── preload.js
    └── renderer/
        ├── index.html
        ├── academics/
        │   └── academics.js
        ├── campus-life/
        │   └── campus-life.js
        ├── dashboard/
        │   └── dashboard.js
        ├── desktop-tools/
        │   └── desktop-tools.js
        ├── focus/
        │   └── focus.js
        ├── js/
        │   ├── app.js
        │   └── router.js
        ├── study/
        │   └── study.js
        └── styles/
            ├── main.css
            └── variables.css
```

---

## 🗄️ Local Persistence Architecture (`storage.js`)

All application state is stored locally inside `database.json` at:
- **macOS**: `~/Library/Application Support/unidesk/database.json`
- **Linux**: `~/.config/unidesk/database.json`
- **Windows**: `%APPDATA%\unidesk\database.json`

### Atomic Write Protocol
To prevent data corruption if the application exits mid-write, `storage.js` uses an atomic file replacement strategy:
1. Data is serialized with a 2-space indentation format.
2. The payload is written to a temporary file (`database.json.tmp`).
3. An atomic rename (`fs.renameSync`) replaces `database.json`.
4. If invalid or corrupt JSON is detected during startup, an automatic timestamped backup is generated (`database.backup.[timestamp].json`) before restoring default schema keys.

### Default Database Schema
```json
{
  "appVersion": "1.0.0",
  "initializedAt": "2026-11-14T12:00:00.000Z",
  "lastModified": "2026-11-14T12:00:00.000Z",
  "settings": {
    "theme": "dark",
    "studentName": "Alex Rivera",
    "university": "State Polytechnic Institute",
    "major": "Computer Science & Engineering",
    "semester": "Fall 2026",
    "targetGPA": 3.85
  },
  "courses": [
    {
      "id": "course-101",
      "code": "CS 301",
      "name": "Operating Systems & Architecture",
      "instructor": "Dr. Evelyn Hayes",
      "credits": 4,
      "color": "#6366f1",
      "room": "Hall B, Rm 302",
      "schedule": "Mon, Wed 10:00 - 11:30 AM"
    }
  ],
  "assignments": [],
  "notes": [],
  "flashcardDecks": [],
  "pomodoro": {
    "completedSessions": 0,
    "totalFocusMinutes": 0
  },
  "budget": {
    "currency": "$",
    "monthlyBudget": 650,
    "transactions": []
  },
  "quickNotes": "Welcome to UniDesk! All local updates persist instantly."
}
```

---

## 🎨 Design System & Theme Engine

UniDesk features a high-performance theme system powered entirely by CSS custom properties defined in `styles/variables.css`.

- **Dark Theme (Default)**: Deep slate backgrounds (`#0b0f19`, `#111827`, `#182234`) with soft indigo accents (`#6366f1`) optimized for late-night study sessions.
- **Light Theme**: Crisp off-white backgrounds (`#f8fafc`, `#ffffff`) with high contrast text for bright lecture halls.
- **Persistence**: Theme choice persists across restarts via both `localStorage` (instant synchronous CSS paint) and `database.json` (backend state consistency).

---

## 🗺️ 7-Day Course Project Roadmap

| Day | Milestone | Status | Key Deliverables |
| :--- | :--- | :---: | :--- |
| **Day 1** | **Foundational Architecture & UI Shell** | **Completed** | Secure Electron main/preload/renderer, pure CSS shell, dark/light toggle, atomic `fs` storage, macOS `.dmg` config |
| **Day 2** | **Academics & Timetable Planner** | Upcoming | Course CRUD, weekly interactive schedule grid, GPA simulator |
| **Day 3** | **Study Hub & Spaced Repetition** | Upcoming | Leitner/SM-2 flashcards, 3D flip card review, offline Markdown editor |
| **Day 4** | **Desktop Student Utilities** | Upcoming | Scientific calculator, formula cheatsheet, unit & base converter |
| **Day 5** | **Focus Mode & Soundscape** | Upcoming | Pomodoro intervals, Web Audio API procedural soundscape (rain, white noise) |
| **Day 6** | **Campus Life & Ledger** | Upcoming | Monthly budget tracker, student club directory, campus event calendar |
| **Day 7** | **Final Polish, Shortcuts & Packaging** | Upcoming | Global shortcuts (Cmd/Ctrl+K), keyboard navigation, production `.dmg` audit |

---

## 🚀 Getting Started

### Prerequisites
- Node.js (v18.0.0 or later recommended; tested on v24)
- npm (v9.0.0 or later)
- macOS (for Apple Silicon `.dmg` builds)

### 1. Install Dependencies
```bash
npm install
```

### 2. Run in Development Mode
```bash
npm start
```

### 3. Build macOS Apple Silicon (.dmg)
To generate the production installer without code-signing friction:
```bash
npm run build:mac
```
The resulting package will be placed inside `dist/`:
- `dist/UniDesk-1.0.0-arm64.dmg`
- `dist/mac-arm64/UniDesk.app`

---

## 🛡️ Security Audit Checklist

- [x] `contextIsolation: true` enforced on `BrowserWindow`
- [x] `nodeIntegration: false` enforced on `BrowserWindow`
- [x] `sandbox: true` enabled
- [x] `contextBridge` selectively exposes minimal async functions
- [x] CSP header set in `index.html`
- [x] Zero `eval()` or untrusted string-to-code compilation
- [x] Atomic writes prevent file corruption on unexpected power loss
