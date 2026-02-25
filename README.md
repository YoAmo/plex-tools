# plex-tools

Herramientas profesionales en Bash para automatización de flujos relacionados con Plex Media Server.

## Objetivo

Automatizar tareas como:

- Renombrado compatible con Plex
- Validación de estructura de bibliotecas
- Integración con API de Plex
- Normalización de medios

## Estructura del proyecto

bin/        → Scripts ejecutables  
lib/        → Funciones reutilizables  
config/     → Configuración del entorno  
tests/      → Pruebas automatizadas  
logs/       → Logs de ejecución  
docs/       → Documentación técnica  

## Requisitos

- Bash 5+
- curl
- jq
- Acceso a Plex API

## Entorno

Desarrollado en Ubuntu Studio  
Ejecutado sobre NAS Synology vía NFS  

## Autor

Jose Gonzalez
