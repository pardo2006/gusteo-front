# Menu360 · Frontend

Aplicación web de Menu360, construida con **Next.js 16 (App Router) + React 19 + TypeScript 5**, Tailwind CSS 4, TanStack React Query 5 y Three.js / React Three Fiber para la visualización 3D de los platos.

> 📚 La documentación completa del proyecto (arquitectura, base de datos, seguridad, deploy) está en **[`docs/README.md`](../docs/README.md)**. Este archivo solo cubre cómo levantar y operar el frontend.

---

## 📚 Acceso al sistema

Con los servicios corriendo:

- **Landing**: [http://localhost:3000](http://localhost:3000)
- **Carta pública de demostración**: [http://localhost:3000/es/m/demo](http://localhost:3000/es/m/demo)
- **Panel de administración**: [http://localhost:3000/es/admin](http://localhost:3000/es/admin)
- **Kitchen Display (KDS)**: [http://localhost:3000/es/kds](http://localhost:3000/es/kds)
- **API del backend**: [http://localhost:8000/api/v1/](http://localhost:8000/api/v1/)

Credenciales de demostración (tras ejecutar `seed_demo` en el backend) — contraseña `demo1234`:
`owner@demo.menu360.app` · `manager@demo.menu360.app` · `waiter@demo.menu360.app` · `kitchen@demo.menu360.app` · `cashier@demo.menu360.app`

---

## 🚀 Ejecución con Docker (recomendado)

El `docker-compose.yml` está **en la raíz del proyecto**, no aquí: levanta frontend, backend, worker, PostgreSQL y Redis a la vez.

```bash
cd ..                    # raíz del proyecto
cp .env.example .env
docker compose up --build
```

### Comandos de Docker

```bash
# Levantar en segundo plano
docker compose up -d

# Ver logs en tiempo real
docker compose logs -f frontend

# Detener
docker compose down

# Aplicar cambios en dependencias (reconstruir)
docker compose up -d --build frontend

# Abrir una shell dentro del contenedor
docker compose exec frontend sh
```

> [!TIP]
> Todos los comandos de npm pueden ejecutarse dentro del contenedor con `docker compose exec frontend npm run <script>`. Tras añadir una dependencia a `package.json` hay que **reconstruir la imagen**, no basta con reiniciar el contenedor.

---

## 💻 Ejecución local (sin Docker)

Necesitas **Node.js 22 LTS** o superior.

### 1 · Instalar dependencias

```bash
npm install
```

> [!TIP]
> Usa `npm ci` en lugar de `npm install` cuando quieras una instalación reproducible exacta a partir de `package-lock.json` (es lo que hace CI).

### 2 · Configurar el entorno

El frontend lee su configuración del `.env` de la raíz. En local, crea un `.env.local` aquí con lo mínimo:

```bash
NEXT_PUBLIC_API_URL=http://localhost:8000/api/v1
NEXT_PUBLIC_WS_URL=ws://localhost:8000/ws
NEXT_PUBLIC_SITE_URL=http://localhost:3000
NEXT_PUBLIC_MODELS_CDN=http://localhost:8000/media/models
INTERNAL_API_URL=http://localhost:8000/api/v1
```

> [!WARNING]
> Las variables `NEXT_PUBLIC_*` se incrustan literalmente en el JavaScript que descarga el navegador. **Nunca pongas un secreto detrás de ese prefijo.**

`INTERNAL_API_URL` es la URL que usan los Server Components. En local apunta a `localhost`; dentro de Docker apunta a `http://backend:8000/api/v1`, porque el navegador no puede resolver el host `backend`.

### 3 · Necesitas el backend levantado

```bash
cd .. && docker compose up -d postgres redis backend
```

### 4 · Iniciar el servidor de desarrollo

```bash
npm run dev
```

Disponible en [http://localhost:3000](http://localhost:3000) con Fast Refresh.

---

## 📜 Scripts disponibles

```bash
npm run dev              # Servidor de desarrollo con Fast Refresh
npm run build            # Build de producción (salida standalone)
npm start                # Sirve el build de producción
npm run lint             # ESLint 9 (flat config)
npm run lint:fix         # ESLint con autocorrección
npm run format           # Prettier 3 sobre todo el proyecto
npm run typecheck        # tsc --noEmit
npm run test             # Vitest en modo watch
npm run test:run         # Vitest, una sola pasada
npm run test:coverage    # Cobertura (umbral 80 %)
npm run test:e2e         # Playwright
npm run generate:api     # Regenera los tipos desde el esquema OpenAPI del backend
npm run analyze          # Build con @next/bundle-analyzer
```

Con Docker, antepón `docker compose exec frontend`:

```bash
docker compose exec frontend npm run typecheck
docker compose exec frontend npm run test:run
docker compose exec frontend npm run lint
```

---

## ⚠️ Next.js 16 · no es el Next.js que conoces

Esta versión introduce **cambios de ruptura** respecto a versiones anteriores. Antes de escribir código, consulta la guía correspondiente en `node_modules/next/dist/docs/`, que es la documentación de la versión exacta instalada.

| Cambio | Antes | Ahora |
| --- | --- | --- |
| `params` y `searchParams` | Objetos síncronos | **Promesas**: `const { slug } = await params` |
| Middleware | `middleware.ts` | **`proxy.ts`** en la raíz, exportando `proxy` |
| Caché | Heurísticas implícitas de `fetch` | **Cache Components**: `cacheComponents: true` y directiva `'use cache'` con `cacheLife()` |

```tsx
export default async function MenuPage({
  params,
}: {
  params: Promise<{ locale: string; slug: string }>
}) {
  const { locale, slug } = await params
  // …
}
```

---

## 🎨 Convenciones

- **Server Components por defecto.** `'use client'` solo con estado, efectos, eventos del navegador o WebGL, y **lo más abajo posible** en el árbol.
- **Ningún componente importa Axios.** El flujo es componente → hook → `lib/api/`. Solo esa capa conoce el transporte.
- **`any` está prohibido.** `strict: true` y `noUncheckedIndexedAccess: true`.
- **Lo que viene del servidor vive en React Query**, no en Zustand. Los eventos WebSocket escriben en la caché con `setQueryData`; no hay un segundo estado que pueda desfasarse.
- Componentes en `PascalCase.tsx`, hooks en `useCamelCase.ts`, utilidades en `kebab-case.ts`.
- Exportaciones nombradas; `export default` solo donde Next.js lo exige.
- Las pruebas consultan por rol y texto visible (`getByRole`), nunca por clase CSS.

---

## 🧊 Modelos 3D

Los `.glb` de producción viven en la CDN, no en el repositorio: `.gitignore` los excluye salvo los de `public/models/`.

Presupuesto por modelo: **< 2 MB**, **< 40 000 triángulos**, texturas de 2048 px en KTX2.

```bash
npx gltf-transform optimize entrada.glb salida.glb \
  --texture-compress ktx2 --texture-size 2048 --compress draco

npx gltf-transform inspect salida.glb
```

El visor se importa con `next/dynamic` y `ssr: false`, y solo se monta al pulsar «Ver en 3D»: Three.js pesa ~600 kB y no puede estar en el bundle de la carta. La aplicación funciona íntegramente sin 3D — es un realce, nunca un requisito para pedir.

Detalle completo del pipeline en [`docs/README.md §16`](../docs/README.md#16--visualización-3d).

---

## 🌍 Internacionalización

Dos ejes independientes:

| Eje | Qué traduce | Dónde vive |
| --- | --- | --- |
| **Interfaz** | Botones, mensajes, errores | `next-intl` · `messages/{es,en,pt}.json` |
| **Contenido** | Nombres y descripciones de productos | Base de datos · `product_translations` |

El idioma va en la URL (`/es/m/demo`, `/en/m/demo`). Si `en.json` no tiene todas las claves de `es.json`, CI falla.

---

## 🏗️ Build y producción

```bash
npm run build
npm start
```

El build usa la salida `standalone` de Next.js: la imagen de producción copia solo `.next/standalone`, `.next/static` y `public`, y pesa menos de 200 MB.

```bash
# Producción con Docker, desde la raíz
docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d --build
```

> [!IMPORTANT]
> Las variables `NEXT_PUBLIC_*` se incrustan **en tiempo de build**, no de ejecución. Cambiar una en producción exige reconstruir la imagen; por eso el compose de producción las pasa como `build.args`.

---

## 📁 Estructura

```
app/
├─ [locale]/
│  ├─ m/[slug]/            # Carta pública
│  ├─ mesa/[token]/        # Sesión de comensal (QR / NFC)
│  ├─ (auth)/login/
│  ├─ admin/               # Panel: carta, pedidos, inventario, reservas, ajustes
│  └─ kds/                 # Kitchen Display
└─ api/                    # Route Handlers (BFF)

components/                # UI reutilizable
hooks/                     # Lógica de datos y estado
lib/api/                   # Cliente Axios y funciones de la API
lib/store/                 # Stores de Zustand
messages/                  # Traducciones de interfaz
proxy.ts                   # Locale y comprobación optimista de sesión
```

---

## 📄 Documentación adicional

- [Documentación completa del proyecto](../docs/README.md)
- [Guía de contribución](../docs/CONTRIBUTING.md)
- [Política de seguridad](../docs/SECURITY.md)
- [Changelog](../docs/CHANGELOG.md)
