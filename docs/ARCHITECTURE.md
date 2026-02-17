# Grove — Arquitectura de Alto Nivel

> App mobile-first para navegar, editar y gestionar repositorios de GitHub, diseñada para el flujo humano-agente.

---

## 1. Visión del Producto

Grove es una app mobile-first que convierte repositorios de GitHub en workspaces navegables y editables, optimizados para supervisar el trabajo de agentes de IA. El usuario principal consume contenido desde su teléfono: revisa cambios, edita documentos, mueve archivos y hace commits — sin necesidad de conocer git.

---

## 2. Plataforma Target

| Canal | Tecnología | Prioridad |
|-------|-----------|-----------|
| **iOS** | React Native (Expo) | P0 |
| **Android** | React Native (Expo) | P0 |
| **Web** | React (shared codebase) | P1 |

**Decisión: React Native con Expo**, no PWA. Razones:
- Push notifications nativas (crítico para alertas de cambios del agente)
- Gestos nativos para drag & drop, swipe, long-press
- Acceso a App Store / Play Store para distribución
- Expo permite compilar para iOS, Android y web desde un solo codebase
- Rendimiento superior vs PWA para navegación de archivos y scroll largo

---

## 3. Arquitectura General

```
┌─────────────────────────────────────────────────────────┐
│                     CLIENTES                             │
│                                                          │
│   ┌──────────┐   ┌──────────┐   ┌──────────┐           │
│   │    iOS    │   │ Android  │   │   Web    │           │
│   │  (Expo)  │   │  (Expo)  │   │  (Expo)  │           │
│   └────┬─────┘   └────┬─────┘   └────┬─────┘           │
│        └───────────────┼───────────────┘                 │
│                        │                                 │
└────────────────────────┼─────────────────────────────────┘
                         │ HTTPS
                         ▼
┌─────────────────────────────────────────────────────────┐
│                   API GATEWAY                            │
│                  (Supabase Edge Functions)                │
│                                                          │
│   ┌────────────┐ ┌────────────┐ ┌────────────────────┐  │
│   │    Auth     │ │   Proxy    │ │   Activity         │  │
│   │   Service   │ │  GitHub    │ │   Service          │  │
│   │            │ │   API      │ │                    │  │
│   └────────────┘ └────────────┘ └────────────────────┘  │
│                                                          │
└──────────┬──────────────┬──────────────┬─────────────────┘
           │              │              │
           ▼              ▼              ▼
     ┌──────────┐  ┌──────────────┐  ┌──────────┐
     │ Supabase │  │   GitHub     │  │ Supabase │
     │   Auth   │  │  GraphQL API │  │ Postgres │
     │ (GitHub  │  │    (v4)      │  │ (cache,  │
     │  OAuth)  │  │              │  │  prefs)  │
     └──────────┘  └──────┬───────┘  └──────────┘
                          │
                   ┌──────┴───────┐
                   │   GitHub     │
                   │  Webhooks    │
                   │  (push events)│
                   └──────────────┘
```

---

## 4. Componentes Principales

### 4.1 Cliente Mobile (React Native / Expo)

La app corre en el dispositivo del usuario. Es responsable de:

- Renderizar la UI (file browser, document viewer, editor, commits)
- Gestionar navegación entre pantallas
- Manejar gestos (drag & drop, swipe, long-press)
- Cache local de árboles de archivos y contenido reciente
- Recibir push notifications

**No almacena tokens de GitHub directamente** — los tokens viven en Supabase Auth y se usan server-side.

### 4.2 API Gateway (Supabase Edge Functions)

Capa intermedia entre el cliente y GitHub. Funciones:

- **Auth Service**: Maneja el flujo OAuth con GitHub, almacena tokens encriptados, renueva sesiones
- **GitHub Proxy**: Recibe peticiones del cliente, las ejecuta contra GitHub API usando el token del usuario, aplica cache
- **Activity Service**: Procesa webhooks de GitHub, genera el activity feed, dispara push notifications

