![Bandit Image](../../Imagenes/level-16-17-1.png)
# Siempre poner al conectarse a una maquina por SSH : -export TERM=xterm
![Bandit Image](../../Imagenes/bandit-banner.png)


## 🔎 Objetivo del nivel

El objetivo es encontrar **un puerto abierto en el rango 31000–32000 en localhost**.  
Ese puerto debe aceptar conexiones **SSL** y devolverá una **clave privada RSA**, con la cual se podrá iniciar sesión como `bandit17`.

---

## 🪜 Paso a paso (con consola real)

### 1. Crear un directorio temporal

# {Comando}
![Bandit Image](../../Imagenes/Captura%20de%20pantalla%202025-09-05%20122937.png)

# EXPLICACION DEL COMANDO DE ARRIBA 
### 1. `ls`

`bandit16@bandit:~$ ls`

- **Definición**: `ls` lista el contenido del directorio actual.
    
- **Salida esperada**: mostrará archivos o carpetas dentro de `~` (el home de bandit16).
    

---

### 2. `cd /tmp/`

`bandit16@bandit:~$ cd /tmp/`

- **Definición**: `cd` (change directory) cambia el directorio de trabajo actual.
    
- **Uso aquí**: pasa de la carpeta `~` a `/tmp/`, que es un directorio temporal de Linux donde cualquier usuario puede crear archivos o carpetas.
    

---

### 3. `ls` dentro de `/tmp/`

`bandit16@bandit:/tmp$ ls ls: cannot open directory '.': Permission denied`

- **Definición**: intenta listar el contenido de `/tmp/`.
    
- **Salida**: “Permission denied” significa que **ese usuario (bandit16)** no tiene permisos para ver el contenido de `/tmp/` en ese sistema.
    
- ⚠️ Esto no cambia de carpeta: simplemente intenta mostrar lo que hay en `/tmp/`.
    

---

### 4. `mktemp -d`

`bandit16@bandit:/tmp$ mktemp -d /tmp/tmp.PoxVPrvgV6`

- **Definición**: `mktemp` crea archivos o directorios temporales con nombres aleatorios únicos.
    
- **Opción `-d`**: indica que se cree **un directorio**, no un archivo.
    
- **Salida**: `/tmp/tmp.PoxVPrvgV6` → ruta completa de la carpeta temporal recién creada.
    
- Ahora puedes entrar ahí con:
    
    `cd /tmp/tmp.PoxVPrvgV6`
    

---

✅ **Aclaración a tu duda:**  
El comando que **sí cambia de directorio** es **`cd /tmp/`**.  
El comando `ls` **solo lista** (no se mete en ninguna parte).  
Y `mktemp -d` **crea un directorio** nuevo dentro de `/tmp/`, pero tampoco entra automáticamente en él.

# {Salida}

## `/tmp/tmp.PoxVPrvgV6`

## 💬{Comentario del profe}

Es como abrir tu propio “taller” dentro de /tmp para no molestar a nadie.`

---

### 2. Script en Bash para escanear puertos


![Bandit Image](../../Imagenes/level-16-17-4.png)


# {Comando}

#!/bin/bash

  

function ctrl_c(){

  echo -e "\n\n[!] Saliendo...\n"

  tput cnorm; exit 1

}

  

.# Ctrl+C

trap ctrl_c INT

  

tput civis # Ocultar el cursor

for port in $(seq 1 65535); do

  (echo '' > /dev/tcp/127.0.0.1/$port) 2>/dev/null && echo "[+] $port - OPEN" &

done; wait

  

.# Recuperamos el cursor # (siempre que haya un .# quitar el punto depues para ejecutar que yo lo estoy poniendo porque sino me pone titulo y me jode el scriipt)

tput cnorm

# {Salida esperada}

![Bandit Image](../../Imagenes/level-16-17-5.png)

## 💬{Comentario del profe} acuérdate en el script el .# 

Este script marca en verde los “números de teléfono” que descuelgan dentro del rango 31000–32000.`

---

### 3. Escaneo con Nmap

![Bandit Image](../../Imagenes/level-16-17-6.png)

# {Comando}


## `nmap --open -T5 -v -n -p31000-32000 127.0.0.1`

# Explicación comando pincha para abrir. 
 ## Desglose parámetro por parámetro

- **`nmap`**: herramienta de escaneo de puertos/red.
    
- **`--open`**: muestra **solo** los puertos en estado **open** (oculta `closed`, `filtered`, etc.).
    
- **`-T5`**: plantilla de **timing** “Insane” (muy agresiva). Aumenta la velocidad (menos esperas y más paralelismo) a costa de riesgo de **falsos negativos** o bloqueos por firewalls/IDS.
    
- **`-v`**: **verbose**. Muestra más detalles del progreso. Se puede repetir (`-vv`) para aún más verbosidad.
    
- **`-n`**: **no resolver DNS**. Evita búsquedas directas/inversas que ralentizan y generan ruido. Útil cuando ya usas una IP.
    
- **`-p31000-32000`**: **rango de puertos** a escanear.
    
    - Cubre **1001** puertos (32000−31000=1000, +1 por ser inclusivo).
        
    - Acepta listas/comas: `-p22,80,443,31000-32000`.
        
- **`127.0.0.1`**: destino. Es la **interfaz loopback** (la propia máquina).
    
    - Por defecto, si tienes privilegios, Nmap hará un **SYN scan (`-sS`)**; sin privilegios, hará un **connect scan (`-sT`)**.
        

