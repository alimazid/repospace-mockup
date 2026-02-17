# RepoSpace — MVP Scope & Implementation Plan

> Fecha: 2026-02-17
> Objetivo: App mobile funcional que permita autenticarse con GitHub, navegar repos, ver/editar archivos, ver commits, y mover archivos con drag & drop.

---

## 1. Alcance del MVP

### Incluido

| Feature | Descripción |
|---------|------------|
| **Auth con GitHub** | Login via GitHub App OAuth, logout, sesión persistente |
| **Dashboard de repos** | Listar repos conectados del usuario con metadata |
| **File browser** | Navegar carpetas/archivos, breadcrumbs, iconos por tipo |
| **Branch switcher** | Ver y cambiar entre branches |
| **Commit history** | Lista de commits por branch, distinguir humano vs bot |
| **Document viewer** | Renderizar Markdown, toggle raw/source |
| **Document editor** | Editar Markdown inline, commit con mensaje |
| **Drag & drop** | Mover archivos/carpetas con changeset y commit batch |
| **Push notifications** | Notificar cuando hay push en repos conectados |
| **Responsive** | Mobile-first, funcional en web |

### Excluido del MVP

- Búsqueda full-text
- Diff viewer inline
- Comentarios / anotaciones
- Colaboración multi-usuario
- Soporte GitLab / Bitbucket
- Offline editing
- Merge / Pull Requests
- Activity feed cross-repo (solo commit history por repo)

---

## 2. Stack Técnico

| Componente | Tecnología |
|-----------|-----------|
| Mobile app | React Native + Expo (SDK 52) |
| Navegación | Expo Router (file-based) |
| Estado | Zustand |
| UI | Tamagui o NativeWind |
| Backend | Supabase (Auth, Postgres, Edge Functions, Realtime) |
| API GitHub | GraphQL v4 + REST (writes) |
| Push | Expo Notifications (APNs/FCM) |
| CI/CD | EAS Build + EAS Submit |

---

## 3. Estructura del Proyecto

```
repospace/
├── app/                    # Expo Router screens
│   ├── (auth)/             # Login, callback
│   ├── (tabs)/             # Main tab navigator
│   │   ├── repos/          # Repository list & detail
│   │   ├── activity/       # Activity feed (v2)
│   │   └── settings/       # User settings, logout
│   └── _layout.tsx         # Root layout
├── components/             # Shared components
│   ├── FileBrowser/
│   ├── DocumentViewer/
│   ├── DocumentEditor/
│   ├── CommitHistory/
│   ├── BranchSwitcher/
│   └── ChangesetPanel/
├── lib/                    # Core logic
│   ├── github/             # GitHub API client (GraphQL + REST)
│   ├── supabase/           # Supabase client
│   ├── cache/              # Local cache (SHA-based)
│   └── notifications/      # Push notification handlers
├── store/                  # Zustand stores
├── supabase/
│   ├── migrations/         # SQL migrations
│   └── functions/          # Edge Functions
│       ├── github-proxy/   # Proxy requests to GitHub API
│       ├── github-webhook/ # Process incoming webhooks
│       └── github-auth/    # OAuth callback handler
└── assets/
```

---

## 4. Plan de Implementación

### Fase 1: Foundation (Semana 1)
> Infraestructura, auth, y conexión con GitHub.

#### 1.1 — Setup del proyecto
| # | Tarea | Asignado | Estimado |
|---|-------|----------|----------|
| 1.1.1 | Crear proyecto Expo con Expo Router, TypeScript, ESLint | Agente | 2h |
| 1.1.2 | Configurar Supabase: nuevo proyecto "repospace" | Agente | 1h |
| 1.1.3 | Crear schema de base de datos (migraciones SQL) | Agente | 2h |
| 1.1.4 | Configurar Row Level Security (RLS) policies | Agente | 1h |
| 1.1.5 | Crear repo GitHub `alimazid/repospace` (privado) | Agente | 15min |
| 1.1.6 | Configurar EAS Build (dev client) | Agente | 1h |

#### 1.2 — GitHub App Registration
| # | Tarea | Asignado | Estimado |
|---|-------|----------|----------|
| 1.2.1 | Registrar GitHub App en github.com/settings/apps | **Ali** | 15min |
| 1.2.2 | Configurar permisos: contents (r/w), metadata (r) | **Ali** | 10min |
| 1.2.3 | Configurar callback URL → Supabase auth endpoint | **Ali** | 5min |
| 1.2.4 | Configurar webhook URL → Edge Function | **Ali** | 5min |
| 1.2.5 | Guardar App ID, Client ID, Client Secret, Private Key | **Ali** → Agente guarda en Supabase secrets |
| 1.2.6 | Documentar credenciales en tokens.env | Agente | 5min |

