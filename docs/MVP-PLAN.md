# Grove — MVP Scope & Implementation Plan

> Fecha: 2026-02-17
> Objetivo: App mobile funcional que permita autenticarse con GitHub, navegar repos, ver/editar archivos, ver commits, y mover archivos con drag & drop.

---

## 0. Design Source of Truth

**La maqueta interactiva es la referencia de diseño para toda la app.**

- **URL:** https://repospace-mockup.pages.dev
- **Código fuente:** `/mockup/index.html`

Todos los componentes de frontend DEBEN replicar fielmente:
- **Color palette:** `--bg: #09090b`, `--surface: #18181b`, `--accent: #3b82f6`, `--green: #22c55e`, etc.
- **Typography:** Inter font, sizes y weights del mockup
- **Layout:** Sidebar en desktop, hamburger en mobile, same spacing/padding
- **Components:** Repo cards, file rows, commit rows, branch dropdown, document viewer, editor, changeset panel, auth screens — todos deben ser visualmente idénticos a la maqueta
- **Interactions:** Drag & drop behavior, long-press en mobile, toast notifications, skeleton loaders
- **Dark mode** es el default (como la maqueta)

Cualquier desviación del diseño de la maqueta requiere aprobación explícita de Ali.

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

## 3. Task Breakdown

### ID Convention
- `F1.x` = Fase 1: Foundation
- `F2.x` = Fase 2: Core Reading
- `F3.x` = Fase 3: Writing
- `F4.x` = Fase 4: Notifications & Polish

### Assignee Legend
- 🤖 **infra** = infra-agent (Supabase, config, CI/CD)
- 🔧 **backend** = backend-agent (Edge Functions, GitHub API)
- 📱 **frontend** = frontend-agent (React Native screens, components)
- 📦 **build** = build-agent (EAS builds, assets)
- 👤 **Ali** = Manual tasks + functional testing

---

### Fase 1: Foundation (Semana 1)

| ID | Tarea | Descripción | Asignado | Horas | Depende de |
|----|-------|------------|----------|-------|-----------|
| F1.1 | Crear proyecto Expo | Inicializar proyecto con Expo Router, TypeScript, ESLint, prettier. Configurar estructura de carpetas (app/, components/, lib/, store/). Agregar dependencias base: zustand, supabase-js, expo-router. | 🤖 infra | 2h | — |
| F1.2 | Crear repo GitHub | Crear repo privado `alimazid/grove`, configurar .gitignore, README, push initial commit. | 🤖 infra | 0.5h | — |
| F1.3 | Crear proyecto Supabase | Crear nuevo proyecto "grove" en Supabase (región East US). Obtener URL, anon key, service role key. Configurar en el proyecto Expo como variables de entorno. | 🤖 infra | 1h | — |
| F1.4 | Schema de base de datos | Escribir migraciones SQL para todas las tablas: users, connected_repos, activity_feed, tree_cache, notification_queue, push_tokens. Incluir índices y tipos. | 🤖 infra | 2h | F1.3 |
| F1.5 | Row Level Security | Crear policies RLS para cada tabla. Cada usuario solo puede leer/escribir sus propios registros. Service role bypass para Edge Functions. | 🤖 infra | 1h | F1.4 |
| F1.6 | Configurar EAS Build | Crear cuenta EAS, configurar eas.json con perfiles development/preview/production. Generar dev client para testing. | 🤖 infra | 1h | F1.1 |
| F1.7 | Registrar GitHub App | Crear GitHub App en github.com/settings/apps/new. Configurar nombre "Grove", permisos (contents r/w, metadata r), callback URL, webhook URL, logo. | 👤 Ali | 0.5h | F1.3 |
| F1.8 | Configurar GitHub App URLs | Actualizar callback URL y webhook URL de la GitHub App con los endpoints reales de Supabase una vez creados. | 👤 Ali | 0.25h | F1.7, F1.10 |
| F1.9 | Guardar credenciales GitHub App | Recibir de Ali: App ID, Client ID, Client Secret, Private Key (.pem). Almacenar como secrets en Supabase y en tokens.env local. | 🤖 infra | 0.25h | F1.7 |
| F1.10 | Edge Function: github-auth | Implementar OAuth callback handler. Recibe `code` de GitHub, intercambia por access token, crea/actualiza usuario en Supabase Auth, almacena GitHub token encriptado en la DB. Retorna JWT de sesión. | 🔧 backend | 3h | F1.4, F1.9 |
| F1.11 | Encriptación de tokens | Implementar capa de encriptación AES-256-GCM para GitHub tokens antes de almacenar en Postgres. Key derivada de SUPABASE_SERVICE_ROLE_KEY. Funciones encrypt/decrypt reutilizables. | 🔧 backend | 1h | F1.4 |
| F1.12 | Design tokens + Pantalla de Login | **Primero:** Parsear el CSS del mockup (`/mockup/index.html`) y crear un theme file con todos los colores, tipografía, spacing, border-radius, y shadows — este archivo es la referencia para todos los componentes. **Luego:** Crear screen de login replicando exactamente el diseño de la pantalla `auth-login` del mockup: branding Grove, botón "Sign in with GitHub", 3 feature highlights, footer. Dark mode default. | 📱 frontend | 3h | F1.1 |
| F1.13 | Flujo OAuth en la app | Implementar AuthSession de Expo para iniciar OAuth flow. Manejar deep link callback, recibir JWT de Supabase, almacenar sesión con SecureStore. Redirect a dashboard post-login. | 📱 frontend | 3h | F1.10, F1.12 |
| F1.14 | Persistencia de sesión | Al abrir la app, verificar si hay sesión válida en SecureStore. Si existe y no expiró → skip login, ir directo a dashboard. Si expiró → refresh token. Si falla → mostrar login. | 📱 frontend | 1h | F1.13 |
| F1.15 | Logout | Botón de logout en settings/perfil. Limpia sesión de SecureStore, limpia stores de Zustand, navega a pantalla de login. Opcionalmente revocar token en GitHub. | 📱 frontend | 0.5h | F1.14 |
| F1.16 | **TEST: Auth flow** | Probar en dispositivo real: login con GitHub → ver repos → cerrar app → reabrir (auto-login) → logout → login de nuevo. Verificar que tokens se guardan correctamente. | 👤 Ali | 0.5h | F1.15 |

