# RFC-0002: Modelo de Datos y Trazabilidad

> **Estado:** Fase 0 — Borrador reconstruido y alineado con RFC-0001.
> **Dependencias:** RFC-0001 (flujo de compra y pago), `docs/es/trustchain.md`.
> **Tecnología propuesta:** PostgreSQL + SQLAlchemy 2.0 + Alembic.
> **Importante:** este RFC todavía no está "listo para implementación" hasta cerrar las decisiones pendientes de red, activo, finalidad, TrustChain y retención de datos.

---

## 1. Principios del modelo

El modelo debe preservar las decisiones de arquitectura antes de optimizar comodidad de implementación.

### 1.1 Separar conceptos que no significan lo mismo

Ludix distingue explícitamente:

```text
Cuenta
Wallet verificada
Juego
Oferta
Payment Intent
Evidencia on-chain
Pago
Entitlement
```

Una tabla `purchases` que mezcle todos esos significados no es suficiente para el diseño actual.

### 1.2 El dinero no pasa por Ludix

El modelo registra condiciones y evidencia. No debe asumir balances internos, wallets custodiales de plataforma ni movimientos jugador → Ludix → desarrollador.

### 1.3 Snapshots inmutables para compras iniciadas

Un Payment Intent conserva las condiciones vigentes cuando el jugador pulsó Comprar.

Cambios posteriores en precio, wallet, token o configuración no reescriben el intento existente.

### 1.4 Cantidades on-chain en unidades atómicas enteras

Para activos EVM, el monto canónico debe representarse como entero en unidades mínimas del token.

`DECIMAL(10,2)` no es suficiente.

Una opción razonable en PostgreSQL es `NUMERIC(78,0)` para cantidades tipo `uint256`, aunque la decisión final pertenece a implementación.

### 1.5 Identificadores no secuenciales

Las entidades principales deben utilizar UUIDs o identificadores equivalentes no enumerables.

UUIDv4 es una base aceptable para el MVP salvo que posteriormente se adopte UUIDv7 por razones operativas.

### 1.6 Borrado lógico donde la historia importa

Usuarios, desarrolladores, juegos, ofertas y otros registros con historia comercial o de seguridad deben priorizar soft delete o estados de ciclo de vida.

Los registros financieros/auditoría no deben desaparecer por una eliminación normal desde la aplicación.

### 1.7 Trazabilidad sin prometer inmutabilidad absoluta

La aplicación debe producir historia append-only y controles de auditoría donde corresponda.

Una base PostgreSQL bajo control total de un administrador no es absolutamente inmutable. El modelo debe hablar de **trazabilidad**, **append-only** y, en fases posteriores, posible evidencia de manipulación mediante hash chaining o checkpoints firmados.

### 1.8 Minimización de datos

Los datos públicos verificables y la evidencia sensible deben separarse.

Que el código sea open source no implica que la base de producción sea pública.

---

## 2. Core Identity

### 2.1 Tabla conceptual: `users`

Representa una cuenta Ludix.

Campos mínimos propuestos:

- `id`: UUID, PK.
- `email`: VARCHAR, unique, indexed.
- `password_hash`: VARCHAR, si el MVP usa credenciales locales.
- `status`: ENUM (`ACTIVE`, `SUSPENDED`, `DELETED`).
- `created_at`: TIMESTAMPTZ.
- `updated_at`: TIMESTAMPTZ.
- `deleted_at`: TIMESTAMPTZ nullable.

El rol de desarrollador no debería depender únicamente de un boolean mutable si existe una entidad `developers` con ciclo de vida propio.

---

## 3. Wallets y prueba de control

RFC-0001 exige demostrar control tanto de la wallet pagadora como de la wallet receptora.

### 3.1 Tabla conceptual: `wallets`

Representa una dirección cuyo control fue verificado por una cuenta.

Campos propuestos:

- `id`: UUID, PK.
- `user_id`: UUID, FK -> users.id.
- `chain_family`: VARCHAR, por ejemplo `EVM`.
- `address`: VARCHAR, representación normalizada.
- `status`: ENUM (`PENDING`, `VERIFIED`, `QUARANTINED`, `REVOKED`).
- `verified_at`: TIMESTAMPTZ nullable.
- `created_at`: TIMESTAMPTZ.
- `updated_at`: TIMESTAMPTZ.
- `revoked_at`: TIMESTAMPTZ nullable.

La dirección no debe mutar en el mismo registro: cambiar de wallet significa registrar otra identidad de wallet o transición auditable, no sobrescribir silenciosamente la anterior.

### 3.2 Tabla conceptual: `wallet_verification_challenges`

