# Curso de Ansible | Sencillo

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

## 7. Playbooks

Aquí está la magia. Los playbooks usan YAML.

Ejemplo:

```yaml
---
- name: Instalar nginx
  hosts: web
  become: yes

  tasks:
    - name: Instalar nginx
      apt:
        name: nginx
        state: present
```

Guardar como:

```bash
nginx.yml
```

Ejecutar:

```bash
ansible-playbook -i inventory.ini nginx.yml
```

## 8. Estructura YAML

```yaml
---
- hosts: web

  tasks:

    - name: Instalar paquete
      apt:
        name: git
        state: present
```

Explicación:

- ```hosts```: dónde se ejecuta.
- ```tasks```: lista de tareas.
- ```name```: descripción.
- ```apt```: módulo utilizado.

## 9. Variables

Definir variables:

```yaml
---
- hosts: web

  vars:
    paquete: nginx

  tasks:
    - name: Instalar paquete
      apt:
        name: "{{ paquete }}"
        state: present
```

La sintaxis:

```yaml
{{ variable }}
```

## 10. Condicionales

```yaml
- name: Instalar nginx en Ubuntu
  apt:
    name: nginx
    state: present
  when: ansible_os_family == "Debian"
```

## 11. Bucles

Instalar varios paquetes:

```yaml
- name: Instalar paquetes
  apt:
    name: "{{ item }}"
    state: present
  loop:
    - git
    - curl
    - nginx
```

## 12. Handlers

Se ejecutan solo cuando algo cambia.

```yaml
tasks:

  - name: Copiar configuración
    copy:
      src: nginx.conf
      dest: /etc/nginx/nginx.conf
    notify:
      - Reiniciar nginx
```

handlers:

```yaml
  - name: Reiniciar nginx
    service:
      name: nginx
      state: restarted
```

## 13. Facts

Información del servidor.

Ver todos:

```bash
ansible all -m setup
```

Ejemplos:

```yaml
{{ ansible_hostname }}
```

```yaml
{{ ansible_distribution }}
```

```yaml
{{ ansible_processor_vcpus }}
```

## 14. Ejemplo completo de examen

```yaml
- name: Configurar servidor web
  hosts: web
  become: yes

  tasks:

    - name: Instalar nginx
      apt:
        name: nginx
        state: present

    - name: Arrancar nginx
      service:
        name: nginx
        state: started
        enabled: yes

    - name: Crear directorio web
      file:
        path: /var/www/app
        state: directory

    - name: Copiar index
      copy:
        src: index.html
        dest: /var/www/app/index.html
```

## 15. Los 10 comandos que más suelen preguntar

```bash
ansible --version
```

```bash
ansible all -m ping
```

```bash
ansible all -m setup
```

```bash
ansible-playbook playbook.yml
```

```bash
ansible-playbook -i inventory.ini playbook.yml
```

```bash
ansible all -a "df -h"
```

```bash
ansible all -m copy -a "src=f.txt dest=/tmp/f.txt"
```

```bash
ansible all -m service -a "name=nginx state=restarted"
```

```bash
ansible-galaxy init rol_web
```

```bash
ansible-vault create secretos.yml
```

## 16. Chuleta express

| Concepto  | Para qué sirve                |
| --------- | ----------------------------- |
| Inventory | Lista de servidores           |
| Module    | Acción concreta               |
| Playbook  | Automatización en YAML        |
| Task      | Tarea individual              |
| Handler   | Se ejecuta cuando hay cambios |
| Variable  | Guarda valores                |
| Fact      | Información del host          |
| Role      | Organización de playbooks     |
| Vault     | Guardar secretos              |

Si estás estudiando administración de sistemas, DevOps o cloud, céntrate especialmente en **inventarios, módulos, playbooks, variables, loops y handlers**. Entre todos suelen representar el 80% de los ejercicios. El otro 20% consiste en pelearte con la indentación YAML porque dos espacios mal puestos pueden destruir más sueños que una reunión de lunes a las 8 de la mañana.