---

### Fase 2: Core Reading (Semana 2)

| ID | Tarea | Descripción | Asignado | Horas | Depende de |
|----|-------|------------|----------|-------|-----------|
| F2.1 | Edge Function: github-proxy | Proxy genérico que recibe peticiones del cliente, extrae el GitHub token del usuario autenticado (desencripta), ejecuta la query/request contra GitHub API, y retorna la respuesta. Maneja errores (401 → reauth, 403 → rate limit, 404 → not found). | 🔧 backend | 3h | F1.10, F1.11 |
| F2.2 | Query: listar repos | Implementar query GraphQL `viewer.repositories` que retorna: name, owner, description, primaryLanguage, pushedAt, defaultBranch, isPrivate. Paginación con cursor. Envolver en función reutilizable. | 🔧 backend | 1h | F2.1 |
| F2.3 | Query: árbol de archivos | Implementar llamada a REST Git Trees API (`GET /repos/{owner}/{repo}/git/trees/{sha}?recursive=1`) para obtener árbol completo de un branch. Parsear respuesta en estructura jerárquica (folders → children). | 🔧 backend | 2h | F2.1 |
| F2.4 | Query: contenido de archivo | Implementar query GraphQL `repository.object(expression: "{branch}:{path}")` para obtener contenido de un archivo. Decodificar Base64 si viene del REST endpoint. Soportar archivos hasta 1MB. | 🔧 backend | 1h | F2.1 |
| F2.5 | Query: listar branches | Implementar query GraphQL `repository.refs(refPrefix: "refs/heads/")` que retorna nombre, commit SHA asociado, y si es default branch. Ordenar: default first, luego alfabético. | 🔧 backend | 1h | F2.1 |
| F2.6 | Query: historial de commits | Implementar query GraphQL `repository.ref.target.history` con paginación. Retorna: SHA, message, author (login, avatarUrl), committedDate, additions, deletions. Incluir flag `is_bot` basado en author type o "[bot]" en nombre. | 🔧 backend | 2h | F2.1 |
| F2.7 | Cache local por SHA | Implementar capa de cache con AsyncStorage. Trees se cachean por SHA (inmutable — nunca expiran). Contenido de archivos se cachea por blob SHA. Branches y repos se cachean con TTL de 5 min. Función `getCached(key)` / `setCached(key, data, ttl?)`. | 📱 frontend | 2h | F1.1 |
| F2.8 | Zustand stores | Crear stores: `useRepoStore` (repos, selectedRepo), `useFileStore` (currentTree, currentPath, files), `useBranchStore` (branches, selectedBranch), `useCommitStore` (commits por branch). Acciones para fetch + cache integration. | 📱 frontend | 2h | F2.7 |
| F2.9 | Screen: Dashboard de repos | Pantalla principal post-login. FlatList de repos con: icono, owner/name, descripción, lenguaje (dot color + nombre), tiempo desde último push. Pull-to-refresh. Tap → navega a file browser. Empty state si no hay repos. | 📱 frontend | 3h | F2.2, F2.8 |
| F2.10 | Screen: File Browser | Pantalla de navegación de archivos. FlatList con filas: icono por tipo (📁📄📝💻), nombre, size, updated. Tap folder → push en navigation stack. Breadcrumbs arriba para navegar hacia atrás. Sort: folders first, luego files alfabéticos. | 📱 frontend | 3h | F2.3, F2.8 |
| F2.11 | Iconos por tipo de archivo | Mapear extensiones a iconos: .md → documento, .ts/.js/.py/.html → código, .json → llaves, .yml → config, folders → carpeta. Usar iconos SVG o Expo vector icons. Colores por lenguaje (TypeScript azul, Python azul, etc). | 📱 frontend | 1h | F2.10 |
| F2.12 | Badge "agent" | Detectar archivos con commits recientes de bots (basado en commit history o metadata). Mostrar badge verde "agent" al lado del nombre del archivo. Lógica: si el último commit que tocó el archivo tiene author.type === "Bot" o name includes "[bot]". | 📱 frontend | 1h | F2.10, F2.6 |
| F2.13 | Componente: BranchSwitcher | Bottom sheet (o dropdown en web) que muestra lista de branches. Input de búsqueda para filtrar. Marca branch actual con checkmark. Marca default branch con label. Tap → cambia branch en store → recarga tree y commits. | 📱 frontend | 2h | F2.5, F2.8 |
| F2.14 | Filtro de branches | Filtrado client-side por nombre de branch conforme el usuario escribe. Case-insensitive. Mostrar "No branches found" si no hay match. | 📱 frontend | 0.5h | F2.13 |
| F2.15 | Cambiar branch → recargar | Al seleccionar un nuevo branch: (1) actualizar store, (2) fetch nuevo tree SHA para el branch, (3) si tree SHA cambió → fetch nuevo tree, (4) si estamos en tab commits → fetch commits del nuevo branch. Mostrar loading mientras carga. | 📱 frontend | 1h | F2.13 |
| F2.16 | Componente: CommitHistory | FlatList de commits agrupados por fecha ("Today", "Yesterday", fecha). Cada fila: avatar (humano con inicial, bot con ícono ⚙️ en azul), mensaje, author login, timestamp relativo, +additions/-deletions en verde/rojo. SHA copiable con tap. | 📱 frontend | 3h | F2.6, F2.8 |
| F2.17 | Diferenciar humano vs bot | Estilo visual distinto para commits de bot: avatar con fondo azul suave y ícono de gear vs avatar con inicial del autor y fondo gris. Basado en campo `is_bot` del query de commits. | 📱 frontend | 1h | F2.16 |
| F2.18 | Tab switcher: Files ↔ Commits | Componente de tabs en la parte superior del file browser screen. Dos tabs: "Files" y "Commits". Cambia entre FileBrowser y CommitHistory. Mantiene estado al switchear (no re-fetcha si ya tiene data). Indicador visual de tab activo. | 📱 frontend | 1h | F2.10, F2.16 |
| F2.19 | **TEST: Navegación completa** | Probar en dispositivo: ver dashboard → tap repo → navegar carpetas → cambiar branch → ver commits → copiar SHA → pull-to-refresh. Verificar que cache funciona (segunda visita es instantánea). | 👤 Ali | 1h | F2.18 |

