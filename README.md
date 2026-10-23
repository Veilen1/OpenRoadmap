# OpenRoadmap

> WebApp comunitaria de gestión de cursos y rutas de aprendizaje: catálogo colaborativo de cursos gratuitos de calidad auditada + roadmaps personales de estudio.
>
> **Estado:** 📚 **Documentación formal (Instancia A)** — En espera de aprobación docente para iniciar el desarrollo del MVP (Instancia B).

---

## 🛠️ Stack Tecnológico Oficial

| Capa | Tecnología | Versión Registrada | Rol en la Arquitectura |
|------|------------|--------------------|------------------------|
| **Frontend** | React + Vite + pnpm | `React 19.0.0` · `Vite 6.0.0` · `pnpm 9.15.0` | Single Page Application responsive mobile-first |
| **Backend** | FastAPI + uv | `FastAPI 0.115.6` · `uv 0.5.11` · `Python 3.12-slim` | API REST asíncrona con validación Pydantic v2 |
| **Base de Datos** | PostgreSQL | `PostgreSQL 18.6` | Persistencia relacional con integridad referencial |
| **Infraestructura (dev)** | Docker + Docker Compose | `Docker 27.x` · `Compose v2.x` | Orquestación contenerizada reproducible |

---

## 💡 ¿Qué hace OpenRoadmap?

OpenRoadmap resuelve la atomización y falta de confianza en la educación técnica gratuita en internet:
1. **Catálogo Comunitario:** Cualquier usuario registrado puede aportar un curso gratuito indicando su enlace activo, categoría, tema, título, versión, año, docentes, flags de evaluación y la declaración jurada sobre si completó el curso.
2. **Moderación Previa:** Los aportes son auditados por usuarios con rol moderador antes de ingresar al catálogo general, garantizando enlaces activos y libres de spam.
3. **Roadmap Personal:** Los estudiantes exploran el catálogo y construyen su propia ruta de aprendizaje, organizando el orden secuencial de estudio y dando seguimiento a su progreso (`pendiente`, `en_progreso`, `completado`).

---

## 📖 Índice de Documentación de Ingeniería (Instancia A)

| Documento | Responde la Pregunta | Enlace Directo |
|-----------|----------------------|----------------|
| **SPEC (Refinada)** | ¿Qué nos pidieron construir, qué límites fijamos y qué decidimos sobre los vacíos? | [docs/SPEC.md](docs/SPEC.md) |
| **PRD** | ¿Para quién construimos, qué problema resuelve, qué historias P0 integran el MVP y qué queda fuera? | [docs/PRD.md](docs/PRD.md) |
| **TRD** | ¿Cómo lo construimos técnicamente, con qué arquitectura por capas y bajo qué convenciones? | [docs/TRD.md](docs/TRD.md) |
| **UX-UI** | ¿Cómo se ve, se siente y se comporta la interfaz inspirada en Google y con Mobile-First? | [docs/UX-UI.md](docs/UX-UI.md) |
| **APPFlow** | ¿Qué pantallas existen, cómo se navega el sitemap y cómo fluyen los usuarios de punta a punta? | [docs/APPFlow.md](docs/APPFlow.md) |
| **BackendSchema** | ¿Qué modelo de datos persistimos en PostgreSQL 18.6 y qué endpoints expone la API REST? | [docs/BackendSchema.md](docs/BackendSchema.md) |
| **Implementation Plan** | ¿En qué orden modular construimos y qué condiciones definen que cada tarea está terminada? | [docs/IMPLEMENTATION_PLAN.md](docs/IMPLEMENTATION_PLAN.md) |
| **Git Workflow** | ¿Qué estrategia de branching usamos, cómo nombramos ramas y qué convención de commits rige? | [docs/GIT_WORKFLOW.md](docs/GIT_WORKFLOW.md) |
| **Checklist de Entrega** | ¿Se cumplen todos los criterios de revisión del docente punto por punto? | [docs/CHECKLIST_ENTREGA.md](docs/CHECKLIST_ENTREGA.md) |

---

## 🔍 Cómo Revisar este Repositorio

- **Renderizado automático:** Todos los documentos están escritos en GitHub Flavored Markdown (GFM) y renderizan de forma nativa en GitHub.
- **Diagramas en código (Mermaid):** Los diagramas de arquitectura, ERD, máquina de estados, sitemap y secuencias están embebidos en bloques ` ```mermaid ` y se visualizan directamente sin extensiones externas.
- **Previsualización local:** En VS Code mediante `Ctrl+Shift+V` o instalando la extensión recomendada *Markdown All in One*.

---

## 📊 Estado de Avance del Proyecto

| Instancia | Entregable / Hito | Estado |
|-----------|-------------------|--------|
| **Instancia A — Documentación** | Repositorio formal + 7 documentos de ingeniería + Git Workflow | ✅ **Completada y lista para revisión** |
| **Instancia B — Desarrollo del MVP** | Ejecución del Implementation Plan (`docker compose up`) | ⏳ *Pendiente de aprobación docente (Gate)* |
| **Clase 27/10 — Aula** | Testing unitario e integración, Docker multi-stage y gestión del cambio | ⏳ *Programado según calendario* |

---

## 🌿 Flujo de Trabajo en Git y Versionado

- **Estrategia de Ramas:** Variante formal de **GitHub Flow** documentada en [docs/GIT_WORKFLOW.md](docs/GIT_WORKFLOW.md).
- **Historia Observable:** La rama `main` contiene el commit inicial con la `SPEC.md` original tal cual fue recibida (`commit 1fd4c7e`), y la suite completa de documentación fue elaborada en la rama `docs/instancia-a` e integrada a `main` mediante merge explícito `--no-ff`.
- **Conventional Commits:** El 100% del historial sigue la convención estándar (`docs:`, `feat:`, `fix:`, `docker:`, `test:`, `chore:`).

---

## 👤 Información del Alumno

- **Autor:** Valentín García Devrient
- **Legajo:** 33800
- **Comisión / Sección:** S34 — Ciclo Lectivo 2026
- **Materia:** Desarrollo de Software — UTN Facultad Regional La Plata
