# 🛡️ DockerLabs Write-ups — Juan Amorós García

> **EN:** Full-compromise (root) reports for vulnerable boot2root machines from [DockerLabs](https://dockerlabs.es/), written like pentest findings: severity, root cause, proof of concept, impact and recommendation. Written in Spanish.

Informes de compromiso total (root) sobre máquinas vulnerables tipo boot2root de [DockerLabs](https://dockerlabs.es/). Cada informe sigue la metodología **reconocimiento → enumeración → explotación → escalada de privilegios** y documenta cada hallazgo como en un pentest real: severidad, causa raíz, PoC, impacto y recomendación, con capturas como evidencia.

**Estado:** 11 máquinas comprometidas hasta root · 1 en curso (Agua de Mayo).

## Máquinas

| Máquina | Vector inicial | Escalada |
|---|---|---|
| [Allien](Allien/Allien.md) | SMB (token JWT + backup con credenciales) | Reverse shell web + `sudo service` |
| [Amor](Amor/Amor.md) | Fuerza bruta SSH | Esteganografía + `sudo ruby` |
| [Consolelog](Consolelog/Consolelog.md) | Backup expuesto + fuerza bruta dirigida | `sudo nano` (GTFOBins) |
| [Hedgehog](Hedgehog/Hedgehog.md) | Fuerza bruta SSH (diccionario invertido) | `sudo -u sonic` → `sudo su` |
| [Library](Library/Library.md) | Credencial filtrada en `index.php` | Secuestro de script Python (`sudo`) |
| [Obsession](Obsession/Obsession.md) | FTP anónimo + enumeración web | `sudo vim` (GTFOBins) |
| [Stranger](Stranger/Stranger.md) | Fuerza bruta FTP + RSA (CyberChef) | Reutilización de contraseña + `sudo su` |
| [Trust](Trust/Trust.md) | Fuga de información web + fuerza bruta | `sudo vim` (GTFOBins) |
| [Upload](Upload/Upload.md) | Subida de archivos sin restricción (RCE) | `sudo env` (GTFOBins) |
| [Vacaciones](Vacaciones/Vacaciones.md) | Fuga en comentarios HTML + fuerza bruta | `sudo ruby` |
| [WalkingCMS](WalkingCMS/WalkingCMS.md) | WordPress (fuerza bruta + RCE vía plugin) | SUID en `env` |
| [Agua de Mayo](Agua%20de%20Mayo/Agua%20de%20Mayo.md) | Fuga en código fuente (Brainfuck) | 🚧 en curso |

## Técnicas

- **Acceso inicial:** fuerza bruta (SSH, FTP, WordPress), fuga de información en código fuente y comentarios HTML, backups expuestos, SMB, subida de ficheros sin restricción, RCE vía plugin de WordPress.
- **Criptografía y esteganografía:** descifrado RSA (CyberChef), esteganografía (steghide), ofuscación Brainfuck.
- **Escalada de privilegios:** `sudo` mal configurado, binarios SUID, GTFOBins, secuestro de scripts, reutilización de credenciales.

## Herramientas

`nmap` · `hydra` · `gobuster` · `whatweb` · `wpscan` · `smbmap` · `netexec` · `steghide` · `CyberChef` · `netcat` · GTFOBins

---

*Laboratorios realizados en entornos Docker aislados y controlados, con fines educativos.*
