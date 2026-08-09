# 📦 Pull Request — Menu360 Frontend

## Descripción

<!-- ¿Qué cambia y por qué? Explica el contexto y las decisiones de implementación relevantes.
     Si afecta a la interfaz, describe el flujo del usuario resultante. -->

## Issue relacionado

<!-- Usa "Closes #123" para cerrarlo automáticamente al mergear, o "Refs #123" si solo está relacionado.
     Si necesita un cambio en la API, enlaza también el PR de menu360-backend. -->

Closes #

## Tipo de cambio

- [ ] `feat` — nueva funcionalidad
- [ ] `fix` — corrección de un bug
- [ ] `perf` — mejora de rendimiento (LCP, bundle, FPS del visor 3D)
- [ ] `refactor` — cambio interno sin alterar el comportamiento observable
- [ ] `style` — solo formato, sin cambio lógico
- [ ] `docs` — solo documentación o Storybook
- [ ] `test` — solo tests
- [ ] `build` / `ci` — Dockerfile, dependencias o workflows
- [ ] `chore` — mantenimiento
- [ ] 💥 **BREAKING CHANGE** — rompe la API pública de un componente o hook

<!-- Si has marcado BREAKING CHANGE, describe aquí la migración necesaria: -->

## Cómo se ha probado

<!-- Detalla la verificación manual y automatizada. Incluye comandos reales y su resultado. -->

```bash
npm run test:run
npm run test:coverage
npm run test:e2e
```

- [ ] Tests unitarios / de integración nuevos o actualizados (Vitest + Testing Library)
- [ ] Test E2E de Playwright, si toca un flujo crítico (QR → carta → carrito → pedido)
- [ ] Verificado manualmente en escritorio
- [ ] Verificado manualmente en móvil real o emulado (≤ 390 px de ancho)
- [ ] Verificado en un navegador WebKit (Safari / iOS), obligatorio si toca el visor 3D o animaciones
- [ ] Verificado con conexión lenta simulada (Fast 3G) si afecta a la carga de modelos 3D

**Cobertura antes → después:** <!-- p. ej. 83.1 % → 83.8 % -->

## 📸 Capturas / vídeo

> **Obligatorio si hay cualquier cambio visual.** Adjunta móvil y escritorio, y en modo claro y oscuro si el cambio afecta a ambos.

| Antes | Después |
| --- | --- |
| <!-- captura --> | <!-- captura --> |

<!-- Para cambios de interacción o animación, adjunta un vídeo corto. Si no hay cambio visual, escribe "No aplica". -->

## ✅ Checklist de calidad

- [ ] La rama parte de `develop` (o de `main` si es un `hotfix/*`) y está rebasada sobre ella.
- [ ] El título del PR sigue Conventional Commits (`feat(viewer3d): ...`).
- [ ] `npm run lint` sin errores ni advertencias nuevas.
- [ ] `npm run format:check` sin diferencias.
- [ ] `npm run typecheck` sin errores (sin `any` ni `@ts-ignore` añadidos).
- [ ] `npm run test:run` en verde y la cobertura se mantiene ≥ 80 %.
- [ ] `npm run build` compila correctamente.
- [ ] Las respuestas de la API que se consumen están validadas con esquemas Zod.
- [ ] Los componentes nuevos son Server Components salvo que necesiten `'use client'`, y este está lo más abajo posible en el árbol.
- [ ] Accesibilidad: navegable por teclado, foco visible, nombres accesibles y contraste suficiente.
- [ ] Los textos nuevos están en `messages/es.json`, `en.json` y `ca.json` (nada de literales en JSX).
- [ ] Los componentes de UI nuevos tienen su historia en Storybook.
- [ ] Se ha añadido la entrada correspondiente en `CHANGELOG.md` bajo `[Unreleased]`.
- [ ] No se incluyen `.env.local`, tokens, `node_modules/`, `.next/` ni modelos `.glb` de prueba.
- [ ] Este PR no mezcla un refactor con una funcionalidad nueva.

## 📊 Impacto en el bundle

- [ ] No afecta de forma apreciable al tamaño del bundle.
- [ ] Lo modifica; adjunto la comparación de `npm run analyze`:

| Ruta | Antes | Después |
| --- | --- | --- |
| `/m/[slug]` | <!-- kB --> | <!-- kB --> |
| `/admin` | <!-- kB --> | <!-- kB --> |

- [ ] Añade dependencias nuevas y las justifico aquí: <!-- paquete, tamaño y motivo -->

## 🚀 Notas de despliegue

<!-- Marca lo que aplique y detalla los pasos manuales necesarios. -->

- [ ] No requiere ninguna acción especial.
- [ ] Requiere nuevas variables de entorno: <!-- nombre, valor por defecto y dónde configurarlas -->
- [ ] Requiere purgar la caché del CDN o revalidar rutas estáticas: <!-- indícalas -->
- [ ] Requiere despliegue coordinado con `menu360-backend`: <!-- enlaza el PR -->
- [ ] Depende de una versión mínima de la API: <!-- indícala -->

## 📎 Información adicional

<!-- Enlaces a Discussions, decisiones de diseño, métricas de Lighthouse antes/después,
     o cualquier contexto que ayude a quien revise. Elimina esta sección si no aplica. -->

---

> Revisión: se requiere **al menos 1 aprobación** y **toda la CI en verde**.
> Los cambios en `auth`, `api-client` o en el visor 3D requieren **2 aprobaciones**.
