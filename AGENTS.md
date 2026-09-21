# AGENTS.md

Guía para agentes de IA que trabajen en este proyecto.

## Qué es esto

Landing page de pedidos para "El Rey Crocante", un restaurante de pollo crocante. El flujo de pago no usa pasarela en línea: al finalizar el pedido se genera un número de turno (ticket) y el cliente paga después en efectivo o por transferencia bancaria al retirar.

## Stack

- TanStack Start (React 19, TanStack Router, SSR) — enrutamiento por archivos en `src/routes/`
- Tailwind CSS 4 (clases utilitarias, sin config custom más allá de la del scaffold)
- Netlify Database (Postgres) vía Drizzle ORM — ver `db/schema.ts` y `db/index.ts`
- Netlify Image CDN para las imágenes del menú (`/.netlify/images?url=...`)

## Estructura

```
db/
  schema.ts          # tabla `orders` (Drizzle)
  index.ts           # cliente Drizzle (adaptador netlify-db)
drizzle.config.ts     # out apunta a netlify/database/migrations
netlify/database/migrations/   # migraciones generadas, no editar a mano
public/img/           # imágenes del menú (generadas con IA, servidas vía Image CDN)
src/
  data/menu.ts         # catálogo del menú (categorías, productos, precios en centavos)
  lib/cart.tsx          # contexto de carrito (localStorage)
  lib/format.ts         # formateo de precios
  components/
    Header.tsx          # barra superior con nombre de marca y botón de carrito
    CartDrawer.tsx       # panel deslizante del carrito
    MenuItemCard.tsx      # tarjeta de producto con selector de cantidad
  server/orders.functions.ts   # server functions: createOrder, getOrder
  routes/
    index.tsx            # landing con menú
    checkout.tsx          # formulario de datos + método de pago
    pedido/$orderId.tsx    # ticket/turno de confirmación
```

## Convenciones

- Precios se manejan en **centavos (enteros)** en todo el código (`priceCents`, `totalCents`) para evitar errores de punto flotante. Formatear con `formatPrice()` de `src/lib/format.ts`.
- Server functions usan `createServerFn` de `@tanstack/react-start` con `.inputValidator(...)` (nunca `.validator(...)`, esa API no existe).
- El carrito vive solo en el cliente (`localStorage`); el pedido se persiste en la base de datos únicamente al confirmar el checkout.
- El número de turno mostrado al cliente es el `id` autoincremental de la tabla `orders`, formateado con ceros a la izquierda (`#00N`).
- Cambios al esquema de la base de datos requieren una migración: `npx drizzle-kit generate --name <nombre>`. Nunca aplicar migraciones manualmente ni editar `netlify/database/migrations/` a mano.

## Qué falta / posibles mejoras futuras

- No hay panel para el restaurante donde marcar un pedido como "pagado" o "entregado" — el campo `status` en `orders` existe pero no se actualiza desde ninguna UI todavía.
- No hay notificación automática (email/WhatsApp) al cliente o al restaurante cuando llega un pedido nuevo.
