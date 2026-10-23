# SPEC — borrador tal cual quedó en el aula (20/10/2026)

> Preservado **sin corregir** (errores incluidos) como registro histórico.
> La versión oficial y limpia para entregar a los alumnos es [`SPEC.md`](./SPEC.md).

---

WebApp de gesion de comunitaria de cursos

Fucnionalidades principales:
- El usario podra crear y gestion su propio roadmap de cursos
- El usuario podra cargar cursos gratuitos mediante el enlace web, de cualquier plataforma y proveedor. Al cargarlo deberá proporcionar su enlace web activo, su categoria o rubro de una lista, su tema, titulo, vesion, año de edicion, docentes o dicerantes que imparten, si cuenta con cuestionarios, si requiere examen, si extiende certificado, si lo avala un organizacion y demas datos necesarios. El usuario que carga debera informar si el tomo el curso aportado o no. Todos los cursos cargados y validados pasan al catalogo comunitario, a partir de alli cualquier usuario lo puede incluir en su catalogo.
- login con rate limit y encriptacion bcrypt al menos
- register
- recuperacion de password

Frontend: React 19 (ultima version stable), con vite + pnpm 
Backend: Fastapi 0.143.3 (ultima version stable)+ uv
Postgre 18.6 (en su version actual stable)
infra Docker + Docker Compose en entorno de desarrollo

Incluir buenas practicas como:
Diseño Mobile-First con Mejora Progresiva (Progressive Enhancement). [sin usar media queries, y luego usar @media (min-width: ...) para agregar o modificar]estilos a medida que la pantalla es más grande.
paginacion de consultas y tablas
menu sandwichi
Screaming Architecture
