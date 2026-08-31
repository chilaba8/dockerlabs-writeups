# 🛡️ Dockerlabs Writeups — Juan Amorós García

Informes de compromiso total (root) sobre 12 máquinas vulnerables tipo boot2root de [DockerLabs](https://dockerlabs.es/), documentados siguiendo una metodología de reconocimiento → enumeración → explotación → escalada de privilegios, con hallazgos individuales (severidad, causa raíz, PoC, impacto y recomendación).

## Técnicas demostradas

Fuerza bruta (SSH/FTP), fuga de información, esteganografía, descifrado RSA (CyberChef), reutilización de credenciales, subida de ficheros sin restricción, RCE vía WordPress, y escalada de privilegios (`sudo` mal configurado, binarios SUID, GTFOBins, secuestro de scripts).

## Máquinas

| Máquina | Vector inicial | Escalada |
|---|---|---|
| [Allien](Allien/Allien.md) | SMB (token JWT + backup con credenciales) | Reverse shell web + `sudo service` |
| [Amor](Amor/Amor.md) | Fuerza bruta SSH | Esteganografía + `sudo ruby` |
| [Agua de Mayo](Agua%20de%20Mayo/Agua%20de%20Mayo.md) | Fuga en código fuente (Brainfuck) | ⚠️ nota incompleta |
| [Consolelog](Consolelog/Consolelog.md) | Backup expuesto + fuerza bruta dirigida | `sudo nano` (GTFOBins) |
| [Hedgehog](Hedgehog/Hedgehog.md) | Fuerza bruta SSH (diccionario invertido) | `sudo -u sonic` → `sudo su` |
| [Library](Library/Library.md) | Credencial filtrada en `index.php` | Secuestro de script Python (`sudo`) |
| [Obsession](Obsession/Obsession.md) | FTP anónimo + enumeración web | `sudo vim` (GTFOBins) |
| [Stranger](Stranger/Stranger.md) | Fuerza bruta FTP + RSA (CyberChef) | Reutilización de contraseña + `sudo su` |
| [Trust](Trust/Trust.md) | Fuga de información web + fuerza bruta | `sudo vim` (GTFOBins) |
| [Upload](Upload/Upload.md) | Subida de archivos sin restricción (RCE) | `sudo env` (GTFOBins) |
| [Vacaciones](Vacaciones/Vacaciones.md) | Fuga en comentarios HTML + fuerza bruta | `sudo ruby` |
| [WalkingCMS](WalkingCMS/WalkingCMS.md) | WordPress (fuerza bruta + RCE vía plugin) | SUID en `env` |

---

*Laboratorios realizados en entornos Docker aislados y controlados, con fines educativos.*
