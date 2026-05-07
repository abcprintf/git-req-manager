# 🦊 GitLab MR Manager

English | [ภาษาไทย](./README.md)

Desktop application for tracking GitLab Merge Requests awaiting review or merging — supports Windows and macOS.

![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20macOS-blue)
![Tech](https://img.shields.io/badge/built%20with-Electron%20%2B%20TypeScript%20%2B%20React-orange)

---

## 📋 Table of Contents

- [Features](#-features)
- [Installation](#-installation)
- [Initial Setup](#-initial-setup)
- [Usage](#-usage)
- [Building from Source](#-building-from-source)
- [Project Structure](#-project-structure)
- [FAQ](#-faq)

---

## ✨ Features

- 🔔 **Desktop Notifications** — Get notified when new MRs are assigned to you for review.
- 📋 **My Reviews** — View all Merge Requests where you are assigned as a reviewer.
- 📂 **All Open MRs** — Browse all open MRs across all projects you have access to.
- ⏱️ **Auto Sync** — Automatically fetches fresh data based on your preferred interval.
- ⬆️ **In-app Updates** — Check for updates from GitHub Releases and install new versions directly within the app.
- 🔒 **Secure Token Storage** — Personal Access Tokens are protected using OS-level encryption.
- 🌐 **Supports All GitLab Instances** — Works with `gitlab.com` and self-hosted GitLab instances.

---

## 📦 Installation

### Method 1 — Download Installer (Recommended)

1. Go to the [Releases](../../releases) page.
2. Select the latest version.
3. Download the file corresponding to your OS:
   - **Windows**: `GitLab.MR.Manager-x.x.x-Setup.exe`
   - **macOS**: `GitLab.MR.Manager-x.x.x.dmg`
4. Install and run.

> **macOS**: If you see an "unidentified developer" warning, go to System Preferences → Security & Privacy → Open Anyway.

### Method 2 — Build from Source

See the [Building from Source](#-building-from-source) section.

---

## ⚙️ Initial Setup

### Step 1 — Create a GitLab Personal Access Token

1. Log in to GitLab → **User Settings** → **Access Tokens**.
2. Click **Add new token**.
3. Name it (e.g., `mr-manager`).
4. Select Scopes: Check **`read_api`** only.
5. Click **Create personal access token**.
6. **Copy the token immediately** (it will not be shown again).

### Step 2 — Configure the App

1. Open the app → click the **⚙️** icon in the top-right corner.
2. Enter the following details:

| Field | Example | Description |
|------|---------|-------------|
| **GitLab URL** | `https://gitlab.com` | Your GitLab URL (supports self-hosted). |
| **Personal Access Token** | `glpat-xxxx...` | The token created in Step 1. |
| **Refresh Interval** | `5` | Sync frequency in minutes (1–120). |
| **Project IDs** | *(Optional)* | Comma-separated IDs to limit the scope. |

3. Click **Save & Connect**.

> **Project IDs**: If left blank, the app will automatically track MRs from all projects you have access to.

---

## 📖 Usage

### Dashboard Overview

```
┌─────────────────────────────────────┐
│ 🦊 GitLab MR Manager    ⚙️  ✕      │  ← Title bar (Draggable)
├──────────────┬──────────────────────┤
│  My Reviews  │  All Open            │  ← Tabs
│     (3)      │    (12)              │
├─────────────────────────────────────┤
│ 👤 somchai                          │
│  feat: add user login          !42  │  ← Click to open in browser
│  my-project · 2h ago               │
│  main ← feature/login              │
├─────────────────────────────────────┤
│ ...                                 │
├─────────────────────────────────────┤
│ Last sync: 21:05:00    ↻ Refresh   │  ← Footer
└─────────────────────────────────────┘
```

### Components and Controls

| Component | Description |
|-----------|-------------|
| **My Reviews** | MRs where you are assigned as a reviewer (`reviewer_id=me`). |
| **All Open** | All open MRs in projects you have access to. |
| **MR Card** | Click any card to open the MR in your default browser. |
| **↻ Refresh** | Manually trigger an immediate data sync. |
| **✕** | Minimize the app to the system tray (does not quit). |

### System Tray Integration

- **Gray Icon** — No MRs awaiting review.
- **Orange Icon** — You have MRs waiting for review.
- **Left Click** — Toggle window visibility.
- **Right Click** — Context menu (Open / Refresh / Quit).
- **Single Instance** — Opening the app again will bring the existing window to the front.
- **macOS** — The Dock icon is shown only when the window is visible.

### Desktop Notifications

The app sends notifications when:
- A new MR is assigned to you for review.
- Clicking the notification opens the MR in your browser.

---

## 🛠️ Building from Source

### Prerequisites

- **Node.js** 18+ — [download](https://nodejs.org)
- **npm** 9+
- **Git**

### Steps

```bash
# 1. Clone the repository
git clone <repo-url>
cd gitlab-req-manager

# 2. Install dependencies
npm install

# 3. Run in development mode (hot-reload)
npm run dev

# 4. Or build and start
npm run build
npm start
```

### Packaging Installers

```bash
# Windows (.exe installer)
npm run package:win

# macOS (.dmg)
npm run package:mac

# Both platforms
npm run package
```

Built files will be located in the `release/` folder.

> **Note**: Building for macOS requires a macOS environment.

---

## 🗂️ Project Structure

```
gitlab-req-manager/
├── src/
│   ├── main/                   # Electron main process (Node.js)
│   │   ├── index.ts            # App entry point & IPC handling
│   │   ├── tray.ts             # System tray & context menu
│   │   ├── scheduler.ts        # Polling logic & state management
│   │   ├── notifier.ts         # Desktop notification logic
│   │   └── store.ts            # Configuration storage (encrypted tokens)
│   ├── renderer/               # React UI
│   │   ├── App.tsx             # Root component & routing
│   │   ├── pages/
│   │   │   ├── Dashboard.tsx   # MR list view (Tabs)
│   │   │   └── Settings.tsx    # Configuration page
│   │   └── components/
│   │       └── MRCard.tsx      # Individual MR card component
│   ├── shared/
│   │   ├── types.ts            # Shared TypeScript interfaces
│   │   └── gitlab.ts           # GitLab REST API v4 client
│   └── preload.ts              # IPC Bridge (contextBridge)
├── assets/
│   ├── tray-icon.png           # Default tray icon (Gray)
│   └── tray-icon-active.png    # Active tray icon (Orange)
├── dist/                       # Compiled output (auto-generated)
├── release/                    # Packaged installers (auto-generated)
├── package.json
├── tsconfig.json               # TypeScript config for Renderer
├── tsconfig.main.json          # TypeScript config for Main process
├── vite.config.ts              # Vite configuration
└── electron-builder.config.ts  # Packaging configuration
```

---

## ❓ FAQ

**Q: Does it support self-hosted GitLab?**  
A: Yes. Simply enter your instance URL (e.g., `https://gitlab.mycompany.com`) in the settings.

**Q: Is my token stored securely?**  
A: Yes. Tokens are encrypted using Electron's `safeStorage` (OS-level encryption) before being saved.

**Q: What GitLab permissions are required?**  
A: Only the `read_api` scope is needed. The app does not write or modify any data.

**Q: How many MRs can "All Open" display?**  
A: Up to 100 most recent MRs (standard GitLab API limit).

**Q: Where did the app go after I closed the window?**  
A: It's still running in the system tray. Look for the 🦊 icon in your notification area.

**Q: How do I quit the app?**  
A: Right-click the tray icon and select **Quit**.
