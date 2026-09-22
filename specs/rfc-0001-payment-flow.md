# RFC-0001: Flujo de Compra y Pago No Custodial

> **Estado:** Fase 0 — Flujo aprobado conceptualmente; Polygon PoS + USDC nativo, ventana de pago de 60 minutos y reconciliación automática de 72 horas aprobados para Fase 1; finalidad exacta pendiente.
> **Ámbito:** MVP on-chain de Ludix.
> **Objetivo:** definir cómo una transferencia directa entre jugador y desarrollador se convierte, de forma verificable e idempotente, en un derecho de acceso a un juego sin que Ludix custodie fondos ni pueda gastarlos.

---

## 1. Resumen

Ludix no procesa, retiene ni reenvía dinero.

Para una compra on-chain, Ludix crea primero una **intención de pago inmutable** (`Payment Intent`) que congela las condiciones de la compra. El jugador demuestra control de la wallet desde la que pagará. El desarrollador, antes de poder vender, ha demostrado control de la wallet en la que recibirá fondos.

El jugador realiza una transferencia directa desde su wallet a la wallet del desarrollador. Después presenta a Ludix la transacción realizada. Un componente independiente, el **Chain Watcher**, observa la blockchain y aporta evidencia objetiva sobre la transferencia. El Core API compara esa evidencia con el `Payment Intent`.

Cuando la evidencia satisface todas las reglas del intento de pago y alcanza la finalidad exigida por Ludix, el pago se considera confirmado y el Core crea un **Entitlement**: el derecho persistente de la cuenta del jugador a acceder al juego.

La wallet demuestra el pago. La wallet no es el derecho adquirido.

### Principio rector

> **Ludix no mueve dinero. Ludix crea una intención verificable, observa una transferencia directa y, cuando la evidencia es suficiente, concede un derecho de acceso.**

Dos reglas complementarias son fundamentales:

> **Ludix puede verificar una wallet; nunca puede gastar desde ella.**

> **Un pago confirmado produce un Entitlement; la wallet no es el Entitlement.**

---

## 2. Objetivos de diseño

El flujo de pago del MVP debe cumplir simultáneamente con las siguientes propiedades:

1. **No custodia:** los fondos viajan directamente del jugador al desarrollador.
2. **Sin comisión de plataforma:** Ludix no recibe un porcentaje de la venta.
3. **Sin permisos de gasto:** Ludix nunca solicita ni necesita capacidad para mover tokens del jugador.
4. **Correlación inequívoca:** una transferencia válida debe poder asociarse a una compra concreta sin adivinar intención a partir de la blockchain.
5. **Prueba de control:** tanto la wallet pagadora como la wallet receptora deben demostrar control criptográfico antes de participar en el flujo normal del MVP.
6. **Idempotencia:** una transferencia concreta no puede conceder dos compras ni dos Entitlements.
7. **Recuperabilidad:** cerrar el launcher, perder conectividad o sufrir una caída temporal del Chain Watcher no debe destruir una compra válida.
8. **Determinismo:** las mismas evidencias y las mismas reglas deben producir el mismo resultado.
9. **Trazabilidad:** las decisiones relevantes del flujo deben quedar registradas de forma auditable.
10. **Simplicidad del MVP:** una red, un activo y una transferencia por compra son preferibles a una capa financiera compleja.
11. **Modularidad futura:** confirmar un pago y conceder un Entitlement deben ser conceptos separados para permitir otros métodos de adquisición más adelante.

---

## 3. No objetivos del MVP

Este RFC no pretende resolver todavía:

- pagos desde exchanges custodiales;
- pagos realizados por una wallet distinta de la wallet vinculada al intento;
- dividir una compra en múltiples transferencias;
- agrupar varias compras en una sola transferencia;
- pagos mediante smart contracts obligatorios;
- `approve`, allowances, `permit` o autorizaciones para que Ludix gaste tokens del usuario;
- meta-transactions o gas patrocinado;
- abstracción de cuentas;
- puentes entre redes;
- swaps automáticos;
- reembolsos automáticos;
- conversión fiat;
- gateways locales como Pix, UPI o Mercado Pago;
- federación entre instancias Ludix;
- privacidad on-chain avanzada;
- resolución de disputas comerciales fuera de la verificación técnica del pago.

Estos problemas pueden abordarse posteriormente sin modificar el principio fundamental de este RFC: **un método de adquisición aporta evidencia; el Core decide si esa evidencia concede un Entitlement**.

---

## 4. Terminología

### 4.1 Payment Intent

Registro inmutable que representa las condiciones exactas aceptadas al iniciar una compra.

No mueve fondos, no es una factura bancaria y no concede por sí mismo acceso al juego.

### 4.2 Payment Evidence

Evidencia objetiva observada en la blockchain sobre una transferencia concreta: red, transacción, evento, activo, origen, destino, cantidad, bloque y estado de confirmación.

### 4.3 Payment

Resultado de evaluar evidencia de pago contra un `Payment Intent`.

Un pago puede estar pendiente, confirmándose, confirmado o rechazado sin que ello implique que Ludix haya tocado los fondos.

### 4.4 Entitlement

