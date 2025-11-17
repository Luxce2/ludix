# Ludix – Infraestructura abierta para la distribución de videojuegos

> **Estado actual:** Fase 0 – Diseño y documentación.  
> **Código:** aún no hay implementación estable; estamos definiendo la base conceptual y arquitectónica para que el código nazca sobre terreno firme.

---

## ✨ Resumen en una frase

**Ludix** es una infraestructura *open source* para distribuir videojuegos donde:

- los pagos van **directo del jugador al desarrollador** (sin bancos, sin Visa/Mastercard, sin pasarelas centrales),
- la plataforma **no cobra comisiones ni custodia dinero**,
- los juegos se descargan **desde el propio desarrollador** (o redes descentralizadas),
- y donde una empresa de tarjetas **no puede borrar miles de juegos con un correo**.

No es “otra tienda Web3 con token raro”.  
Es la respuesta técnica y ética a la censura financiera en el mundo gamer.

---

## Índice

1. [¿Por qué existe Ludix? (origen e indignación)](#por-qué-existe-ludix-origen-e-indignación)  
2. [¿Qué es Ludix exactamente?](#qué-es-ludix-exactamente)  
3. [Qué quiere ser Ludix (y qué NO)](#qué-quiere-ser-ludix-y-qué-no)  
4. [Principios y filosofía del proyecto](#principios-y-filosofía-del-proyecto)  
5. [Nuestra diferencia: no somos otro launcher Web3 con token y comisiones](#nuestra-diferencia-no-somos-otro-launcher-web3-con-token-y-comisiones)  
6. [Arquitectura general](#arquitectura-general)  
7. [Flujos principales del sistema](#flujos-principales-del-sistema)  
8. [Ludix TrustChain – Verificación de desarrolladores](#ludix-trustchain--verificación-de-desarrolladores)  
9. [Gateways de pago locales (Pix, UPI, MeliDólar, etc.)](#gateways-de-pago-locales-pix-upi-melidólar-etc)  
10. [Venta de Steam Keys y claves externas](#venta-de-steam-keys-y-claves-externas)  
11. [Contenido permitido, contenido ilegal y límites éticos](#contenido-permitido-contenido-ilegal-y-límites-éticos)  
12. [Estado actual del proyecto](#estado-actual-del-proyecto)  
13. [Roadmap por fases](#roadmap-por-fases)  
14. [Estructura del repositorio](#estructura-del-repositorio)  
15. [Cómo contribuir](#cómo-contribuir)  
16. [Licencia](#licencia)  
17. [FAQ breve](#faq-breve)

---

## ¿Por qué existe Ludix? (origen e indignación)

Ludix nace de una indignación muy concreta:

- Plataformas como Steam e itch.io han recibido **presiones directas** de procesadores de pago y bancos (Visa, Mastercard, Stripe, etc.) para eliminar o bloquear miles de juegos “problemáticos”.
- No porque fueran ilegales en todos los países, sino porque **no encajaban con los estándares internos** de riesgo, reputación o lobby moral de un puñado de empresas.
- En algunos casos, bastó:
  - una campaña de presión organizada,
  - un reporte automático de “protección de marca”,
  - o el miedo a perder el soporte de tarjetas,
  para que tiendas enteras cambiaran sus políticas y borraran contenido.

En la práctica, eso significa que:

> **Una empresa de tarjetas de crédito puede decidir, de facto, qué juegos puedes comprar y cuáles no.**

Nadie votó por ellos.  
No son un gobierno.  
No son un poder democrático.  
Pero sí tienen el poder de cortar el acceso al dinero. Y cuando controlas el dinero, controlas qué proyectos pueden sobrevivir.

Ludix nace como respuesta a eso:

- a la idea de que un juego puede morir, no por decisión de sus jugadores o leyes claras,  
- sino porque a una red de tarjetas o a un procesador de pagos “no le gustó”.

Esta no es una queja abstracta.  
Es un **problema estructural** de cómo funciona el dinero en internet.

---

## ¿Qué es Ludix exactamente?

**Ludix no es solo una “tienda de juegos”.**  
Es un conjunto de piezas que, juntas, forman una infraestructura abierta:

- Un **backend (API)** para gestionar usuarios, desarrolladores, juegos, builds, compras y reputación.
- Un **listener de blockchain (Chain Watcher)** que observa una o varias redes (ej. Polygon) y detecta pagos en stablecoins hacia las wallets de los desarrolladores.
- Un **launcher para jugadores** (escritorio; Tauri + React) donde:
  - ves el catálogo,
  - compras juegos,
  - descargas builds desde el host del dev,
  - gestionas tu biblioteca.
- Un **Dev Portal** (panel web) para que desarrolladores puedan:
  - publicar juegos,
  - registrar builds y hashes,
  - configurar precios y wallets,
  - ver compras.
- Una **especificación para Gateways de pago locales** (Pix, UPI, MeliDólar, etc.), que permiten que cada país/región agregue sus propios métodos de pago sin contaminar el core.
- Un sistema de **TrustChain** para verificar desarrolladores legítimos y reducir el fraude.

El objetivo no es construir “la nueva mega empresa de juegos”, sino:

> Proveer un **protocolo y una implementación de referencia** que cualquiera pueda usar, forkar, federar o adaptar.

---

## Qué quiere ser Ludix (y qué NO)

### 🎯 Ludix quiere ser

- Una forma **libre y abierta** de que cualquier desarrollador pueda:
  - publicar su juego,
  - recibir pagos directos en stablecoins,
  - controlar dónde se alojan sus builds.

- Una herramienta para que cualquier jugador pueda:
  - descubrir juegos,
  - pagarle directamente al dev,
  - conservar acceso incluso cuando cambien las reglas de terceros.

- Un proyecto **100% open source**, auditable y forkeable.

- Una **infraestructura neutra**:
  - sin agenda política,
  - sin moralina corporativa,
  - sin dependencia de un solo país, banco o empresa.

### 🚫 Ludix NO quiere ser

- Una plataforma propietaria cerrada que luego cambia las reglas a mitad de camino.
- Un “token” especulativo más que vive de la volatilidad.
- Un sistema que cobra comisión por cada transacción.
- Un custodio de fondos, banco paralelo o pasarela de pago.
- Un reemplazo de todas las leyes del mundo:
  - cada país sigue teniendo sus normas,
  - Ludix no es un refugio para delitos graves.
- Una “tienda gris” de keys robadas o licencias en zonas grises tipo G2A.

---

## Principios y filosofía del proyecto

1. **Libertad financiera para devs y jugadores**  
   El dinero debe fluir del jugador al desarrollador sin que un tercero (banco, procesador, tarjeta) pueda matar ese flujo por pura política interna.

2. **Cero comisiones de plataforma**  
   Ludix no se lleva porcentaje de las ventas.  
   Si un juego cuesta 10 USDT, el dev recibe 10 USDT (más allá de fees de red).

3. **No-custodia de fondos**  
   Ludix nunca tiene tu plata.  
   La plata se mueve:
   - de la wallet del jugador  
   - a la wallet del dev  
   El sistema solo observa la blockchain y registra la compra.

4. **Open source real, no de marketing**  
   - Código visible,
   - licencia clara,
   - posibilidad de fork,
   - posibilidad de desplegar tu propio nodo o instancia.

5. **Interoperabilidad y modularidad**  
   - Soporte a múltiples stablecoins y redes.
   - Gateways de pago locales opcionales.
   - Backends y launchers reemplazables.

6. **Respeto a las leyes básicas, rechazo a la censura financiera privada**  
   - Contenido ilegal grave (CSAM, terrorismo, fraude) no es bienvenido.  
   - Contenido legal pero “incómodo” para bancos debe poder existir sin que Visa/Mastercard dicten la pauta.

7. **Infraestructura, no imperio**  
   Ludix quiere ser:
   - plano,
   - federable,
   - sin un “dueño” que mañana decida monetizar la libertad con un giro de tuerca.

---

## Nuestra diferencia: no somos otro “launcher Web3 con token y comisiones”

En el ecosistema de juegos “alternativos” han aparecido muchas plataformas que prometen:

- libertad,
- propiedad,
- descentralización,

pero en la práctica se centran en:

- vender NFTs que nadie pidió,
- introducir un token propio obligatorio para publicar o comprar,
- cobrar comisiones disfrazadas de “gas”, “metadatos”, “minting” o “fees de red”,
- crear dependencia hacia una empresa, su blockchain o su token,
- cerrar el ecosistema mientras lo venden como “abierto”.

**Ludix no comparte ese modelo.**

Ludix no nace para:

- lanzar un token,  
- hacer ICO,  
- ni levantar capital especulativo.

Ludix nace para resolver problemas estructurales reales:

- censura financiera de contenido,
- dependencia extrema de pasarelas tradicionales,
- dificultad para que devs pequeños cobren a nivel global,
- fragilidad de la distribución digital cuando un solo proveedor cambia las reglas.

Por eso, Ludix se compromete a:

- **No tener un token nativo obligatorio.**  
  Si alguna vez existe un token experimental:
  - no será requisito para usar la plataforma,
  - no será necesario para publicar o comprar juegos.

- **No cobrar comisiones por venta.**  
  El modelo de negocio futuro (si lo hay) nunca será quedarse con un % del dev.

- **No custodiar fondos ni actuar como banco.**  
  Ludix no quiere tu dinero; quiere que el dev lo reciba directo.

- **No convertir todo en NFT por defecto.**  
  Los juegos son juegos.  
  Si un dev quiere experimentar con NFTs, será un módulo aparte, nunca obligatorio.

- **Ser completamente open source y auditable.**  
  Cualquiera puede:
  - revisarlo,
  - criticarlo,
  - forkarlo,
  - levantar su propia instancia.

- **Aceptar donaciones solo cuando el proyecto realmente lo merezca.**  
  No hay “paga primero, después vemos qué sale”.  
  Primero se construye, luego se verá si alguien quiere apoyar.

Esta postura no es marketing.  
Es el núcleo de lo que Ludix quiere ser:  
**infraestructura libre, sin agendas ocultas ni tokenomics turbios.**

---

## Arquitectura general

A alto nivel, Ludix se compone de los siguientes bloques:

### 1. Backend – Ludix Core API (`backend/`)

- Implementado en FastAPI (Python) + PostgreSQL (propuesto).
- Responsabilidades:
  - Autenticación y gestión de usuarios.
  - Gestión de desarrolladores y su verificación (TrustChain).
  - Registro de juegos y builds (con hashes de integridad).
  - Registro de compras y su estado.
  - API para:
    - Launcher (jugadores),
    - Dev Portal (desarrolladores),
    - Chain Watcher,
    - Gateways locales.

### 2. Chain Watcher – Listener de blockchain (`chain-watcher/`)

- Servicio separado que:
  - Escucha una o varias redes (ej. Polygon).
  - Detecta transacciones hacia wallets de desarrolladores.
  - Filtra por tokens soportados (ej. USDT/USDC/DAI).
  - Valida:
    - monto ≥ precio,
    - destinatario correcto,
    - confirmaciones suficientes,
    - no duplicidad.
  - Notifica al backend vía una API bien definida (ej. `POST /payments/confirm`).

Ludix nunca toca los fondos; solo observa la red y confirma compras.

### 3. Launcher – Cliente para jugadores (`launcher/`)

- Desktop (Tauri + React).
- Funcionalidades:
  - Login / registro.
  - Catálogo de juegos (exploración).
  - Pantalla de juego con:
    - descripción,
    - precio,
    - métodos de pago soportados.
  - Generación de instrucciones de pago:
    - QR / dirección / monto en stablecoin.
  - Estado de compra (pendiente / confirmado).
  - Descarga e instalación:
    - descarga desde URL del dev,
    - verificación de hash,
    - gestión de biblioteca.

### 4. Dev Portal – Panel de desarrolladores (`dev-portal/`)

- Aplicación web (React, Svelte o similar).
- Funcionalidades:
  - Configurar perfil de estudio.
  - Verificación de dominio / identidad (TrustChain).
  - Publicar juegos:
    - nombre, descripción, tags, imágenes.
  - Registrar builds:
    - URL de descarga,
    - hash,
    - tamaño estimado.
  - Configurar precios y monedas aceptadas.
  - Ver histórico de compras.

### 5. Gateways de pago locales (`gateways/`)

- Complemento opcional.
- Cada gateway:
  - se conecta a un sistema de pago local (Pix, UPI, MeliDólar, etc.),
  - verifica pagos según las reglas locales,
  - notifica al backend de Ludix exactamente igual que el Chain Watcher on-chain.

Así, el core de Ludix no se contamina con requisitos regulatorios o APIs cerradas de cada país.

### 6. Infraestructura (`infra/`)

- Archivos de:
  - `docker-compose.dev.yml` para levantar entorno de desarrollo (backend + db + watcher).
  - Manifiestos de Kubernetes (en el futuro).
  - Configuración base (`env.example`).

---

## Flujos principales del sistema

> Los detalles finos se describen en `docs/flows/` (cuando existan los archivos).  
> Aquí va una vista general.

### 1. Registro de usuario (jugador)

1. El jugador abre el launcher.
2. Crea una cuenta con email y contraseña (MVP).
3. El backend:
   - crea el usuario,
   - devuelve un token de sesión.
4. El launcher guarda la sesión y muestra el catálogo.

### 2. Onboarding de desarrollador

1. Un usuario registrado solicita convertirse en desarrollador.
2. El sistema le pide:
   - nombre del estudio,
   - sitio web oficial,
   - dominio a verificar,
   - wallet pública principal,
   - datos opcionales (redes, GitHub, etc.).
3. Ludix genera un desafío de verificación (archivo en el dominio, registro DNS, etc.).
4. El dev lo configura.
5. Ludix verifica y activa el modo desarrollador en esa cuenta.
6. De ahí en adelante, ese usuario puede publicar juegos.

### 3. Publicación de un juego

1. El dev entra al Dev Portal.
2. Crea un juego:
   - título,
   - descripción,
   - imágenes,
   - tags,
   - clasificación de contenido.
3. Define:
   - precio en alguna stablecoin,
   - wallet de destino.
4. Registra un build:
   - URL de descarga (su servidor, S3, IPFS, etc.),
   - hash SHA-256 del archivo,
   - tamaño aproximado.
5. El backend guarda todo y el juego aparece publicado en el catálogo.

### 4. Compra on-chain (estable, sin intermediarios financieros)

1. El jugador abre la ficha del juego en el launcher.
2. Ve:
   - precio,
   - red soportada,
   - token (ej. USDT),
   - dirección de wallet del dev (o de un contrato, en el futuro).
3. El launcher genera una instrucción:
   - QR,
   - o dirección + monto exacto.
4. El jugador paga desde su wallet de stablecoins.
5. El Chain Watcher:
   - detecta la transacción,
   - verifica token, monto, destino, confirmaciones.
6. Si es válida:
   - llama al backend (`/payments/confirm`) con:
     - user_id,
     - game_id,
     - tx_hash,
     - amount,
     - timestamp.
7. El backend registra la compra como “confirmada”.
8. El launcher actualiza el estado y habilita el botón “Descargar”.

### 5. Descarga e instalación

1. El jugador hace clic en “Descargar”.
2. El launcher pide al backend la información del build:
   - URL,
   - hash esperado.
3. Descarga el archivo desde el host del dev.
4. Verifica el hash:
   - si coincide → instala/extrae,
   - si no coincide → alerta y bloqueo.
5. El juego queda en la biblioteca.

### 6. Reportes y reputación

1. Si un jugador detecta:
   - malware,
   - contenido fraudulento,
   - key inválida,
   puede enviar un reporte.
2. El backend almacena el reporte asociado a:
   - juego,
   - desarrollador.
3. El sistema puede:
   - mostrar advertencias en ese juego/dev,
   - escalar internamente para revisión,
   - tomar acciones si la evidencia lo justifica.

---

## Ludix TrustChain – Verificación de desarrolladores

Para que no cualquiera se haga pasar por otro estudio, Ludix define **TrustChain**, un sistema por capas para verificar desarrolladores de forma robusta, sin convertir esto en burocracia corporativa.

### Capas propuestas

1. **Criptografía (identidad técnica)**
   - Cada dev tiene una clave pública registrada.
   - Puede firmar hashes de sus builds o metadatos.
   - Ludix valida firmas con esa clave pública.
   - Si alguien roba el juego pero no tiene la clave privada del estudio, no puede firmar.

2. **Verificación de dominio**
   - El dev demuestra control de su dominio oficial:
     - subiendo un archivo específico,
     - o añadiendo un registro DNS.
   - Ludix verifica automáticamente.
   - Esto vincula:
     - cuenta Ludix ↔ dominio ↔ marca.

3. **Vínculos con cuentas oficiales**
   - Opcional:
     - GitHub,
     - Twitter/X,
     - Itch.io,
     - Steam (Steamworks),
     - etc.
   - Estos vínculos aumentan la confianza, pero no son obligatorios para todos.

4. **Builds con hashes y (opcionalmente) firmas**
   - Todo build tiene hash obligatorio.
   - Puede además firmarse con PGP u otra clave asociada al estudio.
   - Cualquier alteración salta.

5. **Reputación y reportes**
   - A medida que los jugadores compran y usan los juegos:
     - se acumula reputación,
     - se registran reportes.
   - Devs con buena conducta y cero incidentes ganan confianza.
   - Devs con muchos reportes por fraude/malware pueden ser limitados o expulsados.

6. **Requisitos extra para vender Steam Keys y claves externas**
   - Para vender Steam Keys (u otras claves externas), se exigirá un nivel de verificación superior:
     - pruebas de que controla el Steam App ID,
     - o mecanismos equivalentes.
   - Esto busca evitar cualquier parecido con mercados grises/ilegales de keys.

---

## Gateways de pago locales (Pix, UPI, MeliDólar, etc.)

La arquitectura de Ludix parte de una premisa:

> **El core es on-chain (stablecoins), pero el mundo es diverso y hay sistemas locales muy potentes.**

En vez de meter Pix, UPI, MeliDólar, etc. dentro del core, Ludix define el concepto de **Ludix Payment Gateways**:

- Pequeños servicios externos que:
  - hablan con un sistema local (Pix, UPI, Mercado Pago, etc.),
  - verifican pagos siguiendo reglas locales,
  - y, una vez confirmados, llaman al backend de Ludix con el mismo formato que el Chain Watcher (`/payments/confirm`).

Ventajas:

- El core permanece:
  - simple,
  - global,
  - independiente.

- Cada país/comunidad puede:
  - implementar su propio gateway,
  - cumplir con sus leyes,
  - agregar sus métodos sin tocar el corazón del sistema.

Ejemplos:

- `gateway-chile-melidolar`  
- `gateway-brasil-pix`  
- `gateway-india-upi`  

Todos obedecen una interfaz definida en `specs/rfc-0006-gateway-plugin-interface.md` (cuando exista).

---

## Venta de Steam Keys y claves externas

Ludix permitirá, como opción, que los desarrolladores vendan **Steam Keys u otras claves externas** para sus juegos, pero con reglas estrictas:

1. **Solo el desarrollador/publisher legítimo puede vender keys de un juego.**
   - No se permite reventa de terceros.
   - Evita mercados grises tipo G2A/Kinguin.

2. **Ludix no genera keys ni valida activaciones en Steam.**
   - La responsabilidad de la clave es del dev.
   - Ludix solo:
     - registra que hubo compra,
     - facilita la entrega de la key.

3. **Entrega de claves**
   - Por API propia del dev (lo ideal),
   - O mediante un stock de claves cifrado, subido y administrado por el dev.

4. **Trusted-only (TrustChain)**
   - Solo devs con nivel de verificación suficiente (TrustChain) pueden usar esta funcionalidad.
   - Si un dev entrega muchas keys inválidas:
     - se verá reflejado en su reputación,
     - y puede perder el acceso a esta función.

---

## Contenido permitido, contenido ilegal y límites éticos

Ludix no quiere ser:

- ni policía moral,
- ni refugio de delitos.

La línea base:

- **Contenido ilegal grave a nivel internacional** (ej. explotación infantil, terrorismo, fraude claro) no es aceptable.
- **Contenido legal pero polémico** (temas adultos, violencia explícita, crítica política dura, etc.) no debería ser eliminado por decisión de bancos o tarjetas.

La idea es que:

- el límite lo marquen:
  - las leyes mínimas reconocidas,
  - la comunidad,
  - y la reputación del dev,  
no una empresa de tarjetas.

Esto se irá afinando con la comunidad, pero la dirección es clara:  
**más libertad que las tiendas actuales, sin caer en la impunidad absoluta.**

---

## Estado actual del proyecto

- **Fase 0 – Diseño y documentación**
  - No hay código de producción todavía.
  - Estamos:
    - definiendo la arquitectura,
    - documentando flujos,
    - formalizando la visión y los principios,
    - diseñando TrustChain y Gateways,
    - dejando la estructura del repo lista para abrir a contribuciones.

Este README y los documentos en `docs/` y `specs/` son parte de esa fase.

---

## Roadmap por fases

> Las fases son conceptuales, no compromisos calendarizados.

### Fase 0 – Fundamentos (donde estamos)

- README(s).
- Visión, contexto de censura financiera y motivación.
- Diseño de arquitectura general.
- Flujos principales definidos.
- TrustChain (concepto).
- Diseño de Gateways locales.
- Estructura del repositorio.

### Fase 1 – MVP técnico (línea de base)

- Backend básico:
  - usuarios,
  - devs,
  - juegos,
  - builds,
  - compras simples.
- Chain Watcher MVP:
  - 1 red (ej. Polygon),
  - 1 stablecoin (ej. USDT).
- Launcher MVP:
  - login,
  - catálogo sencillo,
  - compra,
  - descarga.
- Dev Portal MVP:
  - publicar juegos,
  - registrar builds,
  - configurar una wallet.

### Fase 2 – TrustChain y reputación

- Implementación inicial de TrustChain:
  - verificación de dominio,
  - claves públicas,
  - reputación básica.
- Sistema de reportes y advertencias.
- Restricción de venta de claves externas a devs verificados.

### Fase 3 – Gateways locales y expansión

- Implementación de:
  - un Gateway de ejemplo (mock),
  - y al menos un Gateway real (probablemente Chile o Brasil).
- Soporte para múltiples redes/tokens en el watcher.
- Mejora del launcher (UI/UX) para mostrar métodos de pago alternativos.

### Fase 4 – Ecosistema y comunidad

- Documentación extensa para contribuidores.
- Guías para montar tu propia instancia de Ludix.
- Integración con comunidades de devs y gamers.
- Evolución según el uso real y el feedback.

---

## Estructura del repositorio

A alto nivel, el repositorio se organiza así (propuesta):

```bash
ludix/
├── README.md / README.es.md
├── docs/
│   ├── es/
│   │   ├── vision.md
│   │   ├── context-censorship.md
│   │   ├── architecture.md
│   │   ├── trustchain.md
│   │   ├── gateways.md
│   │   └── flows/...
│   └── en/
│       └── (esqueletos para traducción futura)
├── specs/
│   ├── rfc-0001-payment-flow.md
│   ├── rfc-0002-data-model.md
│   ├── rfc-0003-launcher-api.md
│   ├── rfc-0004-chain-watcher-spec.md
│   ├── rfc-0005-trustchain-spec.md
│   └── rfc-0006-gateway-plugin-interface.md
├── backend/
├── chain-watcher/
├── launcher/
├── dev-portal/
├── gateways/
└── infra/

Los detalles se iran afinando a medida que se agregue codigo

## Cómo contribuir

> **Estado actual:** Fase 0 — aún no aceptamos PRs grandes de código.  
> Pero sí estamos abiertos a feedback, ideas y discusión técnica.

Si quieres opinar sobre:

- arquitectura,  
- flujos,  
- modelo de datos,  
- especificaciones técnicas,

abre un **Issue** y márcalo como `discussion`.

Si quieres ayudar con:

- documentación,  
- traducciones,  
- diseño del launcher (UI/UX),  
- diseño visual (branding, paleta, identidad),  

también eres totalmente bienvenido.

### Cuando exista una base de código estable:

- Se describirá el flujo de contribución en `CONTRIBUTING.md`.  
- Se publicarán issues marcados como **good first issue**.  
- Se definirá un estándar claro de código y estilo.  
- Se abrirán PRs para módulos específicos.

---

## Licencia

La licencia definitiva aún no está definida.  
Las opciones más probables son:

- **MIT**  
- **Apache 2.0**  
- **GPLv3** (o alguna variante copyleft fuerte)

Antes de publicar el primer código funcional del proyecto, se fijará la licencia y se actualizará este apartado para dejarlo explícito.

---

## FAQ breve

### **¿Puedo usar Ludix para cobrar solo con Pix / MercadoPago / UPI sin usar stablecoins?**

El core de Ludix está diseñado en torno a **pagos directos on-chain con stablecoins**.  
Los Gateways locales son **opcionales** y complementan esa base.

Puedes montar una instancia más “localizada”,  
pero la filosofía de Ludix es evitar depender de bancos o pasarelas tradicionales.

---

### **¿Ludix tendrá su propia moneda/token?**

No.  
El proyecto está diseñado **explícitamente** para evitar ese camino.

Si en un futuro experimental se introduce algún token:
- no será obligatorio,
- no será requisito para publicar o comprar juegos,
- no formará parte del core.

---

### **¿Puedo subir cualquier cosa?**

No.

- Contenido **ilegal grave** (CSAM, terrorismo, fraude) no es aceptado.  
- Contenido **legal pero polémico**, en principio sí, mientras respete:
  - leyes mínimas de cada región,
  - lineamientos comunitarios,
  - y la misión del proyecto.  

Una política de contenido formal se documentará junto a la comunidad.

---

### **¿Dónde está el código?**

Ludix está en **Fase 0** (diseño + documentación).  
Antes de escribir código queremos:

- arquitectura clara,  
- flujos bien definidos,  
- seguridad pensada desde el día uno,  
- base filosófica sólida.

Es un proyecto grande: avanzar sin mapa sería un error.  
El código vendrá después, en Fase 1.

---

## Mensaje final

**Ludix nace por un motivo real y urgente:**

> Que ningún juego vuelva a desaparecer solo porque a una empresa de tarjetas  
> o a un procesador de pagos le incomoda su existencia.

Si esta visión te hace sentido —como jugador, como dev, o como amante del software libre—  
**eres bienvenido a unirte.**
