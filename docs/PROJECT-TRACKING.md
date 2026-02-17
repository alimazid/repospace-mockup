# Grove — Project Tracking

> Last updated: 2026-02-17 02:15 UTC

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
| F1.6 | Configurar EAS Build | 🤖 infra | ⬜ | Depende de F1.1 |
| F1.7 | Registrar GitHub App | 👤 Ali | ✅ | App ID: 2881352 |
| F1.8 | Configurar GitHub App URLs | 👤 Ali | ⬜ | Depende de F1.7, F1.10 |
| F1.9 | Guardar credenciales GitHub App | 🤖 infra | ✅ | Stored in ~/.secrets/ |
| F1.10 | Edge Function: github-auth | 🔧 backend | ⬜ | Depende de F1.4, F1.9 |
| F1.11 | Encriptación de tokens | 🔧 backend | ⬜ | Depende de F1.4 |
| F1.12 | Design tokens + Pantalla de Login | 📱 frontend | ✅ | Theme file + login screen matching mockup |
| F1.13 | Flujo OAuth en la app | 📱 frontend | ⬜ | Depende de F1.10, F1.12 |
| F1.14 | Persistencia de sesión | 📱 frontend | ⬜ | Depende de F1.13 |
| F1.15 | Logout | 📱 frontend | ⬜ | Depende de F1.14 |
| F1.16 | **TEST: Auth flow** | 👤 Ali | ⬜ | Depende de F1.15 |

---

## Fase 2: Core Reading (Semana 2)

| ID | Tarea | Asignado | Status | Notes |
|----|-------|----------|--------|-------|
| F2.1 | Edge Function: github-proxy | 🔧 backend | ⬜ | Depende de F1.10, F1.11 |
| F2.2 | Query: listar repos | 🔧 backend | ⬜ | Depende de F2.1 |
| F2.3 | Query: árbol de archivos | 🔧 backend | ⬜ | Depende de F2.1 |
| F2.4 | Query: contenido de archivo | 🔧 backend | ⬜ | Depende de F2.1 |
| F2.5 | Query: listar branches | 🔧 backend | ⬜ | Depende de F2.1 |
| F2.6 | Query: historial de commits | 🔧 backend | ⬜ | Depende de F2.1 |
| F2.7 | Cache local por SHA | 📱 frontend | ⬜ | Depende de F1.1 |
| F2.8 | Zustand stores | 📱 frontend | ⬜ | Depende de F2.7 |
| F2.9 | Screen: Dashboard de repos | 📱 frontend | ⬜ | Depende de F2.2, F2.8 |
| F2.10 | Screen: File Browser | 📱 frontend | ⬜ | Depende de F2.3, F2.8 |
| F2.11 | Iconos por tipo de archivo | 📱 frontend | ⬜ | Depende de F2.10 |
| F2.12 | Badge "agent" | 📱 frontend | ⬜ | Depende de F2.10, F2.6 |
| F2.13 | Componente: BranchSwitcher | 📱 frontend | ⬜ | Depende de F2.5, F2.8 |
| F2.14 | Filtro de branches | 📱 frontend | ⬜ | Depende de F2.13 |
| F2.15 | Cambiar branch → recargar | 📱 frontend | ⬜ | Depende de F2.13 |
| F2.16 | Componente: CommitHistory | 📱 frontend | ⬜ | Depende de F2.6, F2.8 |
| F2.17 | Diferenciar humano vs bot | 📱 frontend | ⬜ | Depende de F2.16 |
| F2.18 | Tab switcher: Files ↔ Commits | 📱 frontend | ⬜ | Depende de F2.10, F2.16 |
| F2.19 | **TEST: Navegación completa** | 👤 Ali | ⬜ | Depende de F2.18 |

---

## Fase 3: Writing (Semana 3)