Derecho persistente de una **cuenta Ludix** a acceder a un juego adquirido.

Un Entitlement no pertenece a una wallet. La wallet es un mecanismo de prueba y pago; el derecho adquirido pertenece a la cuenta.

### 4.5 Wallet Proof

Prueba criptográfica de que una persona o proceso controla una dirección, realizada mediante la firma de un desafío emitido por Ludix.

La prueba nunca requiere revelar una clave privada.

### 4.6 Chain Watcher

Servicio que observa una blockchain y devuelve hechos verificables sobre transferencias y confirmaciones.

El Chain Watcher **no decide qué usuario compró qué juego** y no concede Entitlements.

---

## 5. Invariantes de seguridad

Las siguientes reglas son invariantes del diseño y no simples decisiones de interfaz.

### 5.1 Ludix nunca puede gastar desde la wallet del jugador

El MVP no debe solicitar:

- claves privadas;
- frases semilla;
- approvals ERC-20;
- allowances;
- firmas `permit`;
- autorización general para mover fondos;
- custodia temporal de fondos.

La interacción financiera normal del jugador consta únicamente de:

1. firmar un mensaje para demostrar control de su wallet;
2. firmar una transferencia directa hacia la wallet del desarrollador.

### 5.2 El desarrollador debe demostrar control de su wallet de cobro

Una dirección no puede convertirse en wallet de cobro activa solo porque alguien la escribió en un formulario.

Antes de recibir ventas mediante el flujo normal del MVP, el desarrollador debe demostrar criptográficamente que controla esa dirección.

### 5.3 Un Payment Intent es inmutable

Una vez creado, sus condiciones económicas y criptográficas no se editan.

Cambios posteriores en:

- precio;
- activo;
- red;
- wallet del desarrollador;
- configuración del juego;

solo afectan a nuevos intents.

### 5.4 Una evidencia de transferencia solo puede consumirse una vez

La unidad de identidad del pago on-chain no es únicamente `tx_hash`.

Debe identificar inequívocamente la transferencia concreta mediante, como mínimo:

- red / `chain_id`;
- `tx_hash`;
- identificador del evento de transferencia dentro de la transacción, por ejemplo `log_index` en una red EVM.

La combinación debe ser única dentro de Ludix y no puede confirmar más de un Payment Intent.

### 5.5 El Entitlement pertenece a la cuenta

Después de una compra confirmada, cambiar, perder o dejar de usar la wallet pagadora no elimina el juego de la biblioteca del usuario.

---

## 6. Alcance del MVP on-chain

La primera implementación debe ser deliberadamente pequeña y queda fijada así. **Decisión adoptada: 2026-09-22.**

- **Red:** Polygon PoS mainnet.
- **Chain ID:** `137`.
- **Activo de pago:** USDC nativo emitido por Circle.
- **Contrato USDC nativo:** `0x3c499c542cef5e3811e1192ce70d8cc03d5c3359`.
- **Decimales:** `6`.
- **Gas:** POL.
- **No soportado en el MVP:** USDC.e puenteado u otros tokens que compartan ticker o apariencia similar.
- transferencia directa del token desde jugador a desarrollador;
- una transferencia por compra;
- una wallet pagadora que pueda firmar;
- una wallet receptora verificada por el desarrollador;
- una cuenta Ludix autenticada;
- un Payment Intent por compra activa;
- un Entitlement por cuenta y juego cuando corresponda.

La selección Polygon PoS + USDC nativo es una **decisión de alcance para Fase 1**, no una dependencia arquitectónica permanente. Se elige porque permite mantener el primer Chain Watcher dentro del modelo EVM/ERC-20, ofrece una ruta de pagos de bajo costo y finalidad rápida, y evita introducir desde el día uno múltiples modelos de wallet y parsing de transacciones.

Esta restricción es deliberadamente transitoria. La dirección futura de Ludix es ampliar de forma gradual los activos y redes compatibles, siempre que puedan integrarse sin introducir custodia ni permisos de gasto por parte de Ludix.

El objetivo de esa expansión no es acumular blockchains por cantidad, sino **reducir dependencias concentradas y ofrecer caminos de pago cada vez menos expuestos a la decisión unilateral de un único proveedor, emisor o intermediario privado**.

### 6.1 Estrategia aprobada: A para implementar, D para diseñar

Ludix adopta explícitamente una estrategia combinada:

> **Implementar una sola ruta de pago de forma simple y robusta, pero diseñar el Core para que ninguna red, activo o proveedor concreto forme parte de la definición permanente del protocolo.**

Esto significa que Fase 1 puede ser deliberadamente específica en sus adaptadores y herramientas sin volver específicos al dominio central ni al derecho adquirido.

La primera implementación puede conocer Polygon PoS y USDC en el Chain Watcher, en configuración y en la experiencia de wallet. El Core, sin embargo, debe razonar sobre **Payment Intents, evidencia, pagos y Entitlements**, no sobre la idea de que “Polygon” o “USDC” sean sinónimos de compra.

### 6.2 Salvaguardas arquitectónicas obligatorias

La aprobación de Polygon PoS + USDC nativo está condicionada a estas reglas:

