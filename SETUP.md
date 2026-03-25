# Kolkata LKG/K1 Admissions Directory — Deployment Guide

## Quick Summary

| What | Where | Time |
|---|---|---|
| Firebase (notes sync) | console.firebase.google.com | 5 minutes |
| GitHub Pages (hosting) | github.com → Settings → Pages | 3 minutes |
| Total | | ~8 minutes |

---

## Step 1: Firebase Setup (5 minutes)

### 1a. Create Firebase Project

1. Go to **https://console.firebase.google.com**
2. Click **"Create a project"** (or "Add project")
3. Name it: `kolkata-k1-tracker` (or anything you want)
4. Disable Google Analytics (not needed) → **Create Project**
5. Wait ~30 seconds → Click **Continue**

### 1b. Create Realtime Database

1. In the Firebase console sidebar, click **Build → Realtime Database**
2. Click **Create Database**
3. Choose location: **Singapore (asia-southeast1)** (closest to Kolkata)
4. Start in **Test mode** (we'll lock it down later)
5. Click **Enable**

### 1c. Get Your Config

1. Click the **⚙️ gear icon** (top-left) → **Project settings**
2. Scroll to **"Your apps"** section → Click **</> (Web)**
3. Register app name: `kolkata-k1` → Click **Register app**
4. You'll see a code block like:

```javascript
const firebaseConfig = {
  apiKey: "AIzaSyB...",
  authDomain: "kolkata-k1-tracker.firebaseapp.com",
  databaseURL: "https://kolkata-k1-tracker-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "kolkata-k1-tracker",
  storageBucket: "kolkata-k1-tracker.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef..."
};
```

5. **Copy these values** — you need them in the next step.

### 1d. Paste Config into index.html

Open `index.html` and find this block near the top (line ~12):

```javascript
const FIREBASE_CONFIG = {
  apiKey: "YOUR_API_KEY_HERE",
  ...
};
```

Replace with your actual values:

```javascript
const FIREBASE_CONFIG = {
  apiKey: "AIzaSyB...",
  authDomain: "kolkata-k1-tracker.firebaseapp.com",
  databaseURL: "https://kolkata-k1-tracker-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "kolkata-k1-tracker",
  storageBucket: "kolkata-k1-tracker.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef..."
};
```

### 1e. Secure Your Database (Important!)

Go back to Firebase Console → Realtime Database → **Rules** tab.

Replace the rules with:

```json
{
  "rules": {
    "users": {
      "$pin": {
        ".read": true,
        ".write": true
      }
    }
  }
}
```

Click **Publish**.

### 1f. Enable & Secure Firebase Storage

1. In Firebase Console sidebar, click **Build → Storage**
2. Click **Get started** → Start in **Test mode** → Choose **asia-south1** (Mumbai) → **Done**
3. Go to **Storage → Rules** tab and replace with:

```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /users/{pin}/{allPaths=**} {
      allow read, write: if request.resource == null || request.resource.size < 5 * 1024 * 1024;
    }
  }
}
```

Click **Publish**. This allows uploads up to 5MB per file under each PIN's folder.

---

## Step 2: GitHub Pages Deployment (3 minutes)

### 2a. Create GitHub Repository

1. Go to **https://github.com/new**
2. Repository name: `kolkata-k1-schools` (or anything)
3. Set to **Public** (required for free GitHub Pages)
4. Click **Create repository**

### 2b. Upload Files

**Option A — Web upload (easiest):**
1. On the repo page, click **"uploading an existing file"**
2. Drag and drop `index.html` from this folder
3. Click **Commit changes**

**Option B — Git command line:**
```bash
cd kolkata-k1-directory
git init
git add .
git commit -m "Initial deploy — Kolkata K1 schools directory"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/kolkata-k1-schools.git
git push -u origin main
```

### 2c. Enable GitHub Pages

1. Go to repo → **Settings** → **Pages** (left sidebar)
2. Under "Source", select **Deploy from a branch**
3. Branch: **main** / Folder: **/ (root)**
4. Click **Save**
5. Wait 1–2 minutes → Your site is live at:

```
https://YOUR_USERNAME.github.io/kolkata-k1-schools/
```

### 2d. Custom Domain (Optional)

1. In Settings → Pages → **Custom domain**, type your domain (e.g. `schools.finarb.com`)
2. Add these DNS records at your domain registrar:

| Type | Name | Value |
|---|---|---|
| CNAME | schools | YOUR_USERNAME.github.io |

3. Check **Enforce HTTPS**
4. Wait ~30 minutes for DNS propagation

---

## Step 3: Using the Tracker

### First Visit
1. Open the site on any device
2. Enter a PIN (e.g. `abhishek2026`)
3. Start adding notes to schools
4. Notes sync to Firebase in real-time

### Another Device
1. Open the same URL on phone/tablet/another laptop
2. Enter the **same PIN** (`abhishek2026`)
3. All your notes appear instantly

### Features
- **☁️ Synced** badge (bottom-right) = Firebase connected
- **💾 Local only** badge = Firebase not configured, using browser storage
- **📋 Export TXT** = Download readable text file of all notes
- **💾 Export JSON** = Download JSON backup (can reimport)
- **📥 Import JSON** = Restore from backup
- **🔑 Change PIN** = Switch to a different PIN namespace

---

## Troubleshooting

| Issue | Fix |
|---|---|
| "💾 Local only" badge | Firebase config not filled in or wrong API key |
| Notes not syncing | Check databaseURL matches your Firebase region |
| Blank page | Check browser console (F12) for JavaScript errors |
| "Permission denied" | Update Firebase Realtime Database rules (Step 1e) |
| GitHub Pages 404 | File must be named `index.html` exactly |

---

## File Structure

```
kolkata-k1-directory/
├── index.html      ← The entire app (single file, no dependencies)
├── SETUP.md         ← This guide
└── README.md        ← Brief description for GitHub
```

## Cost

| Service | Free Tier | Your Usage |
|---|---|---|
| Firebase Spark | 1GB storage, 10GB/mo bandwidth | ~0.001% |
| GitHub Pages | Unlimited for public repos | ~0% |
| **Total monthly cost** | | **₹0** |
