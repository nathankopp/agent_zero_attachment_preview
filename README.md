# Attachment Preview

Inline preview panel for chat attachments and workdir file links — supports images, PDF, video, audio, markdown, HTML, and source code.

## What It Does

This plugin adds a split-screen preview panel below the chat area. Clicking a previewable attachment chip or a workdir file link in agent messages opens the file directly inside the UI instead of downloading or navigating away.

## Main Behavior

- **Attachment chips**
  - Intercepts clicks on non-image attachment chips in chat history via capture-phase event delegation.
  - Routes to the preview panel if the file extension is supported.
- **Workdir links**
  - Overrides `openFileLink()` to preview files linked in agent messages (`<a onclick="openFileLink(...)">`).
  - Intercepts `<a href="/api/download_work_dir_file?...">` links in chat history via delegated click handler.
- **Extension point injection**
  - Dynamically injects `<x-extension id="right-panel-after-chat">` into `#right-panel` at init time (detected by MutationObserver).
- **Preview routing**
  - Images → `/api/image_get` (reuses core API).
  - Markdown / text / code → `/api/edit_work_dir_file` (fetches content, renders markdown with safe renderer).
  - HTML / PDF / video / audio → `/api/plugins/attachment_preview/preview` (streams file inline with appropriate Content-Type).
- **Panel features**
  - Vertical split layout with drag-to-resize handle.
  - Maximize to 80% centered overlay with blur backdrop.
  - Download button for saving the previewed file.

## Key Files

- **API**
  - `api/preview.py` — Streams files inline with CSP sandbox for HTML. Handles both Docker and development environments.
- **Frontend**
  - `webui/preview-store.js` — Alpine.js store (`attachmentPreview`) managing state, file type routing, resize, maximize, and click interception logic.
  - `extensions/webui/right-panel-after-chat/preview-panel.html` — Panel component with inlined CSS, loaded via the `right-panel-after-chat` extension point.

## Plugin Metadata

- **Name**: `attachment_preview`
- **Title**: `Attachment Preview`
- **Description**: Inline preview panel for attachments - supports PDF, text, code, video, audio, markdown, and HTML files.
