# RepoSpace — Epics & User Stories

> Derived from the interactive mockup at https://repospace-mockup.pages.dev

---

## Epic 1: Authentication & Onboarding

**Goal:** Users can sign in with GitHub and grant RepoSpace the necessary permissions to access their repositories.

| ID | Story | Acceptance Criteria | Priority |
|----|-------|-------------------|----------|
| 1.1 | As a new user, I can see a landing page that explains what RepoSpace does so I understand the value before signing in | Landing with headline, 3 feature highlights, CTA button | Must |
| 1.2 | As a new user, I can sign in with my GitHub account using OAuth | "Sign in with GitHub" initiates OAuth flow, redirects to GitHub | Must |
| 1.3 | As a user, I can see what permissions RepoSpace requests before authorizing | GitHub OAuth consent screen shows: read repos, write contents, read profile | Must |
| 1.4 | As a user, I can cancel authorization and return to the landing page | Cancel on GitHub consent → return to landing, no data stored | Must |
| 1.5 | As a user, I see a loading sequence after authorizing so I know the app is working | 3-step animation: Authenticating → Loading repositories → Preparing workspace | Should |
| 1.6 | As an authenticated user, I can sign out from the sidebar menu | Sign out clears session, returns to landing page | Must |
| 1.7 | As an authenticated user, I can access settings from the sidebar menu | Settings option in user menu (placeholder for future) | Could |
| 1.8 | As a returning user, I'm automatically signed in if my session is still valid | Token stored in localStorage/cookie, auto-redirect to repos | Should |

---

## Epic 2: Repository Dashboard

**Goal:** Users can see all their connected GitHub repositories at a glance and select one to browse.

| ID | Story | Acceptance Criteria | Priority |
|----|-------|-------------------|----------|
| 2.1 | As a user, I can see a list of all my connected repositories | Repo list showing name, owner, description | Must |
| 2.2 | As a user, I can see the primary language of each repository | Language name + colored dot (e.g., TypeScript blue, Python blue) | Should |
| 2.3 | As a user, I can see when each repository was last updated | Relative timestamp (e.g., "2h ago", "5d ago") | Should |
| 2.4 | As a user, I can see recent activity badges on active repos | Badge like "5 today" for repos with recent commits | Could |
| 2.5 | As a user, I can click a repository to open its file browser | Click → navigate to file browser for that repo | Must |
| 2.6 | As a user, I can see a count of how many repos are connected | "3 repositories connected" header text | Should |
| 2.7 | As a user, I can connect a new repository from the dashboard | Button to add/connect new repos from GitHub | Should |
| 2.8 | As a user, I can disconnect a repository I no longer want | Remove repo from dashboard, revoke access | Could |

---

## Epic 3: File Browser

**Goal:** Users can navigate repository files and folders with an intuitive filesystem-like interface.

| ID | Story | Acceptance Criteria | Priority |
|----|-------|-------------------|----------|
| 3.1 | As a user, I can see files and folders in the current directory | List view with icon, name, size, and last updated | Must |
| 3.2 | As a user, I can navigate into folders by clicking them | Click folder → show its contents, update breadcrumb | Must |
| 3.3 | As a user, I can navigate back using breadcrumbs | Breadcrumb trail: repo > folder > subfolder, each clickable | Must |
| 3.4 | As a user, I can see file type icons (folder, markdown, code, JSON, etc.) | Different icons for .md, .ts/.js/.py/.html, .json, folders, other | Should |
| 3.5 | As a user, I can see which files were recently modified by an agent | "agent" badge on files with recent bot commits | Should |
| 3.6 | As a user, I can see file sizes and last modified timestamps | Size column (e.g., "12 KB") and time column (e.g., "2h ago") | Should |
| 3.7 | As a user, I can see a count of folders and files in the current directory | "3 folders, 2 files" in the file header | Should |
| 3.8 | As a user, folders are sorted before files in the listing | Folders first (alphabetical), then files (alphabetical) | Must |

---

## Epic 4: Branch Management

**Goal:** Users can view and switch between repository branches.

| ID | Story | Acceptance Criteria | Priority |
|----|-------|-------------------|----------|
| 4.1 | As a user, I can see which branch I'm currently viewing | Branch name displayed in branch switcher button | Must |
| 4.2 | As a user, I can open a dropdown to see all available branches | Dropdown with list of all branches | Must |
| 4.3 | As a user, I can filter branches by name in the dropdown | Search/filter input at top of dropdown | Should |
| 4.4 | As a user, I can see which branch is the default | "default" label on the default branch | Should |
| 4.5 | As a user, I can see how many commits ahead each branch is | "3 ahead" metadata on non-default branches | Could |
| 4.6 | As a user, I can switch to a different branch and see its files/commits | Select branch → file browser and commit history update | Must |
| 4.7 | As a user, the branch dropdown closes when I click outside | Click outside → dropdown closes | Must |

