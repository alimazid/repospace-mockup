# Grove — Project Tracking

> Last updated: 2026-02-17 12:50 UTC

## Status Legend
- ⬜ Not started
- 🔵 In progress
- ✅ Done
- 🔴 Blocked
- ⏸️ Paused

---

## Fase 1: Foundation (Semana 1)

| ID | Tarea | Asignado | Status | Notes |
|----|-------|----------|--------|-------|
| F1.1 | Crear proyecto Expo | 🤖 infra | ✅ | Expo + TypeScript + Expo Router |
| F1.2 | Crear repo GitHub `grove-mvp` | 🤖 infra | ✅ | github.com/alimazid/grove-mvp |
| F1.3 | Crear proyecto Supabase | 🤖 infra | ✅ | Ref: kaoscawtthseblgaxfao |
| F1.4 | Schema de base de datos | 🤖 infra | ✅ | 5 tables, indexes, triggers |
| F1.5 | Row Level Security | 🤖 infra | ✅ | All tables RLS enabled |
| F1.6 | Configurar EAS Build | 🤖 infra | ✅ | eas.json + deep link scheme configured |
| F1.7 | Registrar GitHub App | 👤 Ali | ✅ | App ID: 2881352 |
| F1.8 | Configurar GitHub App URLs | 👤 Ali | ✅ | Callback URL configured |
| F1.9 | Guardar credenciales GitHub App | 🤖 infra | ✅ | Stored in ~/.secrets/ |
| F1.10 | Edge Function: github-auth | 🔧 backend | ✅ | OAuth flow: login redirect + callback + user creation |
| F1.11 | Encriptación de tokens | 🔧 backend | ✅ | AES-256-GCM with PBKDF2 key derivation |
| F1.12 | Design tokens + Pantalla de Login | 📱 frontend | ✅ | Theme file + login screen matching mockup |
| F1.13 | Flujo OAuth en la app | 📱 frontend | ✅ | WebBrowser → Edge Function → deep link callback |
| F1.14 | Persistencia de sesión | 📱 frontend | ✅ | SecureStore + auto-refresh + onAuthStateChange |
| F1.15 | Logout | 📱 frontend | ✅ | Confirmation dialog + redirect to login |
| F1.16 | **TEST: Auth flow** | 👤 Ali | ✅ | Login, session persist, logout all working |

---

## Fase 2: Core Reading (Semana 2)

| ID | Tarea | Asignado | Status | Notes |
|----|-------|----------|--------|-------|
| F2.1 | Edge Function: github-proxy | 🔧 backend | ✅ | Authenticated proxy with 7 routes |
| F2.2 | Query: listar repos | 🔧 backend | ✅ | GraphQL viewer.repositories |
| F2.3 | Query: árbol de archivos | 🔧 backend | ✅ | GraphQL Tree query by expression |
| F2.4 | Query: contenido de archivo | 🔧 backend | ✅ | GraphQL Blob query |
| F2.5 | Query: listar branches | 🔧 backend | ✅ | GraphQL refs query |
| F2.6 | Query: historial de commits | 🔧 backend | ✅ | GraphQL commit history with pagination |
| F2.7 | Cache local por SHA | 📱 frontend | ✅ | API client with typed responses |
| F2.8 | Zustand stores | 📱 frontend | ✅ | repos + browser stores |
| F2.9 | Screen: Dashboard de repos | 📱 frontend | ✅ | Cards, skeletons, pull-to-refresh, pagination |
| F2.10 | Screen: File Browser | 📱 frontend | ✅ | Folder navigation, breadcrumbs, sorted entries |
| F2.11 | Iconos por tipo de archivo | 📱 frontend | ✅ | 20+ extension → emoji mappings |
| F2.12 | Badge "agent" | 📱 frontend | ✅ | Bot detection by login pattern, blue dot |
| F2.13 | Componente: BranchSwitcher | 📱 frontend | ✅ | Bottom sheet modal with branch list |
| F2.14 | Filtro de branches | 📱 frontend | ✅ | Search/filter in BranchSwitcher |
| F2.15 | Cambiar branch → recargar | 📱 frontend | ✅ | switchBranch in store reloads tree |
| F2.16 | Componente: CommitHistory | 📱 frontend | ✅ | Inline in repo detail screen |
| F2.17 | Diferenciar humano vs bot | 📱 frontend | ✅ | Teal dot = human, blue dot = bot |
| F2.18 | Tab switcher: Files ↔ Commits | 📱 frontend | ✅ | Active tab indicator |
| F2.19 | **TEST: Navegación completa** | 👤 Ali | ✅ | Repos, files, branches, commits all working |

