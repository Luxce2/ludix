# 🔗 Ludix TrustChain: Especificación de Confianza

> **Estado:** Fase 0 — Diseño conceptual en revisión.
> **Propósito:** permitir que jugadores e instancias Ludix distingan a un desarrollador legítimo de un impostor sin convertir al instancia original en dueño de su identidad criptográfica.

---

## 1. Principio central

TrustChain no pretende convertir a Ludix en una autoridad universal de identidad.

Su función es reunir **pruebas verificables**, **atestaciones de una instancia** y **señales de reputación** para que el jugador pueda tomar decisiones con más información.

La identidad criptográfica pertenece al desarrollador. La instancia Ludix puede verificarla, emitir atestaciones y mantener evidencia privada, pero no debe apropiarse de las claves ni hacer que la autenticidad dependa exclusivamente de una base de datos secreta.

La separación fundamental es:

```text
Identidad criptográfica del desarrollador -> verificable y portable
Evidencia sensible de verificación        -> privada de la instancia
Reputación / moderación                    -> propia de cada instancia
```

---

## 2. Principios de diseño

### 2.1 Soberanía del desarrollador

El desarrollador controla sus claves privadas.

Ludix nunca debe solicitar, almacenar ni custodiar una clave privada de firma del estudio.

### 2.2 Verificabilidad pública de la criptografía

Una **clave pública** no es un secreto. Su propósito es permitir que terceros verifiquen firmas sin poder producirlas.

Por tanto, las claves públicas activas, sus fingerprints, estados de revocación y firmas asociadas pueden formar parte del perfil técnico público de un desarrollador.

Conocer una clave pública no permite suplantar al desarrollador. La capacidad de firmar sigue dependiendo de la clave privada.

### 2.3 Privacidad de la evidencia sensible

No toda la información usada para verificar a un desarrollador debe ser pública.

Deben mantenerse privados, según corresponda:

- tokens OAuth;
- secretos temporales de challenges;
- datos personales no necesarios para el perfil público;
- señales antifraude;
- notas internas de moderación;
- evidencias proporcionadas bajo expectativa de confidencialidad;
- direcciones IP y metadatos operativos sensibles;
- credenciales de proveedores externos.

La regla es **minimización de datos**: publicar solo aquello necesario para verificar una afirmación pública y proteger el resto.

### 2.4 Transparencia de las atestaciones

Cuando una instancia diga que un desarrollador está `VERIFIED`, debe quedar claro que se trata de una **atestación emitida por esa instancia**, no de una verdad universal garantizada por el protocolo.

Una instancia diferente puede aceptar esa atestación, volver a verificarla o ignorarla según sus propias reglas.

### 2.5 Forkabilidad sin clonación de confianza

Un fork de Ludix debe poder verificar las firmas originales de un desarrollador si dispone de sus claves públicas y artefactos firmados.

Sin embargo, no debe heredar automáticamente:

- evidencia privada;
- reputación interna;
- notas antifraude;
- decisiones de moderación;
- badges de confianza emitidos por otra instancia.

Esto evita dos extremos:

1. que la identidad quede secuestrada por un servidor central;
2. que copiar una base de datos permita clonar automáticamente la confianza construida en otra instancia.

---

## 3. Las capas de TrustChain

TrustChain se construye por capas. Ninguna capa aislada convierte mágicamente a un actor en confiable.

### Capa 1 — Identidad criptográfica

Cada desarrollador registra al menos una clave pública de firma.

**Tecnología propuesta para el MVP:** Ed25519.

La clave privada permanece bajo control del desarrollador.

Usos esperados:

- firmar manifiestos de builds;
- firmar metadatos de releases;
- demostrar continuidad de identidad;
- autorizar rotaciones de claves cuando sea posible.

El Core y el Launcher pueden verificar esas firmas usando la clave pública publicada.

### Capa 2 — Verificación de dominio

El desarrollador demuestra control de un dominio oficial mediante uno o más mecanismos:

- archivo bajo `/.well-known/ludix-verify.json`;
- registro DNS TXT;
- otro método técnicamente verificable que se defina posteriormente.

