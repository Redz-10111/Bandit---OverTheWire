![Bandit Image](../../Imagenes/level-5-6-1.png)

### Buscar con File pero con caracterÃ­sticas de Bytes 


# Siempre poner al conectarse a una maquina por SSH :

## export TERM=xterm
![Bandit Image](../../Imagenes/bandit-banner.png)



## ðŸ“„ Enunciado del nivel

La contraseÃ±a para el siguiente nivel estÃ¡ almacenada en un archivo **dentro del directorio `inhere`** que cumple estas condiciones:

- âœ… Legible por humanos (texto ASCII).
    
- âœ… TamaÃ±o exacto de **1033 bytes**.
    
- âœ… No es ejecutable.
    

---

## ðŸ”Ž Objetivo del nivel

Aprender a usar `find` con **condiciones mÃºltiples** para localizar archivos por tamaÃ±o, permisos y tipo.

---

## ðŸªœ Paso a paso (con consola real)

### 1. Buscar archivos con las condiciones

![Bandit Image](../../Imagenes/level-5-6-3.png)

# {Comando}

## `find . -type f ! -executable -size 1033c`

# {Salida}

## `./inhere/maybehere07/.file2`

## ðŸ’¬{Comentario del profe}  

El `! -executable` descarta ejecutables. `-size 1033c` busca exactamente 1033 bytes.

---

### 2. Mostrar el contenido del archivo encontrado
![Bandit Image](../../Imagenes/level-5-6-4.png)

# {Comando}

## `find . -type f ! -executable -size 1033c | xargs cat`

# {Salida}

## `DXjZPULLxYr17uwoI01bNLQbtFemEgo7`

## ðŸ’¬{Comentario del profe}  

Â¡AquÃ­ estÃ¡ la contraseÃ±a para el siguiente nivel!

---

## âŒ Errores comunes y soluciones

- âŒ Usar `-size 1033` sin `c` â†’ busca bloques de 512 bytes, no bytes exactos.
    
- âŒ Olvidar el `! -executable` â†’ puede listar binarios ilegibles.
    
- âŒ No usar `xargs cat` â†’ solo muestra la ruta del archivo.
    

---

## ðŸ§¾ Chuleta final

|Comando|PropÃ³sito|Uso mÃ­nimo|
|---|---|---|
|`find . -type f`|Buscar todos los archivos|`find . -type f`|
|`find . -size 1033c`|Buscar archivos de tamaÃ±o exacto (1033 B)|`find . -size 1033c`|
|`find . ! -executable`|Filtrar archivos no ejecutables|`find . ! -executable`|
|`xargs cat`|Leer automÃ¡ticamente el archivo encontrado|`find ...|

---

## ðŸ§© Script final completo
![Bandit Image](../../Imagenes/level-5-6-5.png)

`#!/usr/bin/env bash set -euo pipefail  
`# Buscar archivo legible, no ejecutable y de 1033 bytes find ./inhere -type f ! -executable -size 1033c 
`# Mostrar directamente su contenido find ./inhere -type f ! -executable -size 1033c | xargs cat`

---

## ðŸ—’ï¸ Notas adicionales

âœ”ï¸ **VersiÃ³n manual**: probar `ls -lh` y `file` en cada archivo.  
âœ”ï¸ **VersiÃ³n intermedia**: usar `find` con solo `-size`.  
âœ”ï¸ **VersiÃ³n avanzada**: encadenar filtros con `find` y leer de golpe con `xargs`.


