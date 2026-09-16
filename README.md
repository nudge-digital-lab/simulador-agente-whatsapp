# Simulador — agente de IA para WhatsApp

Demo comercial interactiva para mostrarle a prospectos cómo funciona un agente de IA de atención al cliente y ventas por WhatsApp.

**Ver en vivo:** https://nudge-digital-lab.github.io/simulador-agente-whatsapp/

---

## Qué es

Una pieza de venta, no un producto. Sirve para una reunión: se abre, se deja corriendo y explica sola qué hace el agente y — más importante — **qué pasa por dentro en cada mensaje**, que es lo que el prospecto no puede imaginarse solo.

La pantalla se divide en dos:

| Panel | Qué muestra |
|---|---|
| **Izquierda** | Una conversación de WhatsApp simulada en un celular. Es lo que ve el cliente final. |
| **Derecha** | El "detrás de escena": el flujo que ejecuta el agente en cada mensaje, paso por paso, con la intención detectada, las consultas a sistemas y la latencia de cada etapa. |

Abajo hay métricas que se acumulan durante la demo (mensajes resueltos, consultas a sistemas, tiempo promedio de respuesta, derivaciones a humano) y un bloque de cierre con los cuatro puntos de venta.

## Escenarios incluidos

1. **Talle y stock → venta.** Una clienta pregunta por un producto, el agente consulta catálogo y stock real, recomienda talle, avisa que quedan pocas unidades y cierra con link de pago.
2. **¿Dónde está mi pedido?** El agente identifica el número de pedido en el texto, consulta el sistema de pedidos y el tracking del correo, y responde con ubicación y fecha estimada.
3. **Deriva a una persona.** Un reclamo con pago dividido en dos tarjetas. El agente detecta que está por debajo del umbral de confianza, no improvisa, avisa al cliente y le pasa al equipo humano una tarjeta de contexto con el pedido, el motivo y la traba.

El tercero es el más importante en una venta: responde de antemano la objeción "¿y si el bot se manda una macana?".

## Controles

| Control | Función |
|---|---|
| Reproducir / Pausar | Arranca y congela la simulación en cualquier punto (útil para explicar un paso). |
| Reiniciar | Vuelve al estado inicial y resetea las métricas. |
| Velocidad | 0,5× a 3×. Para una reunión, 1× o 1,5×. |
| Loop continuo | Al terminar un escenario pasa solo al siguiente y cicla indefinidamente. Pensado para dejarlo proyectado sin tocar nada. |

**Atajos:** `Espacio` reproduce/pausa · `R` reinicia.

## Cómo se usa

Abrí `index.html` en cualquier navegador, o entrá a la URL de arriba. No hay servidor, build ni instalación.

Para una reunión presencial conviene `F11` (pantalla completa) y activar el loop antes de que entre el cliente.

## Detalles técnicos

- **Un solo archivo.** Todo el HTML, CSS y JavaScript está embebido en `index.html`. Cero dependencias externas, cero peticiones de red, funciona offline.
- **Tipografía embebida.** Plus Jakarta Sans va como `@font-face` con la fuente en base64 (~36 KB) dentro del propio archivo. Por eso la marca se ve igual en cualquier máquina sin depender de Google Fonts.
- **Peso total:** ~67 KB.
- **Motor de animación.** Un `sleep()` cancelable basado en promesas que descuenta tiempo real multiplicado por la velocidad. Eso permite pausar sin perder la posición y cambiar la velocidad en pleno vuelo. Cada corrida lleva un `runId`; al cambiar de escenario o reiniciar se incrementa y las corridas viejas se cancelan solas en vez de pisarse entre sí.
- **Degradación en segundo plano.** Como usa `setTimeout` y no `requestAnimationFrame`, si la pestaña queda en background Chrome throttlea los timers a ~1 Hz y la animación va más lenta, pero no se rompe ni se desincroniza.
- **Responsive.** Dos columnas arriba de 1100 px, una sola abajo. Hay modo compacto por `max-height` para notebooks de poca altura vertical (1366×768 con escalado al 150%, que es donde se ve más apretado).
- **Accesibilidad.** Respeta `prefers-reduced-motion`.

### Datos

Los escenarios están en la constante `SC` dentro del `<script>`. Cada uno es un array de *beats*:

```js
{t:"in",   text:"..."}                          // mensaje del cliente (verde, derecha)
{t:"out",  text:"..."}                          // mensaje del agente  (blanco, izquierda)
{t:"flow", intent:"...", steps:[{l,d,ms,q,h}]}  // pipeline en el panel derecho
```

En los pasos: `l` es el título, `d` el detalle, `ms` la latencia simulada, `q` marca que es una consulta a un sistema (suma a la métrica) y `h` que dispara la tarjeta de derivación a humano.

Para agregar un escenario nuevo alcanza con sumar un objeto a `SC` — los botones de arriba se generan solos a partir de ese array.

## Stack que representa

La demo simula una implementación real con:

- **n8n** como orquestador del flujo
- **Claude (API de Anthropic)** como motor de comprensión y redacción
- **WhatsApp Business API** como canal
- Conectores al catálogo, stock y sistema de pedidos del cliente

Los tiempos que muestra reflejan latencias típicas de producción. Son simulados, no medidos.

## Marca

Fondo negro `#0A0A0A`, texto `#F5F4F0`, acento dorado `#D4A843` (único color de marca). Titulares en serif, resto en Plus Jakarta Sans. El wordmark **nudge** va siempre en minúscula.

---

Hecho por [nudge](https://nudge.com.ar) · agencia digital