El resultado puede mostrarse públicamente como una atestación de la instancia.

Los tokens temporales usados durante el challenge no deben quedar publicados después de cumplir su propósito.

### Capa 3 — Vínculos con plataformas externas

Opcionalmente, un desarrollador puede demostrar control de cuentas oficiales en servicios como:

- GitHub;
- Itch.io;
- Steamworks;
- otras plataformas relevantes.

Estos vínculos aumentan contexto, no reemplazan la identidad criptográfica.

Credenciales OAuth, access tokens y secretos equivalentes son siempre privados.

### Capa 4 — Integridad de builds

Todo build debe tener al menos un hash criptográfico fuerte registrado.

**Base propuesta:** SHA-256.

La arquitectura debe permitir que el desarrollador firme un manifiesto del release con una clave de identidad activa.

La diferencia es importante:

- el hash detecta que un archivo cambió;
- la firma permite demostrar quién afirmó que ese hash era legítimo.

El Launcher debe bloquear o advertir de forma inequívoca cuando la integridad esperada no coincide.

### Capa 5 — Reputación dinámica

Una instancia puede mantener señales como:

- antigüedad;
- historial de ventas;
- reportes válidos;
- incidentes de seguridad;
- entregas fallidas;
- cumplimiento histórico de sus propias reglas.

La reputación es **contexto de una instancia**, no identidad criptográfica.

Un fork no debe declarar como propia la reputación de otra instancia sin señalar claramente su procedencia.

### Capa 6 — Requisitos de alta confianza

Funciones de mayor riesgo pueden exigir verificaciones adicionales.

Ejemplo: venta de Steam Keys o claves externas.

Ese nivel podría requerir demostrar control de un App ID, publisher account u otra evidencia equivalente antes de habilitar la función.

---

## 4. Rotación, revocación y recuperación de claves

Las claves pueden perderse o ser comprometidas. TrustChain debe diseñarse suponiendo que eso ocurrirá alguna vez.

### 4.1 Clave de revocación

Un desarrollador puede registrar una clave pública secundaria de revocación cuyo secreto correspondiente permanezca fuera de línea.

Su único propósito es permitir invalidar una clave activa comprometida según el protocolo que se defina.

### 4.2 Rotación normal

Cuando sea posible, una clave activa debería poder firmar la transición hacia su reemplazo.

Esto crea continuidad criptográfica entre identidad antigua y nueva.

### 4.3 Cuarentena

Cambios sensibles como:

- clave principal de firma;
- wallet de cobro;
- dominio principal;

pueden activar un período de cuarentena y advertencias visibles.

La duración exacta se definirá en RFCs posteriores.

Los Payment Intents ya creados conservan las condiciones congeladas en ellos, conforme a RFC-0001.

### 4.4 Compromiso sin clave de recuperación

Si no existe una cadena criptográfica suficiente para recuperar una identidad, una instancia puede recurrir a un proceso de reverificación.

Ese proceso debe quedar auditado y nunca fingir continuidad criptográfica que no puede probarse.

---

## 5. Qué es público y qué es privado

La siguiente separación es conceptual y deberá formalizarse en RFC-0002.

### Datos potencialmente públicos

- nombre público del estudio;
- clave pública de firma;
- fingerprint de la clave;
- estado de una clave: activa / revocada;
- dominio declarado;
- resultado de una verificación de dominio;
- firmas de releases;
- hashes de builds;
- atestaciones públicas emitidas por una instancia;
- procedencia y fecha de una atestación.

### Datos que deben permanecer privados salvo obligación legal o consentimiento explícito

- claves privadas;
- seed phrases;
- secretos OAuth;
- tokens de sesión;
- challenges reutilizables o aún vigentes;
- documentos personales no destinados al perfil público;
- señales antifraude internas;
- notas de moderación;
- evidencia sensible aportada durante investigaciones;
- IPs y metadatos operativos sensibles cuando no sean necesarios públicamente.

El hecho de que Ludix sea open source no implica que las bases de datos de producción deban ser públicas.

---

## 6. Atestaciones de instancia

Una instancia Ludix puede afirmar, por ejemplo:

```text
"La instancia X verificó el control del dominio example.com
para la identidad criptográfica con fingerprint Y
en la fecha Z."
```

Esa afirmación debe incluir procedencia.

En fases futuras, las atestaciones podrían firmarse criptográficamente por la instancia para que otras implementaciones puedan verificar quién las emitió sin tener que confiar ciegamente en una copia de base de datos.

Esto permite federación futura sin convertir una instancia en autoridad universal.

---

## 7. Auditoría y límites de la palabra "inmutable"

TrustChain necesita trazabilidad fuerte, pero una tabla normal de PostgreSQL no es matemáticamente inmutable frente a un administrador que controla completamente la base de datos.

Por tanto, la documentación no debe prometer "inmutabilidad absoluta" si todavía no existe un mecanismo que la garantice.

Para el MVP, el objetivo es:

- registros append-only donde sea posible;
- permisos mínimos;
- historial de cambios;
- auditoría de quién, cuándo y por qué cambió un estado;
- backups y controles operacionales;
- posibilidad futura de encadenar hashes o firmar checkpoints para obtener evidencia de manipulación.

La amenaza exacta que estos mecanismos deben resistir se definirá antes de implementación.

---

## 8. Relación con forks e instancias independientes

Un fork legítimo puede reutilizar el protocolo y el código, pero debe reconstruir o importar confianza de forma explícita.

Lo que permanece verificable independientemente:

- claves públicas;
- firmas de desarrolladores;
- hashes de builds;
- artefactos firmados;
- atestaciones firmadas cuya procedencia sea conocida.

Lo que no se vuelve confiable solo por copiarlo:

- una etiqueta `VERIFIED` sin procedencia;
- reputación interna;
- notas privadas;
- decisiones administrativas;
- datos sensibles de otra instancia.

La meta es que **ninguna instancia pueda secuestrar la identidad técnica de un desarrollador**, pero tampoco que un clon pueda apropiarse de la reputación de otro sistema sin pruebas.

---

## 9. Relación con pagos

TrustChain no autoriza pagos ni custodia dinero.

La wallet de cobro de un desarrollador debe demostrar control criptográfico según RFC-0001 antes de participar en el flujo de ventas del MVP.

Una wallet verificada prueba control de una dirección; no prueba por sí sola la legitimidad completa del estudio.

El cambio de wallet es una operación sensible, auditable y potencialmente sujeta a cuarentena.

---

## 10. Relación con contenido y moderación

TrustChain prueba identidad, integridad y procedencia. No convierte al sistema en juez moral del contenido.

Ludix respeta procesos legales legítimos y no pretende proteger contenido ilegal.

Al mismo tiempo, una empresa privada o un intermediario financiero no debe poder sustituir a un juez y cortar de facto la financiación de contenido legal solo por una política interna de moralidad o riesgo reputacional.

Esta separación entre **ley**, **identidad**, **reputación** y **censura financiera privada** es parte esencial de la arquitectura de Ludix.

---

## 11. Flujo conceptual de onboarding

```text
Usuario crea cuenta
       ↓
Solicita perfil de desarrollador
       ↓
Genera o registra clave pública de identidad
       ↓
Demuestra control mediante challenge firmado
       ↓
Verifica dominio y señales opcionales
       ↓
Instancia registra resultados + evidencia privada
       ↓
Instancia puede emitir atestación de verificación
       ↓
Desarrollador publica builds y metadatos verificables
```

La clave privada nunca entra en Ludix.

---

## 12. Decisiones pendientes

Antes de implementar TrustChain deben cerrarse, como mínimo:

1. formato canónico de identidad y fingerprint;
2. esquema exacto de firmas Ed25519;
3. formato de manifiesto de release;
4. protocolo de rotación y revocación;
5. formato de atestaciones de instancia;
6. qué campos son públicos, privados o configurables;
7. política de cuarentena;
8. política de reputación;
9. amenaza concreta que debe resistir el sistema de auditoría;
10. tratamiento de importación/exportación de atestaciones entre instancias.

Hasta resolver esos puntos, TrustChain sigue siendo diseño de Fase 0 y no debe considerarse una especificación lista para implementación.