Registra challenges de un solo uso.

Campos propuestos:

- `id`: UUID, PK.
- `user_id`: UUID, FK.
- `wallet_address`: VARCHAR.
- `chain_family`: VARCHAR.
- `purpose`: ENUM (`PLAYER_PAYMENT`, `DEVELOPER_PAYOUT`, `REVERIFY`, etc.).
- `nonce_hash`: VARCHAR/BYTEA.
- `issued_at`: TIMESTAMPTZ.
- `expires_at`: TIMESTAMPTZ.
- `consumed_at`: TIMESTAMPTZ nullable.
- `result`: ENUM (`PENDING`, `VALID`, `INVALID`, `EXPIRED`).

El modelo no necesita conservar secretos reutilizables una vez cumplido su propósito.

---

## 4. Developers y TrustChain

### 4.1 Tabla conceptual: `developers`

Perfil público del estudio asociado a una cuenta.

Campos propuestos:

- `user_id`: UUID, PK/FK -> users.id.
- `studio_name`: VARCHAR.
- `official_website`: VARCHAR nullable.
- `trust_status`: ENUM (`UNVERIFIED`, `PENDING`, `VERIFIED`, `QUARANTINED`, `SUSPENDED`).
- `created_at`: TIMESTAMPTZ.
- `updated_at`: TIMESTAMPTZ.
- `deleted_at`: TIMESTAMPTZ nullable.

`trust_status` expresa una decisión/atestación de la instancia. No reemplaza la identidad criptográfica.

### 4.2 Tabla conceptual: `developer_identity_keys`

Claves públicas de identidad técnica del estudio.

Campos propuestos:

- `id`: UUID, PK.
- `developer_id`: UUID, FK -> developers.user_id.
- `algorithm`: VARCHAR, inicialmente `Ed25519`.
- `public_key`: TEXT/BYTEA.
- `fingerprint`: VARCHAR, indexed.
- `purpose`: ENUM (`SIGNING`, `REVOCATION`).
- `status`: ENUM (`ACTIVE`, `REVOKED`, `RETIRED`).
- `valid_from`: TIMESTAMPTZ.
- `revoked_at`: TIMESTAMPTZ nullable.
- `replaced_by_key_id`: UUID nullable.
- `created_at`: TIMESTAMPTZ.

**Nunca se almacena una clave privada.**

### 4.3 Tabla conceptual: `trustchain_checks`

Resultado de una verificación realizada por la instancia.

Campos propuestos:

- `id`: UUID, PK.
- `developer_id`: UUID, FK.
- `check_type`: ENUM (`CRYPTO`, `DOMAIN_DNS`, `DOMAIN_WELL_KNOWN`, `SOCIAL`, `PLATFORM`, etc.).
- `status`: ENUM (`PENDING`, `VERIFIED`, `FAILED`, `REVOKED`).
- `public_summary`: JSONB nullable.
- `verified_at`: TIMESTAMPTZ nullable.
- `expires_at`: TIMESTAMPTZ nullable.
- `created_at`: TIMESTAMPTZ.

El resumen público no debe contener secretos.

### 4.4 Tabla conceptual: `trustchain_private_evidence`

Evidencia sensible usada por una instancia para justificar un `trustchain_check`.

Campos propuestos:

- `id`: UUID, PK.
- `check_id`: UUID, FK -> trustchain_checks.id.
- `evidence_type`: VARCHAR.
- `encrypted_payload` o referencia segura equivalente.
- `created_at`: TIMESTAMPTZ.
- `retention_until`: TIMESTAMPTZ nullable.

El mecanismo exacto de cifrado/almacenamiento se decidirá posteriormente.

### 4.5 Tabla conceptual: `trustchain_attestations`

Permite representar afirmaciones públicas emitidas por una instancia.

Campos propuestos:

- `id`: UUID, PK.
- `developer_id`: UUID, FK.
- `issuer_instance`: VARCHAR/UUID.
- `claim_type`: VARCHAR.
- `claim_payload`: JSONB.
- `issued_at`: TIMESTAMPTZ.
- `revoked_at`: TIMESTAMPTZ nullable.
- `signature`: TEXT/BYTEA nullable hasta definir el formato de atestación firmada.

Una atestación siempre debe conservar procedencia.

---

## 5. Catálogo

### 5.1 Tabla conceptual: `games`

El juego no debe contener directamente una única wallet y un único precio si queremos separar catálogo de método de pago.

Campos propuestos:

- `id`: UUID, PK.
- `developer_id`: UUID, FK.
- `title`: VARCHAR.
- `description`: TEXT.
- `status`: ENUM (`DRAFT`, `PUBLISHED`, `SUSPENDED`, `REMOVED`).
- `created_at`: TIMESTAMPTZ.
- `updated_at`: TIMESTAMPTZ.
- `deleted_at`: TIMESTAMPTZ nullable.

Metadatos como tags, clasificación e imágenes pueden normalizarse en tablas adicionales o JSONB según necesidades posteriores.

### 5.2 Tabla conceptual: `builds`

Campos propuestos:

- `id`: UUID, PK.
- `game_id`: UUID, FK.
- `version`: VARCHAR.
- `platform`: VARCHAR.
- `architecture`: VARCHAR nullable.
- `download_url`: TEXT.
- `sha256_hash`: VARCHAR, obligatorio.
- `signing_key_id`: UUID nullable, FK -> developer_identity_keys.id.
- `signature`: TEXT/BYTEA nullable mientras la política de firma siga pendiente.
- `created_at`: TIMESTAMPTZ.
- `deleted_at`: TIMESTAMPTZ nullable.

La política final de firma obligatoria se decidirá antes de prometer resistencia frente a un backend comprometido.

---

## 6. Ofertas y rutas de pago

Separar `games` de `offers` permite que un juego tenga condiciones comerciales versionadas sin reescribir la entidad del catálogo.

### 6.1 Tabla conceptual: `offers`

Campos propuestos para el MVP:

- `id`: UUID, PK.
- `game_id`: UUID, FK.
- `chain_id`: valor `137` para Polygon PoS en Fase 1.
- `token_contract`: `0x3c499c542cef5e3811e1192ce70d8cc03d5c3359` para USDC nativo en Fase 1.
- `token_symbol`: `USDC`, dato de presentación.
- `token_decimals`: `6`.
- `price_atomic`: NUMERIC(78,0) o representación entera equivalente.
- `payout_wallet_id`: UUID, FK -> wallets.id.
- `status`: ENUM (`ACTIVE`, `PAUSED`, `RETIRED`).
- `valid_from`: TIMESTAMPTZ.
- `valid_until`: TIMESTAMPTZ nullable.
- `created_at`: TIMESTAMPTZ.

Para Fase 1 probablemente exista una única oferta activa por juego, pero el esquema no debe confundir esa simplificación con una limitación eterna.

---

## 7. Payment Intents

### 7.1 Tabla conceptual: `payment_intents`

Es el snapshot inmutable definido por RFC-0001.

Campos mínimos propuestos:

- `id`: UUID, PK.
- `user_id`: UUID, FK -> users.id.
- `game_id`: UUID, FK -> games.id.
- `offer_id`: UUID, FK -> offers.id.
- `payer_wallet_id`: UUID, FK -> wallets.id.
- `receiver_wallet_id`: UUID, FK -> wallets.id.
- `payer_address`: VARCHAR, snapshot.
- `receiver_address`: VARCHAR, snapshot.
- `chain_id`: NUMERIC/VARCHAR, snapshot.
- `token_contract`: VARCHAR, snapshot.
- `token_symbol`: VARCHAR, snapshot de presentación.
- `token_decimals`: SMALLINT, snapshot.
- `expected_amount_atomic`: NUMERIC(78,0).
- `finality_policy_version`: VARCHAR/UUID.
- `status`: ENUM (`CREATED`, `TX_SUBMITTED`, `CONFIRMING`, `CONFIRMED`, `EXPIRED`, `INVALID`, `CANCELLED`).
- `created_at`: TIMESTAMPTZ.
- `expires_at`: TIMESTAMPTZ.
- `confirmed_at`: TIMESTAMPTZ nullable.

Los campos económicos congelados no se actualizan después de crear el intent.

---

## 8. Referencias enviadas por el usuario

### 8.1 Tabla conceptual: `payment_submissions`

Conserva los tx hashes presentados por el launcher sin tratarlos como verdad.

Campos propuestos:

- `id`: UUID, PK.
- `payment_intent_id`: UUID, FK.
- `tx_hash`: VARCHAR.
- `submitted_at`: TIMESTAMPTZ.
- `superseded_at`: TIMESTAMPTZ nullable.
- `status`: ENUM (`SUBMITTED`, `MATCHED`, `REJECTED`, `SUPERSEDED`).
- `rejection_reason`: VARCHAR/TEXT nullable.

Un usuario puede corregir un hash equivocado sin destruir el intent.

---

## 9. Evidencia on-chain

### 9.1 Tabla conceptual: `payment_evidence`

Representa hechos observados por Chain Watcher.

Campos propuestos:

- `id`: UUID, PK.
- `chain_id`: NUMERIC/VARCHAR.
- `tx_hash`: VARCHAR.
- `log_index`: BIGINT o tipo equivalente.
- `block_number`: NUMERIC/BIGINT.
- `block_hash`: VARCHAR.
- `token_contract`: VARCHAR.
- `from_address`: VARCHAR.
- `to_address`: VARCHAR.
- `amount_atomic`: NUMERIC(78,0).
- `tx_status`: ENUM (`SUCCESS`, `FAILED`).
- `confirmation_count`: INTEGER.
- `finality_status`: ENUM (`OBSERVED`, `CONFIRMING`, `FINAL`, `REORGED`).
- `first_observed_at`: TIMESTAMPTZ.
- `last_observed_at`: TIMESTAMPTZ.

Restricción fundamental:

```text
UNIQUE(chain_id, tx_hash, log_index)
```

La identidad exacta puede variar fuera de EVM, pero el concepto debe conservarse.

---

## 10. Evaluación del pago

### 10.1 Tabla conceptual: `payments`

Vincula un Payment Intent con una evidencia concreta y registra el resultado de la evaluación del Core.

Campos propuestos:

- `id`: UUID, PK.
- `payment_intent_id`: UUID, FK.
- `payment_evidence_id`: UUID, FK.
- `status`: ENUM (`PENDING`, `OBSERVED`, `CONFIRMING`, `CONFIRMED`, `REJECTED`).
- `expected_amount_atomic`: NUMERIC(78,0), snapshot de auditoría.
- `observed_amount_atomic`: NUMERIC(78,0).
- `rejection_reason`: VARCHAR/TEXT nullable.
- `created_at`: TIMESTAMPTZ.
- `confirmed_at`: TIMESTAMPTZ nullable.

Reglas de integridad:

- una evidencia concreta no puede confirmar dos intents;
- un intent normal no puede producir dos pagos confirmados;
- una evidencia rechazada puede coexistir con una evidencia posterior válida;
- los reintentos deben converger en un solo estado final.

Estas garantías deben reforzarse con índices/restricciones transaccionales, no solo con `if` en la aplicación.

---

## 11. Entitlements

### 11.1 Tabla conceptual: `entitlements`

Representa el derecho persistente de una cuenta a un juego.

Campos propuestos:

- `id`: UUID, PK.
- `user_id`: UUID, FK.
- `game_id`: UUID, FK.
- `acquisition_type`: ENUM (`ONCHAIN_PAYMENT`, `FREE`, `GIFT`, `PROMOTION`, `GATEWAY`, etc.).
- `source_payment_id`: UUID nullable, FK -> payments.id.
- `status`: ENUM (`ACTIVE`, `REVOKED`).
- `granted_at`: TIMESTAMPTZ.
- `revoked_at`: TIMESTAMPTZ nullable.
- `revocation_reason`: TEXT nullable.

Para una compra normal del MVP debe existir como máximo un Entitlement activo por `user_id + game_id`.

El Entitlement pertenece a la cuenta, no a la wallet.

---

## 12. Juegos gratuitos

Un juego gratuito no genera una falsa transferencia de valor cero.

El Core puede crear un Entitlement con `acquisition_type = FREE` siguiendo una política separada.

Esto mantiene limpio el dominio: adquisición no significa necesariamente pago.

---

## 13. Auditoría

### 13.1 Tabla conceptual: `audit_events`

En lugar de prometer una tabla mágicamente inmutable, proponemos un registro append-only de eventos relevantes.

Campos posibles:

- `id`: UUID.
- `event_type`: VARCHAR.
- `actor_type`: VARCHAR.
- `actor_id`: UUID/VARCHAR nullable.
- `entity_type`: VARCHAR.
- `entity_id`: UUID/VARCHAR.
- `old_state`: JSONB nullable.
- `new_state`: JSONB nullable.
- `reason`: TEXT nullable.
- `request_id`: VARCHAR nullable.
- `ip_address`: INET nullable y sujeto a política de retención.
- `created_at`: TIMESTAMPTZ.

Eventos especialmente sensibles:

- alta/revocación de claves;
- cambio de wallet de cobro;
- cambios de TrustChain;
- creación/expiración de Payment Intents;
- intentos de reutilizar evidencia;
- confirmaciones/rechazos de pagos;
- creación/revocación de Entitlements;
- acciones administrativas.

En fases futuras puede añadirse hash chaining o checkpoints firmados para hacer manipulación histórica detectable.

---

## 14. Índices y restricciones críticas

La lista exacta se definirá durante implementación, pero el diseño exige como mínimo:

1. email de usuario único donde corresponda;
2. fingerprint de clave indexado y no ambiguo;
3. wallet normalizada con reglas de ownership definidas para el MVP;
4. `UNIQUE(chain_id, tx_hash, log_index)` sobre evidencia EVM;
5. imposibilidad de consumir una evidencia confirmada en más de un intent;
6. máximo un pago confirmado por intent normal;
7. máximo un Entitlement activo por cuenta + juego;
8. valores `amount_atomic >= 0`;
9. `expires_at > created_at` en Payment Intents;
10. referencias a wallets receptoras solo si estaban verificadas al crear la oferta/intento.

---

## 15. Datos públicos vs privados

### Públicos o publicables

Según política del producto:

- perfil del estudio;
- claves públicas/fingerprints;
- hashes de builds;
- firmas de release;
- dominio declarado;
- atestaciones públicas con procedencia;
- metadatos del catálogo.

### Privados

- password hashes;
- sesiones;
- secretos OAuth;
- challenges vigentes;
- evidencia privada de TrustChain;
- señales antifraude;
- notas internas;
- asociación cuenta ↔ wallet cuando no sea necesaria públicamente;
- datos operacionales sujetos a privacidad y retención.

Nunca se almacenan claves privadas de desarrolladores ni seed phrases de usuarios.

---

## 16. Soft delete y conservación histórica

No todos los registros deben comportarse igual.

- `users`, `developers`, `games`, `offers`: pueden usar soft delete/estados.
- `payment_intents`, `payment_evidence`, `payments`, `entitlements`, `audit_events`: su historia no debe desaparecer por una operación normal de borrado de UI.
- evidencia sensible de TrustChain: puede requerir una política de retención y eliminación por privacidad/legalidad distinta de la historia pública.

El objetivo es conservar evidencia necesaria sin convertir "guardar todo para siempre" en una política accidental.

---

## 17. Concurrencia e idempotencia

El modelo debe permitir transacciones atómicas para:

```text
marcar evidencia como consumida
        +
confirmar Payment
        +
confirmar Payment Intent
        +
crear Entitlement
```

El sistema debe poder repetir el mismo evento del Watcher sin duplicar ningún efecto.

Los detalles de locks, isolation level y partial unique indexes pertenecen a implementación, pero la garantía funcional se considera obligatoria desde el diseño.

---

## 18. Migraciones

Si se confirma FastAPI/Python como stack del Core:

- SQLAlchemy 2.0 async es una opción propuesta;
- `asyncpg` es una opción propuesta;
- Alembic debe gestionar todo cambio de esquema;
- las migraciones deben formar parte del historial de Git;
- ninguna migración destructiva debe eliminar historia comercial sin un procedimiento explícito.

Estas tecnologías aún son decisiones de implementación, no principios filosóficos del protocolo.

---

## 19. Cambios respecto del borrador anterior

Este RFC reemplaza explícitamente varios supuestos del borrador inicial:

### Antes

```text
games.price
games.currency
games.wallet_address
purchases.tx_hash UNIQUE
purchases.amount_paid DECIMAL(10,2)
```

### Ahora

```text
game
  ↓
offer versionada
  ↓
payment_intent inmutable
  ↓
payment_submission (no confiable)
  ↓
payment_evidence (hecho on-chain)
  ↓
payment (evaluación)
  ↓
entitlement (derecho adquirido)
```

Esto deriva directamente de RFC-0001 y permite añadir otros métodos de adquisición en el futuro sin deformar el modelo central.

---

## 20. Decisiones pendientes antes de implementación

1. representación final de `chain_id` en el esquema;
2. duración de Payment Intent;
3. política exacta de finalidad para Polygon PoS;
4. reglas de ownership/uso compartido de wallets;
5. formato del Wallet Proof;
6. política final de firma de builds;
7. formato de atestaciones TrustChain;
8. política de retención de evidencia privada y auditoría;
9. tratamiento exacto de revocaciones de Entitlements;
10. comportamiento ante pagos tardíos/duplicados que requieran soporte manual.

Hasta cerrar estas decisiones y revisar este modelo contra RFC-0003/RFC-0004, el documento permanece en Fase 0.

---

## 21. Decisión arquitectónica

El modelo de datos de Ludix adopta como regla:

> **La base de datos no representa "una compra" como una sola fila ambigua. Representa por separado intención, evidencia, evaluación y derecho adquirido.**

Esto permite que Ludix siga siendo no custodial, auditable, recuperable e idempotente sin convertir la blockchain o la wallet en sustitutos de la cuenta del jugador.
