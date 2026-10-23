# SPEC — OpenRoadmap (Versión Refinada)

> **Qué es este documento:** Versión refinada y formalizada de la SPEC original de clase entregada el 21/10/2026.
> Este documento resuelve los vacíos de especificación del cliente/docente, establece los límites del producto y fundamenta cada decisión de diseño para que el sistema sea inequívoco y auditable.
> **Historia:** El primer commit del repositorio contiene la SPEC original intacta (`commit 1fd4c7e`). Esta versión documenta su evolución.

---

## 1. Identidad del producto

- **Nombre del producto:** OpenRoadmap
- **Una línea:** Plataforma web comunitaria para estudiantes y desarrolladores donde descubrir cursos gratuitos de calidad auditada y organizar rutas personales de aprendizaje progresivo.

---

## 2. Problema

Hoy en día, el material educativo gratuito disponible en internet (YouTube, plataformas abiertas, blogs, repositorios) se encuentra profundamente atomizado y desorganizado. Quien busca aprender tecnología se enfrenta a tres fricciones críticas:
1. **Falta de curaduría y obsolescencia:** Miles de cursos tienen enlaces caídos, versiones obsoletas de frameworks o no aclaran si entregan certificación, requieren examen previo o cuentan con cuestionarios interactivos.
2. **Asimetría de confianza:** En las plataformas comerciales, las valoraciones suelen estar sesgadas por marketing. Rara vez se sabe si quien recomienda o comparte un recurso realmente lo cursó y completó.
3. **Parálisis por sobreinformación y falta de secuencia:** Tener una lista de enlaces no equivale a aprender. Los estudiantes carecen de una herramienta simple para secuenciar cursos en un roadmap coherente con seguimiento de su progreso personal (pendiente, en curso, completado).

OpenRoadmap resuelve esta problemática combinando el poder del **crowdsourcing comunitario** con un **proceso riguroso de validación previa** y una **suite de roadmaps personales**, garantizando que el catálogo público solo contenga recursos accesibles, legítimos y catalogados en detalle.

---

## 3. Visión a 12 meses

Convertirse en el punto de encuentro y referencia abierto para comunidades técnicas de habla hispana, donde más de 10.000 estudiantes colaboren auditando recursos educativos libres, reduciendo la deserción en el autoaprendizaje y permitiendo a cualquier persona trazar una ruta formativa clara hacia su inserción profesional.

---

## 4. Alcance

### Dentro del alcance (In-Scope — MVP Instancia B)
- **Catálogo comunitario:** Exploración pública de cursos gratuitos con paginación obligatoria y filtros facetados por categoría, tema, certificación, examen y aval institucional.
- **Aporte comunitario de cursos:** Formulario de alta para usuarios registrados que captura URL activa, categoría cerrada, tema, título, versión, año, docentes/disertantes, flags de evaluación (cuestionarios, examen, certificado, aval) y la declaración jurada obligatoria: *"¿Usted tomó este curso?"*.
- **Ciclo de validación y moderación:** Cola de revisión donde usuarios con rol moderador auditan los aportes antes de que ingresen al catálogo público, aprobándolos o rechazándolos con motivo justificado.
- **Roadmap personal de aprendizaje:** Capacidad para que cada estudiante incorpore cursos del catálogo a su ruta personal, establezca el orden de estudio, actualice su estado de avance (`pendiente`, `en_progreso`, `completado`) y registre notas de aprendizaje.
- **Autenticación y seguridad robusta:** Registro de usuario, inicio de sesión protegido con **rate limiting** estricto por IP y correo, almacenamiento seguro de contraseñas mediante **bcrypt (costo 12)** y flujo completo de recuperación de contraseña vía tokens temporales criptográficos de 15 minutos.
- **Experiencia de usuario inspirada en Google:** Interfaz construida bajo principios de **Material Design 3**, responsive con estrategia **Mobile-First con mejora progresiva** (estilos base sin media queries), menú sándwich accesible y soporte completo de accesibilidad WCAG 2.1 AA.

### Fuera del alcance (Out-of-Scope explícito)
- **Cursos pagos y pasarelas de cobro:** El sistema rechaza cualquier curso con costo monetario. No se integran pasarelas (Stripe, MercadoPago).
- **Hosting de contenido multimedia:** La plataforma no almacena archivos de video ni PDFs de cursos; únicamente persiste y valida el hipervínculo activo hacia la plataforma emisora original.
- **Emisión de certificados propios:** OpenRoadmap no emite certificados propios de aprobación; únicamente releva si el proveedor externo emite certificación válida.
- **Aplicación móvil nativa:** Se desarrolla exclusivamente como Single Page Application (SPA) web responsive adaptada a navegadores móviles y de escritorio.
- **Mensajería instantánea y foros de discusión complejos:** La colaboración se centra en la curaduría de cursos y feedback de moderación; no se incluye chat en tiempo real ni hilos anidados.

---

## 5. Reglas de producto clave

