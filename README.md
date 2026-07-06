# Daily Work Tracker

Daily Work Tracker is a small personal Kanban board for recording everyday work—not just the task name, but also why the task exists, what happened while working on it, feedback received, and the final result.

It is intentionally a simple single-file React app. There is no installation or build process.

## Open the app

1. Download this project folder.
2. Open `index.html` in a modern web browser such as Chrome, Edge, Firefox, or Safari.

An internet connection is needed when the page first loads because React and Babel are loaded from public CDNs. No development tools are required.

If you prefer to run it through a local web server and already have Node.js installed, open a terminal in the project folder and run:

```bash
npx serve .
```

Then open the local address shown in the terminal.

## What you can record

Each task supports:

- Task title
- Task source or person who assigned it
- Task background and context
- Received date
- Project or category
- Priority
- Deadline
- Current progress
- Next action
- Work process notes
- Feedback records
- Final result
- Links or attachment notes

Tasks move through six stages: **New**, **To Do**, **In Progress**, **Waiting for Feedback**, **Done**, and **Archived**.

## Daily use

1. Click **+ New task** when work arrives.
2. Add the task source, background, date, project, priority, and deadline if known.
3. Write a clear **Next action** so you know how to restart the work later.
4. Drag the card to another column as its progress changes, or change **Current progress** in the task details.
5. Click a task card whenever you need to add process notes or feedback.
6. When work is finished, record the final result and move it to **Done**.
7. Move older completed work to **Archived** to keep the active board tidy.

For longer notes, adding a date at the beginning of each update makes the history easier to follow, for example:

```text
2026-07-06 — Sent the first draft to the project owner.
2026-07-07 — Updated the summary after feedback.
```

## Where data is stored

All tasks are stored in the browser's `localStorage` on the device where you use the app. Nothing is automatically uploaded to a server.

This also means:

- Tasks do not automatically sync between devices or browsers.
- Clearing browser website data may delete your tasks.
- Opening the app under a different local address may create a separate set of saved data.
- The links/attachments field stores text and links only; it does not upload files.

The app automatically imports cards saved by the original React Kanban version when it finds them in the same browser storage.

## Project structure

```text
react-kanban/
├── index.html   # Complete app: interface, logic, and styles
└── README.md    # Usage guide
```

## Technology

- React 18 loaded from a CDN
- Babel Standalone for in-browser JSX
- Browser `localStorage` for persistence
- Native HTML drag and drop
- Plain CSS
