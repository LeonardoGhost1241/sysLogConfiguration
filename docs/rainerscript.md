# RainerScript

## 1. Introducción

RainerScript es el lenguaje de configuración utilizado por rsyslog
para expresar lógica de procesamiento de mensajes.

A diferencia de la sintaxis clásica, permite utilizar condiciones,
propiedades, operadores y acciones de una forma más estructurada.

El modelo básico es:

    mensaje
       ↓
    condición
       ↓
    acción

Ejemplo:

    if ($programname == "sshd") then {
        action(
            type="omfile"
            file="/var/log/ssh.log"
        )
    }

---

## 2. Condiciones

Las condiciones se expresan mediante `if`.

Ejemplo:

    if ($programname == "sshd") then {
        action(
            type="omfile"
            file="/var/log/ssh.log"
        )
    }

La condición comprueba el valor de `$programname`.

---

## 3. Propiedades de los mensajes

Rsyslog proporciona propiedades que permiten consultar
información del mensaje.

Algunas propiedades importantes:

| Propiedad | Información |
|---|---|
| `$msg` | Contenido del mensaje |
| `$programname` | Programa que generó el mensaje |
| `$hostname` | Host que generó el mensaje |
| `$fromhost-ip` | IP de origen |
| `$syslogfacility-text` | Facility |
| `$syslogseverity-text` | Severidad |
| `$syslogtag` | Tag del mensaje |

Ejemplo:

    if ($programname == "sshd") then {
        ...
    }

---

## 4. Operadores

Los operadores permiten construir condiciones.

### Igualdad

    $programname == "sshd"

### Diferencia

    $programname != "sshd"

### Contiene

    $msg contains "Failed password"

### Comienza con

    $msg startswith "Connection"

### Condiciones múltiples

    if ($programname == "sshd" and
        $msg contains "Failed password") then {
        ...
    }

---

## 5. Acciones

Las acciones indican qué hacer con el mensaje.

Una de las acciones más comunes es `omfile`.

Ejemplo:

    action(
        type="omfile"
        file="/var/log/ssh.log"
    )

Esto almacena el mensaje en el archivo indicado.

---

## 6. Envío remoto

Para enviar mensajes a otro servidor se puede utilizar
el módulo `omfwd`.

Ejemplo conceptual:

    action(
        type="omfwd"
        target="192.168.1.100"
        port="514"
        protocol="tcp"
    )

En este caso, los mensajes son enviados al servidor
`192.168.1.100` mediante TCP.

---

## 7. Ejemplo de filtrado

Se puede combinar una propiedad del mensaje con su contenido.

    if ($programname == "sshd" and
        $msg contains "Failed password") then {

        action(
            type="omfile"
            file="/var/log/ssh-failed.log"
        )

    }

La regla:

1. comprueba que el programa sea `sshd`
2. comprueba que el mensaje contenga `Failed password`
3. almacena el mensaje en `ssh-failed.log`

---

## 8. Detener el procesamiento

La instrucción `stop` permite detener el procesamiento
del mensaje en ese punto.

Ejemplo:

    if ($programname == "sshd") then {
        action(
            type="omfile"
            file="/var/log/ssh.log"
        )
        stop
    }

Esto evita que el mensaje continúe siendo procesado por
reglas posteriores.

---

## 9. Templates

Los templates permiten definir dinámicamente cómo se
almacenan o formatean los mensajes.

Ejemplo conceptual:

    template(
        name="PerHost"
        type="string"
        string="/var/log/remote/%hostname%/%programname%.log"
    )

Después puede utilizarse el template en una acción.

---

## 10. Ejemplo utilizado en el proyecto

El cliente puede reenviar mensajes al servidor central:

    action(
        type="omfwd"
        target="192.168.1.100"
        port="514"
        protocol="tcp"
    )

El servidor central puede utilizar condiciones para
procesar los mensajes recibidos.

---

## 11. Verificación

La configuración puede validarse mediante:

    sudo rsyslogd -N1

También puede generarse un mensaje de prueba:

    logger -p local0.info "Mensaje de prueba"

Posteriormente se verifica que el mensaje haya sido
procesado correctamente.

---

## 12. Referencia rápida

    if (condición) then {
        action(
            type="omfile"
            file="/var/log/example.log"
        )
    }

Propiedades frecuentes:

    $msg
    $programname
    $hostname
    $fromhost-ip
    $syslogfacility-text
    $syslogseverity-text

Operadores frecuentes:

    ==
    !=
    contains
    startswith
    and
    or
    not
