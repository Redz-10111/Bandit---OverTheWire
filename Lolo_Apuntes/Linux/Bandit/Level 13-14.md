![Bandit Image](../../Imagenes/level-13-14-1.png)


# Siempre poner al conectarse a una maquina por SSH : -export TERM=xterm
![Bandit Image](../../Imagenes/bandit-banner.png)


# Se ha conectado con la clave de SSH/OpenSSH/Keys

## 📜 Enunciado oficial

> The password for the next level is stored in **/etc/bandit_pass/bandit14** and can only be read by user **bandit14**. For this level, you don’t get the next password, but you get a private **SSH key** that can be used to log into the next level. **Note:** _localhost_ is a hostname that refers to the machine you are working on.

---

## 🔎 Objetivo del nivel

Acceder como **bandit14** usando la **clave privada SSH** que nos dan en `sshkey.private` y, una vez dentro, leer el archivo:

`/etc/bandit_pass/bandit14`

---

## 🪜 Paso a paso (con consola real)

### 1) Ver qué tenemos en el HOME de bandit13

# {Comando}

## `ls`

# {Salida}

## `sshkey.private`

## {Desglose del comando}

- **Binario**: `ls` → lista ficheros del directorio actual.
    
- **Parámetros**: _(ninguno)_.  
## 💬{Comentario del profe}:

Nos confirman que el recurso clave de este nivel es el fichero **sshkey.private**.
    

---

### 2) Probar a leer directamente la contraseña (fallo esperado)

# {Comando}

## `cat /etc/bandit_pass/bandit14`

# {Salida}

## `cat: /etc/bandit_pass/bandit14: Permission denied`

\Tag{Desglose del comando}

- **Binario**: `cat` → imprime el contenido del archivo.
    
- **Ruta**: `/etc/bandit_pass/bandit14` → contiene la password del siguiente nivel.  
    💬 \Tag{Comentario del profe} No somos **bandit14**, por eso el sistema nos deniega el acceso.
    

---

### 3) Comprobar permisos del archivo para entender el error

![Bandit Image](../../Imagenes/level-13-14-3.png)

# {Comando}

## `ls -l /etc/bandit_pass/bandit14`

# {Salida}

## `-r-------- 1 bandit14 bandit14 33 May  7  2020 /etc/bandit_pass/bandit14`

# {Desglose del comando}

- **`ls -l`** → listado largo (permisos/owner/grupo/tamaño/fecha/nombre).
    
- **Permisos**: `-r--------` → _solo_ el propietario puede leerlo.
    
- **Owner/Group**: `bandit14 bandit14`.  
## 💬{Comentario del profe}:

Solo **bandit14** puede leerlo. Necesitamos **convertirnos** en bandit14 (no suplantar, sino **loguearnos** con su clave privada).
    

---

### 4) Ver la clave privada que nos dan
![Bandit Image](../../Imagenes/level-13-14-4.png)

# {Comando}

## `cat sshkey.private`

# {Salida}

## `-----BEGIN RSA PRIVATE KEY-----
## `MIIF... (clave larga recortada) ...  
## `-----END RSA PRIVATE KEY-----`

## {Desglose del comando}

- **Es un RSA PRIVATE KEY** → se usa con `ssh -i`.  
## 💬{Comentario del profe}:

Nunca compartas claves privadas. Aquí es parte del reto, pero trata este material como secreto.
    

---

### 5) (Recomendado) Ajustar permisos de la clave privada

# {Comando}

## `chmod 600 sshkey.private`



## {Desglose del comando}

- **`chmod 600`** → dueño puede leer/escribir; nadie más.  
## 💬{Comentario del profe}:

OpenSSH suele exigir permisos estrictos o salta el error **“Bad permissions”**.
    

---

### 6) Conectar por SSH a **localhost** como **bandit14** usando la clave

![Bandit Image](../../Imagenes/level-13-14-5.png)

# {Comando}

## `ssh -i sshkey.private bandit14@localhost`

# {Salida} (típico)

`Could not create directory '/home/bandit13/.ssh'. The authenticity of host 'localhost (127.0.0.1)' can't be established. ECDSA key fingerprint is SHA256:98UL0ZW... Are you sure you want to continue connecting (yes/no)? yes Warning: Permanently added 'localhost' (ECDSA) to the list of known hosts. bandit14@bandit:~$`

## {Desglose del comando}

- **`ssh`**: cliente SSH.
    