1. **Solo USDC nativo satisface el rail del MVP.** USDC.e, tokens puenteados o contratos distintos no se aceptan por parecido de nombre o símbolo.
2. **Un activo se identifica siempre por red + contrato**, nunca por ticker aislado.
3. **La confirmación no depende de una API de Circle.** El Chain Watcher obtiene la evidencia desde la blockchain mediante infraestructura RPC reemplazable.
4. **El Core no contiene lógica de negocio equivalente a “Polygon = pago válido”.** Polygon pertenece al adaptador/rail y a su política, no al significado de una compra.
5. **El Entitlement es independiente del rail.** Una vez concedido, no depende de Polygon, USDC ni de la wallet pagadora para existir en la biblioteca.
6. **Polygon y USDC se documentan como dependencias iniciales reemplazables**, no como requisitos eternos del protocolo Ludix.
7. **La expansión futura debe incluir diversidad real de rails**, no únicamente múltiples redes que compartan exactamente los mismos puntos de control. Una segunda familia tecnológica, como Solana u otra equivalente, es una dirección válida a estudiar.
8. **Ludix debe estudiar en fases futuras al menos una ruta que no dependa de un emisor central de stablecoin**, aceptando que ello puede introducir volatilidad, pricing u otras complejidades que no pertenecen a Fase 1.

Estas reglas permiten empezar pragmáticamente sin confundir simplicidad inicial con dependencia estructural.

---

## 7. Identidad del activo y cantidades

Un ticker como `USDC` no identifica de forma suficiente un activo. Para el MVP, la única combinación válida es la definida por `chain_id = 137` y el contrato nativo de USDC indicado en la sección anterior.

El símbolo mostrado en UI nunca sustituye la validación por red + contrato. En particular, **USDC.e no es válido para el flujo de compra del MVP** aunque una wallet o explorador lo muestre con un nombre parecido.

El Payment Intent debe congelar al menos:

- `chain_id`;
- dirección del contrato del token;
- cantidad esperada en **unidades atómicas enteras** del token;
- número de decimales conocido para presentación;
- símbolo únicamente como dato de interfaz.

La cantidad canónica nunca debe almacenarse como un decimal financiero genérico de dos posiciones.

Ejemplo conceptual:

```text
Activo mostrado: 10.00 USDC
Cantidad canónica: 10000000 unidades atómicas
Decimales: 6
```

La comparación de montos se realiza usando enteros en unidades atómicas.

---

## 8. Prueba de control de wallet

### 8.1 Wallet del jugador

Antes de crear o completar un Payment Intent, Ludix debe conocer una wallet pagadora cuya posesión haya sido demostrada por el usuario autenticado.

El servidor entrega un desafío de firma que debe incluir suficiente contexto para impedir reutilización, como mínimo:

- identificador o dominio de la instancia Ludix;
- dirección que se pretende verificar;
- nonce aleatorio de un solo uso;
- propósito explícito de la firma;
- instante de emisión;
- expiración;
- red prevista cuando sea relevante.

Una firma válida demuestra control de la clave en ese momento. No concede permiso para mover fondos.

### 8.2 Wallet del desarrollador

La activación o sustitución de una wallet de cobro requiere el mismo principio: desafío de un solo uso y prueba criptográfica de control.

El cambio de wallet debe tratarse como una operación sensible y auditable.

Los Payment Intents ya creados conservan la wallet receptora que fue congelada en ellos.

### 8.3 Replay

Un desafío consumido o expirado no puede reutilizarse.

Las firmas de prueba de wallet no deben ser aceptadas como autorización de pago ni como autorización general sobre la cuenta.

---

## 9. Creación del Payment Intent

Cuando un usuario elegible pulsa **Comprar**, el Core crea un Payment Intent.

El intento debe congelar como mínimo:

- identificador único del intent;
- usuario Ludix comprador;
- juego y/o oferta adquirida;
- wallet pagadora verificada;
- wallet receptora verificada;
- `chain_id`;
- contrato exacto del token;
- cantidad esperada en unidades atómicas;
- metadatos necesarios para mostrar precio y activo;
- instante de creación;
- instante de expiración;
- política de confirmaciones/finalidad aplicable;
- estado inicial.

El intent debe conservar un snapshot de las condiciones relevantes incluso si el catálogo cambia después.

### 9.1 Reutilización de un intento activo

Si el usuario intenta comprar nuevamente el mismo juego mientras existe un Payment Intent activo compatible, el Core debe devolver el intento existente en lugar de crear silenciosamente múltiples oportunidades de pago.

### 9.2 Usuario que ya posee el juego

Si existe un Entitlement vigente para la cuenta y el juego, el Core no debe crear una segunda compra normal por accidente.

La interfaz debe presentar el juego como perteneciente a la biblioteca.

---

## 10. Ejecución del pago

El launcher presenta las condiciones congeladas del intent y solicita al wallet software del jugador una transferencia directa del token.

Flujo económico:

```text
wallet del jugador  ───── stablecoin ─────>  wallet del desarrollador
```

No existe un salto intermedio por una wallet de Ludix.

Ludix debe mostrar de forma clara:

