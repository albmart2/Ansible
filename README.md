# Ansible

Ansible es una herramienta de automatización. Básicamente le dice a los administradores: *"¿Y si dejamos de entrar por SSH a 50 máquinas para repetir el mismo comando como si fuéramos un loro con estrés laboral?"*.

## Índice

1. [¿Qué es Ansible?](#1-qué-es-ansible)
2. [Conceptos básicos](#2-conceptos-básicos)
3. [Instalar Ansible](#3-instalar-ansible)
4. [Probar conectividad](#4-probar-conectividad)
5. [Comandos ad-hoc](#5-comandos-ad-hoc)
6. [Módulos](#6-módulos)
7. [Playbooks](#7-playbooks)
8. [Estructura YAML](#8-estructura-yaml)
9. [Variables](#9-variables)
10. [Condicionales](#10-condicionales)
11. [Bucles](#11-bucles)
12. [Handlers](#12-handlers)
13. [Facts](#13-facts)
14. [Ejemplo completo de examen](#14-ejemplo-completo-de-examen)
15. [Los 10 comandos que más suelen preguntar](#15-los-10-comandos-que-más-suelen-preguntar)
16. [Chuleta express](#16-chuleta-express)

## 1. ¿Qué es Ansible?

Permite:

- Instalar software.
- Configurar servidores.
- Desplegar aplicaciones.
- Automatizar tareas repetitivas.

La gran ventaja es que **no necesita agentes** en los servidores remotos. Solo SSH.

## 2. Conceptos básicos

### Nodo de control

Tu máquina donde ejecutas Ansible.

### Nodos gestionados

Los servidores que vas a administrar.

### Inventario

Lista de máquinas que contro Ansible. Ejemplo ```inventory.ini```:

```ini
[web]
192.168.1.10
192.168.1.11

[db]
192.168.1.20
```

## 3. Instalar Ansible

Ubuntu:

```bash
sudo apt update
sudo apt install ansible -y
```

Comprobar:

```bash
ansible --version
```

## 4. Probar conectividad

Ping de Ansible:

```bash
ansible all -i inventory.ini -m ping
```

Resultado:

```json
{
    "ping": "pong"
}
```

Si responde ```pong```, ya puedes empezar a mandar órdenes como un pequeño dictador de infraestructura.

## 5. Comandos ad-hoc

Son comandos rápidos.

### Ver espacio en disco

```bash
ansible all -i inventory.ini -a "df -h"
```

### Ver usuario actual

```bash
ansible all -i inventory.ini -a "whoami"
```

### Reiniciar servicio

```bash
ansible web -i inventory.ini -a "systemctl restart nginx"
```

## 6. Módulos

Ansible trabaja mediante módulos.

### Ping

```bash
ansible all -m ping
```

### Copy

Copiar archivo:

```bash
ansible all -m copy \
-a "src=archivo.txt dest=/tmp/archivo.txt"
```

### Service

```bash
ansible web -m service \
-a "name=nginx state=restarted"
```

