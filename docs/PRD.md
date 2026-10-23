# PRD — Product Requirements Document — OpenRoadmap

> **Qué es este documento:** Define el QUÉ y el POR QUÉ de **OpenRoadmap**. Modela a nuestros usuarios objetivos, establece los requisitos funcionales y no funcionales, prioriza las historias de usuario bajo el esquema P0/P1 y delimita estrictamente el alcance para el desarrollo del MVP de la Instancia B.
> **Definition of Done del PRD:** El documento permite a cualquier desarrollador construir la funcionalidad exacta sin requerir aclaraciones de producto; los criterios de aceptación son verificables de forma unívoca (Given/When/Then); las reglas de negocio están formalizadas y el out-of-scope es tajante.

---

## 1. Resumen ejecutivo

**OpenRoadmap** es una webapp colaborativa orientada a democratizar y estructurar el aprendizaje tecnológico mediante la curaduría comunitaria de cursos gratuitos y la creación de rutas formativas personalizadas. Los estudiantes pueden descubrir cursos auditados mediante búsqueda y filtros facetados, aportar nuevos recursos detallando exhaustivamente sus metadatos formativos (certificación, examen, docentes, aval), auditar aportes mediante una cola de moderación y secuenciar su aprendizaje en un roadmap privado con seguimiento de progreso en tiempo real.

---

## 2. Objetivos y métricas de éxito

| Objetivo Estratégico | Indicador de Éxito / Métrica Observable (MVP) |
|-----------------------|-----------------------------------------------|
| **Curaduría comunitaria de alta calidad** | El 100% de los cursos visibles en el catálogo público cuentan con URL activa verificada, categoría asignada y declaración de toma por parte del aportante. |
| **Utilidad y retención del Roadmap Personal** | Al menos el 80% de los usuarios registrados crean su roadmap y asocian 2 o más cursos durante su primera sesión de uso. |
| **Moderación ágil y libre de spam** | Cero cursos duplicados o enlaces rotos ingresan al catálogo público gracias al filtro de moderación previa y unicidad de URL. |
| **Rendimiento y accesibilidad técnica** | Tiempo de carga inicial de catálogo inferior a 1.5 segundos en conexiones móviles 3G/4G simuladas y cumplimiento de contraste WCAG 2.1 AA en el 100% de las pantallas. |

---

## 3. Personas

### Persona 1: Lucía Gómez — La Estudiante Autodidacta
- **Rol:** Estudiante universitaria de Ingeniería Informática y desarrolladora junior autodidacta (21 años).
- **Contexto:** Estudia en sus ratos libres y fines de semana. Tiene un presupuesto ajustado y depende de cursos gratuitos en YouTube, edX y plataformas abiertas para complementar la facultad. Usa su notebook en casa y su smartphone en el transporte público.
- **Motivaciones:**
  - Quiere encontrar rápidamente recursos confiables sin perder tiempo probando cursos incompletos o con enlaces caídos.
  - Necesita saber de antemano si un curso le otorgará certificado para su CV o si incluye exámenes para evaluar su comprensión.
  - Desea organizar los cursos en una secuencia lógica (ej: *Git → Python básico → FastAPI → Docker*) sin tener decenas de pestañas abiertas en el navegador.
- **Frustraciones actuales:**
  - *"Pierdo horas empezando cursos que resultan estar desactualizados o incompletos."*
  - *"Guardo links en marcadores del navegador o en un bloc de notas, y después no sé cuál iba primero ni por dónde me quedé."*
- **Historias tipo:**
  - *"Necesito ver de un vistazo qué cursos tienen certificado y fueron recomendados por alguien que realmente los cursó."*
  - *"Quiero marcar un curso como 'en progreso' y saber exactamente cuál es el siguiente paso en mi ruta de aprendizaje."*

