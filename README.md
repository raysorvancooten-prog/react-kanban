# react-kanban

A drag-and-drop Kanban board built with React. Manage tasks across four columns — To Do, In Progress, In Review, and Done — with card tagging, real-time progress tracking, and localStorage persistence.

## Features

- **Drag & drop** — move cards between columns natively (HTML5 Drag and Drop API)
- **4 columns** — To Do · In Progress · In Review · Done
- **Card tags** — Feature, Bug, Docs, Design, Research (color-coded)
- **Progress stats** — live card count and completion percentage in the header
- **localStorage** — board state persists across page refreshes
- **Add & delete cards** — inline form with tag selector; keyboard shortcut `Enter` to submit
- **Dark theme** — purple/cyan accent design system
- **Zero build step** — React via CDN; just open `index.html`

## Getting Started

```bash
git clone https://github.com/jonatakuzi/react-kanban.git
cd react-kanban
```

Open `index.html` in any modern browser — no server or build required.

Or serve locally:

```bash
npx serve .
# → http://localhost:3000
```

## Usage

- **Add a card** — click "+ Add card" at the bottom of any column, type a title, pick a tag, and press Enter or "Add card"
- **Move a card** — drag it to any other column
- **Delete a card** — hover a card and click the ✕ button
- **Board resets** — clear localStorage to reset to the sample board

## Project Structure

```
react-kanban/
└── index.html   # Complete app — React components, state, styles
```

## Tech Stack

| Layer | Technology |
|-------|-----------|
| UI library | React 18 (CDN) |
| State | React hooks (useState, useRef) |
| Drag & drop | HTML5 Drag and Drop API |
| Persistence | localStorage |
| Styling | Vanilla CSS (custom properties, flexbox) |
| Transpiling | Babel Standalone (in-browser JSX) |

## License

MIT
