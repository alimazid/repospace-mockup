# Grove — Epics Review #1

> Comparison of EPICS.md user stories vs actual implementation in `grove-mvp`
> Date: 2026-02-18

**Legend:**
- ✅ Implemented
- ⚠️ Partially implemented
- ❌ Not implemented
- 🔮 Post-MVP (not in scope)

---

## Epic 1: Authentication & Onboarding

| ID | Story | Status | Notes |
|----|-------|--------|-------|
| 1.1 | Landing page with value explanation | ✅ | Login screen with headline, GitHub CTA |
| 1.2 | Sign in with GitHub OAuth | ✅ | Full OAuth flow via Supabase Edge Function |
| 1.3 | See permissions before authorizing | ✅ | GitHub consent screen shows requested permissions |
| 1.4 | Cancel authorization and return | ✅ | Cancel returns to login screen |
| 1.5 | Loading sequence after authorizing | ⚠️ | Shows loading state but not the 3-step animation from spec |
| 1.6 | Sign out from settings | ✅ | Ephemeral session + coolDownAsync for proper sign out |
| 1.7 | Access settings from menu | ✅ | Settings tab with user info, sign out, app version |
| 1.8 | Auto sign-in on return | ✅ | Session persisted via SecureStore, auto-redirect to repos |

**Epic 1: 7/8 ✅, 1 ⚠️**

---

## Epic 2: Repository Dashboard

| ID | Story | Status | Notes |
|----|-------|--------|-------|
| 2.1 | List of connected repositories | ✅ | Repo list with name, owner, description |
| 2.2 | Primary language per repo | ✅ | Language name + colored dot |
| 2.3 | Last updated timestamp | ✅ | Relative time via `timeAgo()` (e.g., "2h ago") |
| 2.4 | Recent activity badges | ❌ | No "5 today" style badges |
| 2.5 | Click repo to open file browser | ✅ | Navigates to `repo/[owner]/[name]` |
| 2.6 | Count of connected repos | ✅ | Header shows count |
| 2.7 | Connect a new repository | ❌ | No add/connect button (relies on GitHub App installation) |
| 2.8 | Disconnect a repository | ❌ | Not implemented |

**Epic 2: 5/8 ✅, 3 ❌**

---

## Epic 3: File Browser

| ID | Story | Status | Notes |
|----|-------|--------|-------|
| 3.1 | Files and folders in current directory | ✅ | List view with icon, name |
| 3.2 | Navigate into folders | ✅ | Click folder → shows contents, updates path |
| 3.3 | Breadcrumb navigation | ✅ | Breadcrumb trail, each segment clickable |
| 3.4 | File type icons | ✅ | Different icons for folders, .md, code, .json, etc. |
| 3.5 | Agent-modified file badges | ❌ | No "agent" badge on files |
| 3.6 | File sizes and timestamps | ⚠️ | Size shown in file viewer but not in file list |
| 3.7 | Count of folders and files | ❌ | No count header in file browser |
| 3.8 | Folders sorted before files | ✅ | Folders first, then files, alphabetical |

**Epic 3: 5/8 ✅, 1 ⚠️, 2 ❌**

---

## Epic 4: Branch Management

| ID | Story | Status | Notes |
|----|-------|--------|-------|
| 4.1 | See current branch | ✅ | Branch name in BranchSwitcher button |
| 4.2 | Dropdown with all branches | ✅ | Dropdown list of branches |
| 4.3 | Filter branches by name | ❌ | No search/filter in dropdown (rewritten as simple dropdown) |
| 4.4 | Default branch label | ❌ | No "default" label shown |
| 4.5 | Commits ahead count | ❌ | Not implemented |
| 4.6 | Switch branch, files update | ✅ | Branch switch reloads files and commits |
| 4.7 | Dropdown closes on outside click | ✅ | Closes on outside press |

**Epic 4: 4/7 ✅, 3 ❌**

---

## Epic 5: Commit History

| ID | Story | Status | Notes |
|----|-------|--------|-------|
| 5.1 | Files/Commits tab switch | ✅ | Tab bar with Files and Commits |
| 5.2 | Chronological commit list | ✅ | Newest first with message, author, timestamp |
| 5.3 | Commits grouped by date | ❌ | Flat list, no date group headers |
| 5.4 | Human vs bot commit distinction | ✅ | `isBot()` detection, different dot styles |
| 5.5 | Lines added/removed per commit | ✅ | +additions (green) / -deletions (red) |
| 5.6 | Copy commit SHA | ❌ | No SHA badge / copy action |
| 5.7 | Branch-specific commit history | ✅ | Switching branch updates commits |
| 5.8 | Click commit to see diff | ❌ | Not implemented (Could priority) |

**Epic 5: 5/8 ✅, 3 ❌**

---

## Epic 6: Document Viewer

| ID | Story | Status | Notes |
|----|-------|--------|-------|
| 6.1 | Open file to view contents | ✅ | File viewer at `file/[...path]` |
| 6.2 | Markdown rendered properly | ✅ | `react-native-markdown-display` with full dark theme |
| 6.3 | Toggle rendered/source view | ✅ | "Source" / "Preview" toggle button |
| 6.4 | File metadata (last edited, size) | ⚠️ | File size shown, but no last edited timestamp |
| 6.5 | Navigate back to file browser | ✅ | Back button returns to file listing |
| 6.6 | Filename in toolbar | ✅ | Filename displayed in header |
| 6.7 | Code files with syntax display | ⚠️ | Monospace display for code files, but no syntax highlighting |

**Epic 6: 5/7 ✅, 2 ⚠️**