### Persona 2: Martín Rossi — El Desarrollador y Colaborador Comunitario
- **Rol:** Desarrollador backend semi-senior (27 años) y moderador voluntario de comunidades de software libre.
- **Contexto:** Consume y evalúa constantemente contenido técnico nuevo. Le gusta compartir recursos valiosos con estudiantes que recién arrancan y participar activamente en la curaduría de la comunidad.
- **Motivaciones:**
  - Dar visibilidad a cursos gratuitos de excelencia creados por docentes independientes u organizaciones sin fines de lucro.
  - Evitar que los recién llegados caigan en trampas comerciales o material de baja calidad.
  - Colaborar en la moderación del catálogo aprobando o rechazando aportes con feedback claro.
- **Frustraciones actuales:**
  - *"En las redes sociales los buenos recursos se pierden en el feed en menos de 24 horas."*
  - *"Muchas plataformas están llenas de spam y enlaces de afiliados que nadie modera."*
- **Historias tipo:**
  - *"Quiero aportar un curso con su versión, año y docentes para que los demás sepan exactamente qué van a aprender."*
  - *"Como moderador, quiero una cola clara de revisión para auditar enlaces y aprobar aportes en un par de clics."*

---

## 4. User Stories

> Clasificación de prioridad:
> - **P0 (Must Have - Indispensable para el MVP de la Instancia B):** Sin estas historias el sistema no cumple la SPEC básica ni se considera entregable.
> - **P1 (Should Have):** Enriquecen la experiencia y completan flujos avanzados.

| ID | Prioridad | Historia de Usuario | Criterios de Aceptación (Dado / Cuando / Entonces) |
|----|-----------|---------------------|---------------------------------------------------|
| **US-001** | **P0** | **Como** visitante o usuario registrado,<br>**quiero** visualizar el catálogo comunitario de cursos paginado con tarjetas informativas,<br>**para** explorar y descubrir contenido educativo gratuito de calidad. | - **Dado** que accedo a `/catalogo`, **veo** la primera página de cursos con máximo 12 elementos por página.<br>- **Dado** que existen más de 12 cursos, **veo** el control de paginación con número de página actual y botones Siguiente/Anterior.<br>- **Dado** que no hay cursos que coincidan con la búsqueda o filtro, **veo** un estado vacío (*empty state*) con ilustración y un botón para sugerir o aportar un curso.<br>- **Dado** que los cursos están cargando, **veo** esqueletos de carga (*skeletons*) sin saltos de layout. |
| **US-002** | **P0** | **Como** usuario autenticado,<br>**quiero** aportar un curso gratuito mediante un formulario con metadatos y declaración de toma,<br>**para** sumarlo a la cola de validación comunitaria. | - **Dado** que estoy autenticado y accedo a `/aportar`, **veo** los campos obligatorios: URL, categoría, tema, título, versión, año, docentes, flags (cuestionarios, examen, certificado, aval) y checkbox *"Yo tomé este curso"*.<br>- **Cuando** envío el formulario con datos válidos y URL activa, **el sistema** registra el curso en estado `en_revision` y muestra un toast de éxito.<br>- **Cuando** intento enviar una URL ya existente en el sistema, **el sistema** bloquea el envío y muestra un mensaje de error inline indicando que el curso ya fue registrado. |
| **US-003** | **P0** | **Como** usuario autenticado,<br>**quiero** agregar cursos del catálogo a mi roadmap personal y gestionar su estado de avance,<br>**para** estructurar mi plan de estudio paso a paso. | - **Dado** que navego una tarjeta de curso en `/catalogo`, **veo** el botón *"Agregar a mi Roadmap"*.<br>- **Cuando** hago clic en agregar, **el curso** se incorpora a `/roadmap` en estado `pendiente`.<br>- **Dado** que estoy en `/roadmap`, **puedo** cambiar el estado del curso a `en_progreso` o `completado`.<br>- **Dado** que tengo cursos en mi roadmap, **puedo** modificar su orden secuencial y eliminar cursos que ya no desee cursar. |
| **US-004** | **P0** | **Como** usuario nuevo o recurrente,<br>**quiero** registrarme, iniciar sesión de forma segura y recuperar mi contraseña si la olvido,<br>**para** resguardar mi identidad y mis datos de aprendizaje. | - **Dado** que visito `/registro`, **puedo** crear una cuenta ingresando nombre, email único y contraseña (mínimo 8 caracteres).<br>- **Dado** que visito `/login`, **puedo** autenticarme y recibir un token de sesión seguro.<br>- **Cuando** realizo más de 5 intentos fallidos de login en menos de 1 minuto desde una misma IP, **el sistema** bloquea las peticiones devolviendo HTTP 429.<br>- **Dado** que olvido mi contraseña en `/recuperar-password`, **solicito** un enlace/token temporal válido por 15 minutos para establecer una nueva clave. |
| **US-005** | **P0** | **Como** usuario con rol moderador,<br>**quiero** acceder a una cola de aportes pendientes para aprobar o rechazar cursos,<br>**para** garantizar que solo material legítimo y gratuito ingrese al catálogo público. | - **Dado** que inicio sesión como moderador y accedo a `/validar`, **veo** la lista de cursos en estado `en_revision`.<br>- **Cuando** presiono *"Aprobar"*, **el curso** pasa inmediatamente a estado `publicado` y queda visible en el catálogo comunitario.<br>- **Cuando** presiono *"Rechazar"*, **el sistema** solicita un motivo breve y cambia el estado a `rechazado`, impidiendo su publicación. |
| **US-006** | **P1** | **Como** usuario visitante o registrado,<br>**quiero** acceder a la página de detalle de un curso específico,<br>**para** examinar en profundidad todos sus metadatos antes de decidir cursarlo. | - **Dado** que hago clic en una tarjeta de `/catalogo`, **navego** a `/catalogo/:id`.<br>- **En la pantalla**, **visualizo** título, descripción del tema, versión, año, docentes, plataforma de origen, enlace externo activo y badges de evaluación (cuestionario, examen, certificado, aval).<br>- **Si estoy autenticado**, **veo** el botón interactivo para añadirlo directamente a mi roadmap. |
| **US-007** | **P1** | **Como** usuario,<br>**quiero** filtrar el catálogo por categoría y realizar búsquedas por texto libre,<br>**para** encontrar de manera instantánea cursos específicos de mi interés. | - **Dado** que escribo en la barra de búsqueda en `/catalogo`, **el catálogo** filtra los resultados por coincidencia en título, tema o docentes.<br>- **Dado** que selecciono una categoría del selector, **se muestran** únicamente los cursos pertenecientes a ese rubro.<br>- **Dado** que activo el filtro *"Con Certificado"*, **solo** se listan los cursos que entregan certificación. |