- red;
- token;
- cantidad;
- dirección receptora;
- fee de red estimado cuando sea posible;
- advertencia de que el usuario necesita el activo nativo necesario para pagar gas cuando la red lo requiera.

### 10.1 Gas

El fee de red no es una comisión de Ludix.

Para el MVP, Ludix no intenta ocultar el gas mediante paymasters, meta-transactions ni mecanismos equivalentes. La interfaz debe explicarlo de forma transparente.

---

## 11. Presentación de la transacción

Después de enviar la transferencia, el launcher presenta al Core el `tx_hash` asociado al Payment Intent.

El `tx_hash` suministrado por el cliente es una **afirmación no confiable** hasta que el Chain Watcher encuentre y verifique evidencia compatible.

Presentar un hash incorrecto no debe destruir el Payment Intent. Mientras el intento continúe siendo recuperable, el usuario puede corregir la referencia presentada.

Cerrar el launcher tampoco cancela el intento ni invalida una transferencia ya realizada.

---

## 12. Responsabilidad del Chain Watcher

El Chain Watcher debe ser deliberadamente pequeño y reemplazable.

Su responsabilidad es responder preguntas objetivas como:

> "En esta red existe una transferencia de este contrato de token, desde A hacia B, por cantidad C, dentro de la transacción D, en el bloque E, con N confirmaciones."

No debe decidir:

- qué usuario compró;
- qué juego fue adquirido;
- si debe concederse un Entitlement;
- si una transferencia corresponde comercialmente a una compra;
- políticas de catálogo o reputación.

Esas decisiones pertenecen al Core.

### 12.1 Evidencia mínima esperada

Para una transferencia EVM, la evidencia normal debería incluir como mínimo:

- `chain_id`;
- `tx_hash`;
- `block_number`;
- identificador del bloque cuando sea necesario para detectar reorgs;
- `log_index` o identificador equivalente del evento;
- contrato del token;
- dirección de origen;
- dirección de destino;
- cantidad en unidades atómicas;
- número de confirmaciones o estado de finalidad;
- estado de la transacción subyacente.

---

## 13. Reglas de validación del Core

Una evidencia puede confirmar un Payment Intent únicamente si todas las condiciones obligatorias coinciden.

El Core debe comprobar, como mínimo:

1. que el intent existe;
2. que pertenece al usuario autenticado que intenta completarlo;
3. que la red coincide;
4. que el contrato del token coincide exactamente;
5. que la transferencia proviene de la wallet pagadora congelada;
6. que la transferencia llega a la wallet receptora congelada;
7. que la cantidad satisface la regla de monto;
8. que la transferencia concreta no fue consumida antes;
9. que la transacción subyacente tuvo éxito;
10. que se alcanzó la política de confirmaciones/finalidad requerida;
11. que la evidencia no quedó invalidada por una reorganización antes de alcanzar finalidad;
12. que las reglas temporales del intento se cumplen.

El launcher nunca es autoridad sobre estos hechos.

---

## 14. Regla de monto

### 14.1 Pago exacto

Es el caso normal y preferido.

Si la cantidad observada es exactamente la cantidad congelada, el pago puede continuar hacia confirmación.

### 14.2 Pago inferior

Una transferencia inferior al monto requerido **no confirma la compra**.

En el MVP no se acumulan múltiples transferencias para completar el saldo.

El Core debe marcar la evidencia como insuficiente y explicar al usuario que Ludix no controla los fondos ya enviados ni puede recuperarlos automáticamente.

### 14.3 Pago superior

Una transferencia superior al monto requerido puede confirmar la compra si cumple todas las demás reglas.

Ludix debe registrar tanto el monto esperado como el monto efectivamente observado.

El excedente no genera crédito interno, saldo a favor ni Entitlements adicionales y Ludix no puede devolverlo automáticamente porque nunca custodió esos fondos.

La interfaz debe diseñarse para minimizar este caso generando la transferencia con la cantidad exacta.

---

## 15. Expiración del Payment Intent

La expiración protege contra condiciones comerciales indefinidas; no debe convertir una transferencia válida en una pérdida artificial solo porque las confirmaciones tardaron.

### 15.1 Ventana de pago aprobada

Para Fase 1, un Payment Intent tiene una **ventana normal de pago de 60 minutos** desde `created_at`. Por tanto, su `expires_at` normal es:

```text
expires_at = created_at + 60 minutos
```

Durante esa hora permanecen congeladas las condiciones del intent: cuenta, juego/oferta, wallet pagadora, wallet receptora, red, contrato del token, monto y política de finalidad.

La hora se elige como margen humano razonable para abrir o configurar la wallet, verificar la red, disponer de POL para gas, revisar monto/destino y completar la transferencia sin mantener indefinidamente una condición comercial antigua.

### 15.2 Ventana de pago != ventana de resolución

La ventana de 60 minutos responde únicamente a **cuándo debe ocurrir la transferencia** para pertenecer al flujo normal de ese intent. No define cuánto tiempo puede tardar Ludix en detectar, verificar o reconciliar una transferencia que ya ocurrió.

Para Fase 1 se aprueba una **ventana automática de resolución/reconciliación de 72 horas**. Durante ese período, Ludix puede seguir reintentando consultas RPC, observación del Watcher, correlación y validación de finalidad para una compra potencialmente pagada.