| ID | Tarea | Asignado | Status | Notes |
|----|-------|----------|--------|-------|
| F3.1 | Componente: DocumentViewer | 📱 frontend | ⬜ | Depende de F2.4, F2.10 |
| F3.2 | Toggle raw/source | 📱 frontend | ⬜ | Depende de F3.1 |
| F3.3 | Back navigation | 📱 frontend | ⬜ | Depende de F3.1 |
| F3.4 | Componente: DocumentEditor | 📱 frontend | ⬜ | Depende de F3.1 |
| F3.5 | Toolbar de formateo | 📱 frontend | ⬜ | Depende de F3.4 |
| F3.6 | Preview toggle en editor | 📱 frontend | ⬜ | Depende de F3.4, F3.1 |
| F3.7 | Commit bar | 📱 frontend | ⬜ | Depende de F3.4 |
| F3.8 | API: editar archivo | 🔧 backend | ⬜ | Depende de F2.1, F2.4 |
| F3.9 | Conflict detection | 🔧 backend | ⬜ | Depende de F3.8 |
| F3.10 | Drag gesture handler | 📱 frontend | ⬜ | Depende de F2.10 |
| F3.11 | Visual feedback de drag | 📱 frontend | ⬜ | Depende de F3.10 |
| F3.12 | Componente: ChangesetPanel | 📱 frontend | ⬜ | Depende de F3.10 |
| F3.13 | Undo individual + discard all | 📱 frontend | ⬜ | Depende de F3.12 |
| F3.14 | API: batch move (Git Trees) | 🔧 backend | ⬜ | Depende de F2.1, F2.3 |
| F3.15 | Auto-generate commit message | 📱 frontend | ⬜ | Depende de F3.12 |
| F3.16 | **TEST: Edición y drag & drop** | 👤 Ali | ⬜ | Depende de F3.15 |

---

## Fase 4: Notifications & Polish (Semana 4)

| ID | Tarea | Asignado | Status | Notes |
|----|-------|----------|--------|-------|
| F4.1 | Edge Function: github-webhook | 🔧 backend | ⬜ | Depende de F1.4 |
| F4.2 | Verificación HMAC | 🔧 backend | ⬜ | Depende de F4.1 |
| F4.3 | Procesar push → activity feed | 🔧 backend | ⬜ | Depende de F4.1 |
| F4.4 | Invalidar cache de tree | 🔧 backend | ⬜ | Depende de F4.1, F1.4 |
| F4.5 | Setup Expo Push Notifications | 📱 frontend | ⬜ | Depende de F1.1, F1.4 |
| F4.6 | Guardar push token | 🤖 infra | ⬜ | Depende de F1.4, F4.5 |
| F4.7 | Enviar push notification | 🔧 backend | ⬜ | Depende de F4.1, F4.6 |
| F4.8 | Deep link desde notification | 📱 frontend | ⬜ | Depende de F4.5, F2.10 |
| F4.9 | Screen: Settings | 📱 frontend | ⬜ | Depende de F1.15 |
| F4.10 | Toggle notificaciones por repo | 📱 frontend | ⬜ | Depende de F4.9, F4.7 |
| F4.11 | Loading states (skeletons) | 📱 frontend | ⬜ | Depende de F2.9, F2.10, F2.16, F3.1 |
| F4.12 | Error handling global | 📱 frontend | ⬜ | Depende de F2.8 |
| F4.13 | Pull-to-refresh | 📱 frontend | ⬜ | Depende de F2.9, F2.10, F2.16 |
| F4.14 | Empty states | 📱 frontend | ⬜ | Depende de F2.9, F2.10, F2.16 |
| F4.15 | App icon + splash screen | 📦 build | ⬜ | Depende de F1.1 |
| F4.16 | Dark/light mode | 📱 frontend | ⬜ | Depende de F4.9 |
| F4.17 | EAS Build: Android APK | 📦 build | ⬜ | Depende de F4.16 |
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
| 1. Foundation | 16 | 8 | 0 | 8 | 0 |
| 2. Core Reading | 19 | 19 | 0 | 0 | 0 |
| 3. Writing | 16 | 16 | 0 | 0 | 0 |
| 4. Notifications | 23 | 23 | 0 | 0 | 0 |
| **Total** | **74** | **66** | **0** | **8** | **0** |

---

## Activity Log

| Date | Tasks | Notes |
|------|-------|-------|
| 2026-02-17 | F1.7 ✅, F1.9 ✅ | GitHub App "Grove" registered. Credentials stored in ~/.secrets/ |
| 2026-02-17 | F1.1 ✅, F1.2 ✅, F1.3 ✅, F1.4 ✅, F1.5 ✅, F1.12 ✅ | Expo project scaffolded, repo created, Supabase provisioned, schema + RLS applied, design tokens + login screen built |
