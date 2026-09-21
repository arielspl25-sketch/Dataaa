# El Rey Crocante

Landing page de pedidos para un restaurante de pollo crocante. Los clientes navegan el menú, agregan productos a su pedido y al finalizar reciben un **número de turno (ticket)** para pagar después en efectivo o por transferencia bancaria al retirar su pedido — sin pasarela de pago en línea.

## Tecnologías

- [TanStack Start](https://tanstack.com/start) (React 19 + TanStack Router) con SSR
- Tailwind CSS 4
- Netlify Database (Postgres administrado) con Drizzle ORM para guardar los pedidos
- Netlify Image CDN para servir las imágenes del menú optimizadas
- Imágenes generadas con Gemini (Nano Banana) vía Netlify AI Gateway

## Cómo funciona

1. **Inicio (`/`)** — hero, navegación por categorías y grilla de productos (combos, pollo, hamburguesas, acompañamientos, bebidas, postres). Cada producto se puede agregar al pedido con un contador de cantidad.
2. **Carrito** — panel deslizante accesible desde el botón "Mi pedido" en cualquier página, con edición de cantidades y subtotal.
3. **Checkout (`/checkout`)** — formulario con nombre y teléfono, y selección del método de pago: efectivo o transferencia. No se cobra nada en este paso.
4. **Confirmación (`/pedido/:orderId`)** — muestra el ticket con el número de turno, el detalle del pedido y las instrucciones de pago (datos bancarios para transferencia, o aviso de pago en caja para efectivo). Se puede imprimir.

Cada pedido se guarda en la base de datos de Netlify (tabla `orders`) con su número de turno, cliente, teléfono, ítems, total y método de pago.

## Desarrollo local

```bash
npm install
npm run dev
```

La app corre en el puerto 3000; usa `netlify dev --port 8889` para la emulación completa de la plataforma (base de datos, Image CDN, etc.).

## Estructura clave

- `src/data/menu.ts` — catálogo del menú y categorías
- `src/lib/cart.tsx` — contexto de carrito (persistido en `localStorage`)
- `src/server/orders.functions.ts` — funciones de servidor para crear y consultar pedidos
- `db/schema.ts` — esquema de la tabla `orders` (Drizzle ORM)
- `src/routes/` — páginas: inicio, checkout y confirmación del ticket