Las 72 horas no son una fecha de caducidad de la compra. Al finalizar esa ventana, el sistema puede dejar de realizar seguimiento automático intensivo y mover el caso a un estado operativo de recuperación, pero **no debe declarar inválida una transferencia únicamente porque Ludix tardó en verificarla**.

Un fallo del Watcher, RPC, Core o Launcher nunca debe convertir retroactivamente una transferencia incluida a tiempo en un pago fuera de plazo.

Se distinguen dos momentos:

1. **momento de inclusión de la transferencia en la cadena**;
2. **momento en que Ludix alcanza suficientes confirmaciones para aceptarla**.

### 15.3 Regla temporal de validez

Si la transferencia fue incluida en cadena dentro de la ventana válida del Payment Intent, puede terminar de confirmarse después de `expires_at`.

La evidencia histórica de una transferencia válida **no caduca conceptualmente por superar las 72 horas de reconciliación automática**. Mientras Ludix conserve los datos necesarios para correlacionarla de forma inequívoca y la blockchain permita verificarla, una transferencia incluida dentro de los 60 minutos puede ser recuperada posteriormente y producir el Entitlement correspondiente.

La ventana de 72 horas controla el esfuerzo automático de reconciliación; no redefine retrospectivamente la validez económica del pago.

Si el intent expira sin evidencia de una transferencia incluida a tiempo, deja de aceptar pagos nuevos bajo esas condiciones.

Una transferencia enviada después de la expiración no concede automáticamente el Entitlement aunque casualmente tenga el mismo monto y destino. Debe tratarse como un caso de recuperación/manual claramente registrado, no como una coincidencia silenciosa.

### 15.4 Estados de resolución prolongada

La implementación puede refinar los nombres, pero debe distinguir semánticamente al menos:

```text
CONFIRMING          -> existe evidencia y se espera finalidad o verificación adicional
UNRESOLVED          -> Ludix todavía no puede concluir si la compra es válida
RECOVERY_REQUIRED   -> terminó la reconciliación automática; puede requerir reintento posterior o acción del usuario/soporte
```

`UNRESOLVED` y `RECOVERY_REQUIRED` **no significan `FAILED`**. Expresan incapacidad operativa para concluir, no evidencia de que el jugador no pagó.

---

## 16. Política de confirmaciones y reorganizaciones

Una transferencia observada no se considera inmediatamente definitiva.

El flujo distingue entre:

- transferencia encontrada;
- transferencia confirmándose;
- transferencia con finalidad suficiente.

La cantidad exacta de confirmaciones o criterio de finalidad debe ser configurable para la red elegida y formar parte de la política congelada o versionada que aplica al Payment Intent.

### 16.1 Reorg antes de finalidad

Si una reorganización elimina o modifica la evidencia antes de alcanzar la finalidad requerida, el pago vuelve a un estado no confirmado y el Core no debe crear el Entitlement.

Una indisponibilidad del RPC o del Watcher **no equivale a un pago fallido**. Equivale a evidencia aún no verificable.

### 16.2 Evento extremo después de finalidad

Una vez que Ludix declaró el pago final y creó el Entitlement conforme a su política documentada, el sistema no debe revocar automáticamente el juego por una reorganización extraordinaria posterior.

El incidente debe quedar registrado y ser tratable operativamente, pero la biblioteca del usuario no debe comportarse como un estado financiero eternamente reversible.

---

## 17. Máquinas de estado

La implementación puede refinar nombres internos, pero debe conservar la semántica siguiente.

### 17.1 Payment Intent

```text
CREATED
   |
   | usuario presenta tx_hash
   v
TX_SUBMITTED
   |
   | Watcher encuentra evidencia compatible
   v
CONFIRMING
   |
   | alcanza finalidad requerida
   v
CONFIRMED
```

Estados alternativos:

```text
EXPIRED       -> no apareció una transferencia válida dentro de la ventana
INVALID       -> la evidencia presentada no satisface las condiciones
CANCELLED     -> cancelación permitida antes de que exista pago observado
```

`INVALID` no debe confundirse con "los fondos nunca salieron". Puede existir una transferencia real que no satisfaga el intent.

### 17.2 Payment

El sistema debe poder distinguir al menos:

```text
PENDING
OBSERVED
CONFIRMING
CONFIRMED
REJECTED
```

### 17.3 Entitlement

Para el MVP, un Entitlement normal nace cuando el pago queda `CONFIRMED`.

Su existencia no depende de que la wallet original continúe vinculada posteriormente.

---

## 18. Idempotencia y concurrencia

El flujo debe soportar reintentos sin generar efectos duplicados.

### Reglas obligatorias

- registrar dos veces el mismo `tx_hash` para un intent no crea dos pagos;
- recibir dos veces la misma evidencia del Watcher no crea dos Entitlements;
- una misma transferencia concreta no puede consumirse por dos Payment Intents;
- crear el Entitlement debe ser idempotente;
- los reintentos de red entre Launcher, Core y Watcher deben ser seguros;
- operaciones concurrentes que intenten confirmar el mismo pago deben converger en un solo resultado.