---

## 5. Requisitos Funcionales (RF)

| ID | Requisito Funcional | Historia Relacionada | Prioridad |
|----|---------------------|----------------------|-----------|
| **RF-001** | El sistema debe permitir el registro de usuarios con nombre completo, correo electrónico único y contraseña cifrada. | US-004 | P0 |
| **RF-002** | El sistema debe validar credenciales y emitir tokens JWT para sesiones autenticadas. | US-004 | P0 |
| **RF-003** | El sistema debe limitar los intentos de inicio de sesión a un máximo de 5 peticiones por minuto por IP (Rate Limiting). | US-004 | P0 |
| **RF-004** | El sistema debe proveer un flujo de recuperación de contraseña de 2 pasos mediante token criptográfico con expiración de 15 minutos. | US-004 | P0 |
| **RF-005** | El sistema debe exponer el catálogo de cursos comunitarios con estado `publicado`, con paginación numérica obligatoria. | US-001 | P0 |
| **RF-006** | El sistema debe permitir filtrar cursos por categoría (lista cerrada) y metadatos de auditoría (certificado, examen, aval). | US-001, US-007 | P0 |
| **RF-007** | El sistema debe permitir a usuarios autenticados dar de alta aportes de cursos con todos los metadatos exigidos por la SPEC. | US-002 | P0 |
| **RF-008** | El sistema debe exigir la declaración obligatoria booleana *"¿Tomó usted este curso?"* en el formulario de aporte. | US-002 | P0 |
| **RF-009** | El sistema debe asignar por defecto el estado `en_revision` a todo curso recién aportado. | US-002, US-005 | P0 |
| **RF-010** | El sistema debe validar la unicidad de la URL normalizada del curso para impedir duplicados activos. | US-002 | P0 |
| **RF-011** | El sistema debe restringir el acceso a la cola de validación `/validar` exclusivamente a usuarios con rol `moderador`. | US-005 | P0 |
| **RF-012** | El sistema debe permitir al moderador cambiar el estado de un curso a `publicado` o `rechazado`. | US-005 | P0 |
| **RF-013** | El sistema debe permitir a los usuarios autenticados agregar cursos publicados a su roadmap personal (`roadmap_items`). | US-003 | P0 |
| **RF-014** | El sistema debe permitir actualizar el estado del ítem del roadmap (`pendiente`, `en_progreso`, `completado`). | US-003 | P0 |
| **RF-015** | El sistema debe permitir modificar el orden secuencial numérico (`orden`) y remover ítems del roadmap personal. | US-003 | P0 |

