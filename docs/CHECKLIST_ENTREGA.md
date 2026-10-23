# Checklist de Verificación y Autocontrol de Entrega — Instancia A

> **Propósito:** Lista de control formal de verificación previa al envío del correo de solicitud de aprobación docente. Un solo ítem en estado no verificado o incumplido equivale a una observación.

---

## 1. Reglas de Oro y Gate de Aprobación

- [x] **Gate de código respetado:** Cero archivos fuente de la aplicación (`.py`, `.js`, `.jsx`, `.ts`, lockfiles, `node_modules/`, `.venv/`). El repositorio contiene exclusivamente documentación, `README.md` y `.gitignore`.
- [x] **Todo se versiona en Git:** El repositorio está inicializado en la raíz del proyecto y contiene historial desde el primer momento.
- [x] **Primer commit con SPEC original intacta:** El commit inicial (`commit 1fd4c7e`) contiene exclusivamente la `SPEC.md` tal cual fue entregada por la cátedra el 21/10/2026.
- [x] **Flujo de Git observable y documentado:** El desarrollo de la documentación se realizó en la rama `docs/instancia-a` con estrategia GitHub Flow documentada en `docs/GIT_WORKFLOW.md` y se integra a `main` con merge `--no-ff`.
- [x] **100% Conventional Commits:** Todos los commits del historial respetan el estándar formal (`docs: ...`).

---

## 2. Estructura de Archivos del Repositorio

- [x] `README.md` en la raíz, basado en la plantilla oficial, con presentación del producto, stack, enlaces directos a los 7 documentos e información del alumno.
- [x] `.gitignore` en la raíz, exhaustivo y preparado para React 19 + Vite + pnpm, FastAPI + uv + Python, PostgreSQL y Docker.
- [x] `docs/SPEC.md`: Versión refinada que responde qué nos pidieron construir y qué decidimos sobre los 7 vacíos.
- [x] `docs/PRD.md`: Documento de requisitos de producto con resumen, métricas, 2 personas completas, historias P0/P1 con criterios Dado/Cuando/Entonces, RF, RNF, reglas de negocio y out-of-scope.
- [x] `docs/TRD.md`: Documento técnico con stack y versiones exactas, 5 decisiones técnicas justificadas, arquitectura en capas, árbol Screaming Architecture para Instancia B, servicios Docker dev, convenciones de API (RFC 7807) y riesgos.
- [x] `docs/UX-UI.md`: Guía de estilos inspirada en Google / Material 3, paleta de colores con contraste WCAG AA verificado, tipografía Roboto, tokens CSS listos para copiar, estrategia Mobile-First con mejora progresiva, componentes con todos sus estados y accesibilidad.
- [x] `docs/APPFlow.md`: Mapa del sitio en Mermaid, tabla de rutas con roles, fichas completas por pantalla con estados y endpoints asociados, y 4 flujos de usuario de punta a punta.
- [x] `docs/BackendSchema.md`: Diagrama ER en Mermaid, especificación de tablas en PostgreSQL 18.6 con tipos, PK, FK, constraints e índices, máquina de estados del curso y especificación de API REST `/api/v1`.
- [x] `docs/IMPLEMENTATION_PLAN.md`: Criterio estricto de MVP P0, grafo de dependencias de módulos, desglose de tareas con DoD verificable, tabla de seguimiento de ejecución para Instancia B y estrategia de testing planificada para el 27/10.

---

## 3. Coherencia Transversal entre Documentos (Requisito Crítico)

- [x] **Consistencia de Endpoints:** Todos los endpoints invocados en las fichas y flujos de `APPFlow.md` (`/api/v1/auth/*`, `/api/v1/cursos/*`, `/api/v1/aportes/*`, `/api/v1/roadmap/*`, `/api/v1/categorias`) existen de manera idéntica en `BackendSchema.md`.
- [x] **Consistencia de Requisitos e Historias:** Todas las historias P0 definidas en `PRD.md` (US-001 a US-005) están desglosadas en tareas del MVP en `IMPLEMENTATION_PLAN.md`.
- [x] **Consistencia Visual:** Los componentes de las pantallas en `APPFlow.md` utilizan los tokens y componentes descriptos en `UX-UI.md`.
- [x] **Consistencia de Dominio:** Los módulos de `Screaming Architecture` descriptos en `TRD.md` coinciden con los módulos de negocio de `BackendSchema.md` y las features de frontend.

---

## 4. Renderizado y Formato

- [x] Todos los archivos están escritos en formato Markdown estándar compatible con GitHub (GFM).
- [x] Todos los diagramas Mermaid (`flowchart`, `erDiagram`, `sequenceDiagram`, `stateDiagram-v2`, `gitGraph`) renderizan sin errores de sintaxis.
- [x] Los enlaces entre documentos dentro del repositorio son relativos y funcionales.
- [x] No hay caracteres rotos ni rutas absolutas locales a máquinas individuales.

---

## 5. Datos de Entrega

- **Alumno:** Valentín García Devrient
- **Legajo:** 33800
- **Comisión:** S34 — Ciclo Lectivo 2026
- **Fecha de Verificación:** 23/10/2026
- **Resultado:** ✅ **100% Criterios Cumplidos — Apto para Envío a Revisión**
