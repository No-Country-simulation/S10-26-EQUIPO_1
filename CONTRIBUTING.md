# Guía de Contribución: HostFlow - S10-26-EQUIPO_1

Este documento define las convenciones de trabajo del **Equipo 1** en la Simulación Laboral Abierta de No Country, para mantener un flujo ordenado, claro y escalable entre las áreas de **Backend** y **Frontend**.

---

## 0. Sobre el proyecto

**HostFlow – Onboarding y Gestión para Pequeños Alojamientos** (Proyecto B2B2C – Delta del Tigre).

Los dueños de complejos turísticos (cabañas, hoteles) piden hoy los datos de sus huéspedes por WhatsApp. HostFlow digitaliza ese proceso y, en el momento de mayor necesidad del turista, le ofrece el *upsell* de un servicio de **lancha remis premium**.

### Interfaces principales

1. **Dashboard B2B (dueño):** CRUD del complejo, subida de logo, gestión básica de disponibilidad y creación de "adicionales" (comidas, experiencias, etc.).
2. **Ficha de Onboarding B2C (turista):** formulario dinámico *white-label* con la marca del complejo. Incluye datos personales, alergias, preferencias y la opción **"Cotizar lancha remis"** con sus horarios.
3. **Mini-Ecommerce Lean (adicionales):** el dueño crea adicionales con precio. El sistema **NO procesa pagos**: al finalizar el onboarding se muestra un resumen de la compra y el Alias/CBU precargado por el complejo ("Transfiere el monto total a este Alias para asegurar tus adicionales"). El dueño recibe un email con el pedido y el turista paga por fuera del sistema.

### Flujo técnico del sistema

| Módulo | Descripción | Usuario |
|--------|-------------|---------|
| Dashboard B2B | CRUD de complejo, logo, disponibilidad y adicionales | Dueño |
| Onboarding B2C | Formulario white-label con datos personales, alergias y preferencias | Turista |
| Trigger Lancha | Si selecciona "Cotizar Lancha", registra la solicitud y prepara un JSON vía Webhook/API para un módulo futuro | Sistema |
| Mini-Ecommerce | Selección de adicionales, resumen de compra + Alias/CBU | Turista / Dueño |
| Notificación | Email automático al dueño con el pedido de adicionales | Dueño |

### Reglas de negocio y alcance (Scope MVP)

- **Mobile-First obligatorio:** el 95% de los turistas completará la ficha desde el celular. La UI/UX debe ser impecable en móviles.
- **Gestión de reservas simplificada:** no se construye un motor de reservas tipo Booking/Airbnb. Solo una lista de reservas (Check-in, Check-out, Estado) ordenada cronológicamente.
- **White-label liviano:** sin dominios personalizados. Se usan rutas dinámicas (`app.nuestrodominio.com/onboarding/id-complejo`) y se cambian logo y colores en el frontend.
- **Trigger de lancha:** si el huésped selecciona "Cotizar Lancha", no se le cobra. Solo se registra la solicitud y se deja preparado el envío automático de un JSON (Webhook/API) a un módulo futuro.
- **Sin pasarela de pagos:** los adicionales se pagan por transferencia, fuera del sistema.

### Estructura del repositorio

```text
S10-26-EQUIPO_1/
├── backend/     # API, lógica de negocio, base de datos, emails, webhook
├── frontend/    # Dashboard B2B y ficha de onboarding B2C (mobile-first)
├── CONTRIBUTING.md
└── README.md
```

---

## 1. Ramas (Branches)

### Ramas del repositorio

| Rama | Propósito |
|------|-----------|
| `main` | Rama por defecto. Código estable y listo para entrega. Solo recibe merges desde `develop`. |
| `develop` | Rama de integración. Aquí se juntan los cambios de backend y frontend. |
| `feature/*` | Ramas de trabajo. Cada una sale de `develop` y vuelve a `develop`. |

Ramas de trabajo existentes y previstas por área:

- `feature/back-desarrollo`: trabajo de **backend**.
- `feature/front-desarrollo`: trabajo de **frontend** (equivalente a la anterior para el otro equipo).

### Formato de nombres

```bash
tipo/descripcion-corta
```

**Tipos permitidos:**

| Tipo       | Cuándo usarlo |
|------------|---------------|
| `feature`  | Nueva funcionalidad (endpoint, pantalla, componente, etc.) |
| `fix`      | Corrección de bugs |
| `refactor` | Reestructuración sin cambiar comportamiento |
| `docs`     | Documentación únicamente (README, comentarios, etc.) |
| `test`     | Agregar o modificar pruebas |
| `chore`    | Mantenimiento, configuraciones, tooling, actualización de dependencias |
| `perf`     | Mejoras de rendimiento (consultas, optimización de código) |

**Ejemplos válidos:**

- `feature/back-desarrollo`
- `feature/front-desarrollo`
- `feature/complejo-crud`
- `feature/onboarding-form`
- `feature/adicionales-resumen`
- `feature/lancha-webhook`
- `fix/null-handling-in-dto`
- `docs/update-readme`
- `chore/update-dependencies`

