# Visión de Ludix

> **Estado:** Fase 0 — Documento vivo. Se irá expandiendo con la comunidad.

---

## 1. El problema que nadie debería tener que resolver

En 2020, Mastercard presionó a Pornhub para eliminar contenido. En días, millones de archivos desaparecieron.  
En 2021, Stripe y PayPal comenzaron a cortar servicios a plataformas de contenido adulto "de riesgo".  
En distintos momentos entre 2019 y 2023, itch.io, Steam y otras tiendas de juegos recibieron presión directa o indirecta de procesadores de pago para eliminar juegos NSFW legales, juegos con temáticas políticamente sensibles, o simplemente juegos que alguien en un departamento de riesgo consideró "inapropiados".

No hubo juicios. No hubo leyes nuevas. No hubo votos.

Hubo correos. Hubo amenazas de cortar el procesamiento de pagos. Y las plataformas, que dependen de ese procesamiento para sobrevivir, obedecieron.

El resultado fue siempre el mismo:

- Desarrolladores que perdieron su fuente de ingresos de un día para otro.
- Jugadores que abrieron su biblioteca y encontraron huecos donde antes había juegos.
- Comunidades que vieron desaparecer años de trabajo sin explicación real.

**Eso es censura financiera.** No importa cómo se llame en los comunicados corporativos.

Ludix nace para que eso no pueda volver a pasar. Al menos, no tan fácilmente.

---

## 2. Por qué las soluciones actuales no resuelven el problema

### Las tiendas tradicionales (Steam, itch.io, GOG)

Son plataformas centralizadas que dependen de procesadores de pago tradicionales para operar.  
Por muy buena voluntad que tengan sus equipos, están estructuralmente atadas a Visa, Mastercard, Stripe y similares.  
Si esas empresas presionan, las tiendas ceden — porque no pueden no hacerlo sin perder su capacidad de cobrar.

El problema no es la mala fe de Steam o itch.io.  
El problema es la **dependencia estructural** hacia intermediarios financieros con sus propias agendas.

### Las alternativas Web3 actuales

Han aparecido docenas de plataformas que prometen descentralización y libertad.  
La mayoría comparte el mismo patrón:

- Token propio obligatorio para publicar o comprar.
- Comisiones disfrazadas de "gas fees" o "minting fees".
- NFTs presentados como "propiedad real" cuando en práctica son imágenes en una blockchain.
- Dependencia hacia una empresa, su red, o su token.
- Ecosistema cerrado vendido como "abierto".

No resuelven el problema de fondo. Lo reemplazan por una dependencia diferente, generalmente más opaca y más especulativa.

### Lo que falta

Una infraestructura que sea:

- **Técnicamente no-custodial**: nadie toca el dinero excepto el comprador y el vendedor.
- **Financieramente independiente**: sin bancos, sin tarjetas, sin pasarelas centrales como requisito.
- **Abierta de verdad**: forkeable, auditable, desplegable por cualquiera.
- **Sin token obligatorio**: los juegos son juegos, no activos financieros.
- **Respetuosa de las leyes reales**: no un refugio de delitos, sino un espacio libre de censura privada.

Eso es Ludix.

---

## 3. La filosofía central

### Libertad y responsabilidad son inseparables

Ludix no promete protegerte de todo.

Cuando usas Linux, eres dueño absoluto de tu sistema. También puedes romperlo. Nadie viene a salvarte si ejecutas el comando equivocado. Esa es la misma libertad que Ludix ofrece: **real, con consecuencias, y tuya**.

El sistema te da información. La decisión es tuya. Siempre.

### Transparencia radical sobre perfección imposible

No buscamos ser perfectos. Buscamos ser honestos.

Si un desarrollador es nuevo y no tiene historial, el sistema lo dice.  
Si un build no está firmado criptográficamente, el sistema lo dice.  
Si un juego tiene reportes negativos de la comunidad, el sistema lo dice.

La plataforma no decide por ti. Te da la información para que decidas tú.

### La ley de cada país, no la política de riesgo de un banco

Ludix reconoce tres categorías de contenido. Son distintas y deben tratarse distinto.

**Categoría 1 — Ilegal universalmente**  
CSAM, terrorismo, fraude. No tiene lugar en Ludix. Sin discusión.  
Hay consenso global, hay leyes claras, y Ludix las respeta.

**Categoría 2 — Ilegal en una jurisdicción específica**  
Si un gobierno emite una notificación legal legítima, con proceso  
democrático y transparente, Ludix acata — pero *solo para esa jurisdicción*.  
Un juego ilegal en un país no desaparece para el resto del mundo.  
La ley tiene alcance territorial. Ludix también.

**Categoría 3 — Legal, pero incómodo para privados**  
Aquí es donde Ludix planta la bandera.  
Ningún correo de Visa. Ninguna campaña de lobby. Ninguna "política  
interna de riesgo reputacional" de Stripe tiene autoridad sobre esto.  
Si es legal en la jurisdicción del jugador y del desarrollador,  
ningún privado sin mandato democrático debería poder hacerlo desaparecer.