El proxy existe por tres razones:
1. **Seguridad**: El token de GitHub nunca llega al cliente
2. **Cache**: Reducir llamadas a GitHub API (rate limit: 5,000 puntos/hora)
3. **Transformación**: Adaptar respuestas de GitHub al formato que necesita la app

### 4.3 Base de Datos (Supabase Postgres)

Almacena datos complementarios — **no almacena código ni contenido de repos**:

- **Usuarios**: Perfil, preferencias, última actividad
- **Repos conectados**: Qué repos tiene cada usuario vinculados
- **Cache de árboles**: Estructura de archivos cacheada por branch + SHA
- **Activity feed**: Historial de commits procesados para consulta rápida
- **Notificaciones**: Cola de notificaciones pendientes por usuario

### 4.4 GitHub API (GraphQL v4)

Fuente de verdad para todo el contenido. La app **nunca clona repos** — todo se lee y escribe via API.

**Operaciones principales:**

| Operación | API | Notas |
|-----------|-----|-------|
| Listar repos del usuario | GraphQL `viewer.repositories` | Una query trae nombre, descripción, lenguaje, último push |
| Árbol de archivos | REST `GET /repos/{owner}/{repo}/git/trees/{sha}?recursive=1` | Trae todo el árbol en una llamada. Cache por SHA (inmutable) |
| Leer archivo | GraphQL `repository.object(expression)` | Trae contenido + metadata en una query |
| Listar branches | GraphQL `repository.refs` | Filtra por `refs/heads/` |
| Historial de commits | GraphQL `repository.ref.target.history` | Paginado, incluye author, message, additions, deletions |
| Editar archivo | REST `PUT /repos/{owner}/{repo}/contents/{path}` | Requiere SHA actual del archivo (conflict detection) |
| Mover archivos (batch) | REST Git Trees API: crear tree → crear commit → actualizar ref | Permite mover múltiples archivos en un solo commit |
| Crear rama | REST `POST /repos/{owner}/{repo}/git/refs` | A partir de un SHA existente |

**Límites clave:**
- GraphQL: 5,000 puntos/hora por usuario
- REST: 5,000 requests/hora por usuario
- Contenido de archivos: hasta 100MB via API (>1MB solo raw media type)
- Directorio: máximo 1,000 archivos (usar Trees API para más)

### 4.5 GitHub Webhooks

GitHub envía eventos a nuestro servidor cuando ocurren cambios en repos conectados:

- **`push` event**: Alguien (humano o agente) hizo push → actualizar activity feed, invalidar cache, enviar push notification
- **`create`/`delete` event**: Branch creada/eliminada → actualizar lista de branches

Los webhooks se configuran automáticamente cuando el usuario conecta un repo (via GitHub App installation).

---

## 5. Autenticación

### GitHub App (no OAuth App)

Usamos **GitHub App** en vez de OAuth App. Razones:

| Criterio | GitHub App | OAuth App |
|----------|-----------|-----------|
| Permisos | Fine-grained (solo lo necesario) | Scopes amplios (`repo` = acceso total) |
| Selección de repos | El usuario elige qué repos compartir | Acceso a todos los repos |
| Tokens | Short-lived (8 horas, auto-refresh) | Long-lived (hasta que el usuario revoque) |
| Webhooks | Built-in centralizados | Configurar manualmente por repo |
| Rate limits | Escalan con repos e instalaciones | Fijos |
| Recomendación de GitHub | Preferred | Legacy |

### Flujo de autenticación

```
Usuario                    Grove                  GitHub
  │                           │                          │
  ├── Tap "Sign in" ─────────►│                          │
  │                           ├── Redirect ──────────────►│
  │                           │                          │
  │◄──────────────── GitHub login + authorize ───────────│
  │                           │                          │
  │   ── Consent + select repos ─────────────────────────►│
  │                           │                          │
  │                           │◄── Callback (code) ──────│
  │                           │                          │
  │                           ├── Exchange code ─────────►│
  │                           │◄── User access token ────│
  │                           │                          │
  │                           ├── Store token (encrypted) │
  │                           │   in Supabase Auth        │
  │                           │                          │
  │◄── Session cookie ────────│                          │
  │    (Supabase JWT)         │                          │
```