---

## 6. Requisitos No Funcionales (RNF)

| ID | Requisito No Funcional | Justificación y Criterio de Verificación |
|----|------------------------|------------------------------------------|
| **RNF-001** | **Mobile-First con Mejora Progresiva:** La maquetación base debe estructurarse sin media queries; el uso de `@media` se reserva exclusivamente para pantallas de mayor resolución. | Verificable en Chrome DevTools a 360px de ancho: sin scroll horizontal, navegación mediante menú sándwich y targets táctiles ≥ 44px. |
| **RNF-002** | **Paginación uniforme en API y UI:** Ningún endpoint de listado devolverá colecciones ilimitadas. | Todas las tablas y cuadrículas respetan el contrato `{ items, total, page, page_size }` con tamaño estándar de 12 tarjetas. |
| **RNF-003** | **Seguridad en almacenamiento de credenciales:** Las contraseñas deben cifrarse con algoritmo **bcrypt** y salt aleatorio de costo 12. | Prohibido almacenar contraseñas en texto claro o con hashes vulnerables (MD5, SHA1). Verificable en base de datos. |
| **RNF-004** | **Accesibilidad Web (WCAG 2.1 Nivel AA):** Contraste mínimo de 4.5:1 en textos estándar y 3:1 en componentes interactivos; navegación por teclado accesible. | Verificable con Lighthouse / axe DevTools obteniendo score de accesibilidad ≥ 90. |
| **RNF-005** | **Rendimiento de backend:** El 95% de las respuestas de API REST deben completarse en menos de 200 ms bajo carga normal de desarrollo. | Verificable en logs de FastAPI y métricas de red. |
| **RNF-006** | **Despliegue unificado y reproducible:** La aplicación completa (BD, backend y frontend) debe orquestarse en desarrollo mediante un único comando `docker compose up`. | Verificable en un entorno limpio sin dependencias preinstaladas excepto Docker. |

---

## 7. Reglas de Negocio (BR)

