# Telegram Notes Backup — README

## 1. Repo folder layout

Your GitHub repo root should look like this:

```
repo-root/
├── chats.html          ← top-level list of all chats (the one you customized order/names in)
├── export_results.html
├── css/
│   └── style.css        ← your customized readable stylesheet (keep a backup copy outside the repo too)
├── js/
│   └── script.js
├── images/               ← Telegram's icons, used by style.css
└── notes/                ← (this was originally called "chats") one folder per channel
    ├── Angular/
    │   ├── messages.html
    │   └── photos/ ...
    ├── SPRING 8 - AOP/
    ├── JSP/
    └── ... (one folder per topic)
```

Everything under `notes/` and the top-level `chats.html` are the two places
that ever need editing. `css/`, `js/`, `images/` stay as-is once set up.

---

## 2. Adding ONE new channel later

You don't need to re-run any script for just one new channel — do it by hand,
it's quick:

**Step 1 — Export only the new channel**
- Telegram Desktop → Settings → Advanced → **Export Telegram data**.
- Uncheck everything except the new channel.
- Format: **HTML**.
- Export to any temp folder, e.g. `Downloads\NewExport`.

**Step 2 — Find the new chat's folder**
- Inside `Downloads\NewExport\chats\`, there'll be a `chat_01` folder (since
  it's a fresh export, numbering restarts at 1 — that's fine, it's a folder
  in isolation).
- Open `Downloads\NewExport\chats.html` to confirm the channel name shown
  next to it.

**Step 3 — Move it into your repo**
- Copy the whole `chat_01` folder into your repo's `notes/` folder.
- Rename that copied folder (right-click → Rename) to the channel's actual
  name, e.g. `Kubernetes`.

**Step 4 — Check for broken internal links**
- Open `notes/Kubernetes/messages.html` (and `messages2.html`,
  `messages3.html` etc. if the chat is long enough to split into multiple
  pages) in a text editor.
- Press Ctrl+F and search for `chats/` and `chat_0` (or `chat_1`).
- If you find any matches in `href=` or `src=` attributes, replace them:
  - `chats/` → `notes/`
  - `chat_01/` (or whatever number) → `Kubernetes/`
- Usually a single-channel export has no such references (photos/media stay
  in that same folder), but always check once.

**Step 5 — Add it to the chat list**
- Open your repo's root `chats.html`.
- Copy an existing `<a class="entry block_link clearfix" href="...">...</a>`
  block (any one) and paste it where you want the new channel to appear in
  the order.
- Change its `href` to `../notes/Kubernetes/messages.html#allow_back`.
- Change the `<div class="name bold">` text to `Kubernetes`.
- (Optional) update the userpic letter/color div and message count text to
  match — cosmetic only, won't break anything if left as copied.

**Step 6 — Test**
- Open `chats.html` in your browser, click into the new channel, confirm
  text and images load correctly.

---

## 3. Re-exporting MANY/ALL channels at once (bulk refresh)

If you ever redo the whole export from scratch (all channels together),
follow the full scripted procedure instead — ask me and I'll regenerate the
scripts, or reuse the earlier ones:

1. Export all channels as HTML into a fresh folder.
2. Open the new `chats.html`, note which `chat_XX` = which topic.
3. Rename the `chats` folder → `notes`.
4. Update `$FolderMap` in `Rename-TelegramNotes.ps1` and
   `Fix-ChatsPathReferences.ps1` to match the new chat_XX → name mapping.
5. Run both scripts (Shift + right-click → "Open PowerShell window here" →
   `powershell -ExecutionPolicy Bypass -File "script.ps1"` — never
   double-click a `.ps1`, the window closes before you can read errors).
6. Manually reorder `chats.html` and fix its `href`s to the new folder names.
7. Copy your saved master `style.css` into `css/style.css`.
8. Test in browser, then follow the git steps below.

---

## 4. Git: committing and pushing

From inside your repo folder:

```bash
cd C:\Users\jawah\MY_FILES\MY_NOTES
git add .
git commit -m "Add Kubernetes channel notes"
git push
```

**First time only** (if the repo isn't set up yet):
```bash
git init
git remote add origin https://github.com/<your-username>/<repo-name>.git
git branch -M main
git add .
git commit -m "Initial notes backup"
git push -u origin main
```

**Good commit message habits for this repo:**
- `"Add <Channel Name> notes"` — when adding a new channel
- `"Update <Channel Name> - new content"` — when re-exporting an existing
  channel because you added more messages to it
- `"Refresh full export - <date>"` — for a full bulk re-export

---

## Quick checklist for adding a new channel

- [ ] Export only the new channel (HTML format)
- [ ] Copy its `chat_01` folder into `notes/`, rename to the real topic name
- [ ] Search its `messages.html` for `chats/` or `chat_0` and fix if found
- [ ] Add a new `<a class="entry...">` block to root `chats.html` with the
      correct `href` and name, in the position you want
- [ ] Test in browser (links + images load)
- [ ] `git add .` → `git commit -m "..."` → `git push`