**Permisos solicitados (GitHub App):**
- `contents: read & write` — leer/escribir archivos, árboles, commits
- `metadata: read` — info del repo, branches, lenguajes
- No necesitamos: issues, PRs, actions, admin

**El token de GitHub nunca llega al dispositivo del usuario.** El cliente solo maneja un JWT de Supabase.

---

## 6. Estrategia de Cache

El cache es crítico para mantener la app rápida sin agotar el rate limit de GitHub.

### Niveles de cache

| Nivel | Qué cachea | TTL | Invalidación |
|-------|-----------|-----|-------------|
| **Cliente (memoria)** | Árboles, contenido visto recientemente | Sesión | Navegación o pull-to-refresh |
| **Cliente (persistente)** | Árboles por SHA, preferencias | Indefinido (SHA = inmutable) | Nunca (SHA cambia = nueva entrada) |
| **Servidor (Postgres)** | Activity feed, árboles procesados | 1 hora | Webhook push invalida |

### Principio clave: SHA = inmutable

Git identifica todo por SHA. Si cacheas un árbol por su SHA, **nunca necesitas invalidarlo** — si el contenido cambia, el SHA cambia y pides el nuevo. Solo necesitas saber cuál es el SHA actual de un branch (que cambia con cada push).

```
Branch "main" → SHA abc123 → árbol cacheado
Push event      → SHA def456 → pedir nuevo árbol, cachear
```

---

## 7. Operaciones de Escritura

### Editar un archivo

1. Cliente envía: `{ repo, path, newContent, commitMessage }`
2. API Gateway lee SHA actual del archivo desde GitHub
3. Compara con SHA que tenía el cliente al abrir el archivo
4. Si coinciden → `PUT /contents/{path}` con nuevo contenido
5. Si no coinciden → **conflicto** → notificar al usuario

### Mover archivos (batch / changeset)

Mover archivos en Git requiere crear un nuevo tree. El flujo:

1. Cliente acumula cambios: `[{from: "a/file.md", to: "b/file.md"}, ...]`
2. Envía changeset al servidor
3. Servidor:
   a. GET árbol actual del branch
   b. Crear nuevo tree (Git Trees API) con los paths actualizados
   c. Crear commit apuntando al nuevo tree
   d. Actualizar ref del branch al nuevo commit
4. Un solo commit con todos los movimientos

---

## 8. Notificaciones Push

```
GitHub ──webhook──► Supabase Edge Function ──► Evaluar reglas
                                                    │
                                          ┌─────────┼──────────┐
                                          ▼         ▼          ▼
                                       Ignorar   Activity    Push
                                                  Feed     Notification
                                                              │
                                                    ┌─────────┼─────────┐
                                                    ▼                   ▼
                                                  Expo              Supabase
                                                Push Service       Realtime
                                                 (APNs/FCM)      (web/in-app)
```

**Reglas de notificación (configurables por usuario):**
- Notificar cuando un agente modifica un archivo específico
- Notificar en cualquier push a un branch
- Modo silencioso (solo activity feed, sin push)

---

## 9. Modelo de Datos

```
users
├── id (uuid, PK)
├── github_id (int)
├── github_login (text)
├── avatar_url (text)
├── access_token_encrypted (text)  ← token de GitHub, encriptado
├── notification_settings (jsonb)
└── created_at, updated_at

connected_repos
├── id (uuid, PK)
├── user_id (uuid, FK → users)
├── github_owner (text)
├── github_repo (text)
├── default_branch (text)
├── webhook_id (int)              ← ID del webhook instalado
├── last_tree_sha (text)          ← SHA del último árbol conocido
└── connected_at

activity_feed
├── id (uuid, PK)
├── repo_id (uuid, FK → connected_repos)
├── commit_sha (text)
├── author_login (text)
├── author_is_bot (boolean)
├── message (text)
├── files_changed (jsonb)         ← [{path, additions, deletions}]
├── branch (text)
└── pushed_at (timestamptz)

tree_cache
├── sha (text, PK)                ← SHA del tree (inmutable)
├── repo_id (uuid, FK)
├── tree_data (jsonb)             ← árbol completo
└── cached_at (timestamptz)

notification_queue
├── id (uuid, PK)
├── user_id (uuid, FK → users)
├── type (text)                   ← push_event, file_changed, etc.
├── payload (jsonb)
├── delivered (boolean)
└── created_at
```

