# 🔗 Ludix TrustChain: Especificación de Confianza

> **Estado:** Fase 0 - Diseño Conceptual.
> **Propósito:** Garantizar la autenticidad de los desarrolladores y la integridad de los juegos sin intermediarios centralizados.

---

## 1. Introducción
El **TrustChain** es un sistema de verificación por capas diseñado para proteger la reputación de los desarrolladores legítimos y la seguridad de los jugadores. En Ludix, la identidad no la otorga una empresa, sino que se construye mediante pruebas criptográficas y validaciones de terceros.

---

## 2. Principios de Diseño
* **Transparencia Total**: Cada certificación y cambio de estado debe ser auditable.
* **Soberanía del Desarrollador**: El desarrollador es dueño de sus claves y de su identidad técnica.
* **Resiliencia a Forks**: Si alguien crea un fork de Ludix, las firmas originales siguen siendo válidas.

---

## 3. Las 6 Capas de Confianza

### ## Capa 1: Identidad Criptográfica (El Ancla)
Cada desarrollador debe generar un par de claves (pública/privada) al registrarse.
* **Tecnología**: Algoritmo **Ed25519**.
* **Uso**: La clave privada firma los metadatos y hashes de los archivos.
* **Validación**: El Core API y el Launcher verifican la firma contra la clave pública registrada.

### ## Capa 2: Verificación de Dominio
Vincular una cuenta de Ludix con un dominio web oficial.
* **Métodos**: Carga de archivo JSON en `/.well-known/ludix-verify.json` o registro **DNS TXT**.
* **Resultado**: Evita la suplantación de marcas conocidas.

### ## Capa 3: Vínculos Sociales y de Plataforma
Integración opcional con GitHub, Twitter/X, Itch.io o Steamworks para heredar reputación.

### ## Capa 4: Integridad de los Builds
* **Hash Obligatorio**: Registro de **SHA-256** para cada build.
* **Firma de Build**: Firma digital opcional con la clave privada del estudio.
* **Seguridad**: El Launcher bloquea la ejecución si el hash no coincide.

### ## Capa 5: Sistema de Reputación Dinámica
Algoritmo que pondera antigüedad, ventas exitosas y reportes de la comunidad.

### ## Capa 6: Requisitos de Alta Confianza
Nivel superior exigido para la venta de Steam Keys y claves externas, evitando mercados grises.

---

## 4. Manejo de Crisis y Revocación (Blindaje de Seguridad)

Para mitigar el riesgo de robo o pérdida de claves (Capa 1), se implementan dos mecanismos:

### ## Clave de Revocación (Cold Storage)
Al registrarse, el desarrollador puede registrar una **clave de revocación** secundaria. Esta clave debe guardarse fuera de línea (papel o hardware wallet) y solo sirve para invalidar la clave activa en caso de compromiso.

### ## Período de Cuarentena
Cualquier cambio en la clave pública principal o en la billetera de cobro disparará un estado de "Cuarentena" de 48-72 horas, alertando a los compradores y pausando las ventas de alta confianza hasta que se confirme la legitimidad.

---

## 5. Transparencia y Auditoría de Forks

Para asegurar que un fork de Ludix sea "limpio" y no haya sido manipulado por su administrador:

### ## Registro Público de Verificaciones (Auditoría)
El backend mantendrá un log público e inmutable de cada evento de verificación (ej: "Dominio X verificado por el Oráculo Y en fecha Z"). Los launchers podrán consultar múltiples fuentes para confirmar que un desarrollador marcado como "Verificado" en un fork, realmente superó las pruebas técnicas.

---

## 6. Flujo de Implementación Técnica (MVP)
1. **Registro**: Generación de *challenge* aleatorio por el backend.
2. **Firma**: El dev firma el *challenge* y lo devuelve.
3. **Verificación**: El backend guarda la clave pública y marca la identidad como verididcada.