#### 1.3 — Autenticación
| # | Tarea | Asignado | Estimado |
|---|-------|----------|----------|
| 1.3.1 | Edge Function: `github-auth` (OAuth callback, exchange code → token) | Agente | 3h |
| 1.3.2 | Almacenar GitHub token encriptado en Supabase (Vault) | Agente | 1h |
| 1.3.3 | Pantalla de Login en la app (UI + botón "Sign in with GitHub") | Agente | 2h |
| 1.3.4 | Flujo OAuth: deep link callback → crear sesión Supabase | Agente | 3h |
| 1.3.5 | Persistencia de sesión (auto-login en reopen) | Agente | 1h |
| 1.3.6 | Logout (limpiar sesión, volver a login) | Agente | 30min |
| 1.3.7 | **Prueba funcional**: Login → ver repos → logout → login de nuevo | **Ali** | 30min |

---

### Fase 2: Core Reading (Semana 2)
> Navegar repos, archivos, branches, y commits.

#### 2.1 — GitHub API Client
| # | Tarea | Asignado | Estimado |
|---|-------|----------|----------|
| 2.1.1 | Edge Function: `github-proxy` (proxy autenticado a GitHub API) | Agente | 3h |
| 2.1.2 | Query GraphQL: listar repos del usuario | Agente | 1h |
| 2.1.3 | Query GraphQL: árbol de archivos (tree) por branch | Agente | 2h |
| 2.1.4 | Query GraphQL: contenido de archivo | Agente | 1h |
| 2.1.5 | Query GraphQL: listar branches | Agente | 1h |
| 2.1.6 | Query GraphQL: historial de commits por branch | Agente | 2h |
| 2.1.7 | Cache local por SHA (AsyncStorage + memoria) | Agente | 2h |

#### 2.2 — Dashboard de Repos
| # | Tarea | Asignado | Estimado |
|---|-------|----------|----------|
| 2.2.1 | Screen: lista de repos (nombre, descripción, lenguaje, última actividad) | Agente | 3h |
| 2.2.2 | Pull-to-refresh para recargar repos | Agente | 30min |
| 2.2.3 | Navegación: tap repo → file browser | Agente | 30min |

#### 2.3 — File Browser
| # | Tarea | Asignado | Estimado |
|---|-------|----------|----------|
| 2.3.1 | Componente: FileBrowser (lista de archivos/carpetas) | Agente | 3h |
| 2.3.2 | Iconos por tipo de archivo (.md, .ts, .py, .json, folder, etc.) | Agente | 1h |
| 2.3.3 | Navegación por carpetas con breadcrumbs | Agente | 2h |
| 2.3.4 | Sort: folders first, luego archivos alfabéticos | Agente | 30min |
| 2.3.5 | Badge "agent" en archivos con commits recientes de bots | Agente | 1h |

#### 2.4 — Branch Switcher
| # | Tarea | Asignado | Estimado |
|---|-------|----------|----------|
| 2.4.1 | Componente: BranchSwitcher (dropdown/bottom sheet) | Agente | 2h |
| 2.4.2 | Filtro de branches por nombre | Agente | 30min |
| 2.4.3 | Cambiar branch → recargar árbol y commits | Agente | 1h |

#### 2.5 — Commit History
| # | Tarea | Asignado | Estimado |
|---|-------|----------|----------|
| 2.5.1 | Componente: CommitHistory (lista agrupada por fecha) | Agente | 3h |
| 2.5.2 | Diferenciar commits humano vs bot (avatar, estilo) | Agente | 1h |
| 2.5.3 | Stats por commit (+additions / -deletions) | Agente | 30min |
| 2.5.4 | Tab switcher: Files ↔ Commits | Agente | 1h |
| 2.5.5 | **Prueba funcional**: Navegar repos, carpetas, branches, commits | **Ali** | 1h |

---

### Fase 3: Writing (Semana 3)
> Editar archivos, mover archivos, hacer commits.

#### 3.1 — Document Viewer
| # | Tarea | Asignado | Estimado |
|---|-------|----------|----------|
| 3.1.1 | Componente: DocumentViewer (Markdown renderizado) | Agente | 3h |
| 3.1.2 | Toggle raw/source view | Agente | 1h |
| 3.1.3 | Metadata: last edited, file size | Agente | 30min |
| 3.1.4 | Back navigation al file browser | Agente | 30min |