---

## Epic 5: Commit History

**Goal:** Users can view the commit history of a repository on any branch, with clear distinction between human and agent commits.

| ID | Story | Acceptance Criteria | Priority |
|----|-------|-------------------|----------|
| 5.1 | As a user, I can switch between "Files" and "Commits" tabs | Tab bar with Files and Commits, active state indicator | Must |
| 5.2 | As a user, I can see a chronological list of commits | Commits listed newest first with message, author, timestamp | Must |
| 5.3 | As a user, I can see commits grouped by date | Group headers: "Today", "Yesterday", "Feb 14", etc. | Should |
| 5.4 | As a user, I can distinguish human commits from agent/bot commits | Different avatar style: human = letter initial, bot = gear icon with blue accent | Must |
| 5.5 | As a user, I can see lines added/removed per commit | +additions (green) / -deletions (red) stats | Should |
| 5.6 | As a user, I can copy a commit SHA to clipboard | Click SHA badge → copies to clipboard, shows toast | Should |
| 5.7 | As a user, I can see commit history specific to the selected branch | Switching branch updates the commit list | Must |
| 5.8 | As a user, I can click a commit to see its details/diff | Click commit row → show changed files and diffs | Could |

---

## Epic 6: Document Viewer

**Goal:** Users can read markdown documents with beautiful rendering.

| ID | Story | Acceptance Criteria | Priority |
|----|-------|-------------------|----------|
| 6.1 | As a user, I can open a file to view its contents | Click file → document viewer with rendered content | Must |
| 6.2 | As a user, I can see markdown rendered with proper formatting | Headers, bold, italic, code, lists, blockquotes, checklists | Must |
| 6.3 | As a user, I can toggle between rendered and raw/source view | "Source" button shows raw markdown in code block | Should |
| 6.4 | As a user, I can see file metadata (last edited, file size) | Meta bar above content with timestamp and size | Should |
| 6.5 | As a user, I can navigate back to the file browser from a document | "← Back" button returns to file listing | Must |
| 6.6 | As a user, I can see the filename in the document toolbar | Filename displayed next to back button | Should |
| 6.7 | As a user, I can view code files with syntax-appropriate display | Code files shown in monospace with code block styling | Could |

---

## Epic 7: Document Editor

**Goal:** Users can edit files inline and commit changes directly from the browser.

| ID | Story | Acceptance Criteria | Priority |
|----|-------|-------------------|----------|
| 7.1 | As a user, I can enter edit mode on any viewable document | "Edit" button toggles to markdown editor | Must |
| 7.2 | As a user, I have a markdown toolbar for common formatting | Toolbar: Bold, Italic, H2, Link, Code, List | Should |
| 7.3 | As a user, I can toggle between editor and preview while editing | "Preview" button (replaces "Edit") shows rendered markdown | Should |
| 7.4 | As a user, I can write a custom commit message when saving | Commit message input in save bar, with sensible default | Must |
| 7.5 | As a user, I can cancel editing and discard my changes | "Cancel" button reverts to original content | Must |
| 7.6 | As a user, I see a confirmation toast after committing changes | Toast: "Committed: {message}" | Should |
| 7.7 | As a user, the save bar appears only when in edit mode | Sticky bottom bar with commit input + save/cancel buttons | Must |

---

## Epic 8: Drag & Drop File Management

**Goal:** Users can reorganize files and folders by dragging them, building up a changeset to commit.

| ID | Story | Acceptance Criteria | Priority |
|----|-------|-------------------|----------|
| 8.1 | As a user, I can drag a file or folder onto another folder to move it | Drag file → drop on folder → adds move to changeset | Must |
| 8.2 | As a user, I see visual feedback when dragging (ghost element, highlighting) | Dragged item fades, target folder highlights with blue outline | Must |
| 8.3 | As a user, I can drag items to breadcrumb segments to move to parent folders | Drop on breadcrumb → move item to that directory level | Should |
| 8.4 | As a user, I see a changeset panel when I have pending moves | Bottom panel appears with count of pending changes | Must |
| 8.5 | As a user, I can see each pending change with from/to paths | Each change shows: action type (move), old path (strikethrough), new path (green) | Must |
| 8.6 | As a user, I can undo individual changes from the changeset | ✕ button per change removes it from the changeset | Must |
| 8.7 | As a user, I can discard all pending changes at once | "Discard all" button clears the entire changeset | Must |
| 8.8 | As a user, I can commit all pending changes with a single commit | "Commit changes" button with auto-generated commit message | Must |
| 8.9 | As a user, I can customize the commit message for the changeset | Editable commit message input, auto-populated (e.g., "Move 3 files") | Should |
| 8.10 | As a user, drag and drop works on mobile via long-press | Hold 300ms to initiate drag, haptic feedback, same drop targets | Must |
| 8.11 | As a user, the changeset panel is expandable/collapsible | Click header to toggle item list visibility | Should |

