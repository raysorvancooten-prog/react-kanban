# Daily Work Tracker

Daily Work Tracker is a small personal Kanban board for recording everyday work: the task background, current progress, process notes, feedback, and final result.

The app remains a single `index.html` file. Firebase Authentication provides Google Sign-In, and Cloud Firestore keeps each user's tasks synchronized between computers and phones.

## Features

- Six stages: **New**, **To Do**, **In Progress**, **Waiting for Feedback**, **Done**, and **Archived**
- Detailed task records and long-form notes
- Drag-and-drop status changes
- Google Sign-In
- Private task collections for each user
- Real-time synchronization through Cloud Firestore
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

## How data is stored

The main copy of each task is stored in this Firestore path:

```text
users/{uid}/tasks/{taskId}
```

The `{uid}` is supplied by Firebase Authentication. This separates one account's tasks from another account's tasks.

The app also keeps a per-account `localStorage` cache so the last received tasks can appear while Firestore reconnects. Firestore remains the source of truth after sign-in; clearing the cache does not delete cloud tasks.

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
├── index.html   # Complete app, Firebase integration, interface, logic, and styles
└── README.md    # Firebase setup and usage guide
```

## Technology

- React 18 loaded from a CDN
- Babel Standalone for in-browser JSX
- Firebase Web SDK loaded as browser modules from the Firebase CDN
- Firebase Authentication with Google Sign-In
- Cloud Firestore real-time listeners
- Native HTML drag and drop
- Plain CSS
