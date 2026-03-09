# Autenticación SSH Basada en Certificados (Linux)

## 📝 Descripción General
Este proyecto forma parte del módulo de "Bastionado de Redes y Sistemas". Su objetivo es elevar el nivel de seguridad en la administración remota de servidores Linux (Ubuntu Server) sustituyendo el modelo tradicional de claves públicas individuales y confianza a ciegas (TOFU - *Trust on First Use*) por una infraestructura basada en certificados SSH. Mediante la creación de una Autoridad Certificadora (CA) propia, se establece un control centralizado y robusto sobre quién y cómo se accede a los sistemas.

## 🎯 Objetivos del Proyecto
* Eliminar la gestión manual de archivos `authorized_keys` y `known_hosts`.
* Crear y gestionar una Autoridad Certificadora (CA) interna para firmar claves de hosts y de usuarios.
* Configurar el demonio OpenSSH (`sshd_config`) para confiar exclusivamente en los certificados emitidos por la CA.
* Implementar mecanismos de seguridad como el principio de caducidad temporal de las credenciales.
* Comprender y aplicar métodos para revocar el acceso de un usuario comprometido sin necesidad de eliminar su cuenta del sistema operativo.

## 🛠️ Tecnologías y Herramientas
* **Sistema Operativo**: Ubuntu Server (Versión 22.04 LTS o superior).
* **Protocolo de Administración**: OpenSSH.
* **Criptografía**: Herramientas nativas de generación de claves (`ssh-keygen`) con algoritmos seguros (RSA 4096 bits o ED25519).

## ⚙️ Detalles de Implementación Destacados
* **Infraestructura CA**: Se generaron pares de claves dedicados para actuar como CA de Hosts y CA de Usuarios. Esto permite que el servidor valide a los clientes y, a su vez, que los clientes confíen automáticamente en el servidor, mitigando ataques *Man-in-the-Middle* (MitM).
* **Firma de Certificados**: Se documentó el uso del comando `ssh-keygen -s` para firmar las claves públicas de los usuarios, inyectando metadatos críticos como el identificador de la clave (`-I`), los usuarios o *principals* permitidos (`-n`) y el periodo de validez del certificado (`-V`).
* **Bastionado del Servidor**: Se modificó el archivo de configuración `/etc/ssh/sshd_config` introduciendo las directivas `TrustedUserCAKeys` y `HostCertificate` para forzar la autenticación mediante certificados.
* **Estrategias de Revocación**: Se plantearon y resolvieron escenarios de respuesta ante incidentes. Para invalidar el acceso de un usuario sin borrar su cuenta local, se demostró el uso de directivas como `RevokedKeys` (KRL) o la dependencia de certificados de muy corta duración (ej. 1 día) para caducar accesos automáticamente.

## 📄 Documentación Completa
Para consultar el paso a paso de los comandos ejecutados, la configuración exacta de los ficheros del servidor y las pruebas de conexión realizadas desde la terminal del cliente, revisa la memoria técnica del proyecto:

👉 [Enlace al Informe Técnico en Google Drive](https://docs.google.com/document/d/1f2-yPNn8Q_OaQy0iQOIMIWwqy1fzIasp/edit?usp=sharing&ouid=117736854197780331273&rtpof=true&sd=true)

## ⚠️ Aviso Legal / Ética
*La configuración de accesos y la gestión de identidades detallada en este proyecto se han realizado en un entorno virtualizado de laboratorio. La implementación de una CA en entornos de producción reales requiere medidas adicionales de protección para la clave privada de la CA (como módulos HSM o bóvedas aisladas).*
