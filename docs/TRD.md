# TRD — Technical Requirements Document — OpenRoadmap

> **Qué es este documento:** Establece el CÓMO técnico de **OpenRoadmap**. Documenta las decisiones de arquitectura, stack tecnológico con versiones exactas, patrón de diseño por capas con **Screaming Architecture**, estructura de directorios para backend y frontend, servicios en contenedores Docker y convenciones de ingeniería.
> **Definition of Done del TRD:** Un desarrollador externo puede clonar el repositorio, comprender la arquitectura en capas sin ambigüedades, levantar el entorno dev con `docker compose up` y desarrollar nuevas funcionalidades respetando las mismas pautas de calidad y diseño de software.

---

## 1. Stack y Versiones Exactas

| Capa / Subsistema | Tecnología Elegida | Versión Exacta | Justificación Técnica |
|-------------------|--------------------|----------------|-----------------------|
| **Frontend SPA** | React + Vite + pnpm | `React 19.0.0` · `Vite 6.0.0` · `pnpm 9.15.0` | React 19 introduce el compilador optimizado y gestión nativa de acciones; Vite provee Hot Module Replacement instantáneo; pnpm garantiza instalaciones atómicas deterministas y bajo consumo en disco. |
| **Backend API** | FastAPI + uv | `FastAPI 0.115.6` · `uv 0.5.11` · `Python 3.12-slim` | FastAPI ofrece soporte nativo asíncrono, validación estricta con Pydantic v2 y generación automática de OpenAPI/Swagger; `uv` provee resolución de dependencias ultraveloz. |
| **ORM y Migraciones** | SQLAlchemy + Alembic | `SQLAlchemy 2.0.36` · `Alembic 1.14.0` | Patrón Data Mapper moderno que desacopla los modelos de dominio de la persistencia; Alembic provee migraciones de esquema versionadas y reproducibles. |
| **Driver de Base de Datos** | psycopg 3 (async) | `psycopg 3.2.3` | Driver PostgreSQL nativo de alto rendimiento con soporte nativo de I/O asíncrono (`asyncpg` / `psycopg3`). |
| **Base de Datos** | PostgreSQL | `PostgreSQL 18.6` | Motor relacional estándar exigido por la SPEC, garantizando integridad referencial ACID, índices B-Tree y tipos de datos nativos robustos. |
| **Seguridad y Criptografía** | Bcrypt + PyJWT | `bcrypt 4.2.1` · `PyJWT 2.10.1` | Hash seguro de contraseñas con factor de trabajo 12 y salts aleatorios; tokens JWT firmados digitalmente con clave simétrica HS256. |
| **Rate Limiting** | Slowapi | `slowapi 0.1.9` | Middleware de rate limiting para ASGI/FastAPI basado en el algoritmo Leaky Bucket para protección contra ataques de fuerza bruta. |
| **Infraestructura Dev** | Docker + Docker Compose | `Docker 27.x` · `Compose v2.x` | Orquestación contenerizada reproducible con aislamiento de red para dev (`postgres`, `backend`, `frontend`). |

---

## 2. Decisiones Técnicas (Trade-Offs y Fundamentación)

### TD-001: Estrategia de Autenticación y Gestión de Sesión
- **Alternativas consideradas:**
  1. Sesiones tradicionales stateful almacenadas en servidor con cookies de sesión y Redis.
  2. Autenticación delegada exclusivamente mediante OAuth 2.0 externo (Google/GitHub).
  3. Tokens JWT (JSON Web Tokens) stateless firmados con expiración corta (24 horas).
- **Decisión elegida:** **Tokens JWT stateless (Opción 3)** transmitidos mediante encabezado HTTP `Authorization: Bearer <token>`.
- **Por qué:** Permite desacoplar completamente el backend REST del frontend SPA en Vite; no requiere sincronización de estado de sesión en memoria distribuida en el entorno local dev y facilita la verificación de roles (`estudiante`, `moderador`) de forma directa en el payload del token.

### TD-002: Estrategia de Paginación en Listados
- **Alternativas consideradas:**
  1. Paginación basada en cursores (`cursor-based pagination` con `id > last_id`).
  2. Paginación basada en desplazamiento (`offset-based pagination` con `page` y `page_size`).
- **Decisión elegida:** **Paginación Offset-based estándar (Opción 2)**: parámetros `page=1` y `page_size=12`, con metadata de total en respuesta.
- **Por qué:** Los catálogos educativos requieren navegación no lineal (ir directamente a la página 3 o a la última página) y mostrar el total de cursos disponibles para dar contexto al estudiante. Dado el volumen estimado del catálogo comunitario para el MVP (< 50.000 registros), el impacto de rendimiento de `OFFSET` es despreciable y otorga máxima compatibilidad con controles de paginación numérica exigidos por UX.