---

## Epic 7: Document Editor

| ID | Story | Status | Notes |
|----|-------|--------|-------|
| 7.1 | Enter edit mode on document | ✅ | "Edit" button toggles to editor |
| 7.2 | Markdown toolbar | ✅ | Toolbar: Bold, Italic, H2, Link, Code, List |
| 7.3 | Toggle editor/preview while editing | ✅ | "Preview" / "Edit" toggle during editing |
| 7.4 | Custom commit message on save | ✅ | Commit message input with alert if empty |
| 7.5 | Cancel editing, discard changes | ✅ | Cancel with confirmation dialog if changes exist |
| 7.6 | Confirmation toast after commit | ⚠️ | Alert shown, but not a styled toast notification |
| 7.7 | Save bar only in edit mode | ✅ | Sticky bottom bar with commit input + save/cancel |

**Epic 7: 6/7 ✅, 1 ⚠️**

---

## Epic 8: Drag & Drop File Management

| ID | Story | Status | Notes |
|----|-------|--------|-------|
| 8.1 | Drag file onto folder to move | ✅ | LongPress + Pan gesture → adds to changeset |
| 8.2 | Visual feedback while dragging | ✅ | Dragged item fades, drag indicator shown |
| 8.3 | Drag to breadcrumb for parent move | ❌ | Breadcrumb drop targets not implemented |
| 8.4 | Changeset panel for pending moves | ✅ | ChangesetPanel component shows at bottom |
| 8.5 | From/to paths in changeset | ✅ | Shows old path → new path |
| 8.6 | Undo individual changes | ✅ | `undoMove()` per change |
| 8.7 | Discard all pending changes | ✅ | `discardAll()` button |
| 8.8 | Commit all changes at once | ✅ | Single commit via Git Trees API |
| 8.9 | Customizable commit message | ✅ | Auto-generated message, editable |
| 8.10 | Mobile long-press drag | ✅ | 300ms long press with haptic feedback |
| 8.11 | Expandable/collapsible panel | ⚠️ | Panel exists but expand/collapse not explicitly implemented |

**Epic 8: 9/11 ✅, 1 ⚠️, 1 ❌**

---

## Epic 9: Navigation & Layout

| ID | Story | Status | Notes |
|----|-------|--------|-------|
| 9.1 | Sidebar with repos for quick switching | ❌ | Mobile app uses tab navigation, no sidebar with repo list |
| 9.2 | Logo click returns to dashboard | ✅ | Tab navigation takes back to repos |
| 9.3 | Sidebar collapses on mobile (hamburger) | N/A | Native app uses tab bar instead of sidebar — different UX pattern |
| 9.4 | Sidebar overlay closes on outside tap | N/A | Same — tab navigation replaces sidebar concept |
| 9.5 | Mobile header with logo and avatar | ✅ | Header with app branding and user context |
| 9.6 | Metadata columns hide on mobile | N/A | Native app has mobile-first layout by default |
| 9.7 | Toast notifications for actions | ⚠️ | Alerts used instead of toast overlays |
| 9.8 | No horizontal scroll | ✅ | No horizontal overflow issues |

**Epic 9: 3/8 ✅, 1 ⚠️, 1 ❌, 3 N/A** (web sidebar stories don't apply to native app)

---

## Epic 10-12: Post-MVP

| Epic | Status |
|------|--------|
| 10. Search & Filtering | 🔮 Not in scope |
| 11. Activity Feed | 🔮 Not in scope |
| 12. Collaboration | 🔮 Not in scope |

---

## Summary

| Epic | Total | ✅ | ⚠️ | ❌ | N/A |
|------|-------|-----|-----|-----|-----|
| 1. Authentication | 8 | 7 | 1 | 0 | 0 |
| 2. Repository Dashboard | 8 | 5 | 0 | 3 | 0 |
| 3. File Browser | 8 | 5 | 1 | 2 | 0 |
| 4. Branch Management | 7 | 4 | 0 | 3 | 0 |
| 5. Commit History | 8 | 5 | 0 | 3 | 0 |
| 6. Document Viewer | 7 | 5 | 2 | 0 | 0 |
| 7. Document Editor | 7 | 6 | 1 | 0 | 0 |
| 8. Drag & Drop | 11 | 9 | 1 | 1 | 0 |
| 9. Navigation & Layout | 8 | 3 | 1 | 1 | 3 |
| **Totals (Epics 1-9)** | **72** | **49** | **7** | **13** | **3** |

### Coverage: **49 fully implemented** + **7 partial** = **56/69 applicable** stories (81%)

### Not Implemented (13 stories):
- 2.4 — Activity badges on repos
- 2.7 — Connect new repo button
- 2.8 — Disconnect repo
- 3.5 — Agent-modified file badges
- 3.7 — File/folder count header
- 4.3 — Branch search/filter
- 4.4 — Default branch label
- 4.5 — Commits ahead count
- 5.3 — Commits grouped by date
- 5.6 — Copy commit SHA
- 5.8 — Click commit for diff (Could)
- 8.3 — Drag to breadcrumb
- 9.1 — Sidebar with repo list

### Partially Implemented (7 stories):
- 1.5 — Loading sequence (basic, not 3-step animation)
- 3.6 — File sizes (in viewer, not in list)
- 6.4 — File metadata (size yes, timestamp no)
- 6.7 — Code display (monospace, no syntax highlighting)
- 7.6 — Commit confirmation (Alert, not styled toast)
- 8.11 — Changeset panel (exists, no expand/collapse toggle)
- 9.7 — Notifications (Alerts, not toast overlays)
