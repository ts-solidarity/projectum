# Changelog

All notable changes to Projectum are documented in this file.
The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and the project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.0] — 2026-05-29

A large correctness, performance, and distribution release. Adds a self‑contained Linux AppImage, replaces the Markdown preview toggle with a live WYSIWYG highlighter, and fixes a broad set of data‑loss, crash, and smoothness issues found in an exhaustive review.

### Added

- **Linux AppImage** — a self‑contained, single‑file build (bundles Python, Qt, PySide6, `yt-dlp`) plus a tagged GitHub release pipeline that builds and publishes it automatically.
- **WYSIWYG Markdown** in every notes pane — headings, bold/italic, inline & fenced code, lists, blockquotes, strikethrough and links render live as you type; the old editor⇄preview toggle is gone.
- **Smooth wheel scrolling** for mouse wheels, with high‑precision/trackpad input passed through to the native (lower‑latency) path.

### Fixed

- **Playlist *Refresh* could corrupt the wrong playlist.** If you switched playlists while a refresh was in flight, the fetched data merged into the newly‑selected playlist. Refreshes now resolve their target by URL.
- **Drag‑reordering could blank rows and crash.** Qt's internal move dropped the row widget and left stale item pointers, which could raise `RuntimeError` on the next filter/search; rows are now re‑attached correctly.
- **Pinned state and manual order were lost** when a project folder disappeared and later returned (the orphan store ignored `pinned`/`position`).
- **Opening a folder wiped persisted settings** — theme, font, and window geometry were clobbered because the last‑folder write replaced the whole state file.
- **Tag row "squeeze."** Removing one of two tags crushed the sidebar row to the no‑tags height; row heights now settle correctly after a tag change.
- **Mid‑edit notes could be lost** when a filter or search hid the selected project before the debounced save fired.
- **Duplicate video IDs** in a playlist collapsed onto one object on reload/refresh, losing per‑occurrence watched state and notes.
- **Filter and search leaked across folders** when opening a new folder.
- **Corrupt or hand‑edited `state.json` / `.projectum.json`** could crash startup or make a folder unopenable; parsing is now defensive and type‑guarded.
- **Stuck buttons** — *Refreshing…* / *Fetching…* no longer get stuck after a folder change mid‑operation.
- **Changing a tag's color** no longer closes the open tag editor or discards typed input.
- **Markdown rendering** — inline code is no longer overwritten by bold/italic when nested, and inline markup inside headings keeps the heading size.
- Several Qt object leaks (color‑picker popup, command palette, settings dialog, per‑call animations) and assorted type‑guard crashes on unexpected persisted values.

### Performance

- Drag‑reorder reconciles in milliseconds instead of rebuilding the entire list (which froze for ~½ s on large folders).
- Search filtering is debounced, so typing stays responsive on big folders.
- Shorter, snappier transition animations.

### Engineering

- Continuous integration boots `MainWindow` on a headless `offscreen` display and lints with `ruff`; the release is verified end‑to‑end before publishing.

## [1.0.0] — 2026-05-21

The first stable release.

### Highlights

- **Three workspaces** in one window — Projects (filesystem-backed), Playlists (YouTube via `yt-dlp`), Notes (folder-wide scratchpad).
- **Markdown everywhere.** Per-project, per-playlist, per-video and global notes all toggle between an editor and a live preview.
- **Pin & drag-to-reorder** for projects and playlists.
- **Tested toggle** (blue) alongside the Done toggle (green); tested projects show their name in blue on the sidebar.
- **`Ctrl+K` command palette** over projects, playlists, videos, tags and the scratchpad — prefix-ranked, type-ahead.
- **9 themes** (Catppuccin Mocha/Latte, Nord, Dracula, Tokyo Night, Rosé Pine, Gruvbox Dark, Solarized Dark/Light), **any installed font family**, free-form font size — live via the settings panel.
- **Animation system overhaul.** Pixmap-snapshot crossfades replace the QGraphicsOpacityEffect path that visibly glitched on widget trees with custom paints; geometry slides for collapsible sections.
- **Per-folder state** persisted in a single `.projectum.json` — Git-friendly. Window/theme prefs live in `~/.config/projectum/state.json`.
- **Frameless window** with custom title bar, drag-to-move, resize handles on all edges.
- **Tag system** with custom colors per tag (right-click any chip), automatic cleanup when nothing references a tag, and a tag filter on the sidebar.
- **Video tracking** — per-video done state, per-video notes, watched count, animated progress bar.

### Engineering notes

- Instances of `Project`, `Playlist`, `Video` are now preserved across `store.load()` so callers holding references stay valid across the file-watcher debounce.
- `QApplication.setFont` + per-window broadcast on settings change, so font family selection actually applies (was previously silently overridden by QSS).
- Custom-painted widgets read `theme.X` lazily so theme changes don't require widget rebuilds for repaints.

[1.1.0]: https://github.com/ts-solidarity/projectum/releases/tag/v1.1.0
[1.0.0]: https://github.com/ts-solidarity/projectum/releases/tag/v1.0.0
