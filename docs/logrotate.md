# Logrotate - Administracion y rotacion de archivos de registro 



### Descripcion : 
Es un script que se ejecuta para la administracio de sistemas que generan una gran cantidad de archivos de registro. 

Permite:
- Autorotacion 
- Compresion 
- Borrado 
- Y envio de archivos de registro por correo

Generalmente este script se ejecuta como un cron job o en distros mas actuales, se usa systemd como logrotate.timer

Usaremos 

```
systemctl status logrotate.timer
```


### Ficheros de configuracion 

Los archivos de configuracion se organiza principalmente mediante:

1. `/etc/logrotate.conf` -> Archivos de configuracion global y contiene algunas configraciones predeterminadas para la rotacion para algunos que no tienen configuracion
2. `/etc/logrotate.d/*` -> Archivos que constan de especificaciones para los grupos de archivos de registro 


### `/etc/logrotate.conf`

Contiene opciones globales de logrotate y normalmente incluye las configuraciones adicionales ubicadas en `/etc/logrotate.d/`.

Ejemplo:

```
# see "man logrotate" for details

# global options do not affect preceding include directives

# rotate log files weekly
weekly

# use the adm group by default, since this is the owning group
# of /var/log/.
su root adm

# keep 4 weeks worth of backlogs
rotate 4

# create new (empty) log files after rotating old ones
create

# use date as a suffix of the rotated file
#dateext

# uncomment this if you want your log files compressed
#compress

# packages drop log rotation information into this directory
include /etc/logrotate.d

# system-specific logs may also be configured here.

```

### `/etc/logrotate.d/`

```
/var/log/samba/*.log {
     notifempty
     copytruncate
     sharedscripts
     postrotate
          /bin/kill -HUP `cat /var/lock/samba/*.pid`
     endscript
}
```

## Directivas principales

| Directiva | Función |
|---|---|
| `compress` | Comprime los archivos de log rotados con el algoritmo gzip. |
| `daily` | Realiza la rotación diariamente. |
| `weekly` | Realiza la rotación semanalmente. |
| `monthly` | Realiza la rotación mensualmente. |
| `delaycompress` | Retrasa la compresión de la versión más reciente rotada. |
| `create` | Crea un nuevo archivo de log después de la rotación. |
| `notifempty` | No rota el archivo si está vacío. |
| `missingok` | No genera un error si el archivo no existe. |
| `olddir` | Especifica un directorio para almacenar los logs rotados. |
| `rotate` | Especifica cuántas versiones antiguas se conservarán. |
| `size` | Permite realizar la rotación según el tamaño del archivo. |
| `prerotate` | Ejecuta un script antes de la rotación. |
| `postrotate` | Ejecuta un script después de la rotación. |
| `endscript` | Indica el final de un bloque `prerotate` o `postrotate`. |
| `sharedscripts` | Ejecuta el script una sola vez para todo el bloque. |
| `copytruncate` | Copia el archivo y después trunca el original. |
| `errors` | Envía los errores de logrotate a la dirección indicada. |

## Debuggin configuration file

```
logrotate -d /etc/logrotate.conf
```

