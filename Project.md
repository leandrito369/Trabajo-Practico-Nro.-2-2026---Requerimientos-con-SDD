# Project.md — AcadEvents Platform

> Este archivo define el contexto general, arquitectura, stack tecnológico y convenciones del proyecto.
> Todo agente o colaborador que trabaje en una spec **debe leer este archivo antes de implementar cualquier feature**.

---

## 1. Descripción del Proyecto

**AcadEvents** es una aplicación web para la organización y gestión de eventos académicos (cursos, jornadas, congresos, charlas, talleres, etc.).

Permite a grupos de personas:
- Crear y publicar eventos académicos con información detallada.
- Gestionar inscripciones de participantes (autónoma o por el personal del evento).
- Administrar roles (organizador, participante, disertante).
- Acreditar asistencia y generar certificados.
- Recolectar feedback post-evento mediante encuestas.
- Generar informes y agendas de eventos.

La plataforma debe ser **accesible desde cualquier dispositivo** mediante navegador web.

---

## 2. Stack Tecnológico

### Frontend
| Tecnología | Versión mínima | Uso |
|---|---|---|
| React | 18.x | Framework de UI |
| Vite | 5.x | Bundler y dev server |
| Tailwind CSS | 3.x | Estilos utilitarios |
| React Router | 6.x | Navegación SPA |
| Axios | 1.x | Cliente HTTP |
| React Hook Form | 7.x | Manejo de formularios |
| Zod | 3.x | Validación de esquemas en cliente |

### Backend
| Tecnología | Versión mínima | Uso |
|---|---|---|
| Node.js | 20.x LTS | Runtime |
| Express | 4.x | Framework HTTP |
| Prisma | 5.x | ORM y migraciones |
| PostgreSQL | 15.x | Base de datos relacional |
| JWT (jsonwebtoken) | 9.x | Autenticación stateless |
| bcrypt | 5.x | Hash de contraseñas |
| Zod | 3.x | Validación de esquemas en servidor |
| nodemailer | 6.x | Envío de emails (confirmaciones, certificados) |

### Testing
| Tecnología | Uso |
|---|---|
| Vitest | Tests unitarios del frontend |
| Jest | Tests unitarios del backend |
| Supertest | Tests de integración de la API REST |

### Infraestructura (desarrollo local)
| Herramienta | Uso |
|---|---|
| Docker + Docker Compose | Levantar PostgreSQL en local |
| ESLint + Prettier | Linting y formateo |
| Husky + lint-staged | Hooks de pre-commit |

---

## 3. Estructura de Carpetas del Repositorio

```
/
├── README.md
├── Project.md
├── Contracts.md
├── specs/
│   ├── spec-01-gestion-eventos.md
│   ├── spec-02-inscripcion-participantes.md
│   └── ... (specs adicionales)
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── hooks/
│   │   ├── services/       ← llamadas a la API (axios)
│   │   ├── store/          ← estado global si se usa (Context o Zustand)
│   │   ├── utils/
│   │   └── App.jsx
│   ├── package.json
│   └── vite.config.js
├── backend/
│   ├── src/
│   │   ├── controllers/    ← lógica de cada endpoint
│   │   ├── routes/         ← definición de rutas Express
│   │   ├── middlewares/    ← auth, validación, errores
│   │   ├── services/       ← lógica de negocio
│   │   ├── utils/
│   │   └── app.js
│   ├── prisma/
│   │   ├── schema.prisma   ← modelo de datos central
│   │   └── migrations/
│   ├── tests/
│   └── package.json
└── docker-compose.yml
```

---

## 4. Convenciones de Código

### General
- **Idioma del código:** inglés (variables, funciones, clases, comentarios de código).
- **Idioma de la documentación:** español (specs, README, comentarios de negocio).
- **Encoding:** UTF-8 en todos los archivos.
- **Fin de línea:** LF (Unix).

