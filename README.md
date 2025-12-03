# Tratamiento de una Shell Reversa

## 📌 ¿Qué es?
Cuando obtienes acceso a una máquina mediante una **shell reversa**, normalmente la terminal es muy limitada:  
- No hay historial con flechas arriba/abajo.  
- No funciona el autocompletado con tabulador.  
- El prompt es mínimo (no sabes usuario ni directorio).  
- `Ctrl+C` cierra la shell en lugar de detener procesos.  
- Comandos como `clear` no funcionan.  

Esto ocurre porque la shell no está asociada a un **TTY interactivo**. El “tratamiento” consiste en convertir esa shell en una pseudo‑terminal más cómoda.

---

## ⚙️ Pasos completos

### 1. En tu máquina atacante (Kali)
1. Si tu terminal es **zsh** (por defecto en Kali), cámbiala a bash:  
   ```bash
   exec bash --login
   ```
   Esto evita problemas posteriores con `stty` y `fg`.

2. Abre el listener para recibir la conexión:  
   ```bash
   nc -lvnp 4444
   ```

---

### 2. Cuando recibes la shell remota
La víctima te dará una shell básica (`/bin/sh`). Aquí aplicas el tratamiento.

#### Método 1: Si hay Python en la víctima
```bash
python -c 'import pty; pty.spawn("/bin/bash")'
# o
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

#### Método 2: Sin Python (alternativa más común)
```bash
script -qc /bin/bash /dev/null
```

#### Método 3: Plan B
```bash
script /dev/null -c bash
```

---

### 3. Ajustes adicionales
1. Suspende la shell con `Ctrl+Z` para volver a tu terminal local.  
2. Configura tu terminal:  
   ```bash
   stty raw -echo
   fg
   ```
   (pulsa Enter).  
3. Exporta el tipo de terminal:  
   ```bash
   export TERM=xterm-256color
   ```
   (o el valor que te dé `echo $TERM`).

---

## ✅ Resultado
Después del tratamiento tendrás:  
- Historial con flechas arriba/abajo.  
- Autocompletado con tabulador.  
- `Ctrl+C` funcionando como interrupción de procesos.  
- Comandos como `clear` funcionando correctamente.  
- Prompt más informativo (usuario y directorio).

---

## 📝 Resumen rápido
1. `exec bash --login` (local, si usas zsh).  
2. `nc -lvnp 4444` (local, listener).  
3. En la víctima:  
   - `python -c 'import pty; pty.spawn("/bin/bash")'` **o** `script -qc /bin/bash /dev/null`.  
   - `Ctrl+Z` → `stty raw -echo` → `fg` → Enter.  
   - `export TERM=xterm-256color`.  

---