#### 3.2 — Document Editor
| # | Tarea | Asignado | Estimado |
|---|-------|----------|----------|
| 3.2.1 | Componente: DocumentEditor (textarea con toolbar) | Agente | 3h |
| 3.2.2 | Toolbar: Bold, Italic, H2, Link, Code, List | Agente | 2h |
| 3.2.3 | Preview toggle (editor ↔ rendered) | Agente | 1h |
| 3.2.4 | Commit bar: mensaje + botones save/cancel | Agente | 1h |
| 3.2.5 | API write: PUT /contents/{path} con SHA + contenido | Agente | 2h |
| 3.2.6 | Conflict detection: SHA mismatch → alerta al usuario | Agente | 2h |

#### 3.3 — Drag & Drop + Changeset
| # | Tarea | Asignado | Estimado |
|---|-------|----------|----------|
| 3.3.1 | Long-press + drag gesture en file browser (react-native-gesture-handler) | Agente | 4h |
| 3.3.2 | Visual feedback: ghost element, folder highlighting | Agente | 2h |
| 3.3.3 | Componente: ChangesetPanel (lista de cambios pendientes) | Agente | 2h |
| 3.3.4 | Undo individual + discard all | Agente | 1h |
| 3.3.5 | API write: Git Trees → crear tree → commit → update ref (batch move) | Agente | 4h |
| 3.3.6 | Auto-generate commit message | Agente | 30min |
| 3.3.7 | **Prueba funcional**: Editar archivo, mover archivos, verificar en GitHub | **Ali** | 1h |

---

### Fase 4: Notifications & Polish (Semana 4)
> Webhooks, push notifications, pulir UX, preparar para beta.

#### 4.1 — Webhooks + Push
| # | Tarea | Asignado | Estimado |
|---|-------|----------|----------|
| 4.1.1 | Edge Function: `github-webhook` (recibir push events) | Agente | 3h |
| 4.1.2 | Verificación de webhook signature (HMAC SHA-256) | Agente | 1h |
| 4.1.3 | Procesar push event → insertar en activity_feed | Agente | 2h |
| 4.1.4 | Invalidar cache de tree cuando llega push | Agente | 1h |
| 4.1.5 | Integrar Expo Push Notifications | Agente | 3h |
| 4.1.6 | Registrar push token del dispositivo en Supabase | Agente | 1h |
| 4.1.7 | Enviar push notification cuando agente hace commit | Agente | 2h |
| 4.1.8 | Pantalla de settings: toggle notificaciones por repo | Agente | 2h |

#### 4.2 — Polish & UX
| # | Tarea | Asignado | Estimado |
|---|-------|----------|----------|
| 4.2.1 | Loading states (skeletons) en todas las pantallas | Agente | 2h |
| 4.2.2 | Error handling global (network, auth expired, rate limit) | Agente | 2h |
| 4.2.3 | Pull-to-refresh en file browser y commits | Agente | 1h |
| 4.2.4 | Empty states (repo sin archivos, sin commits, etc.) | Agente | 1h |
| 4.2.5 | App icon + splash screen | Agente | 1h |
| 4.2.6 | Dark mode (default) + light mode toggle | Agente | 2h |

#### 4.3 — Build & Deploy
| # | Tarea | Asignado | Estimado |
|---|-------|----------|----------|
| 4.3.1 | EAS Build: generar APK para Android (internal testing) | Agente | 1h |
| 4.3.2 | EAS Build: generar IPA para iOS (TestFlight) | Agente | 1h |
| 4.3.3 | Configurar Apple Developer account para TestFlight | **Ali** | 30min |
| 4.3.4 | Subir a TestFlight para beta testing | **Ali** | 15min |
| 4.3.5 | Subir APK a Google Play internal testing | **Ali** | 15min |
| 4.3.6 | **Prueba funcional completa**: Flujo end-to-end en dispositivo real | **Ali** | 2h |
| 4.3.7 | Fix bugs de testing | Agente | 4h |

---

## 5. Resumen de Asignación

### Ali (Total: ~7h)

| Tarea | Tiempo |
|-------|--------|
| Registrar GitHub App + configurar permisos/URLs | 35min |
| Pasar credenciales (App ID, secrets, private key) | 10min |
| Prueba funcional Fase 1: Auth flow | 30min |
| Prueba funcional Fase 2: Navegación | 1h |
| Prueba funcional Fase 3: Edición + drag & drop | 1h |
| Configurar Apple Developer / Google Play accounts | 30min |
| Subir builds a TestFlight + Play Store internal | 30min |
| Prueba funcional Fase 4: E2E en dispositivo real | 2h |
| **Total** | **~7h** |

### Agente / Sub-agentes (Total: ~105h)

| Fase | Horas |
|------|-------|
| Fase 1: Foundation | ~22h |
| Fase 2: Core Reading | ~30h |
| Fase 3: Writing | ~30h |
| Fase 4: Notifications & Polish | ~27h |
| **Total** | **~109h** |