El diseño del modelo de datos debe reforzar estas garantías mediante restricciones de unicidad y transacciones, no depender únicamente de lógica de aplicación.

---

## 19. Recuperación ante fallos

### 19.1 El launcher se cierra después de pagar

No se pierde la compra.

El Payment Intent y su estado viven en el servidor. Al volver a iniciar sesión, el usuario puede recuperar el intento y continuar la verificación.

### 19.2 El Chain Watcher o RPC está temporalmente caído

El estado permanece pendiente de verificación.

Nunca se debe mostrar "pago fallido" únicamente porque Ludix no puede consultar la cadena en ese momento.

### 19.3 Se presentó un tx_hash equivocado

Mientras el intent siga siendo recuperable, el usuario puede reemplazar la referencia presentada por una correcta.

Las referencias anteriores deben conservarse en auditoría cuando sea útil para investigación de fraude o soporte.

### 19.4 El usuario paga dos veces accidentalmente

Solo una transferencia puede satisfacer la compra normal y crear el Entitlement.

La segunda transferencia no crea automáticamente saldo ni una segunda licencia. Ludix debe mostrar el incidente y proporcionar evidencia suficiente para que jugador y desarrollador puedan resolverlo fuera de custodia.

### 19.5 El usuario paga desde otra wallet

Aunque el destino, token y monto coincidan, el pago no satisface automáticamente el intent del MVP si el origen no es la wallet pagadora congelada.

Esto evita atribuir pagos por coincidencia y mantiene verificable la relación entre cuenta, intent y transferencia.

---

## 20. Cambio de precio, activo o wallet durante una compra

El Payment Intent actúa como snapshot.

Ejemplo:

1. el juego cuesta 10 USDT;
2. se crea el intent por 10 USDT hacia `0xAAA`;
3. el desarrollador cambia el precio a 12 USDT y su wallet a `0xBBB`;
4. el intent existente sigue esperando 10 USDT hacia `0xAAA`;
5. las compras nuevas usan 12 USDT y `0xBBB`.

Nunca se reescriben silenciosamente las condiciones de una compra iniciada.

---

## 21. Juegos gratuitos

Un juego gratuito no debe crear una transferencia de valor cero para simular una compra.

La adquisición gratuita es otro mecanismo que puede crear directamente un Entitlement conforme a sus propias reglas.

Esto refuerza la separación conceptual entre:

- método de adquisición;
- evidencia;
- derecho adquirido.

---

## 22. Privacidad

Ludix debe distinguir privacidad de cuenta y privacidad blockchain.

La asociación interna entre una cuenta y sus wallets no debe publicarse innecesariamente.

Sin embargo, las transferencias realizadas en una blockchain pública son visibles conforme a las propiedades de esa red. Si un jugador reutiliza una misma wallet, terceros pueden correlacionar actividad on-chain aunque Ludix nunca publique su identidad de cuenta.

Ludix no debe prometer anonimato on-chain que la red subyacente no proporciona.

El MVP no incorpora mixers ni mecanismos de ofuscación financiera.

---

## 23. Límites de resistencia a censura de stablecoins

Usar stablecoins permite eliminar del flujo normal de Ludix a redes de tarjetas, adquirentes y pasarelas de pago tradicionales, pero no convierte automáticamente el activo en dinero completamente descentralizado.

Algunas stablecoins pueden incluir controles administrativos del emisor, listas de bloqueo u otras capacidades que afectan direcciones o transferencias.

Por tanto, Ludix no debe prometer:

> "Nadie puede censurar este pago."

La promesa técnicamente correcta es más limitada:

> **Ludix no necesita custodiar el dinero ni actuar como intermediario financiero entre jugador y desarrollador; el pago ocurre directamente sobre la infraestructura elegida por ambos.**

El protocolo debe identificar activos por red y contrato, evitando diseñar Ludix alrededor de una marca concreta de stablecoin.

### 23.1 Dependencia inicial y dirección futura

El MVP acepta conscientemente una dependencia sobre la stablecoin y la red elegidas. Esa dependencia se considera un **compromiso de implementación inicial**, no el estado final deseado de Ludix.

La evolución del protocolo debe favorecer, cuando sea técnicamente razonable y seguro:

- soporte para múltiples activos compatibles;
- soporte para múltiples redes;
- capacidad de una instancia para elegir qué combinaciones acepta;
- reducción de puntos únicos de fallo o censura;
- sustitución de un activo o red sin redefinir el significado de Payment Intent, Payment Evidence o Entitlement.

La independencia se entiende como una propiedad progresiva: cada nueva opción útil debe disminuir la necesidad de confiar en un único actor sin convertir el sistema en una colección innecesariamente compleja de integraciones.

### 23.2 Alcance de la resistencia a censura

Ludix no busca anonimato financiero absoluto ni pretende ocultar contenido o actividad ilegal. La infraestructura debe conservar trazabilidad técnica suficiente para operar de forma responsable y respetar procesos legales legítimos.

El principio del proyecto es el mismo expresado en el Manifiesto Ludix: **si algo debe ser ilegal, para eso están las leyes; un banco o una empresa privada no debe reemplazar a un juez**.