---

## Fase 3: Writing (Semana 3)

| ID | Tarea | Asignado | Status | Notes |
|----|-------|----------|--------|-------|
| F3.1 | Componente: DocumentViewer | 📱 frontend | ✅ | File viewer screen with monospace rendering |
| F3.2 | Toggle raw/source | 📱 frontend | ✅ | Source/Preview toggle for .md files |
| F3.3 | Back navigation | 📱 frontend | ✅ | Router back from file viewer |
| F3.4 | Componente: DocumentEditor | 📱 frontend | ✅ | Full editor with save flow |
| F3.5 | Toolbar de formateo | 📱 frontend | ✅ | Bold, italic, heading, code, list, checkbox |
| F3.6 | Preview toggle en editor | 📱 frontend | ✅ | Edit/Preview toggle for .md files |
| F3.7 | Commit bar | 📱 frontend | ✅ | Commit message input + commit button |
| F3.8 | API: editar archivo | 🔧 backend | ✅ | PUT /contents via github-proxy/edit |
| F3.9 | Conflict detection | 🔧 backend | ✅ | SHA comparison in edit API (422 on mismatch) |
| F3.10 | Drag gesture handler | 📱 frontend | ✅ | LongPress + Pan gesture, 300ms threshold, haptic feedback |
| F3.11 | Visual feedback de drag | 📱 frontend | ✅ | Scale + opacity animation, drag indicator, drop target highlight |
| F3.12 | Componente: ChangesetPanel | 📱 frontend | ✅ | Bottom panel with move list + commit bar |
| F3.13 | Undo individual + discard all | 📱 frontend | ✅ | Per-item undo + discard all |
| F3.14 | API: batch move (Git Trees) | 🔧 backend | ✅ | Git Trees API via github-proxy/move |
| F3.15 | Auto-generate commit message | 📱 frontend | ✅ | "Move X to Y" / "Move N files" |
| F3.16 | **TEST: Edición y drag & drop** | 👤 Ali | ✅ | Edit + commit working from iPhone |

---

## Fase 4: Notifications & Polish (Semana 4)