| # | Regla de Producto | Origen | Justificación / Impacto |
|---|-------------------|--------|--------------------------|
| **BR-001** | Un curso aportado ingresa siempre en estado `en_revision` y **solo** pasa a `publicado` en el catálogo comunitario tras la aprobación explícita de un moderador. | SPEC de clase | Evita spam, enlaces caídos, contenido malicioso o promociones comerciales no gratuitas. |
| **BR-002** | Quien aporta un curso debe declarar obligatoriamente si él/ella completó o cursó el contenido (`tomo_el_curso: boolean`). | SPEC de clase | Transparencia comunitaria: pondera la confiabilidad del aporte frente a otros estudiantes. |
| **BR-003** | Cada usuario tiene un único roadmap personal activo, privado y editable exclusivamente por su propietario. | Decisión de diseño | Foco en la experiencia individual de aprendizaje sin complejidad prematura de permisos compartidos. |
| **BR-004** | No se permite la carga de URLs duplicadas en estado activo (`en_revision` o `publicado`). | Decisión de diseño | Evita fragmentación de reseñas y duplicidad en el catálogo. |
| **BR-005** | La paginación es obligatoria en toda vista de listado o consulta de API (`page`, `page_size`), con un tamaño predeterminado de 12 tarjetas en frontend y máximo 50 por consulta. | SPEC de clase | Garantiza rendimiento predecible y carga rápida en dispositivos móviles con bajo ancho de banda. |

---

## 6. Stack tecnológico registrado (Versiones Exactas)

| Capa | Tecnología | Versión Exacta | Justificación |
|------|------------|----------------|---------------|
| **Frontend** | React + Vite + pnpm | `React 19.0.0` · `Vite 6.0.0` · `pnpm 9.15.0` | React 19 optimiza el renderizado con el nuevo compilador; pnpm asegura reproducibilidad estricta de lockfile y bajo consumo de disco. |
| **Backend** | FastAPI + uv | `FastAPI 0.115.6` · `uv 0.5.11` · `Python 3.12-slim` | FastAPI entrega tipado estricto con Pydantic v2 y alto rendimiento asíncrono; uv gestiona paquetes y entornos virtuales a velocidad extrema en C/Rust. |
| **ORM / Acceso** | SQLAlchemy + Alembic | `SQLAlchemy 2.0.36` · `psycopg 3.2.3` (async) | Patrón Data Mapper moderno con tipado completo, soporte nativo de transacciones y migraciones reproducibles. |
| **Base de Datos** | PostgreSQL | `PostgreSQL 18.6` | Motor relacional estándar exigido por la SPEC de clase, con integridad referencial estricta y consultas indexadas. |
| **Seguridad** | Passlib / Bcrypt + PyJWT | `bcrypt 4.2.1` · `PyJWT 2.10.1` · `slowapi 0.1.9` | Cifrado robusto con salt dinámico; tokens JWT firmados criptográficamente; rate limiting basado en leaky bucket en memoria/Redis. |
| **Infraestructura (dev)** | Docker + Docker Compose | `Docker Engine 27.x` · `Compose v2.x` | Orquestación reproducible en un solo comando (`docker compose up`) garantizando que PostgreSQL, Backend y Frontend corran sin divergencia ambiental. |

---

## 7. Restricciones técnicas heredadas de la SPEC de clase

| # | Restricción Oficial | Cumplimiento e Implementación |
|---|---------------------|-------------------------------|
| **1** | **Mobile-First con Mejora Progresiva:** estilos base sin media queries; `@media (min-width: ...)` exclusivamente para ampliar/mejorar. | La hoja de estilos base define la UI para pantallas móviles (360px a 767px) en una sola columna con targets táctiles ≥ 44px. Se añaden capas con `@media (min-width: 768px)` para tablets y `@media (min-width: 1024px)` para desktop. |
| **2** | **Paginación obligatoria de todas las consultas y tablas.** | Endpoints con contrato estándar `{ items: [], total: N, page: n, page_size: n, total_pages: m }`. En UI se visualiza barra de paginación numérica y selectores de tamaño. |
| **3** | **Menú sándwich en mobile.** | Componente de navegación responsive: botón hamburguesa con atributos `aria-expanded` y `aria-controls` en pantallas < 768px; barra de navegación fija horizontal en pantallas de escritorio. |
| **4** | **Screaming Architecture.** | La estructura de carpetas tanto en backend (`app/catalogo`, `app/roadmap`, `app/aportes`, `app/identidad`) como en frontend (`src/features/catalogo`, `src/features/roadmap`, `src/features/aportes`) comunica las áreas de negocio, nunca la estructura genérica del framework. |
| **5** | **Apariencia Google / Material Design 3.** | Paleta de colores basada en Google Blue (`#1a73e8`), fondos neutros (`#f8f9fa`), textos de alto contraste (`#202124`), elevaciones limpias con sombras suaves y tipografía legible (`Roboto`, `system-ui`). |

---

## 8. Vacíos de la SPEC de clase y decisiones tomadas

