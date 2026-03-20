# 🔗 Ludix TrustChain: Especificación de Confianza
    Estado: Fase 0 - Diseño Conceptual.
    Propósito: Garantizar la autenticidad de los desarrolladores y la integridad de los juegos sin intermediarios centralizados.

##1. Introducción
El TrustChain es un sistema de verificación por capas diseñado para proteger la reputación de los desarrolladores legítimos y la seguridad de los jugadores. En Ludix, la identidad no la otorga una empresa, sino que se construye mediante pruebas criptográficas y validaciones de terceros.

##2. Principios de Diseño
Transparencia Total: Cada certificación y cambio de estado debe ser auditable.

Soberanía del Desarrollador: El desarrollador es dueño de sus claves y de su identidad técnica.

Resiliencia a Forks: Si alguien crea un fork de Ludix, las firmas criptográficas de los desarrolladores originales siguen siendo válidas y verificables.

##3. Las 6 Capas de Confianza
Capa 1: Identidad Criptográfica (El Ancla)
Cada desarrollador debe generar un par de claves (pública/privada) al registrarse.

Tecnología Propuesta: Algoritmo Ed25519 (rápido, seguro y con firmas pequeñas).

Uso: La clave privada firma los metadatos del juego y los hashes de los archivos.

Validación: El Core API de Ludix y el Launcher verifican que la firma coincida con la clave pública registrada del estudio.

Capa 2: Verificación de Dominio (Vinculación Web)
Vincular una cuenta de Ludix con un dominio web oficial (ej. estudio-indie.com).

Método A (HTTP): Subir un archivo JSON en https://dominio.com/.well-known/ludix-verify.json.

Método B (DNS): Añadir un registro TXT con un token único generado por Ludix.

Resultado: Esto evita que un tercero suplante a una marca conocida.

Capa 3: Vínculos Sociales y de Plataforma
Integración opcional con cuentas externas para heredar reputación:

GitHub/GitLab: Verificación de código fuente.

Steamworks: Para desarrolladores que ya tienen presencia en Steam.

Twitter/X e Itch.io: Validación de comunidad.

Capa 4: Integridad de los Builds
Asegurar que el archivo que el jugador descarga es exactamente el que el desarrollador subió.

Hash Obligatorio: Cada build debe registrar su SHA-256.

Firma de Build: (Opcional) El archivo puede estar firmado digitalmente con la clave privada del estudio.

Alerta del Launcher: Si el hash descargado no coincide con el registrado, el Launcher bloquea la ejecución.

Capa 5: Sistema de Reputación Dinámica
Un algoritmo que calcula un "Nivel de Confianza" basado en señales positivas y negativas:

Señales Positivas: Antigüedad de la cuenta, volumen de ventas exitosas, dominio verificado.

Señales Negativas: Reportes de malware, keys de Steam inválidas, cambios frecuentes de wallets.

Capa 6: Requisitos de Alta Confianza (Venta de Claves)
Para vender Steam Keys u otros productos externos, se requiere el máximo nivel de verificación:

Validación Humana/Comunitaria: Solo devs con reputación consolidada y dominio verificado pueden habilitar esta función para evitar mercados grises.

##4. Flujo de Implementación Técnica (MVP)
Registro: El backend genera un challenge (un string aleatorio).

Firma: El desarrollador firma el challenge con su clave privada y lo envía de vuelta.

Verificación: El backend guarda la clave pública y marca la cuenta como "Identidad Técnica Verificada".