- **`-i sshkey.private`**: ruta a la **clave privada** a usar.
    
- **`bandit14@localhost`**: usuario/host destino (localhost = misma máquina).  
## 💬{Comentario del profe}
    
- El warning de “Could not create directory '/home/bandit13/.ssh'” es normal (no tenemos permisos para escribir known_hosts).
    
- Responde **yes** para aceptar la huella la primera vez.
    
- Si vieras “bad permissions”, vuelve al paso 5 (`chmod 600`).
    

---

### 7) Ya dentro como bandit14, leer la password

![Bandit Image](../../Imagenes/level-13-14-6.png)

# {Comando}

## `cat /etc/bandit_pass/bandit14`

# {Salida} _(de tu captura)_

## `4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e`

## {Desglose del comando}

- **Ahora sí** el propietario es bandit14 → lectura permitida.  
## 💬{Comentario del profe}:

Copia esa cadena; te servirá para el siguiente nivel (si te piden password en vez de clave).
    

---

## 🧰 Todas las opciones del comando (mini-manual)

**`ssh` (básico)**

- **Uso:** `ssh [opciones] usuario@host`
    
- **`-i <fichero>`**: usa la clave privada indicada.
    
    - _Ejemplo_: `ssh -i sshkey.private bandit14@localhost`  
        💬 Profe: **sin `-i`**, SSH intentará usar tus claves por defecto y fallará.
        
- **`-p <puerto>`**: puerto alternativo (por defecto 22).
    
    - _Ejemplo_: `ssh -p 2220 bandit14@localhost` (en Bandit el puerto del _juego_ suele ser 2220 para entrar a la máquina al inicio, pero aquí ya estamos dentro).
        
- **`-o StrictHostKeyChecking=no`**: (opcional) omite el prompt de huella.  
    💬 Profe: útil en automatización, pero **no recomendado** en producción.
    

**`chmod`**

- **Uso:** `chmod 600 sshkey.private`  
    💬 Profe: 600 es el ajuste típico para claves privadas.
    

---

## ❌ Errores comunes y soluciones

- **“Permission denied (publickey)”**  
    ✔️ Causa: clave incorrecta o mala ruta en `-i`.  
    ✔️ Solución: asegura `ssh -i ./sshkey.private bandit14@localhost` y `chmod 600`.
    
- **“Bad permissions: sshkey.private”**  
    ✔️ Solución: `chmod 600 sshkey.private`.
    
- **No acepta la huella del host**  
    ✔️ Solución: responde `yes` al aviso la primera vez. Si no puedes escribir `known_hosts`, es **solo un warning** y se puede seguir.
    
- **Intentar `cat /etc/bandit_pass/bandit14` como bandit13**  
    ✔️ Solución: primero **loguearse como bandit14** con la clave.
    

---

## 🧾 Chuleta final

|Comando|Propósito|Uso mínimo|
|---|---|---|
|`ls`|Ver ficheros disponibles|`ls`|
|`cat`|Leer fichero|`cat /etc/bandit_pass/bandit14`|
|`ls -l`|Ver permisos/owner|`ls -l /etc/bandit_pass/bandit14`|
|`chmod 600`|Permisos estrictos a la clave|`chmod 600 sshkey.private`|
|`ssh -i`|Conectar con clave privada|`ssh -i sshkey.private bandit14@localhost`|

---

## 🧩 Script final (automatiza el acceso y la lectura)
![Bandit Image](../../Imagenes/level-13-14-7.png)

> Úsalo dentro de la sesión de **bandit13**.

`#!/usr/bin/env bash set -euo pipefail  KEY="sshkey.private" FILE="/etc/bandit_pass/bandit14" HOST="localhost" USER="bandit14"  # Asegurar permisos correctos a la clave chmod 600 "$KEY" 2>/dev/null || true  # Conexión SSH y lectura remota del archivo ssh -i "$KEY" -o StrictHostKeyChecking=accept-new ${USER}@${HOST} "cat ${FILE}"`

---

## 🗒️ Notas adicionales

- **localhost** apunta a la **misma máquina** en la que ya estás conectado (la caja Bandit).
    
- No necesitas `telnet`, `nc`, `openssl` ni `nmap` para este nivel; con **SSH** es suficiente.
    
- Si ves el warning de `Could not create directory '/home/bandit13/.ssh'`, **ignóralo**: no impide la conexión.
