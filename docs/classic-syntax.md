# Sintaxis clásica de rsyslog

## 1. Introducción

Rsyslog permite configurar el procesamiento de mensajes mediante una
sintaxis clásica basada principalmente en tres elementos:

    selector    action

El selector determina qué mensajes serán procesados y la acción
determina qué hacer con ellos.

Ejemplo:

    authpriv.*    /var/log/auth.log

En este caso, los mensajes pertenecientes a la facility `authpriv`
se almacenan en `/var/log/auth.log`, independientemente de su
prioridad.

---

## 2. Estructura básica

La estructura general es:

    facility.priority    action

Por ejemplo:

    cron.info    /var/log/cron.log

Donde:

- `cron` → facility
- `info` → prioridad mínima seleccionada
- `/var/log/cron.log` → acción

---

## 3. Facilities

Las facilities representan el origen o categoría del mensaje.

| Facility | Descripción |
|---|---|
| kern | Mensajes del kernel |
| user | Mensajes de procesos de usuario |
| mail | Sistema de correo |
| daemon | Servicios/daemons |
| auth | Autenticación |
| authpriv | Autenticación privada |
| cron | Cron y tareas programadas |
| syslog | Mensajes internos de syslog |
| local0-local7 | Uso definido por el administrador |

Ejemplo:

    authpriv.*    /var/log/auth.log

---

## 4. Prioridades

Las prioridades representan la severidad del mensaje.

De mayor a menor severidad:

    emerg
    alert
    crit
    err
    warning
    notice
    info
    debug

El selector:

    *.info

selecciona mensajes desde `info` hasta `debug`, dependiendo del
comportamiento de selección de prioridades de rsyslog.

---

## 5. Uso de `*`

El carácter `*` representa cualquier valor.

Por ejemplo:

    *.info    /var/log/messages

Significa que se procesan mensajes de cualquier facility con la
prioridad seleccionada.

También puede utilizarse:

    authpriv.*

para seleccionar cualquier prioridad de `authpriv`.

---

## 6. Exclusión de facilities

Es posible excluir una facility utilizando `none`.

Ejemplo:

    *.info;authpriv.none    /var/log/messages

Esto significa:

- aceptar mensajes de todas las facilities
- excluir `authpriv`
- almacenar los mensajes seleccionados en `/var/log/messages`

---

## 7. Varias reglas

Es posible definir varias reglas:

    authpriv.*    /var/log/auth.log
    cron.*        /var/log/cron.log
    mail.*        /var/log/mail.log

Cada regla define un selector y una acción.

---

## 8. Acciones

Una acción define qué hacer con un mensaje.

### Archivo local

    authpriv.*    /var/log/auth.log

### Consola

    *.emerg    :omusrmsg:*

### Envío remoto mediante UDP

    authpriv.*    @192.168.1.100:514

### Envío remoto mediante TCP

    authpriv.*    @@192.168.1.100:514

La diferencia entre `@` y `@@` es:

    @   → UDP
    @@  → TCP

---

## 9. Ejemplo utilizado en el proyecto

En este proyecto se utiliza un servidor central de logs.

    Cliente
        |
        | TCP/514
        v
    Servidor rsyslog

El cliente puede utilizar:

    authpriv.*    @@192.168.1.100:514

Esto permite enviar los mensajes de autenticación al servidor
central mediante TCP.

---

## 10. Verificación

Antes de aplicar una configuración se puede verificar la sintaxis:

    sudo rsyslogd -N1

Para generar un mensaje de prueba:

    logger -p local0.info "Mensaje de prueba"

Después se verifica el archivo configurado:

    sudo tail -f /var/log/...

---

## 11. Archivos de configuración

La configuración principal normalmente se encuentra en:

    /etc/rsyslog.conf

También pueden utilizarse archivos adicionales:

    /etc/rsyslog.d/*.conf

Para este proyecto se recomienda mantener las configuraciones
específicas del proyecto dentro de `/etc/rsyslog.d/`.

---

## 12. Referencia rápida

    facility.priority    action

Ejemplos:

    authpriv.*           /var/log/auth.log
    cron.*               /var/log/cron.log
    *.info;authpriv.none /var/log/messages
    authpriv.*           @192.168.1.100:514
    authpriv.*           @@192.168.1.100:514