> Nota de la captura: las comillas invertidas `…` y el prefijo `##` pertenecen al **formato Markdown** del apunte, **no** se escriben en la terminal.

# {Salida resumida}

## `La de la captura`

## 💬{Comentario del profe}

Nmap confirma rápidamente cuáles son los puertos abiertos.

---

### 4. Probar los puertos con SSL
![Bandit Image](../../Imagenes/level-16-17-7.png)

# {Comando}

## `openssl s_client -connect 127.0.0.1:31790`

# {Salida esperada}

## `CONNECTED(00000003) ... (RSA PRIVATE KEY) ...`

## 💬{Comentario del profe}

De todos los puertos abiertos, solo uno habla SSL y devuelve una clave RSA.`

---

### 5. Obtener la clave privada RSA

# {Comando}
![Bandit Image](../../Imagenes/level-16-17-8.png)

`bandit16@bandit:/tmp/tmp.PoxVPrvgV6$ ncat --ssl 127.0.0.1 31790`

# {Salida}

![Bandit Image](../../Imagenes/level-16-17-9.png)

`cluFn7wTiGryunymYOu4RcffSxQluehd Correct! -----BEGIN RSA PRIVATE KEY----- MIIEogIBAAKCAQEAvmOkui... ... (clave completa) ... -----END RSA PRIVATE KEY-----`

# 💬{Comentario del profe}

El servidor devuelve la clave privada de bandit17. Es tu nueva llave de acceso.`

---

### 6. Guardar la clave en un archivo

# {Comando}

## `nano id_rsa`

⚠️ Si aparece `Permission denied` al abrir `nano`, guarda la clave dentro del directorio temporal (`/tmp/tmp.PoxVPrvgV6/id_rsa`).

---

### 7. Ajustar permisos del archivo

![Bandit Image](../../Imagenes/level-16-17-10.png)

# {Comando}

## `chmod 600 id_rsa ls -l`

# {Salida}

`-rw------- 1 bandit16 root 1675 Apr 13 16:12 id_rsa -rwxr-xr-x 1 bandit16 root  219 Apr 13 16:07 portScan.sh`

## 💬{Comentario del profe}

SSH exige que la clave privada sea solo tuya (600).  Si otros usuarios pudieran leerla, la conexión sería rechazada.`

---

### 8. Conectarse al siguiente nivel

\Tag{Comando}

`ssh -i id_rsa bandit17@localhost`

\Tag{Desglose}

- **ssh** → cliente seguro de conexión.
    
- **-i id_rsa** → indica la clave privada.
    
- **bandit17@localhost** → usuario y host de destino.
    

---

## 🧰 Opciones útiles

- **nmap -p rango host** → escanea un rango de puertos.
    
- **openssl s_client -connect host:puerto** → prueba conexión SSL.
    
- **chmod 600 archivo** → ajusta permisos para claves privadas.
    
- **ssh -i clave usuario@host** → conecta usando una clave RSA.
    

---

## ❌ Errores comunes y soluciones

- ❌ Escanear mal el rango → ✔️ usar `-p31000-32000`.
    
- ❌ Usar `nc` sin SSL → ✔️ el puerto rechaza la conexión, usar `ncat --ssl`.
    
- ❌ No cambiar permisos de la clave → ✔️ aplicar `chmod 600 id_rsa`.
    

---

## 🧾 Chuleta final

|Comando|Propósito|Uso mínimo|
|---|---|---|
|seq 31000 32000|Generar rango de puertos|`seq 31000 32000`|
|(echo '' > /dev/tcp/127.0.0.1/PUERTO)|Probar conexión TCP|`(echo '' > /dev/tcp/127.0.0.1/31046)`|
|nmap --open -p31000-32000 127.0.0.1|Escanear rango de puertos|`nmap --open -p31000-32000 127.0.0.1`|
|ncat --ssl 127.0.0.1 PUERTO|Conectar a puerto con SSL|`ncat --ssl 127.0.0.1 31790`|
|chmod 600 id_rsa|Asegurar permisos de clave privada|`chmod 600 id_rsa`|
|ssh -i id_rsa bandit17@localhost|Acceder con clave privada RSA|`ssh -i id_rsa bandit17@localhost`|

---

## 🧩 Script final completo

![Bandit Image](../../Imagenes/level-16-17-11.png)


`#!/usr/bin/env bash set -euo pipefail  PASSWORD="cluFn7wTiGryunymYOu4RcffSxQluehd"  # Escanear puertos abiertos for port in $(seq 31000 32000); do   if (echo "" > /dev/tcp/127.0.0.1/$port) 2>/dev/null; then     echo "[+] Puerto abierto: $port"   fi done  # Conectar al puerto correcto con SSL y guardar clave ncat --ssl 127.0.0.1 31790 > id_rsa  # Ajustar permisos de clave chmod 600 id_rsa  # Conectar a bandit17 usando la clave ssh -i id_rsa bandit17@localhost`

---

## 🗒️ Notas adicionales

- Versión manual → probar cada puerto con `nc`/`openssl`.
    
- Versión intermedia → usar un script de escaneo con `/dev/tcp`.
    
- Versión completa → script automatizado con conexión SSH incluida.
    

---

## 📚 Referencias

- `man nmap`, `man ssh`, `man openssl`
    
- OverTheWire Bandit
    
- Documentación de claves privadas en OpenSSH
    

---

## ⚖️ Marco legal/ético

El escaneo de puertos y uso de claves privadas **solo debe hacerse en entornos controlados o con permiso expreso**.  
En Bandit está permitido y es legal; en un sistema ajeno, sería considerado un ataque.