Cuando exista una obligación legal legítima, transparente y aplicable en una jurisdicción, Ludix no pretende diseñarse para evadirla. Lo que Ludix busca reducir es otra cosa: la capacidad de intermediarios financieros privados de decidir, mediante políticas internas, presión comercial o juicios propios de moralidad, qué contenido legal puede sostenerse económicamente.

Por tanto, la resistencia a censura perseguida por Ludix no significa ausencia de reglas ni ausencia de responsabilidad. Significa **evitar que actores privados indispensables para cobrar actúen de facto como tribunales globales sobre contenido legal**.

La dirección futura de múltiples redes y activos debe evaluarse siempre con ese objetivo: aumentar resiliencia frente a puntos privados de censura sin convertir Ludix en una herramienta para esconder delitos, evadir la ley o sacrificar la seguridad de jugadores y desarrolladores.

---

## 24. Entitlements

El Entitlement es la salida durable de una compra confirmada.

Debe representar, como mínimo:

- cuenta beneficiaria;
- juego adquirido;
- origen de la adquisición;
- referencia al pago o evidencia que lo produjo;
- fecha de concesión;
- estado;
- trazabilidad suficiente para auditoría.

Para el MVP, una compra normal confirmada concede un Entitlement persistente para la cuenta.

La descarga y biblioteca deben consultar Entitlements, no reinterpretar la blockchain cada vez que el usuario abre el launcher.

Esta separación permitirá que, en fases futuras, distintos mecanismos puedan producir el mismo resultado:

```text
pago on-chain ------\
Gateway local -------+--> Entitlement --> Biblioteca
promoción ------------+
regalo ---------------/
```

Cada mecanismo deberá aportar su propia evidencia y política sin contaminar el significado del Entitlement.

---

## 25. Separación de responsabilidades

### Launcher

Responsable de:

- mostrar las condiciones del intent;
- facilitar la prueba de wallet;
- solicitar al wallet software la transferencia;
- enviar el `tx_hash` al Core;
- mostrar estados y errores comprensibles;
- recuperar compras pendientes tras reinicios.

No es autoridad sobre el pago.

### Core API

Responsable de:

- autenticar la cuenta;
- crear y congelar Payment Intents;
- validar Wallet Proofs;
- correlacionar intents con evidencia;
- aplicar reglas comerciales y de seguridad;
- garantizar idempotencia;
- confirmar pagos;
- crear Entitlements;
- mantener auditoría.

### Chain Watcher

Responsable de:

- observar la red;
- extraer hechos objetivos;
- seguir confirmaciones/finalidad;
- detectar cambios relevantes por reorg;
- entregar evidencia al Core de manera idempotente.

No conoce políticas de catálogo ni concede acceso.

### Desarrollador

Responsable de:

- demostrar control de su wallet de cobro;
- mantener sus claves bajo su control;
- configurar una dirección válida;
- resolver directamente los casos económicos que Ludix no puede revertir, como pagos excedentes o duplicados, según las políticas que se definan posteriormente.

---

## 26. Auditoría mínima

Sin definir todavía el esquema exacto, el sistema debe poder reconstruir posteriormente:

- qué condiciones tenía el Payment Intent;
- qué wallet del jugador estaba verificada;
- qué wallet del desarrollador estaba verificada;
- cuándo se creó y expiraba el intent;
- qué hashes presentó el usuario;
- qué evidencia observó el Watcher;
- cuántas confirmaciones/finalidad se exigían;
- por qué el Core aceptó o rechazó la evidencia;
- qué transferencia fue consumida;
- cuándo se creó el Entitlement.

Los logs de auditoría no deben contener secretos criptográficos ni claves privadas.

---

## 27. Implicaciones para RFC-0002: Modelo de Datos

El modelo de datos actual deberá revisarse a partir de este RFC.

Como mínimo, RFC-0002 debe separar conceptos que hoy aparecen mezclados en una única tabla `purchases`:

1. **Payment Intent** — condiciones congeladas de la compra.
2. **Payment Evidence / Payment** — transferencia realmente observada y su estado.
3. **Entitlement** — derecho adquirido por la cuenta.
4. **Verified Wallets** — pruebas de control y ciclo de vida de wallets del jugador/desarrollador.

También deberá reemplazar supuestos como:

- `DECIMAL(10,2)` como representación canónica de tokens;
- `tx_hash UNIQUE` como identidad suficiente de un pago;
- precio y wallet únicamente como propiedades mutables del juego;
- confirmación de compra sin snapshot de red, token, cantidad y destino.

Este RFC tiene prioridad semántica sobre el borrador actual de RFC-0002 en lo relativo al flujo de pago.

---

## 28. Implicaciones para RFC-0004: Chain Watcher

RFC-0004 deberá definir el contrato técnico mediante el cual el Watcher aporta evidencia al Core.

Debe preservar estos límites:

- el Watcher informa hechos de blockchain;
- el Watcher no acepta `user_id` o `game_id` como hechos derivados de la cadena;
- el Watcher no crea Entitlements;
- el Watcher debe tolerar reintentos;
- el Watcher debe poder seguir el estado de una transferencia hasta finalidad;
- el Watcher debe identificar una transferencia específica dentro de una transacción.