### TD-003: Formato Estándar de Respuestas de Error
- **Alternativas consideradas:**
  1. Respuestas de error heterogéneas por defecto de FastAPI (`{"detail": "..."}`).
  2. Envoltorio propio arbitrario (`{"success": false, "data": null, "err": "..."}`).
  3. Estándar de la industria **RFC 7807 (Problem Details for HTTP APIs)**.
- **Decisión elegida:** **RFC 7807 Problem Details (Opción 3)** con Content-Type `application/problem+json`.
- **Por qué:** Proporciona un contrato de error estándar de la industria, estructurado y semántico, con campos unívocos: `type`, `title`, `status`, `detail` e `instance`. Esto permite al frontend React manejar los errores de red y validación de forma homogénea mediante interceptores.

### TD-004: Modelado del Roadmap Personal
- **Alternativas consideradas:**
  1. Arreglo plano de IDs de cursos en una columna JSONB dentro de la tabla `usuarios`.
  2. Tabla de relación intermedia `roadmap_items` con atributos de dominio (`orden`, `estado`, `notas`).
- **Decisión elegida:** **Tabla intermedia relacional `roadmap_items` (Opción 2)** con clave foránea a `usuarios` y `cursos`.
- **Por qué:** Mantiene la normalización relacional de PostgreSQL; permite indexar por `(usuario_id, orden)` para consultas ordenadas eficientes, aplicar constraint `UNIQUE(usuario_id, curso_id)` para impedir duplicados y registrar estados individuales (`pendiente`, `en_progreso`, `completado`) sin manipular payloads JSON complejos en la base de datos.

### TD-005: Mecanismo de Rate Limiting en Autenticación
- **Alternativas consideradas:**
  1. Configuración de rate limit en un reverse proxy externo (Nginx/Traefik).
  2. Middleware en FastAPI usando `slowapi` con almacenamiento en memoria en entorno dev.
- **Decisión elegida:** **Middleware `slowapi` en FastAPI (Opción 2)** limitando a 5 requests por minuto por dirección IP en `POST /api/v1/auth/login`.
- **Por qué:** Mantiene la infraestructura mínima dev simple (sin requerir un contenedor Nginx adicional antes de la clase del 27/10) y garantiza el cumplimiento estricto del requerimiento de seguridad de la SPEC de clase de forma autónoma en el código del backend.

---

## 3. Arquitectura

### 3.1 Patrón y Capas (Screaming Architecture)

El backend implementa una arquitectura en capas limpias con separación estricta de responsabilidades:

```mermaid
flowchart TD
    subgraph Presentation ["Capa de Presentación"]
        HTTP[Routers FastAPI / Controllers]
        SCHEMAS[Schemas Pydantic - In/Out DTOs]
    end

    subgraph Domain ["Capa de Dominio / Negocio"]
        SVC[Services - Lógica de Negocio Pura y Testeable]
        RULES[Reglas de Dominio y Validaciones]
    end

    subgraph Persistence ["Capa de Persistencia"]
        REPO[Repositories - Acceso a Datos]
        ORM[Modelos SQLAlchemy - Mapeo Relacional]
    end

    DB[(PostgreSQL 18.6)]

    HTTP -->|DTO Validado| SVC
    SVC -->|Entidades de Dominio| REPO
    REPO -->|Consultas SQL| DB
    REPO -->|Retorna| ORM
    ORM -->|Mapeo| SVC
    SVC -->|DTO de Respuesta| SCHEMAS
    SCHEMAS -->|JSON Serializado| HTTP
```

- **Routers / Controllers (`router.py`):** Puntos de entrada HTTP. Se encargan exclusivamente de recibir la petición, validar parámetros de entrada mediante schemas Pydantic, invocar al servicio correspondiente y retornar el código de estado HTTP adecuado. No contienen lógica de negocio.
- **Services (`service.py`):** Albergan las reglas de negocio puras (ej: validación de aportes, cálculo de orden de roadmap, transiciones de estado de cursos). Son funciones puras y clases testeables de forma aislada sin levantar la base de datos (patrón AAA).
- **Repositories (`repository.py`):** Único punto de acceso a la base de datos PostgreSQL. Encapsulan las consultas SQLAlchemy, filtros, ordenamientos y transacciones.
- **Models & Schemas (`models.py`, `schemas.py`):** Modelos ORM para persistencia y modelos Pydantic para tipado estricto en los límites de la API.