| ID | Regla de Negocio | Fuente / Origen | Detalle Operativo |
|----|------------------|-----------------|-------------------|
| **BR-001** | **Aprobación previa obligatoria:** Un curso aportado nunca es visible en el catálogo público hasta que su estado sea `publicado`. | SPEC de clase | El endpoint público `/api/v1/cursos` aplica un filtro estricto e inmutable `WHERE estado = 'publicado'`. |
| **BR-002** | **Declaración jurada de toma de curso:** El usuario que aporta un curso debe declarar expresamente si lo cursó o no (`tomo_el_curso`). | SPEC de clase | Campo obligatorio booleano no nulo. En la UI se muestra como badge informativo ("Aportado por quien completó el curso" / "Aporte referencial"). |
| **BR-003** | **Aislamiento y privacidad del roadmap:** Cada usuario ve y administra únicamente su propio roadmap de aprendizaje. | Decisión de diseño | Las consultas al roadmap filtran automáticamente por el `usuario_id` extraído del token JWT autenticado. |
| **BR-004** | **Unicidad de cursos por URL:** No pueden coexistir dos cursos con la misma URL normalizada en estados activos (`en_revision` o `publicado`). | Decisión de diseño | Si un usuario intenta aportar una URL existente, el sistema responde `HTTP 409 Conflict` informando el ID del curso registrado. |
| **BR-005** | **Prevención de duplicados en roadmap:** Un usuario no puede agregar dos veces el mismo curso a su roadmap. | Decisión de diseño | Restricción `UNIQUE(usuario_id, curso_id)` en la tabla `roadmap_items`. |
| **BR-006** | **Categorías de catálogo cerradas:** Las categorías son gestionadas centralizadamente por el sistema. | SPEC de clase | Dropdown predefinido (ej: Desarrollo Web, Backend, DevOps, Data Science, Mobile, Ciberseguridad, QA/Testing). No se permite la creación libre de categorías por parte del usuario final. |
| **BR-007** | **Expiración de tokens de recuperación:** Los tokens para reset de contraseña tienen una validez máxima de 15 minutos y son de un solo uso. | SPEC de clase | Una vez utilizado el token o transcurridos los 15 minutos, el token queda invalidado automáticamente. |
| **BR-008** | **Restricción de rol moderador:** Las acciones de validación (`aprobar`, `rechazar`) exigen privilegios de moderación. | Decisión de diseño | Los endpoints `/api/v1/aportes/{id}/validar` verifican que el token contenga el rol `moderador`. De lo contrario responden `HTTP 403 Forbidden`. |

---

## 8. Fuera de Alcance (Out of Scope Estricto)

Para garantizar la viabilidad del MVP de cara a la Instancia B y la clase del 27/10, los siguientes componentes quedan formalmente excluidos:
- **Cursos pagos y pasarelas de cobro:** El sistema no procesa pagos, suscripciones ni propinas.
- **Reproductor de video propio o streaming:** No se aloja contenido audiovisual; solo se enlaza a plataformas externas (YouTube, Coursera, plataformas abiertas).
- **Emisión de certificados digitales:** OpenRoadmap registra si el proveedor externo emite certificado, pero no genera constancias ni credenciales propias.
- **Aplicación móvil nativa compilada:** No se generará código para Android ni iOS; se garantiza soporte móvil integral vía navegador web responsive.
- **Foro social y mensajería en tiempo real:** No se incluyen salas de chat, hilos de comentarios anidados ni sistema de mensajería privada entre usuarios.

---

## 9. Supuestos y Decisiones sobre la SPEC de Clase

1. **Gestión de Sesión:** Se asume el uso de tokens **JWT (JSON Web Tokens)** stateless transmitidos mediante el encabezado `Authorization: Bearer <token>` para facilitar el desacople total entre el frontend React y el backend FastAPI.
2. **Envío de Correos en Desarrollo:** En el entorno local de desarrollo Docker, el flujo de recuperación de contraseña no dependerá de un servidor SMTP externo; el enlace y token de recuperación serán impresos en los logs de la consola del backend y devueltos en la respuesta de prueba para permitir testing ágil de punta a punta.
3. **Puntuación y Reseñas:** Para el MVP de la Instancia B se prioriza la declaración de si tomó el curso y la categorización estructurada; los sistemas de ratings numéricos (estrellas) quedan para iteraciones futuras.

---

## 10. Preguntas Abiertas y Respuestas de Diseño

| Pregunta Abierta | Impacto | Decisión de Diseño Adoptada |
|------------------|---------|-----------------------------|
| ¿Qué sucede si un curso publicado deja de estar disponible en internet? | Medio | Los usuarios pueden reportar enlaces rotos (para una fase posterior). En el MVP, el moderador tiene la potestad de cambiar el estado de un curso de `publicado` a `rechazado`/`inactivo`. |
| ¿Qué ocurre con los aportes rechazados? | Bajo | Permanecen en la base de datos con estado `rechazado` y el motivo registrado, visibles únicamente para el moderador y para el autor del aporte a fin de mantener trazabilidad. |
| ¿Puede un usuario desvincular un curso de su roadmap sin perder su historial? | Bajo | Sí, el borrado de un ítem de roadmap es físico sobre la tabla intermedia `roadmap_items`, sin afectar en absoluto al curso en el catálogo general. |
