# Segurización Infraestructura Extagram
A continuación, detallamos qué hemos elegido para cada tarea del Sprint 4 (firewall S1, hardening web/SO/BBDD con WAF) y por qué esta opción y no otras, basado en compatibilidad Docker/Alpine, AWS, rendimiento, simplicidad y cobertura OWASP.

## Firewall (davant S1):
**Eleccion:** AWS Security Groups + nftables (SG externa, nftables host).
| Opción | ¿Por qué elegida? | ¿Por qué NO otras? |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| AWS SG | Stateful, nativo AWS VPC, bloquea antes host (Layer 4). Fácil: solo 80/443 inbound. Zero overhead EC2. docs.aws.amazon​ | **NACLs:** Stateless (necesita rules bidireccionales), subnet-level no instance. Host firewalls solos: No bloquean antes llegada (menos eficiente). |
| nftables | Moderno (sustituye iptables), Docker nativo (--firewall-backend=nftables), granular para bridge intern. Bajo overhead Alpine. docs.docker​ | **UFW:** Conflicto Docker (ignora rules bridge). iptables: Obsoleto, sintaxis compleja, peor perf. firewalld: Overhead extra no needed. baeldung+1 |

## WAF (Hardening Web)
**Elección:** ModSecurity v3 + OWASP CRS en S1 nginx:alpine.
| Opción | ¿Por qué elegida? | ¿Por qué NO otras? |
| ------ | ----------------- | ------------------ |
| ModSecurity+CRS | OWASP Top 10 coverage, Nginx módulo oficial, Alpine Docker ready. Logs stdout → S5. Gratuito/actualizado. hub.docker+1 | NAXSI: Menos rules/CRS. AWS WAF: Costo $0.60/1M requests, managed no S1 local. Fail2ban: Solo IPs, no app-layer (XSS/SQLi). frank-korving​ |

## Hardening SO (Alpine Docker)
| Aspecto | Elegido           | ¿Por qué NO otras?                 |
| ------- | ----------------- | ---------------------------------- |
| Acceso  | SSH keys          | Passwords: Vulnerable brute-force. |
| Usuario | appuser (no root) | Root: Escalada privilegios fácil.  |

## Hardening PHP (S2-S4)
| Config            | Valor                    | Justificación OWASP |
| ----------------- | ------------------------ | ------------------- |
| disable_functions | exec,passthru,shell_exec | A03 Injection/RCE.  |
| PDO               | prepared statements      | A07 Injection.      |
| Sessions          | httponly=1, secure=1     | A05 XSS, A08 CSRF.  |

## Hardening BBDD (S7 MySQL)
| Config       | Valor             | Justificación             |
| ------------ | ----------------- | ------------------------- |
| bind-address | 127.0.0.1         | No remoto (solo Docker).  |
| Usuario      | 'app'@'localhost' | No root, least privilege. |

## Pruebas & Validación
- **nmap:** Solo SG abiertos.
- **Nikto/ZAP:** WAF bloquea >70%.
- **Manual:** Funciona web post-hardening.

## Conclusión: 
Opciones óptimas costo/eficacia AWS/Docker. Defensa multicapa.