---

### 3.2 Estructura de Carpetas Prevista para la Instancia B

La estructura de carpetas implementa **Screaming Architecture**: el árbol refleja inmediatamente el **dominio del problema** (cursos, aportes, roadmap, identidad), no los artefactos del framework.

```text
roadmap-cursos/
├── backend/
│   ├── pyproject.toml               # Configuración uv, dependencias y scripts
│   ├── Dockerfile                   # Contenedor de desarrollo Python 3.12 + uv
│   ├── alembic/                     # Migraciones versionadas de base de datos
│   │   ├── env.py
│   │   └── versions/
│   ├── alembic.ini
│   └── app/
│       ├── main.py                  # Entrypoint FastAPI, CORS, middlewares, lifespan
│       ├── config.py                # Pydantic Settings con variables de entorno
│       ├── database.py              # Engine async SQLAlchemy y Dependency get_db
│       ├── core/                    # Utilidades transversales
│       │   ├── security.py          # Bcrypt hashing y generación/validación JWT
│       │   ├── errors.py            # Handlers RFC 7807 y excepciones de dominio
│       │   └── rate_limit.py        # Configuración de slowapi
│       ├── identidad/               # Dominio: Usuarios, Auth y Recuperación
│       │   ├── router.py
│       │   ├── service.py
│       │   ├── repository.py
│       │   ├── models.py
│       │   └── schemas.py
│       ├── catalogo/                # Dominio: Cursos validados y Categorías
│       │   ├── router.py
│       │   ├── service.py
│       │   ├── repository.py
│       │   ├── models.py
│       │   └── schemas.py
│       ├── aportes/                 # Dominio: Alta de cursos y Cola de Moderación
│       │   ├── router.py
│       │   ├── service.py
│       │   ├── repository.py
│       │   ├── models.py
│       │   └── schemas.py
│       └── roadmap/                 # Dominio: Roadmap personal y seguimiento
│           ├── router.py
│           ├── service.py
│           ├── repository.py
│           ├── models.py
│           └── schemas.py
├── frontend/
│   ├── package.json                 # Scripts pnpm y dependencias
│   ├── vite.config.js               # Proxy dev /api hacia backend:8000
│   ├── Dockerfile                   # Contenedor de desarrollo Node 20-alpine
│   ├── index.html
│   └── src/
│       ├── main.jsx                 # Entrypoint React 19 y montaje en DOM
│       ├── app/                     # Shell global de la SPA
│       │   ├── App.jsx              # Layout principal, Header, Footer
│       │   ├── routes.jsx           # React Router DOM y rutas protegidas
│       │   └── providers.jsx        # Context Providers (Auth, Toast)
│       ├── features/                # Screaming Architecture por feature de negocio
│       │   ├── catalogo/            # Vistas y componentes del catálogo público
│       │   │   ├── components/      # CourseCard, CourseGrid, CourseFilters, Pagination
│       │   │   ├── pages/           # CatalogoPage, CourseDetailPage
│       │   │   ├── hooks/           # useCourses.js
│       │   │   └── api.js           # Fetchers a /api/v1/cursos
│       │   ├── aportes/             # Formulario de aporte y cola de moderación
│       │   │   ├── components/      # AporteForm, ModerationTable
│       │   │   ├── pages/           # AportarPage, ValidarPage
│       │   │   └── api.js
│       │   ├── roadmap/             # Vista de ruta personal de aprendizaje
│       │   │   ├── components/      # RoadmapList, RoadmapItemCard, StatusSelector
│       │   │   ├── pages/           # RoadmapPage
│       │   │   └── api.js
│       │   └── auth/                # Autenticación y recuperación
│       │       ├── components/      # LoginForm, RegisterForm, ResetPasswordForm
│       │       ├── pages/           # LoginPage, RegisterPage, ForgotPasswordPage
│       │       └── api.js
│       ├── components/              # UI Components genéricos y reutilizables
│       │   ├── Button/
│       │   ├── Input/
│       │   ├── Modal/
│       │   ├── Toast/
│       │   ├── Skeleton/
│       │   └── Navbar/              # Menú sándwich mobile + nav desktop
│       └── styles/                  # Tokens globales CSS (Variables de UX-UI)
│           ├── tokens.css           # Colores Material 3, tipografía, espaciado
│           ├── base.css             # Resets y layout mobile-first sin media queries
│           └── utilities.css
├── docker-compose.yml               # Orquestación dev con postgres, backend y frontend
├── .gitignore                       # Ignorados exhaustivos (Python, Node, OS, Docker)
├── README.md                        # Documento de presentación y guía de ejecución
└── docs/                            # Documentación completa de arquitectura
    ├── SPEC.md
    ├── PRD.md
    ├── TRD.md
    ├── UX-UI.md
    ├── APPFlow.md
    ├── BackendSchema.md
    ├── IMPLEMENTATION_PLAN.md
    ├── GIT_WORKFLOW.md
    └── CHECKLIST_ENTREGA.md
```