### Distribución por sub-agente

| Sub-agente | Responsabilidad | Tareas |
|-----------|----------------|--------|
| **infra-agent** | Supabase setup, migrations, RLS, secrets, EAS config | 1.1.x, 1.2.6 |
| **backend-agent** | Edge Functions (auth, proxy, webhook), GitHub API queries | 1.3.1-1.3.2, 2.1.x, 3.2.5-3.2.6, 3.3.5, 4.1.x |
| **frontend-agent** | React Native screens, components, navigation, gestures | 1.3.3-1.3.6, 2.2.x, 2.3.x, 2.4.x, 2.5.x, 3.1.x, 3.2.1-3.2.4, 3.3.1-3.3.4, 3.3.6, 4.2.x |
| **build-agent** | EAS builds, app icons, splash, CI/CD | 1.1.6, 4.2.5, 4.3.1-4.3.2 |

---

## 6. Dependencias y Orden de Ejecución

```
Semana 1
  ├── 1.1 Setup proyecto (Agente) ─────────────────────┐
  ├── 1.2 GitHub App registration (Ali) ───────────────┤
  │                                                     ▼
  └── 1.3 Auth flow (Agente, depende de 1.1 + 1.2) ──► Ali prueba

Semana 2
  ├── 2.1 GitHub API client (Agente) ──────────────────┐
  │                                                     ▼
  ├── 2.2 Dashboard (Agente, depende de 2.1) ─────────┐
  ├── 2.3 File Browser (Agente, depende de 2.1) ──────┤
  ├── 2.4 Branch Switcher (Agente, depende de 2.1) ───┤
  ├── 2.5 Commit History (Agente, depende de 2.1) ────┤
  │                                                     ▼
  └──────────────────────────────────────────────────► Ali prueba

Semana 3
  ├── 3.1 Document Viewer (Agente) ────────────────────┐
  ├── 3.2 Document Editor (Agente, depende de 3.1) ───┤
  ├── 3.3 Drag & Drop (Agente, depende de 2.3) ───────┤
  │                                                     ▼
  └──────────────────────────────────────────────────► Ali prueba

Semana 4
  ├── 4.1 Webhooks + Push (Agente) ────────────────────┐
  ├── 4.2 Polish (Agente, paralelo) ──────────────────┤
  ├── 4.3 Builds (Agente + Ali) ──────────────────────┤
  │                                                     ▼
  └──────────────────────────────────────────────────► Ali prueba E2E
```

---

## 7. Entregables por Semana

| Semana | Entregable | Demo |
|--------|-----------|------|
| 1 | App con login de GitHub funcional, ve lista de repos | Video/screenshot del auth flow |
| 2 | Navegar archivos, cambiar branches, ver commits | Ali prueba en Expo Go |
| 3 | Editar markdown, mover archivos, commits reales en GitHub | Ali edita un archivo real desde el teléfono |
| 4 | Push notifications, polish, builds en TestFlight/Play Store | App instalada en el teléfono de Ali |

---

## 8. Riesgos

| Riesgo | Impacto | Mitigación |
|--------|---------|-----------|
| Deep linking OAuth falla en Expo Go | Bloquea auth | Usar dev client (EAS Build) desde día 1 |
| GitHub rate limit en testing intensivo | Slows development | Cache agresivo + mock data para UI development |
| Drag & drop complejo en React Native | Retrasa Fase 3 | Usar react-native-gesture-handler + reanimated (probados) |
| Apple Developer review demora | Retrasa beta | Distribuir via TestFlight (no requiere review completo) |
| Supabase Vault no disponible en plan actual | Tokens inseguros | Encriptar tokens con AES antes de guardar en Postgres |

---

## 9. Prerequisitos de Ali

Antes de que los agentes arranquen, Ali necesita:

- [ ] **Upgrade Supabase** (ya confirmado)
- [ ] **Registrar GitHub App** en github.com/settings/apps/new
  - Name: `RepoSpace`
  - Homepage URL: `https://repospace.dev` (o placeholder)
  - Callback URL: se definirá cuando tengamos el proyecto Supabase
  - Permissions: Repository contents (Read & Write), Metadata (Read)
  - Webhook: Active, URL se configurará después
  - Where can this app be installed: "Any account"
- [ ] **Compartir credenciales**: App ID, Client ID, Client Secret, Private Key (.pem)
- [ ] **Apple Developer account** (para TestFlight en Semana 4)
- [ ] **Google Play Console** (para internal testing en Semana 4)

---

*Plan sujeto a ajustes conforme avancen las fases. Se actualizará semanalmente.*