| ID | Tarea | Asignado | Status | Notes |
|----|-------|----------|--------|-------|
| F4.1 | Edge Function: github-webhook | 🔧 backend | ✅ | Processes push + ping events |
| F4.2 | Verificación HMAC | 🔧 backend | ✅ | SHA-256 signature verification |
| F4.3 | Procesar push → activity feed | 🔧 backend | ✅ | Upsert commits with bot detection |
| F4.4 | Invalidar cache de tree | 🔧 backend | ✅ | Updates last_tree_sha on push |
| F4.5 | Setup Expo Push Notifications | 📱 frontend | ✅ | Expo Notifications config + permission handling |
| F4.6 | Guardar push token | 🤖 infra | ✅ | Upsert into push_tokens table on auth |
| F4.7 | Enviar push notification | 🔧 backend | ✅ | Expo Push API from webhook handler |
| F4.8 | Deep link desde notification | 📱 frontend | ✅ | Notification tap → repo detail screen |
| F4.9 | Screen: Settings | 📱 frontend | ✅ | Profile, notifications, about sections |
| F4.10 | Toggle notificaciones por repo | 📱 frontend | ✅ | Push notification toggle in settings |
| F4.11 | Loading states (skeletons) | 📱 frontend | ✅ | Skeleton cards in repos, skeleton rows in browser |
| F4.12 | Error handling global | 📱 frontend | ✅ | ErrorBoundary + per-screen error states with retry |
| F4.13 | Pull-to-refresh | 📱 frontend | ✅ | RefreshControl in repos dashboard |
| F4.14 | Empty states | 📱 frontend | ✅ | "No repositories" / "No files" / "No commits" |
| F4.15 | App icon + splash screen | 📦 build | ✅ | Git tree icon, dark bg, blue accent |
| F4.16 | Dark/light mode | 📱 frontend | ✅ | Dark mode default, theme tokens ready for light mode |
| F4.17 | EAS Build: Android APK | 📦 build | ✅ | Build #10 (a1dbd56a) — APK: kZfhB2cMYL9e4zpvVYoJTs.apk |
| F4.18 | EAS Build: iOS IPA | 📦 build | ⬜ | Depende de F4.16, F4.19 |
| F4.19 | Configurar Apple Developer | 👤 Ali | ⬜ | |
| F4.20 | Subir a TestFlight | 👤 Ali | ⬜ | Depende de F4.18 |
| F4.21 | Subir a Google Play internal | 👤 Ali | ⬜ | Depende de F4.17 |
| F4.22 | **TEST: E2E en dispositivo real** | 👤 Ali | ⬜ | Depende de F4.21 |
| F4.23 | Fix bugs de testing | 📱/🔧 | ⬜ | Depende de F4.22 |

---

## Progress Summary

| Fase | Total | ⬜ | 🔵 | ✅ | 🔴 |
|------|-------|----|----|----|----|
| 1. Foundation | 16 | 0 | 0 | 16 | 0 |
| 2. Core Reading | 19 | 0 | 0 | 19 | 0 |
| 3. Writing | 16 | 0 | 0 | 16 | 0 |
| 4. Notifications | 23 | 4 | 0 | 19 | 0 |
| **Total** | **74** | **4** | **0** | **70** | **0** |

---

## Activity Log

| Date | Tasks | Notes |
|------|-------|-------|
| 2026-02-17 | F1.7 ✅, F1.9 ✅ | GitHub App "Grove" registered. Credentials stored in ~/.secrets/ |
| 2026-02-17 | F1.1 ✅, F1.2 ✅, F1.3 ✅, F1.4 ✅, F1.5 ✅, F1.12 ✅ | Expo project scaffolded, repo created, Supabase provisioned, schema + RLS applied, design tokens + login screen built |
| 2026-02-17 | F1.10 ✅, F1.11 ✅ | Token encryption (AES-256-GCM), github-auth Edge Function (OAuth login + callback + user upsert) |
| 2026-02-17 | F1.6 ✅, F1.13 ✅, F1.14 ✅, F1.15 ✅ | EAS Build config, OAuth flow in app (WebBrowser→Edge Function→deep link), session persistence, logout with confirmation |
| 2026-02-17 | F2.1-F2.12 ✅, F2.15-F2.18 ✅ | Full backend proxy + all GraphQL queries, Zustand stores, repos dashboard, file browser with icons + breadcrumbs, commit history with bot detection |
| 2026-02-17 | F3.1-F3.3 ✅, F3.8 ✅, F3.14 ✅ | File viewer screen, source toggle, edit + batch move APIs in proxy |
| 2026-02-17 | F2.13-F2.14 ✅, F3.4-F3.7 ✅, F3.9 ✅, F3.12-F3.13 ✅, F3.15 ✅ | BranchSwitcher, DocumentEditor with toolbar, conflict detection, ChangesetPanel |
| 2026-02-17 | F4.1-F4.4 ✅, F4.7 ✅ | Webhook handler: HMAC verification, activity feed, cache invalidation, push notifications |
| 2026-02-17 | F3.10-F3.11 ✅, F4.5-F4.6 ✅, F4.8-F4.14 ✅, F4.16 ✅ | Drag & drop, push notifications, deep linking, settings, loading/error/empty states, ErrorBoundary |