---

### 3.3 Servicios Docker en Entorno de Desarrollo

El archivo `docker-compose.yml` orquesta 3 servicios interconectados a través de una red interna bridge (`openroadmap-net`):

| Servicio | Imagen / Dockerfile | Puerto Host:Container | Rol y Descripción |
|----------|---------------------|-----------------------|-------------------|
| **postgres** | `postgres:18.6-alpine` | `5432:5432` | Servidor PostgreSQL con volumen persistente `pgdata` y base inicial `openroadmap_dev`. |
| **backend** | `backend/Dockerfile` (`python:3.12-slim` + `uv`) | `8000:8000` | Servidor ASGI FastAPI con `uvicorn app.main:app --reload --host 0.0.0.0 --port 8000` y montaje de volumen en vivo para recarga en caliente. |
| **frontend** | `frontend/Dockerfile` (`node:20-alpine` + `pnpm`) | `5173:5173` | Servidor de desarrollo Vite con Hot Reloading y montaje de volumen de código fuente. |

```mermaid
flowchart LR
    CLIENT[Navegador del Desarrollador] -->|Puerto 5173| FE[Frontend: Vite Dev Server]
    CLIENT -->|Puerto 8000 /api| BE[Backend: FastAPI ASGI]
    FE -.->|Proxy /api| BE
    BE -->|Puerto 5432 (async)| DB[(PostgreSQL 18.6)]
    DB -.-> VOL[(Volumen Persistente: pgdata)]
```

---

## 4. Convenciones de Ingeniería

### 4.1 Convención de Commits y Versionado
- Estándar estricto **Conventional Commits**:
  - `docs:` Modificaciones o altas en la documentación de `/docs` o `README.md`.
  - `feat:` Incorporación de una nueva funcionalidad visible o endpoint.
  - `fix:` Corrección de un defecto o bug reportado.
  - `refactor:` Modificación de código interno sin alteración de comportamiento funcional.
  - `docker:` Ajustes en Dockerfiles, Compose o configuraciones de contenedor.
  - `test:` Incorporación o ajuste de pruebas automatizadas.
  - `chore:` Tareas rutinarias, configuración de linters o actualización de dependencias.
- **Flujo de Git:** GitHub Flow documentado exhaustivamente en `docs/GIT_WORKFLOW.md` (ramas de funcionalidad/documentación con merge explícito a `main`).

---

### 4.2 Formato Estándar de Errores de API (RFC 7807)

Todas las respuestas con código HTTP 4xx o 5xx utilizan la estructura canónica:

```json
{
  "type": "https://openroadmap.com/errors/conflict-duplicate-url",
  "title": "Conflicto de recurso duplicado",
  "status": 409,
  "detail": "El curso con la URL especificada ya fue registrado y se encuentra en revisión.",
  "instance": "/api/v1/aportes"
}
```

En caso de errores de validación de formulario (HTTP 422), se incluye el detalle de campos observados:

```json
{
  "type": "https://openroadmap.com/errors/validation-error",
  "title": "Error de validación de entrada",
  "status": 422,
  "detail": "Uno o más campos del formulario no cumplen con el formato exigido.",
  "instance": "/api/v1/auth/registro",
  "invalid_params": [
    {
      "field": "password",
      "reason": "La contraseña debe contener al menos 8 caracteres"
    }
  ]
}
```

---

### 4.3 Variables de Entorno Requeridas