La distinción es simple:  
**Ley legítima con proceso democrático = Ludix respeta.**  
**Privado con poder económico actuando como juez = Ludix rechaza.**

### La comunidad como guardián

Ludix no tiene un departamento de moderación corporativa.  
Tiene un sistema de verificación (TrustChain), un sistema de reputación, y una comunidad que participa activamente en mantener la salud del ecosistema.

Las reglas son transparentes. Los cambios son auditables. Los forks son posibles.

---

## 4. Qué significa ser infraestructura y no una tienda

Esta distinción es fundamental y vale la pena entenderla bien.

**Una tienda** tiene inventario, toma decisiones editoriales, cobra comisiones, y es responsable de la experiencia completa. Steam es una tienda. itch.io es una tienda.

**Una infraestructura** provee el protocolo, las herramientas, y la implementación de referencia. Lo que se construye encima es responsabilidad de quien lo construye. Internet es infraestructura. Linux es infraestructura. SMTP es infraestructura.

Ludix quiere ser lo segundo.

Esto significa:

- Ludix no decide qué juegos existen — los devs los publican.
- Ludix no almacena los builds — los devs los hostean.
- Ludix no toca el dinero — viaja directo del jugador al dev.
- Ludix no puede ser "presionado" para eliminar contenido de la misma manera que una tienda, porque no es el dueño de ese contenido.

Lo que Ludix sí hace es proveer:

- El protocolo de pago y verificación.
- El sistema de identidad y confianza (TrustChain).
- El launcher de referencia para jugadores.
- El portal de referencia para desarrolladores.
- Las especificaciones para que cualquiera pueda construir su propio nodo, fork, o gateway.

---

## 5. El poder de los forks como garantía real

Una de las garantías más importantes que Ludix puede dar no es técnica ni legal. Es arquitectónica.

**Si Ludix algún día traiciona sus principios, la comunidad puede forkearlo.**

Pero más importante: si alguien forkea Ludix, las firmas criptográficas de los desarrolladores siguen siendo válidas. La reputación construida es portable. Los builds verificados siguen verificados.

Esto significa que ninguna empresa — ni siquiera los creadores originales de Ludix — puede tomar el proyecto "de rehén". La infraestructura pertenece a quien la usa, no a quien la creó.

Eso es open source real, no de marketing.

---

## 6. Lo que Ludix no va a ser nunca

Vale la pena ser explícitos:

- **No es un banco ni un custodio de fondos.** Nunca.
- **No cobra comisiones por ventas.** Si un juego cuesta 10 USDT, el dev recibe 10 USDT.
- **No tiene token obligatorio.** Ningún "LudixCoin" que debas comprar para publicar o jugar.
- **No es un mercado gris de keys robadas.** Solo el dev legítimo puede vender sus propias keys.
- **No es un refugio de delitos.** El contenido ilegal no tiene lugar aquí.
- **No cambia las reglas sin que la comunidad lo sepa.** Todo es auditable.

---

## 7. A quién le habla Ludix

### Al desarrollador independiente

Que pasó meses construyendo su juego, lo publicó en una plataforma, y un día recibió un correo diciéndole que su juego fue eliminado por "violar las políticas de contenido" — políticas que cambiaron porque un procesador de pagos presionó.

Ludix le dice: **aquí puedes publicar, cobrar directamente, y nadie puede cortar ese flujo con un correo.**

### Al jugador que perdió su biblioteca

Que tenía juegos comprados y pagados que simplemente dejaron de existir. Que nunca recibió reembolso ni explicación real.

Ludix le dice: **los builds viven en los servidores del dev, verificados por hash. Si el dev decide que siguen disponibles, siguen disponibles.**

### Al contribuidor open source

Que cree en la infraestructura abierta, en la soberanía digital, y en que el software libre es una herramienta de libertad real.

Ludix le dice: **el código es tuyo para ver, auditar, criticar y mejorar.**

### A la comunidad regional

Que quiere un sistema que funcione con sus métodos de pago locales, su idioma, sus leyes, y su cultura.

Ludix le dice: **los gateways son modulares y la gobernanza es local. Construye lo que tu comunidad necesita.**

---

## 8. La apuesta

Ludix es una apuesta.

No garantizamos que funcione. No garantizamos que la adopción llegue. No garantizamos que resolvamos todos los problemas del ecosistema de distribución de juegos.

Lo que sí garantizamos es que lo intentamos con honestidad, con código abierto, y con los principios claros desde el día uno.

Si esto resuena contigo — como jugador, como dev, como contribuidor, o simplemente como alguien que cree que un banco no debería tener el poder de borrar cultura —

**bienvenido.**

---

*Documento vivo — última revisión: Fase 0.*  
*Para el contexto técnico y arquitectónico, ver `docs/es/architecture.md`.*  
*Para los principios condensados, ver `MANIFESTO.es.md`.*