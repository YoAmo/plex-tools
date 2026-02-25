# Git & GitHub Configuration – plex-tools

Este documento describe de forma detallada la configuración realizada en el equipo de desarrollo para trabajar con Git y GitHub en el proyecto **plex-tools**, alojado físicamente en el NAS Synology y montado vía NFS.

---

# 1. Entorno de Trabajo

- Sistema: Ubuntu Studio  
- Usuario: `nasdev`  
- Proyecto ubicado en NFS: `/home/nasdev/Syno/bin/projects/plex-tools`  
- NAS: Synology DS223j  
- Código versionado con Git  
- Repositorio remoto en GitHub (cuenta: YoAmo)  
- Acceso remoto mediante SSH (no HTTPS)  

---

# 2. Configuración Global de Git

Se configuró la identidad global para todos los repositorios:

```bash
git config --global user.name "Jose Gonzalez"
git config --global user.email "59877274+YoAmo@users.noreply.github.com"
```

Esto garantiza que todos los commits queden correctamente firmados con la identidad asociada a la cuenta de GitHub.

---

# 3. Claves SSH

## 3.1 Clave utilizada para GitHub

Se utiliza la siguiente clave privada:

`~/.ssh/yoamo_ed25519`

Clave pública correspondiente:

`~/.ssh/yoamo_ed25519.pub`

Tipo de clave: `ed25519`

Fingerprint verificado con:

```bash
ssh-keygen -lf ~/.ssh/yoamo_ed25519.pub
```

Esta clave está registrada en GitHub bajo la cuenta YoAmo.

---

## 3.2 Problema Detectado

El sistema intentaba usar automáticamente:

`~/.ssh/id_ed25519`

Pero esa clave no estaba registrada en GitHub, provocando:

```
Permission denied (publickey)
```

---

## 3.3 Solución Implementada (Configuración SSH explícita)

Se creó el archivo:

`~/.ssh/config`

Con el siguiente contenido:

```ssh
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/yoamo_ed25519
  IdentitiesOnly yes
```

Permisos aplicados:

```bash
chmod 600 ~/.ssh/config
```

Resultado:  
Cualquier conexión a `git@github.com` usará siempre la clave correcta (`yoamo_ed25519`), independientemente del agente SSH.

Verificación:

```bash
ssh -T git@github.com
```

Salida esperada:

```
Hi YoAmo! You've successfully authenticated...
```

---

# 4. Inicialización del Repositorio

En el directorio del proyecto:

```bash
cd /home/nasdev/Syno/bin/projects/plex-tools
git init
git branch -m main
```

Se añadió el remoto:

```bash
git remote add origin git@github.com:YoAmo/plex-tools.git
```

---

# 5. Manejo de Historias Divergentes

El repositorio remoto tenía un commit inicial (LICENSE), mientras que el local ya tenía estructura creada.  
Esto generó el error:

```
Updates were rejected because the remote contains work that you do not have locally.
```

Se resolvió mediante:

```bash
git pull origin main --allow-unrelated-histories --no-rebase
```

Esto permitió fusionar ambas raíces independientes mediante un merge clásico.

Posteriormente:

```bash
git push -u origin main
```

---

# 6. Estrategia de Pull por Defecto

Git moderno exige especificar estrategia cuando hay divergencias.

Se decidió que el comportamiento por defecto sea **merge**, no rebase.

Configuración global aplicada:

```bash
git config --global pull.rebase false
```

Implicaciones:

- `git pull` realizará un merge clásico  
- No reescribirá historia automáticamente  
- Es más seguro en entornos NFS y proyectos colaborativos  

---

# 7. Consideraciones por Trabajar sobre NFS

El repositorio está alojado físicamente en el NAS (montado por NFS).

Implicaciones:

- No trabajar como root  
- Mantener permisos consistentes  
- Hacer commits frecuentes  
- Evitar operaciones masivas de rebase  
- Si el proyecto crece mucho, considerar clonar localmente y desplegar al NAS  

En caso de advertencia por seguridad:

```bash
git config --global --add safe.directory /home/nasdev/Syno/bin/projects/plex-tools
```

---

# 8. Flujo de Trabajo Recomendado

Estrategia definida:

```
feature branch → desarrollo → commit frecuente → merge a main → push inmediato
```

Ejemplo:

```bash
git checkout -b feature/nueva-funcionalidad
```

---

# 9. Estado Final del Proyecto

- SSH autenticando correctamente  
- Clave explícita configurada  
- Remoto configurado vía SSH  
- Merge strategy establecida  
- Historial integrado correctamente  
- Rama `main` sincronizada con `origin/main`  
- Proyecto listo para desarrollo profesional  

---

# 10. Principios Adoptados

- Uso exclusivo de SSH (no HTTPS)  
- Configuración explícita de claves  
- Evitar ambigüedad en autenticación  
- Merge como estrategia por defecto  
- Flujo profesional desde el primer commit  
- Documentación desde el inicio  

---

Este documento define la base técnica de control de versiones del proyecto plex-tools y debe mantenerse actualizado si se realizan cambios en la configuración de Git o SSH.