| Variable de Entorno | Valor Ejemplo (Desarrollo) | Obligatoria | Descripción |
|---------------------|----------------------------|-------------|-------------|
| `ENVIRONMENT` | `development` | Sí | Define el modo de ejecución (dev/test/prod). |
| `DATABASE_URL` | `postgresql+psycopg://postgres:postgres@postgres:5432/openroadmap_dev` | Sí | Cadena de conexión asíncrona a la base PostgreSQL. |
| `JWT_SECRET_KEY` | `dev_secret_key_super_segura_para_firmar_jwt_33800` | Sí | Clave simétrica para firmar y validar tokens JWT. |
| `JWT_ALGORITHM` | `HS256` | Sí | Algoritmo criptográfico para la firma de tokens. |
| `ACCESS_TOKEN_EXPIRE_MINUTES` | `1440` (24 horas) | Sí | Tiempo de expiración del token de sesión. |
| `PASSWORD_RESET_EXPIRE_MINUTES`| `15` | Sí | Tiempo de expiración de tokens de recuperación. |
| `CORS_ORIGINS` | `http://localhost:5173,http://127.0.0.1:5173` | Sí | Orígenes permitidos para peticiones cruzadas desde la SPA. |
| `RATE_LIMIT_LOGIN` | `5/minute` | Sí | Expresión de límite para el endpoint de login. |

---

### 4.4 Logging y Manejo Global de Excepciones
- **Logging estructurado:** El backend implementa un logger centralizado basado en la librería estándar de Python formateado en JSON en entornos de integración y texto enriquecido con timestamps en desarrollo.
- Se loguea todo arranque del sistema, transiciones de estado de cursos (`borrador` → `en_revision` → `publicado`), disparos del rate limiter e intentos fallidos de autenticación.
- **Handler global de excepciones:** Captura cualquier excepción no controlada (`Exception`), registrando la traza completa (traceback) en los logs internos y respondiendo al cliente con un RFC 7807 seguro (código HTTP 500) sin exponer detalles de la infraestructura interna ni credenciales.

---

## 5. Requisitos No Funcionales Técnicos y Verificación

| Restricción Oficial de la SPEC | Cómo se Cumple Técnicamente | Dónde se Verifica en Código |
|--------------------------------|-----------------------------|-----------------------------|
| **Paginación obligatoria** | Implementación estricta de parámetros `page: int = Query(1, ge=1)` y `page_size: int = Query(12, ge=1, le=50)` en todos los endpoints de listado; cláusulas `LIMIT` y `OFFSET` calculadas en repositorios. | `backend/app/catalogo/router.py`<br>`backend/app/catalogo/repository.py` |
| **Mobile-First con Mejora Progresiva** | Definición de tokens y estilos globales en `tokens.css` y `base.css` sin ninguna media query para la vista móvil base (360px a 767px); solo se declaran `@media (min-width: 768px)` y `@media (min-width: 1024px)`. | `frontend/src/styles/base.css`<br>`frontend/src/styles/tokens.css` |
| **Menú sándwich en mobile** | Componente `Navbar.jsx` que renderiza un botón hamburguesa con `aria-expanded` y `aria-controls` en pantallas menores a 768px y despliega un drawer accesible; en pantallas ≥ 768px se transforma en barra horizontal. | `frontend/src/components/Navbar/Navbar.jsx` |
| **Screaming Architecture** | Los directorios de código en frontend y backend se nombran por dominio de negocio (`catalogo`, `aportes`, `roadmap`, `identidad`), evitando agrupaciones genéricas como `controllers/` o `pages/` en la raíz. | Árbol de directorios de backend y frontend descripto en §3.2. |
| **Estética Google / Material 3** | Uso de la paleta oficial Google Blue (`#1a73e8`), tipografía `Roboto`, bordes redondeados (radios de 8px a 24px) y sombras sutiles con elevaciones Material. | `frontend/src/styles/tokens.css`<br>`docs/UX-UI.md` |

---

## 6. Matriz de Riesgos Técnicos

| Riesgo Técnico Identificado | Probabilidad | Impacto | Estrategia de Mitigación |
|-----------------------------|--------------|---------|--------------------------|
| **Fricción en migraciones de base de datos entre entornos** | Media | Alto | Uso estricto de scripts de migración versionados con Alembic aplicados automáticamente al inicio del contenedor backend. |
| **Desincronización entre schemas Pydantic y entidades ORM** | Baja | Medio | Separación clara de modelos: DTOs Pydantic dedicados exclusivamente a validación y serialización HTTP; modelos SQLAlchemy mapeados uno a uno. |
| **Bloqueo por URLs caídas o enlaces inválidos** | Media | Medio | Validación sintáctica mediante regex estricto de URLs en Pydantic al enviar el aporte; validación funcional humana por parte del moderador en `/validar`. |
| **Colisiones de estado en el Roadmap personal** | Baja | Bajo | Restricción `UNIQUE(usuario_id, curso_id)` a nivel base de datos y control de transacción atómica en `service.py`. |