---

### Fase 3: Writing (Semana 3)

| ID | Tarea | Descripción | Asignado | Horas | Depende de |
|----|-------|------------|----------|-------|-----------|
| F3.1 | Componente: DocumentViewer | Pantalla de visualización de archivo. Renderiza Markdown usando react-native-markdown-display (o similar). Soporta: headers, bold, italic, code blocks, listas, checklists, blockquotes, links, tablas. Scroll vertical. Metadata arriba: last edited, file size. | 📱 frontend | 3h | F2.4, F2.10 |
| F3.2 | Toggle raw/source | Botón "Source" en toolbar del viewer. Alterna entre Markdown renderizado y código fuente crudo en bloque monospace. Mantiene posición de scroll al cambiar. | 📱 frontend | 1h | F3.1 |
| F3.3 | Back navigation | Botón "← Back" en toolbar que regresa al file browser en la misma carpeta donde estaba el usuario. Usa navigation.goBack() preservando el estado del store. | 📱 frontend | 0.5h | F3.1 |
| F3.4 | Componente: DocumentEditor | Textarea con toolbar de formateo. Ocupa pantalla completa al entrar en modo edición. Keyboard-aware: se ajusta cuando aparece el teclado. Fuente monospace. Tab support (2 espacios). | 📱 frontend | 3h | F3.1 |
| F3.5 | Toolbar de formateo | Barra horizontal con botones: Bold (**), Italic (*), H2 (##), Link ([]()), Code (``), List (- ). Cada botón inserta la sintaxis markdown alrededor del texto seleccionado o en la posición del cursor. Scroll horizontal si no caben todos. | 📱 frontend | 2h | F3.4 |
| F3.6 | Preview toggle en editor | Botón para alternar entre editor (textarea) y preview (Markdown renderizado) mientras editas. Permite revisar cómo se ve antes de guardar. No pierde el contenido editado al cambiar. | 📱 frontend | 1h | F3.4, F3.1 |
| F3.7 | Commit bar | Barra fija en la parte inferior al estar en modo edición. Contiene: input para commit message (placeholder auto-generado: "Update {filename}"), botón "Cancel" (descarta cambios), botón "Commit" (guarda). Se oculta al salir de edición. | 📱 frontend | 1h | F3.4 |
| F3.8 | API: editar archivo | Implementar llamada REST `PUT /repos/{owner}/{repo}/contents/{path}` a través del github-proxy. Envía: nuevo contenido (Base64), commit message, SHA actual del archivo. Recibe: nuevo commit SHA. Actualiza cache local post-commit. | 🔧 backend | 2h | F2.1, F2.4 |
| F3.9 | Conflict detection | Antes de guardar, comparar el SHA del archivo que se abrió vs el SHA actual en GitHub. Si difieren → alguien (agente u otro) editó el archivo mientras el usuario editaba. Mostrar alerta: "File was modified. Overwrite or cancel?" con opción de ver la versión actual. | 🔧 backend | 2h | F3.8 |
| F3.10 | Drag gesture handler | Implementar long-press (300ms) + drag usando react-native-gesture-handler y react-native-reanimated. Al hacer long-press en un file row: vibración háptica, el item se eleva visualmente (shadow + scale), sigue el dedo del usuario. Detectar drop target por posición Y. | 📱 frontend | 4h | F2.10 |
| F3.11 | Visual feedback de drag | Durante el drag: item original se atenúa (opacity 0.4), folders target se resaltan con borde azul al pasar encima, breadcrumbs se resaltan como drop targets alternativos. Ghost element flotan con el dedo. | 📱 frontend | 2h | F3.10 |
| F3.12 | Componente: ChangesetPanel | Bottom sheet persistente que aparece cuando hay cambios pendientes. Muestra: contador de cambios, lista con cada movimiento (from → to con paths), botón undo por item (✕), botón "Discard all", input de commit message (auto-generado), botón "Commit changes". Expandible/collapsible. | 📱 frontend | 2h | F3.10 |
| F3.13 | Undo individual + discard all | Cada item en el changeset tiene botón ✕ que lo remueve de la lista. "Discard all" limpia todo el changeset y oculta el panel. Confirmación antes de discard all si hay >3 cambios. | 📱 frontend | 1h | F3.12 |
| F3.14 | API: batch move (Git Trees) | Implementar el flujo completo de mover archivos via Git API: (1) GET tree actual del branch, (2) Crear nuevo tree con paths actualizados (remove old + add new para cada move), (3) Crear commit apuntando al nuevo tree con base en HEAD, (4) Update ref del branch al nuevo commit. Todo atómico — un solo commit. | 🔧 backend | 4h | F2.1, F2.3 |
| F3.15 | Auto-generate commit message | Generar mensaje de commit descriptivo basado en el changeset: 1 archivo → "Move {name} to {folder}", múltiples → "Move {n} files to new locations". Editable por el usuario antes de commit. | 📱 frontend | 0.5h | F3.12 |
| F3.16 | **TEST: Edición y drag & drop** | Probar en dispositivo: abrir archivo .md → editar → commit → verificar en GitHub que el commit existe. Mover 2-3 archivos entre carpetas con drag & drop → commit changeset → verificar en GitHub. Probar conflict detection editando el mismo archivo desde GitHub web simultáneamente. | 👤 Ali | 1h | F3.15 |

---

### Fase 4: Notifications & Polish (Semana 4)

| ID | Tarea | Descripción | Asignado | Horas | Depende de |
|----|-------|------------|----------|-------|-----------|
| F4.1 | Edge Function: github-webhook | Endpoint que recibe webhook deliveries de GitHub. Valida signature (HMAC SHA-256 con webhook secret). Parsea evento `push`: extrae repo, branch, commits, author. Inserta registros en `activity_feed`. Retorna 200 OK rápido (procesamiento async si es necesario). | 🔧 backend | 3h | F1.4 |
| F4.2 | Verificación HMAC | Implementar verificación de webhook signature. Calcular HMAC-SHA256 del body con el webhook secret, comparar con header `X-Hub-Signature-256`. Rechazar requests con signature inválida (401). Loggear intentos fallidos. | 🔧 backend | 1h | F4.1 |
| F4.3 | Procesar push → activity feed | Al recibir push event: para cada commit en el payload, insertar en `activity_feed` con: commit SHA, author login, is_bot flag, message, files changed (con additions/deletions por archivo), branch, timestamp. Deduplicar por SHA si el webhook se re-envía. | 🔧 backend | 2h | F4.1 |
| F4.4 | Invalidar cache de tree | Cuando llega un push event para un repo/branch: actualizar `last_tree_sha` en `connected_repos`, marcar tree_cache entries como stale. Enviar señal via Supabase Realtime para que clientes conectados re-fetchen el tree. | 🔧 backend | 1h | F4.1, F1.4 |
| F4.5 | Setup Expo Push Notifications | Configurar expo-notifications en la app. Request permission al usuario. Obtener Expo Push Token. Registrar token en tabla `push_tokens` de Supabase asociado al user_id y device info. Manejar token refresh. | 📱 frontend | 3h | F1.1, F1.4 |
| F4.6 | Guardar push token | Edge Function o insert directo: cuando la app obtiene un nuevo push token, upsert en tabla `push_tokens` (user_id, expo_token, platform, last_seen). Limpiar tokens viejos (>30 días sin actividad). | 🤖 infra | 1h | F1.4, F4.5 |
| F4.7 | Enviar push notification | Cuando el webhook procesa un push event de un bot/agent: buscar push_tokens de los usuarios con ese repo conectado, enviar Expo Push Notification con: título = repo name, body = commit message, data = { repoId, branch, sha } para deep linking. Rate limit: max 1 push por repo por 5 minutos. | 🔧 backend | 2h | F4.1, F4.6 |
| F4.8 | Deep link desde notification | Al tap en una push notification: abrir la app directamente en el file browser del repo y branch indicados en el payload. Si la app estaba cerrada → login automático (sesión persistente) → navegar al repo. | 📱 frontend | 2h | F4.5, F2.10 |
| F4.9 | Screen: Settings | Pantalla de configuración accesible desde tab o menú. Secciones: perfil (avatar + username de GitHub), notificaciones (toggle global + toggle por repo), tema (dark/light), logout, versión de la app. | 📱 frontend | 2h | F1.15 |
| F4.10 | Toggle notificaciones por repo | En settings, lista de repos conectados con toggle on/off para push notifications. Almacenar preferencia en tabla `connected_repos` o `user_preferences`. Al desactivar → no enviar push para ese repo. | 📱 frontend | 1h | F4.9, F4.7 |
| F4.11 | Loading states (skeletons) | Agregar skeleton loaders en: dashboard (repo cards), file browser (file rows), commit history (commit rows), document viewer (text blocks). Usar librería como `react-native-skeleton-placeholder`. Mostrar mientras se cargan datos por primera vez. | 📱 frontend | 2h | F2.9, F2.10, F2.16, F3.1 |
| F4.12 | Error handling global | Implementar error boundary y handler centralizado. Casos: (1) Network error → "No connection" banner, (2) Auth expired → redirect a login, (3) GitHub rate limit → "Try again in X minutes", (4) 404 → "File not found", (5) Generic → retry button. Toast para errores no-críticos. | 📱 frontend | 2h | F2.8 |
| F4.13 | Pull-to-refresh | Agregar RefreshControl a: dashboard de repos, file browser, commit history. Al pull: re-fetch data ignorando cache (force refresh). Mostrar spinner de loading. | 📱 frontend | 1h | F2.9, F2.10, F2.16 |
| F4.14 | Empty states | Diseñar e implementar pantallas empty state para: (1) Dashboard sin repos → "Connect your first repo", (2) Carpeta vacía → "This folder is empty", (3) Branch sin commits → "No commits yet", (4) Sin notificaciones → "All quiet". Ilustraciones simples + CTA cuando aplique. | 📱 frontend | 1h | F2.9, F2.10, F2.16 |
| F4.15 | App icon + splash screen | Diseñar app icon (logo R sobre fondo azul) en los tamaños requeridos (1024x1024 base). Crear splash screen con logo centrado y fondo dark. Configurar en app.json. | 📦 build | 1h | F1.1 |
| F4.16 | Dark/light mode | Implementar theme provider con dos temas: dark (default, colores del mockup) y light. Toggle en settings. Persistir preferencia en AsyncStorage. Todos los componentes usan tokens del tema (no colores hardcoded). | 📱 frontend | 2h | F4.9 |
| F4.17 | EAS Build: Android APK | Configurar perfil de build para Android (preview). Ejecutar `eas build --platform android --profile preview`. Generar APK/AAB para internal testing. Verificar que la app funciona en emulador y dispositivo. | 📦 build | 1h | F4.16 |
| F4.18 | EAS Build: iOS IPA | Configurar perfil de build para iOS (preview). Requiere Apple Developer certificate. Ejecutar `eas build --platform ios --profile preview`. Generar IPA para TestFlight. | 📦 build | 1h | F4.16, F4.19 |
| F4.19 | Configurar Apple Developer | Configurar Apple Developer account para distribución. Crear App ID, provisioning profile. Necesario para TestFlight. | 👤 Ali | 0.5h | — |
| F4.20 | Subir a TestFlight | Subir IPA generada por EAS a TestFlight via EAS Submit o manualmente. Invitar testers. | 👤 Ali | 0.25h | F4.18 |
| F4.21 | Subir a Google Play internal | Subir AAB a Google Play Console como internal testing track. Configurar store listing mínimo. | 👤 Ali | 0.25h | F4.17 |
| F4.22 | **TEST: E2E en dispositivo real** | Prueba completa end-to-end en iPhone y/o Android: instalar desde TestFlight/Play Store → login → navegar repos → cambiar branch → ver commits → abrir documento → editar y commit → mover archivos → recibir push notification cuando un agente hace push al repo → logout. Documentar bugs encontrados. | 👤 Ali | 2h | F4.21 |
| F4.23 | Fix bugs de testing | Corregir bugs reportados por Ali durante las pruebas E2E. Priorizar por severidad: blockers primero, luego UX issues. Re-build y re-deploy. | 📱 frontend / 🔧 backend | 4h | F4.22 |

---

## 4. Dependency Graph

```
F1.1 ──────┬──► F1.6
           ├──► F1.12 ──► F1.13 ──► F1.14 ──► F1.15 ──► F1.16 (Ali test)
           ├──► F2.7 ──► F2.8 ──┬──► F2.9 ──────────────────┐
           │                     ├──► F2.10 ─────────────────┤
           │                     ├──► F2.13 ──► F2.14        │
           │                     │              F2.15         │
           │                     ├──► F2.16 ──► F2.17        │
           │                     │              F2.18 ──────► F2.19 (Ali test)
           │                     │                            │
           │                     │    F3.1 ──┬──► F3.2       │
           │                     │    │       ├──► F3.3       │
           │                     │    │       └──► F3.4 ──┬──► F3.5
           │                     │    │                    ├──► F3.6
           │                     │    │                    └──► F3.7
           │                     │    │                         │
           │                     │    └──► F3.8 ──► F3.9       │
           │                     │                              │
           │                     └──► F3.10 ──► F3.11          │
           │                              └──► F3.12 ──► F3.13 │
           │                                   F3.14    F3.15  │
           │                                          F3.16 (Ali test)
           │                                                    │
           └──► F4.5 ──► F4.8                                  │
                F4.15                                           │
                F4.11                                           │
                F4.12                                           │
                F4.16 ──► F4.17 ──► F4.21                      │
                     └──► F4.18 ──► F4.20                      │
                                                                │
F1.2 (parallel)                                                │
                                                                │
F1.3 ──► F1.4 ──► F1.5                                        │
              └──► F1.10 ──► F1.13                             │
              │    F1.11 ──► F1.10                             │
              └──► F2.1 ──┬──► F2.2 ──► F2.9                  │
                          ├──► F2.3 ──► F2.10                  │
                          ├──► F2.4 ──► F3.1                   │
                          ├──► F2.5 ──► F2.13                  │
                          ├──► F2.6 ──► F2.16                  │
                          ├──► F3.8 ──► F3.9                   │
                          └──► F3.14                           │
                                                                │
F1.7 (Ali) ──► F1.8 (Ali)                                     │
          └──► F1.9                                            │
                                                                │
F4.1 ──► F4.2                                                  │
    └──► F4.3                                                  │
    └──► F4.4                                                  │
    └──► F4.7 ──► F4.10                                       │
                                                                │
F4.6 ──► F4.7                                                  │
F4.9 ──► F4.10, F4.16                                         │
F4.19 (Ali) ──► F4.18                                          │
                                                                ▼
                                                    F4.22 (Ali test)
                                                         │
                                                         ▼
                                                    F4.23 (bug fixes)
```

---

## 5. Critical Path

La ruta más larga que determina la duración mínima del proyecto:

```
F1.3 → F1.4 → F1.10 → F1.13 → F1.14 → F2.1 → F2.3 → F2.10 → F3.10 → F3.12 → F3.14 → F3.16 → F4.22 → F4.23
```

Cualquier retraso en estas tareas retrasa el MVP completo.

---

## 6. Resumen

| Métrica | Valor |
|---------|-------|
| Total de tareas | 62 |
| Tareas de Ali | 8 (~7h) |
| Tareas de agentes | 54 (~109h) |
| Duración | 4 semanas |
| Fases | 4 |
| Tests funcionales | 4 (1 por fase) |

### Por asignado

| Asignado | Tareas | Horas |
|----------|--------|-------|
| 🤖 infra-agent | 8 | ~9h |
| 🔧 backend-agent | 13 | ~30h |
| 📱 frontend-agent | 33 | ~56h |
| 📦 build-agent | 3 | ~3h |
| 👤 Ali | 8 | ~7h |

---

## 7. Prerequisitos de Ali

Antes de que los agentes arranquen:

- [ ] Upgrade Supabase (confirmado)
- [ ] Registrar GitHub App (F1.7)
- [ ] Compartir credenciales: App ID, Client ID, Client Secret, Private Key (F1.9)
- [ ] Apple Developer account (F4.19, puede esperar hasta Semana 4)
- [ ] Google Play Console (puede esperar hasta Semana 4)

---

*Plan sujeto a ajustes conforme avancen las fases. Se actualizará semanalmente.*