| # | Vacío Detectado en la SPEC Original | Opciones Consideradas | Decisión Tomada | Justificación y Dónde se Desarrolla |
|---|-------------------------------------|-----------------------|-----------------|--------------------------------------|
| **V-01** | ¿Quién y cómo se validan los cursos aportados? | A) Auto-aprobación comunitaria por votos.<br>B) Aprobación instantánea sin filtro.<br>C) Cola de moderación con rol específico. | **Opción C:** Se crea el rol `moderador` que visualiza la cola de aportes en `/validar`, audita la URL activa y aprueba/rechaza con un clic. | Evita degradación del catálogo por enlaces maliciosos o spam. Detallado en `docs/PRD.md` (§7 BR-001) y `docs/TRD.md` (§2 TD-001). |
| **V-02** | ¿Qué modelo de roles y permisos maneja el sistema? | A) Solo usuario único sin roles.<br>B) Roles jerárquicos: `estudiante` y `moderador` (con capacidades de administración). | **Opción B:** Roles discretos en la entidad usuario. `estudiante` por defecto al registrarse; `moderador` habilitado para validar cursos. | Mantiene simplicidad conceptual sin requerir un motor ACL complejo. Detallado en `docs/BackendSchema.md` (§1.2). |
| **V-03** | ¿Qué implica exactamente "incluir un curso en mi catálogo/roadmap"? | A) Marcar una lista plana de favoritos (bookmarking).<br>B) Entidad de asociación con secuencia, estado y notas personales. | **Opción B:** Tabla intermedia `roadmap_items` vinculando usuario y curso, con campo `orden` (para secuenciar pasos), `estado` (`pendiente`, `en_progreso`, `completado`) y `notas`. | Permite transformar un listado estático en un plan de acción formativo real. Detallado en `docs/BackendSchema.md` (§1.2) y `docs/APPFlow.md` (§4). |
| **V-04** | ¿Cómo se realiza la búsqueda y filtrado en el catálogo? | A) Solo búsqueda de texto libre.<br>B) Solo dropdowns fijos.<br>C) Búsqueda combinada full-text con filtros facetados multi-criterio. | **Opción C:** Endpoint unificado con parámetros `q` (búsqueda en título, tema y docentes) y filtros por `categoria_id`, `extiende_certificado`, `requiere_examen`, `tiene_cuestionarios`. | Brinda una experiencia de descubrimiento fluida similar a Google Search. Detallado en `docs/BackendSchema.md` (§3.2). |
| **V-05** | ¿Cómo prevenir duplicidad de cursos de distintas plataformas? | A) Permitir duplicados indiscriminadamente.<br>B) Validación de URL normalizada con índice `UNIQUE` condicional en base de datos. | **Opción B:** Normalización de URL (eliminación de trailing slash y parámetros UTM) con constraint de unicidad sobre cursos activos. Si ya existe, se informa al usuario. | Evita dispersión de aportes sobre un mismo recurso educativo. Detallado en `docs/PRD.md` (§7 BR-004). |
| **V-06** | ¿Cuál es la política y mecanismo de rate limiting en autenticación? | A) Sin límite en entorno local.<br>B) Rate limiting por IP y endpoint en middleware con leaky bucket (5 req/min). | **Opción B:** Implementación con `slowapi` limitando `POST /api/v1/auth/login` a 5 peticiones por minuto por IP, respondiendo `429 Too Many Requests`. | Cumple con la exigencia de seguridad de la SPEC de clase contra ataques de fuerza bruta. Detallado en `docs/TRD.md` (§2 TD-003). |
| **V-07** | ¿Cómo se implementa el ciclo de recuperación de contraseña sin servicio SMTP externo obligatorio en dev? | A) Enlace mágico directo sin token.<br>B) Generación de token firmado (HMAC/JWT) con expiración de 15 min, impreso en log de backend en modo dev y expuesto en endpoint de confirmación. | **Opción B:** Flujo completo de dos pasos: `solicitar-recuperacion` (emite token temporal y simula envío en log) y `restablecer-password` (valida token y actualiza hash). | Permite probar el ciclo de punta a punta en desarrollo local sin depender de credenciales SMTP de terceros. Detallado en `docs/APPFlow.md` (§4). |

---

## 9. Registro histórico de cambios

| Fecha | Versión | Autor | Detalle del Cambio | Motivo |
|-------|---------|-------|--------------------|--------|
| 20/10/2026 | 0.1 | Cátedra Desarrollo Software | Borrador inicial en pizarrón del aula. | Relevamiento inicial de requisitos. |
| 21/10/2026 | 1.0 | Cátedra Desarrollo Software | Entrega de SPEC oficial formalizada (`SPEC.md`). | Base contractual para la práctica de la materia. |
| 23/10/2026 | 2.0 | Valentín García Devrient (33800) | Refinamiento formal: resolución de vacíos (V-01 a V-07), especificación de versiones exactas, límites de alcance y reglas de negocio. | Cumplimiento del gate de la Instancia A. |