---

## 10. Seguridad

| Superficie | Medida |
|-----------|--------|
| Tokens de GitHub | Encriptados en reposo (Supabase Vault). Nunca expuestos al cliente |
| Comunicación | HTTPS everywhere. Certificate pinning en mobile |
| Autenticación | JWT de Supabase con expiración corta. Refresh tokens httpOnly |
| Autorización | Row Level Security (RLS) en Postgres. Cada usuario solo ve sus repos |
| Webhooks | Signature verification (HMAC SHA-256) en cada delivery |
| Contenido | No almacenamos código — GitHub es la fuente de verdad |

---

## 11. Componentes de Terceros

| Servicio | Uso | Alternativa |
|----------|-----|-------------|
| **Supabase** | Auth, Postgres, Edge Functions, Realtime | Firebase, AWS Amplify |
| **Expo** | Build system, push notifications, OTA updates | Raw React Native |
| **GitHub API** | Fuente de verdad de repos | GitLab/Bitbucket (futuro) |
| **Expo Push Service** | Envío de push via APNs/FCM | OneSignal, Firebase Cloud Messaging |

---

## 12. Escalabilidad

### Fase 1 (0-1K usuarios)
- Supabase free/pro tier
- Una instancia de Edge Functions
- Cache en Postgres

### Fase 2 (1K-10K usuarios)
- Agregar Redis para cache de árboles (más rápido que Postgres)
- Worker dedicado para procesar webhooks
- CDN para assets estáticos

### Fase 3 (10K+ usuarios)
- Sharding de webhooks por repo
- Rate limit pooling (múltiples GitHub App installations)
- Considerar GitHub App Marketplace listing

---

## 13. Decisiones de Arquitectura

| # | Decisión | Razón |
|---|----------|-------|
| ADR-1 | **React Native (Expo)** sobre PWA | Push nativas, gestos, distribución via stores, rendimiento |
| ADR-2 | **GitHub App** sobre OAuth App | Fine-grained permissions, user selecciona repos, short-lived tokens, webhooks built-in |
| ADR-3 | **GraphQL** como API primaria, REST para writes | GraphQL reduce llamadas para reads; REST necesario para Git Trees/Contents writes |
| ADR-4 | **No clonar repos** — todo via API | Evita almacenar código ajeno, simplifica arquitectura, GitHub maneja permisos |
| ADR-5 | **Supabase** como backend | Auth con GitHub OAuth built-in, Postgres, Edge Functions, Realtime — todo en uno |
| ADR-6 | **Token de GitHub server-side only** | El cliente nunca ve el token. Reduce superficie de ataque |
| ADR-7 | **Cache por SHA** (inmutable) | Trees y blobs identificados por SHA no cambian — cache perfecto, sin invalidación |
| ADR-8 | **Webhooks para invalidación** | En vez de polling, GitHub nos notifica cuando un repo cambia |
| ADR-9 | **Changeset pattern para batch writes** | Acumular cambios → un commit. Usa Git Trees API para atomicidad |
| ADR-10 | **Expo Push** para notificaciones | Abstrae APNs/FCM, integrado con Expo, sin servidor adicional |

---

## 14. Fuera de Alcance (v1)

- Soporte para GitLab o Bitbucket
- Merge de branches / Pull Requests
- CI/CD pipelines
- Code review / line comments
- Colaboración real-time entre usuarios
- Soporte offline (editar sin conexión)

---

*Documento vivo — se actualizará conforme avance el diseño detallado.*
