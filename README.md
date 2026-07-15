# Daily Work Tracker

Daily Work Tracker is a small personal Kanban board for recording everyday work: the task background, current progress, process notes, feedback, and final result.

The app remains a single `index.html` file. Firebase Authentication provides Google Sign-In, and Cloud Firestore keeps each user's tasks synchronized between computers and phones.

## Features

- Three workflow stages: **收集箱**, **进行中**, and **已完成**
- Kanban View for moving work through its workflow
- Table View for searching, filtering, sorting, and reviewing tasks
- Smart Quick Entry for turning Chinese or English paragraphs into task-field previews
- Detailed task records and long-form notes
- Drag-and-drop status changes
- Google Sign-In
- Private task collections for each user
- Real-time synchronization through Cloud Firestore
- Direct Google Calendar synchronization for dated tasks and execution items
- Clear **Not signed in**, **Syncing**, **Synced**, and **Sync failed** indicators
- Per-account browser cache and an optional import for tasks from the old local-only version

## Firebase setup

### 1. Create a Firebase project

1. Open the [Firebase console](https://console.firebase.google.com/).
2. Select **Create a project** and follow the prompts.
3. Analytics is optional for this app.

### 2. Register the web app

1. On the Firebase project overview page, select the **Web** icon (`</>`).
2. Give the app a name such as `Daily Work Tracker`.
3. Select **Register app**.
4. Firebase displays a `firebaseConfig` object. Keep this page open or copy the values.

Firebase Hosting is not required because the app is published with GitHub Pages.

### 3. Enable Google Sign-In

1. In Firebase, open **Build → Authentication**.
2. Select **Get started** if Authentication has not been initialized.
3. Open **Sign-in method**.
4. Select **Google**, enable it, choose a support email, and save.
5. In Authentication settings, open **Authorized domains**.
6. Add your GitHub Pages domain, usually:

```text
YOUR_GITHUB_USERNAME.github.io
```

Add only the domain—not `https://` and not the repository path.

### 4. Create Cloud Firestore

1. In Firebase, open **Build → Firestore Database**.
2. Select **Create database**.
3. Choose a database location near you.
4. Start in **Production mode**.

### 5. Add Firestore security rules

Open **Firestore Database → Rules**, replace the existing rules with the following, and select **Publish**:

```text
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

These rules allow a signed-in user to read and change only data stored under their own Firebase user ID.

### 6. Paste the Firebase configuration

Open `index.html` and find this block near the beginning of the app scripts:

```javascript
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};
```

Replace the entire object with the exact `firebaseConfig` object shown in **Firebase → Project settings → General → Your apps → SDK setup and configuration**.

The Firebase web configuration identifies your Firebase project; it is designed to be included in browser code. Data access is protected by Authentication and the Firestore security rules above.

## Google Calendar synchronization

The app can connect directly to a user's Google Calendar. The task tracker remains the source of truth: saving, changing, dragging, or deleting a task can update the matching Google Calendar event. Changes made directly in Google Calendar do not update the task tracker.

### Google Cloud setup

1. In the existing Google Cloud project, enable **Google Calendar API**.
2. In **Google Auth Platform → Data access**, add this scope:

```text
https://www.googleapis.com/auth/calendar.events
```

3. Create a separate OAuth client with application type **Web application**.
4. Add the GitHub Pages origin under **Authorized JavaScript origins**, for example:

```text
https://raysorvancooten-prog.github.io
```

Use only the origin here, without the repository path or trailing slash. The pop-up token flow does not require a redirect URI.

The public OAuth client ID is stored in `GOOGLE_CALENDAR_CLIENT_ID` in `index.html`. An OAuth client ID is a public web-app identifier. Never add an OAuth client secret to `index.html`.

Calendar access is considered a sensitive Google OAuth scope. An unverified production app may display an unverified-app warning and is subject to Google's user limit. Complete Google OAuth verification before broadly publishing calendar access.

### Connect and use

1. Sign in to the task tracker.
2. Select **日历同步** in the header.
3. Select **连接Google日历** and approve access to calendar events.
4. Keep **保存任务时自动同步** enabled.
5. Select **同步现有任务** once to add existing dated tasks.

Task start and completion dates create an all-day task event. Execution checklist rows with a start/end time create separate timed events. The app stores only returned Google event IDs in the task record so later saves update the same event instead of creating duplicates.

The Google access token is kept only in the current browser session. It is not saved to Firestore or included in exports. When the short-lived Google authorization expires, reconnect from **日历同步**; task saving continues even if calendar synchronization fails.

Deleting a task asks whether its matching Google events should also be deleted. If Google Calendar is disconnected, the task can still be deleted, but old calendar events must be removed manually after reconnecting.

The existing `.ics` download remains available as a fallback for other calendar applications.

## Publish through GitHub Pages

If GitHub Pages is already working:

1. Replace the repository's existing `index.html` with the updated file.
2. Replace `README.md` with this file.
3. Commit the changes to the branch used by GitHub Pages.
4. Wait a minute or two, then reload the GitHub Pages URL.

For a new GitHub Pages setup:

1. Upload `index.html` and `README.md` to a GitHub repository.
2. Open the repository's **Settings → Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**.
4. Select the branch containing the files and the `/ (root)` folder.
5. Save and wait for GitHub to display the published URL.

After publishing, confirm that the GitHub Pages domain is listed under Firebase Authentication's **Authorized domains**.

## Use the app on computer and phone

1. Open the same GitHub Pages URL on both devices.
2. Select **Sign in with Google**.
3. Use the same Google account on both devices.
4. Add or edit a task on one device.
5. The other open device should update automatically. If it was closed, it loads the latest tasks the next time it opens.

The status beside the account controls indicates the connection state:

- **Not signed in** — sign in to access cloud tasks.
- **Syncing** — a change is being sent or the latest tasks are loading.
- **Synced** — Firestore has confirmed the current data.
- **Sync failed** — check the connection, Firebase configuration, authorized domain, and security rules.

Google Sign-In opens a pop-up. If a phone or browser blocks it, allow pop-ups for the GitHub Pages site and try again.

## Kanban View and Table View

Use the switcher near the top of the signed-in app to choose a view. Both views use the same Firestore task records, so editing a task in either view updates the other view and every signed-in device.

### Kanban View

Kanban View is the main workflow workspace. It keeps all six columns and drag-and-drop behavior. Drag a task between columns to update its status, or select a card to open the full task details.

### Table View

Table View is designed for search, filtering, sorting, and review. Each task appears as a row on desktop. On a phone, the same results appear as compact cards so the page does not require a very wide table.

Select any table row or mobile task card to open the same detail and editing form used by Kanban View.

Table View shows:

- Title
- Status
- Project or category
- Task source
- Priority
- Created date
- Due date
- Last updated time
- Final result or summary

New tasks receive a Firestore creation timestamp. For tasks created before Table View was added, the received date is shown as the created date when no creation timestamp exists.

### Filters

- **Search keyword** searches titles, projects, sources, background, next actions, process notes, feedback, final results, and links.
- **Status** shows tasks in one workflow stage.
- **Project / Category** shows tasks assigned to one existing project or category.
- **Priority** filters by Low, Medium, or High.
- **Created from / Created to** filters by creation date. Older tasks use their received date as a fallback.
- **Show only unfinished tasks** hides Done and Archived tasks.
- **Show only tasks waiting for feedback** shows only the Waiting for Feedback column.

Filters can be combined. For example, choose a project, High priority, and unfinished tasks to review urgent open work for that project.

Use **Sort by** to order results by created date, due date, last updated time, status, or priority. The adjacent button switches between ascending and descending order.

## Smart Quick Entry

Smart Quick Entry helps create a task from a natural-language paragraph without removing the manual form.

1. Select **Smart Quick Entry** near **+ New task**.
2. Type, paste, or dictate a Chinese or English description.
3. Select **AI Parse** or **Rule Parse**.
4. Review the parsed result preview.
5. Select **Apply parsed result** to copy non-empty parsed values into the task form.
6. Review or edit the fields manually, then select **Save task**.

Parsing never changes the form immediately. Existing form values remain untouched until **Apply parsed result** is selected. Saving still uses the normal Firestore task flow, so tasks created through Smart Quick Entry synchronize exactly like manually created tasks.

### AI Parse

AI Parse sends the quick-entry paragraph to the API endpoint configured in **AI Settings**. It asks the model to return structured JSON containing only information found in the paragraph. Missing information should remain blank.

The configured endpoint must accept an OpenAI-compatible Chat Completions request and allow browser requests from the GitHub Pages domain. Some providers intentionally block browser-side requests with CORS rules; in that case, use a private backend endpoint or Rule Parse.

### Rule Parse

Rule Parse works entirely in the browser and does not require an API key. It recognizes common Chinese and English expressions for:

- Priority
- Workflow status
- Relative and written due dates
- Next action
- Feedback
- Final result
- Common assignment phrases and task titles

Rule Parse is intentionally conservative and may leave fields blank. Review its preview and complete anything it could not detect manually.

### Dictation

Select **Dictate** to use the browser's speech-recognition feature when available. The browser may ask for microphone permission. If speech recognition is unavailable, type or paste the paragraph instead.

## Configure AI settings

1. Sign in and select **AI Settings** in the header or Smart Quick Entry section.
2. Keep the provider name as **OpenAI-compatible**, or enter the name of the compatible provider.
3. Enter the full API endpoint, such as the provider's `/v1/chat/completions` endpoint.
4. Enter the API key and model name.
5. Select **Save settings**.

The settings are stored under `dailyWorkTrackerAISettings` in that browser's `localStorage`. They are not saved to Firestore and do not synchronize to other devices. Configure each trusted device separately.

### API-key safety

Never hard-code an AI API key in `index.html`, commit it to GitHub, or use a shared/public key. A GitHub Pages site is public, so secrets placed in its source are visible to everyone.

Browser-local storage avoids publishing the key, but scripts running on the same website can still access it. Use a restricted personal key with spending limits when the provider supports them. For stronger security, configure the app to call a private backend endpoint that keeps the provider key on the server.

The AI key is used only when **AI Parse** is selected. Rule Parse, manual entry, Firebase Authentication, and Firestore synchronization do not use it.

## How data is stored

The main copy of each task is stored in this Firestore path:

```text
users/{uid}/tasks/{taskId}
```

The `{uid}` is supplied by Firebase Authentication. This separates one account's tasks from another account's tasks.

The app also keeps a per-account `localStorage` cache so the last received tasks can appear while Firestore reconnects. Firestore remains the source of truth after sign-in; clearing the cache does not delete cloud tasks. Kanban View and Table View both read from the same real-time Firestore listener.

If tasks from the old local-only app are found, the signed-in app displays **Import into my account**. Use it once on the device that contains those old tasks. The local copy is removed only after the Firestore import succeeds.

## Daily use

1. Select **+ New task** when work arrives.
2. Record the source, background, project, priority, and deadline.
3. Write a specific **Next action**.
4. Drag the task as its status changes.
5. Open the card to add process notes and feedback.
6. Record the final result and move the task to **Done**.
7. Move older completed work to **Archived**.

For longer notes, begin each update with a date:

```text
2026-07-06 — Sent the first draft to the project owner.
2026-07-07 — Updated the summary after feedback.
```

## Project structure

```text
react-kanban/
├── index.html              # Complete app, Firebase integration, interface, logic, and styles
├── manifest.webmanifest    # PWA installation metadata
├── sw.js                   # App-shell service worker
├── icons/                  # PWA icons
└── README.md               # Setup and usage guide
```

## Technology

- React 18 loaded from a CDN
- Babel Standalone for in-browser JSX
- Firebase Web SDK loaded as browser modules from the Firebase CDN
- Firebase Authentication with Google Sign-In
- Cloud Firestore real-time listeners
- Google Identity Services and Google Calendar REST API
- Native HTML drag and drop
- Plain CSS
