# ![Google Drive Private Video Downloader icon](assets/icon.svg) Google Drive Private Video Downloader

A Chromium-based extension that lets you download private/shared videos directly from Google Drive by intercepting and extracting the video stream URLs.

---

## ℹ️ Details

This extension hooks into Chromium’s debugging protocol to monitor network requests made by Google Drive’s video player. When you navigate to a Google Drive video preview modal/page, the extension captures the media streaming data, extracts the highest‑quality progressive download URL, and presents it in a simple popup for one‑click download.

### 💡 Features

- **Download Any Video**: Download any video shared with you, even if the download option is not enabled.
- **One‑Click Download**: Automatically lists available videos in the popup with a one-click download button.
- **Auto‑Display**: When new video sources are detected, the extension automatically opens its popup.
- **Disable It Anytime**: Toggle the extension on/off per browser tab without any extra hustle.
- **Quick Retry**: Button to quickly reload the current browser tab if streams aren’t detected initially.

### 🛠️ Installation

1. Clone this repository or download the ZIP file and extract it.
2. Open your chromium-based browser and go to your extensions page.
3. Enable **Developer mode** to allow local extensions to be loaded.
4. Click **Load unpacked** (or similar), then select this project’s folder.
5. The extension icon will appear in your toolbar.

### 🖥️ Usage

#### Video Page

1. Navigate to any Google Drive video URL (e.g. `https://drive.google.com/file/d/…/view`).
2. Click the extension icon to open the popup.
3. Click **ON** to enable URL capturing for the current tab. The extension will reload the tab automatically.
4. As the video loads, the popup will list the video title and a download button.
5. Click the download button to save the video locally.

#### Video Modal

1. Open any Google Drive's folder (e.g. `https://drive.google.com/drive/…`).
2. Click the extension icon to open the popup.
3. Click **ON** to enable URL capturing for the current tab. The extension will reload the tab automatically.
4. Open the preview of a video of your choice.
5. As the video loads, the popup will list the video title and a download button.
The list might additionally contain other videos URLs, as Google Drive loads multiple files at once to allow smooth transitions between previews.
6. Click the download button to save the video locally.

### 🧠 How Does It Work

#### Interception Phase

1. The `background/background.js` script uses the Chrome Debugger API (`chrome.debugger`) to listen for `Network.requestWillBeSent` and `Network.responseReceived` events.
2. When it detects requests to `workspacevideo-pa.clients6.google.com`, it stores the request and retrieves its response body.
3. It parses the JSON response for `progressiveTranscodes` URLs (the direct MP4 links) and the video title.

#### Pooling Phase

1. The `popup/popup.js` script polls in repeat the background script for captured requests, updates the UI with any new videos, and invokes `chrome.downloads.download` when you click a download button.
2. State (enabled/disabled) is persisted via `chrome.storage.local`, and you can toggle it per‑tab.

### 🔐 Permissions

- `debugger` – to attach to the tab’s network events.
- `activeTab` – to detect and reload the active Drive tab.
- `downloads` – to programmatically download video files.
- `storage` – to save the extension’s enabled/disabled state.
- `<all_urls>` host permission – to allow the debugger to attach to any URL (required by the Debugger API).

---

## ❗ Important Notes

- File access permission is needed, but not download permission.
- Only works on Google Drive's video preview modals or video pages.

---

## ⚖️ Disclaimer

By definition, a cloud-based video sharing/streaming platform always downloads and saves the video into your browser for you to watch.

**This does not bypass Google Drive’s security**. Google [states clearly][1] that this specific functionality is intended behaviour and they will not try to block it in the forseeable future.

[1]: https://bughunters.google.com/learn/invalid-reports/google-products/5300109711245312/download-print-copy-protection-bypasses-in-drive "Download protection bypasses in Google Drive"