---

## 29. Experiencia de usuario mínima

El launcher debería traducir la máquina de estados técnica a mensajes simples.

Flujo normal:

```text
Preparando compra
      ↓
Esperando tu transferencia
      ↓
Pago detectado
      ↓
Confirmando en la red
      ↓
Compra confirmada
      ↓
Añadido a tu biblioteca
```

Casos excepcionales deben ser igualmente explícitos:

- "Todavía no podemos verificar la red" en vez de "Pago fallido" cuando el RPC está caído.
- "El monto recibido es menor al requerido" cuando existe un underpayment.
- "Esta transferencia no salió de la wallet vinculada a la compra" cuando el origen no coincide.
- "La intención de compra expiró" cuando no hubo transferencia válida a tiempo.
- "Seguimos intentando verificar tu pago" durante la ventana automática de reconciliación.
- "No pudimos resolverlo automáticamente; puedes volver a comprobar este pago" cuando pase a recuperación sin evidencia concluyente.
- "Esta transferencia ya fue utilizada" ante un intento de replay.

La interfaz nunca debe insinuar que Ludix puede devolver automáticamente fondos que nunca custodió.

---

## 30. Flujo completo de referencia

```text
Jugador autenticado
        |
        | demuestra control de wallet
        v
Wallet pagadora verificada
        |
        | pulsa Comprar
        v
Core crea Payment Intent inmutable
        |
        | muestra red, token, monto y destino
        v
Jugador firma transferencia directa
        |
        | fondos
        +------------------------------------> Wallet verificada del desarrollador
        |
        | tx_hash
        v
Core registra referencia presentada
        |
        v
Chain Watcher observa la blockchain
        |
        | evidencia objetiva
        v
Core compara evidencia vs Payment Intent
        |
        | coincide y alcanza finalidad
        v
Payment CONFIRMED
        |
        v
Entitlement creado de forma idempotente
        |
        v
Juego disponible en Biblioteca
```

En ningún punto del flujo Ludix recibe, retiene o reenvía el dinero.

---

## 31. Criterios de aceptación arquitectónicos para Fase 1

El flujo de pago no debe considerarse listo para implementación hasta que el diseño técnico preserve como mínimo estas garantías:

- [ ] el Core puede crear un Payment Intent inmutable;
- [ ] el jugador demuestra control de su wallet sin entregar secretos;
- [ ] el desarrollador demuestra control de su wallet de cobro;
- [ ] Ludix nunca requiere permisos para gastar tokens del jugador;
- [ ] el pago viaja directamente jugador → desarrollador;
- [ ] el activo se identifica por red + contrato, no solo por símbolo;
- [ ] el monto canónico se maneja en unidades atómicas enteras;
- [ ] una transferencia se identifica de forma más precisa que `tx_hash` aislado;
- [ ] una transferencia concreta solo puede consumirse una vez;
- [ ] el Watcher no decide qué usuario compró qué juego;
- [ ] una caída del Watcher/RPC no se interpreta automáticamente como pago fallido;
- [ ] los reintentos son idempotentes;
- [ ] un pago inferior no concede el juego;
- [ ] un pago superior puede concederlo sin crear saldo interno;
- [ ] un pago incluido a tiempo puede terminar de confirmar después de la expiración del intent;
- [ ] el sistema espera finalidad antes de conceder el Entitlement;
- [ ] el Entitlement pertenece a la cuenta y no a la wallet;
- [ ] el launcher puede recuperar una compra pendiente después de reiniciarse;
- [ ] el modelo de datos conserva evidencia suficiente para auditoría;
- [ ] el sistema declara con honestidad los límites de privacidad y resistencia a censura de la red/activo elegido.

---

## 32. Decisiones pendientes antes de implementación

Este RFC fija el modelo conceptual. Los siguientes parámetros todavía deben cerrarse en RFCs posteriores o en una revisión final de Fase 0:

1. criterio exacto de confirmaciones/finalidad para Polygon PoS;
2. formato canónico del desafío de firma de wallet;
3. política operativa para pagos realizados después de la expiración;
4. política de soporte para pagos duplicados o excedentes;
5. formato API entre Core y Chain Watcher;
6. retención de auditoría y datos de wallet;
7. reglas específicas de rotación/cuarentena de la wallet del desarrollador, coordinadas con TrustChain.

Ninguna de estas decisiones pendientes debe introducir custodia de fondos como atajo.

---

## 33. Decisión arquitectónica

Para el MVP, Ludix adopta el siguiente modelo:

> **Una cuenta autenticada crea una intención de pago inmutable. El jugador demuestra control de la wallet pagadora y transfiere directamente un activo soportado hacia una wallet del desarrollador cuyo control también fue demostrado. El Chain Watcher aporta evidencia objetiva de la transferencia. El Core, y solo el Core, correlaciona esa evidencia con la intención de pago. Tras alcanzar la finalidad requerida, el Core confirma el pago y crea de forma idempotente un Entitlement para la cuenta del jugador.**

Este modelo constituye la base para RFC-0002 (modelo de datos), RFC-0003 (API del launcher) y RFC-0004 (Chain Watcher).