---

## Epic 9: Navigation & Layout

**Goal:** The app provides intuitive navigation across all screens, responsive on desktop and mobile.

| ID | Story | Acceptance Criteria | Priority |
|----|-------|-------------------|----------|
| 9.1 | As a user, I can see all my repos in the sidebar for quick switching | Sidebar lists repos with colored dots, active state on current | Must |
| 9.2 | As a user, I can click the logo to return to the repository dashboard | Logo click → repos screen, clears current repo selection | Must |
| 9.3 | As a user, the sidebar collapses on mobile behind a hamburger menu | ☰ button toggles sidebar overlay on screens < 768px | Must |
| 9.4 | As a user, the sidebar overlay closes when I tap outside it | Overlay click → close sidebar | Must |
| 9.5 | As a user, I see a mobile header with logo and avatar on small screens | Sticky header with hamburger, logo, and user avatar | Must |
| 9.6 | As a user, file metadata columns hide on mobile for a cleaner view | Size column hidden on mobile, rows use less padding | Should |
| 9.7 | As a user, toast notifications confirm my actions | Toast appears at bottom center, auto-dismisses after 2.5s | Should |
| 9.8 | As a user, the page doesn't scroll horizontally on any screen size | No horizontal overflow on mobile or desktop | Must |

---

## Epic 10: Search & Filtering (Post-MVP)

**Goal:** Users can search across files and filter content within repositories.

| ID | Story | Acceptance Criteria | Priority |
|----|-------|-------------------|----------|
| 10.1 | As a user, I can search for files by name within a repository | Search input in file header, filters as you type | Should |
| 10.2 | As a user, I can search full-text across all documents | Global search returns matching files with snippet previews | Could |
| 10.3 | As a user, I can filter files by type (.md only, code only, etc.) | Filter toggles or dropdown in file header | Could |
| 10.4 | As a user, I can filter commits by author (human vs agent) | Toggle/filter in commits view | Could |
| 10.5 | As a user, I can sort files by name, date modified, or size | Sort options in file header | Could |

---

## Epic 11: Activity Feed (Post-MVP)

**Goal:** Users get a real-time feed of changes happening across their repositories, especially from AI agents.

| ID | Story | Acceptance Criteria | Priority |
|----|-------|-------------------|----------|
| 11.1 | As a user, I can see a cross-repo activity feed on the dashboard | Timeline of recent changes across all connected repos | Should |
| 11.2 | As a user, I receive push notifications when an agent modifies important files | Configurable notifications via browser push / mobile push | Could |
| 11.3 | As a user, I can see inline diffs in the activity feed | Expand a change to see what lines were added/removed | Could |
| 11.4 | As a user, I can mark changes as "reviewed" | Checkbox/button to mark agent changes as seen | Could |

---

## Epic 12: Collaboration (Post-MVP)

**Goal:** Multiple users can work on the same repository with awareness of each other.

| ID | Story | Acceptance Criteria | Priority |
|----|-------|-------------------|----------|
| 12.1 | As a user, I can invite team members to a shared workspace | Invite via email or GitHub username | Could |
| 12.2 | As a user, I can see who else is viewing the same file | Presence indicators (avatars) on open documents | Could |
| 12.3 | As a user, I can leave comments on specific lines of a document | Inline commenting with threads | Could |
| 12.4 | As a user, I can approve or reject agent-proposed changes | Review flow: agent proposes → human approves/rejects | Could |

---

## Summary

| Epic | Stories | Must | Should | Could |
|------|---------|------|--------|-------|
| 1. Authentication | 8 | 4 | 3 | 1 |
| 2. Repository Dashboard | 8 | 2 | 4 | 2 |
| 3. File Browser | 8 | 3 | 4 | 1 |
| 4. Branch Management | 7 | 3 | 2 | 2 |
| 5. Commit History | 8 | 3 | 3 | 2 |
| 6. Document Viewer | 7 | 2 | 3 | 2 |
| 7. Document Editor | 7 | 3 | 3 | 1 |
| 8. Drag & Drop | 11 | 5 | 3 | 0 |
| 9. Navigation & Layout | 8 | 5 | 2 | 1 |
| 10. Search (Post-MVP) | 5 | 0 | 2 | 3 |
| 11. Activity Feed (Post-MVP) | 4 | 0 | 1 | 3 |
| 12. Collaboration (Post-MVP) | 4 | 0 | 0 | 4 |
| **Total** | **85** | **30** | **30** | **22** |

### MVP Scope (Must + Should): 60 stories across Epics 1-9
### Post-MVP: 13 stories in Epics 10-12 + Could items from MVP epics