---

## 2. Commits

**Formato:**

```bash
tipo(alcance): descripción en imperativo
```

El alcance es recomendado; si el cambio es general se puede omitir (por ejemplo `chore: create frontend and backend folder structure`).

**Tipos de commit:** `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `perf`.

**Alcances típicos (según capa):**

- **Backend:** `api`, `service`, `repository`, `dto`, `config`, `security`, `email`, `webhook`
- **Frontend:** `dashboard`, `onboarding`, `ui`, `components`, `routes`, `styles`
- **Generales:** `docs`, `tests`, `ci`, `deps`

**Ejemplos válidos:**

- `feat(api): add CRUD endpoints for complejo`
- `feat(onboarding): add dynamic white-label form`
- `feat(webhook): prepare JSON payload for lancha quote request`
- `feat(email): send order notification to owner`
- `fix(service): handle missing fields in request`
- `docs(readme): update project setup instructions`
- `chore(deps): update frontend dependencies`

**Reglas:**

- Descripción en imperativo (ej: "add", no "added").
- En minúscula.
- Máximo 72 caracteres en la primera línea.
- Si necesitas más detalle, usa un cuerpo opcional después de una línea en blanco.
- Mantén el idioma consistente: **siempre en inglés** para commits y mensajes.

---

## 3. Flujo de Trabajo

Este equipo usa un flujo simplificado, sin Pull Requests formales obligatorios:

```text
feature/*  →  develop  →  main
```

### Flujo de trabajo diario

1. Actualiza `develop`:
   ```bash
   git checkout develop
   git pull origin develop
   ```
2. Crea o cambia a tu rama de trabajo:
   ```bash
   git checkout -b feature/nombre-corto      # rama nueva
   # o
   git checkout feature/back-desarrollo      # rama existente
   ```
3. Trabaja y haz commits pequeños siguiendo la convención de la sección 2.
4. Antes de integrar, trae lo último de `develop` y resuelve conflictos localmente:
   ```bash
   git checkout feature/nombre-corto
   git merge develop
   ```
5. Verifica que el código compile y pase las pruebas básicas.
6. Sube tu rama:
   ```bash
   git push origin feature/nombre-corto
   ```
7. Integra en `develop` (con un Pull Request si el equipo lo desea, o por merge directo):
   ```bash
   git checkout develop
   git pull origin develop
   git merge --no-ff feature/nombre-corto
   git push origin develop
   ```

### Entrega a `main`

Al final de cada sprint o hito, el responsable del equipo fusiona `develop` en `main`:

```bash
git checkout main
git pull origin main
git merge --no-ff develop
git push origin main
```

### Reglas importantes

- **No hacer push directo a `main`.** Solo se actualiza mediante merge desde `develop`.
- **No hacer push directo a `develop`** sin pasar por una rama `feature/*` (excepto cambios menores de documentación o configuración, con previo acuerdo del equipo).
- Mantén las ramas **pequeñas y enfocadas** en un solo objetivo.
- Resuelve los conflictos localmente antes de subir tu rama.
- Asegúrate de que tu código compile y pase las pruebas básicas antes de fusionar.
- Backend y frontend trabajan en sus carpetas (`backend/` y `frontend/`); evita modificar la carpeta del otro equipo sin avisar.

---

## 4. Archivos que NUNCA deben incluirse en un commit

- Credenciales: `.env`, `.env.local`, `*.key`, `*.pem`, `*.json` con secretos (claves de email, API keys, datos bancarios reales).
- Entornos virtuales y dependencias: `.venv/`, `venv/`, `env/`, `__pycache__/`, `*.pyc`, `node_modules/`
- Archivos de build: `backend/target/`, `dist/`, `build/`
- Configuraciones locales del editor: `.vscode/`, `.idea/`, `*.swp`
- Archivos de logs: `*.log`
- Archivos pesados (más de 1 MB): imágenes o logos de prueba muy grandes, dumps de base de datos, etc.

---

## 5. Recomendaciones Generales

- Escribe código limpio, claro y con nombres expresivos.
- Documenta funciones o métodos complejos con comentarios claros.
- Mantén consistencia en estilo y convenciones dentro de cada capa (backend y frontend).
- **Diseña pensando en móvil primero**: prueba cada pantalla del frontend en tamaño de celular.
- Valida los datos del formulario de onboarding tanto en frontend como en backend.
- Si una rama crece demasiado, divídela en partes lógicas.
- Actualiza siempre la documentación (README, documentación de la API, etc.) junto con el código.
- Respeta el alcance del MVP: no agregar motor de reservas complejo, pasarela de pagos ni dominios personalizados.

---

## 6. Dudas o Soporte

Antes de hacer un merge o si tienes dudas sobre el flujo:

- Pregunta primero en el canal del equipo (Equipo 1).
- Si el tema es de alcance del producto, consúltalo con el equipo antes de implementarlo.