### JavaScript / TypeScript
- Usar **ES Modules** (`import`/`export`), no CommonJS.
- **No usar `var`**; preferir `const`, usar `let` solo cuando la variable muta.
- Nombres de variables y funciones: `camelCase`.
- Nombres de componentes React: `PascalCase`.
- Nombres de archivos de componentes: `PascalCase.jsx` (ej: `EventCard.jsx`).
- Nombres de archivos de utilidades/servicios: `camelCase.js` (ej: `authService.js`).
- Funciones de controlador: siempre `async/await`, nunca callbacks.
- Todo `async/await` debe estar envuelto en `try/catch` o usando un wrapper de error handling.

### API REST
- Rutas en **kebab-case** y en plural: `/api/eventos`, `/api/participantes`.
- Verbos HTTP correctos: `GET` (leer), `POST` (crear), `PUT`/`PATCH` (actualizar), `DELETE` (eliminar).
- Respuestas siempre en formato JSON con la siguiente estructura:

```json
// Éxito
{
  "success": true,
  "data": { ... },
  "message": "Descripción opcional"
}

// Error
{
  "success": false,
  "error": {
    "code": "EVENTO_NO_ENCONTRADO",
    "message": "El evento solicitado no existe."
  }
}
```

- Códigos HTTP estándar: `200` OK, `201` Created, `400` Bad Request, `401` Unauthorized, `403` Forbidden, `404` Not Found, `422` Unprocessable Entity, `500` Internal Server Error.

### Base de Datos (Prisma)
- Nombres de modelos en Prisma: `PascalCase` singular (ej: `Evento`, `Participante`).
- Nombres de tablas en PostgreSQL: `snake_case` plural (configurado via `@@map` en Prisma).
- Toda tabla debe tener: `id` (UUID), `createdAt`, `updatedAt`.
- Las migraciones deben tener nombres descriptivos (ej: `add_cupo_to_eventos`).

---

## 5. Variables de Entorno

El archivo `.env` **nunca debe commitearse**. Se provee un `.env.example` con todas las variables necesarias (sin valores reales).

Variables requeridas por el backend:

```env
# Base de datos
DATABASE_URL="postgresql://user:password@localhost:5432/acadevents"

# JWT
JWT_SECRET="tu_secreto_muy_largo_y_aleatorio"
JWT_EXPIRES_IN="7d"

# Servidor
PORT=3000
NODE_ENV=development

# Email (nodemailer)
SMTP_HOST=
SMTP_PORT=587
SMTP_USER=
SMTP_PASS=
EMAIL_FROM="noreply@acadevents.com"
```

---

## 6. Cómo Levantar el Proyecto en Local

```bash
# 1. Clonar el repositorio
git clone https://github.com/[usuario]/acadevents.git
cd acadevents

# 2. Levantar PostgreSQL con Docker
docker-compose up -d

# 3. Configurar backend
cd backend
cp .env.example .env
# Editar .env con los valores correspondientes
npm install
npx prisma migrate dev
npm run dev

# 4. Configurar frontend (en otra terminal)
cd frontend
npm install
npm run dev
```

El frontend queda disponible en `http://localhost:5173` y el backend en `http://localhost:3000`.

---

## 7. Roles del Sistema

| Rol | Descripción |
|---|---|
| `ORGANIZADOR` | Crea y administra eventos, gestiona inscripciones y roles. |
| `DISERTANTE` | Participa como expositor/autor. Puede tener agenda propia dentro del evento. |
| `PARTICIPANTE` | Se inscribe a eventos y recibe certificados de asistencia. |

Un usuario puede tener **diferentes roles en diferentes eventos**.

---

## 8. Notas para Agentes de IA

- Cada spec en `/specs/` es autosuficiente para implementar esa feature.
- Antes de implementar cualquier spec, leer este archivo (`Project.md`) y `Contracts.md`.
- El modelo de datos de cada spec debe ser **consistente con el `schema.prisma` existente**. Si una spec agrega modelos nuevos, debe actualizar `schema.prisma` y generar la migración correspondiente.
- No instalar dependencias que no estén listadas en este archivo sin documentarlo en la spec correspondiente.
- Los tests son **obligatorios** para cada feature implementada; ver sección 7 de cada spec.
