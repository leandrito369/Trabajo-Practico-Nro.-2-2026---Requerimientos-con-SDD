# Contracts.md — Contratos de Colaboración

> Este archivo define los acuerdos, restricciones y flujos de trabajo que rigen la colaboración
> en el repositorio. Todo agente o colaborador debe respetar estas reglas **sin excepción**.
> En caso de conflicto entre una spec y este archivo, **este archivo tiene precedencia**.

---

## 1. Gestión de Ramas (Git Branching)

### Ramas principales

| Rama | Propósito | ¿Quién puede pushear directo? |
|---|---|---|
| `main` | Código en producción, siempre estable | ❌ Nadie — solo via PR aprobado |
| `develop` | Integración continua de features | ❌ Nadie — solo via PR aprobado |

### Ramas de trabajo

Toda tarea debe desarrollarse en una rama propia creada desde `develop`:

```
feature/<nombre-descriptivo>     ← nueva funcionalidad
fix/<nombre-del-bug>             ← corrección de errores
docs/<nombre-del-doc>            ← cambios solo de documentación
test/<nombre-del-test>           ← adición/corrección de tests
refactor/<nombre>                ← refactors sin cambio de comportamiento
```

**Ejemplos válidos:**
```
feature/gestion-eventos
feature/inscripcion-participantes
fix/validacion-cupo-maximo
docs/actualizar-project-md
test/inscripcion-service-unit
```

**Reglas:**
- Los nombres de ramas van en **kebab-case** y en **español**.
- Nunca trabajar directamente en `main` o `develop`.
- Eliminar la rama después de que el PR sea mergeado.

---

## 2. Commits

### Formato obligatorio (Conventional Commits)

```
<tipo>(<scope>): <descripción en español, imperativo, minúscula>
```

**Tipos válidos:**

| Tipo | Cuándo usarlo |
|---|---|
| `feat` | Se agrega una nueva funcionalidad |
| `fix` | Se corrige un bug |
| `docs` | Cambios solo en documentación |
| `test` | Se agregan o modifican tests |
| `refactor` | Cambio de código sin agregar features ni fixes |
| `chore` | Tareas de mantenimiento (deps, config) |
| `style` | Cambios de formato/estilo que no afectan lógica |

**Scopes sugeridos:** `eventos`, `inscripciones`, `roles`, `certificados`, `auth`, `api`, `db`, `ui`

**Ejemplos válidos:**
```
feat(eventos): agregar endpoint de creación de eventos
fix(inscripciones): corregir validación de cupo máximo
test(auth): agregar tests unitarios para el middleware JWT
docs(specs): actualizar spec-01 con restricciones técnicas
chore(deps): actualizar prisma a versión 5.14
```

**Reglas:**
- La descripción debe estar en **español**, en modo **imperativo** y en **minúscula**.
- No usar punto final en la descripción.
- El cuerpo del commit (opcional) puede dar más contexto.
- Un commit = un cambio lógico. No mezclar features distintas en un commit.

---

## 3. Pull Requests (PRs)

### Cuándo abrir un PR
- Cuando una feature, fix o tarea documentada en una spec esté **completa y testeada**.
- Una PR **no debe mezclarse** con otra feature o fix no relacionado.

### Título del PR
Seguir el mismo formato que los commits:
```
feat(eventos): implementar CRUD completo de gestión de eventos
```

### Descripción mínima obligatoria del PR

```markdown
## ¿Qué hace este PR?
[Descripción breve de los cambios]

## Spec relacionada
[Nombre del archivo de spec, ej: spec-01-gestion-eventos.md]

## Tareas completadas
- [ ] Tarea 1 de la spec
- [ ] Tarea 2 de la spec
- [ ] Tests escritos y pasando
- [ ] Sin errores de linting

## Cómo probarlo
[Pasos para verificar manualmente el cambio]
```

### Reglas de merge
- Un PR **no puede mergearse** si los tests automatizados fallan (CI debe estar en verde).
- Un PR **no puede mergearse** si hay errores de linting.
- Todo PR debe ser revisado antes del merge (en equipos de una persona, hacer self-review documentado).
- El merge se hace con **Squash and Merge** para mantener el historial limpio en `develop`.

---

## 4. Specs — Reglas de Escritura y Modificación

### Creación de una nueva spec
- El nombre del archivo sigue el patrón: `spec-NN-nombre-en-kebab-case.md` (ej: `spec-01-gestion-eventos.md`).
- Toda spec nueva debe agregarse al índice en `README.md`.
- Una spec **no puede referirse a funcionalidades de otra spec** sin explicitar la dependencia.

### Modificación de una spec existente
- Las specs son documentos **vivos**: pueden modificarse si el entendimiento del negocio cambia.
- Todo cambio en una spec debe registrarse al final del archivo en una sección de **Historial de Cambios**:

```markdown
## Historial de Cambios
| Fecha | Autor | Descripción del cambio |
|---|---|---|
| 2026-05-10 | Lean | Versión inicial |
| 2026-05-15 | Lean | Ajuste en reglas de negocio de cupo mínimo |
```

- Si un agente de IA implementa una feature y encuentra ambigüedades en la spec, debe **documentar el supuesto asumido** en el commit correspondiente, no inventar comportamiento silenciosamente.

### Estructura obligatoria de toda spec
Toda spec debe tener exactamente estas secciones, en este orden:
1. Objetivo y Contexto
2. Historias de Usuario y Criterios de Aceptación
3. Requisitos Funcionales y Reglas de Negocio
4. Restricciones Técnicas Específicas de este Módulo
5. Modelo de Datos de este Módulo
6. Plan de Tareas
7. Estrategia de Verificación
8. Historial de Cambios

---

## 5. Base de Datos y Migraciones

- **Nunca modificar la base de datos directamente** (sin migración de Prisma).
- Toda modificación al `schema.prisma` debe ir acompañada de una migración nombrada descriptivamente.
- Las migraciones **no se revierten** en `main`. Si algo salió mal, se crea una nueva migración correctora.
- El archivo `schema.prisma` es la **fuente de verdad** del modelo de datos. Si hay conflicto entre la spec y el schema, se actualiza la spec.

---

## 6. Variables de Entorno y Secretos

- **Prohibido committear** archivos `.env` con valores reales.
- **Prohibido hardcodear** credenciales, tokens o URLs de producción en el código.
- Si se agrega una variable de entorno nueva, debe agregarse también al `.env.example` con un valor de ejemplo o placeholder.
- Las variables de entorno deben documentarse en la spec del módulo que las requiere (sección 4: Restricciones Técnicas).

---

## 7. Testing — Contratos Mínimos

Todo código mergeado a `develop` debe cumplir:

| Tipo de test | Cobertura mínima esperada |
|---|---|
| Tests unitarios (servicios y utils) | ≥ 80% de las funciones del módulo |
| Tests de integración (endpoints API) | 100% de los endpoints definidos en la spec |
| Tests de componentes React (críticos) | Los componentes con lógica de negocio |

**Reglas:**
- Los tests se ubican en `backend/tests/` y `frontend/src/__tests__/`.
- Nombrar los archivos de test como `<nombre-del-módulo>.test.js`.
- Un test no debe depender del estado de otro test (tests aislados).
- Usar datos de prueba ficticios (mocks/fixtures), nunca datos de producción.

---

## 8. Linting y Formateo

- El proyecto usa **ESLint** y **Prettier** con la configuración definida en `.eslintrc` y `.prettierrc`.
- **Prohibido deshabilitar reglas de ESLint** con comentarios `// eslint-disable` sin justificación documentada.
- El hook de pre-commit (Husky) ejecuta `lint-staged` automáticamente. Si falla, el commit se rechaza.
- Configuración de Prettier: `tabWidth: 2`, `singleQuote: true`, `semi: true`, `trailingComma: 'es5'`.

---

## 9. Restricciones para Agentes de IA

Los siguientes comportamientos están **explícitamente prohibidos** para cualquier agente que implemente specs:

- ❌ Instalar dependencias no listadas en `Project.md` sin documentarlo en la spec.
- ❌ Crear archivos fuera de la estructura de carpetas definida en `Project.md`.
- ❌ Modificar specs existentes sin agregar entrada al historial de cambios.
- ❌ Hacer commits directos a `main` o `develop`.
- ❌ Omitir tests al implementar una feature.
- ❌ Hardcodear valores que deberían ser variables de entorno.
- ❌ Asumir comportamiento no especificado sin documentar el supuesto.
- ❌ Modificar el `schema.prisma` sin crear la migración correspondiente.

---

## 10. Glosario del Proyecto

| Término | Definición |
|---|---|
| **Evento** | Actividad académica organizada (curso, jornada, congreso, charla, taller). |
| **Organizador** | Usuario con rol de administración sobre un evento específico. |
| **Participante** | Usuario registrado en la plataforma que se inscribe a eventos. |
| **Disertante** | Usuario que participa como expositor/autor en un evento. |
| **Inscripción** | Registro de un participante a un evento. |
| **Acreditación** | Confirmación de asistencia efectiva de un participante a un evento. |
| **Certificado** | Documento generado post-evento que acredita la participación/aprobación. |
| **Cupo** | Cantidad mínima y/o máxima de inscriptos permitidos en un evento. |
| **Spec** | Documento de especificación de una feature, ubicado en `/specs/`. |
