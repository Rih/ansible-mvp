# Guía de inicio (Ansible MVP)

## Prerrequisitos
- Docker (con `docker compose`) instalado y funcionando.
- Acceso SSH al repo en Bitbucket (por ejemplo `git@bitbucket.org:...`) y que exista en remoto la app a desplegar.
- El repo debe incluir `express-health-app` y exponer `GET /health` en el puerto `3000` para que pasen los tests de staging.

## Consideraciones previas (keys y secretos)
Antes de levantar contenedores:

## 0) SSH keys y vault
0.a. Copia `keys_template/` a `keys/`.

0.b. Genera/coloca las claves SSH:
- En `keys/a/deploy/` pon `id_rsa` y `id_rsa.pub` (clave para la Máquina A / controller).
- En `keys/b/deploy/` pon `id_rsa` y `id_rsa.pub` (clave para la Máquina B / destino).

Si usas `./bin/keygen.sh`, repite el proceso para obtener 2 pares de claves y cópialos a las rutas anteriores.

0.c. Crea `vault/secrets.yml` copiando `vault/template.yml` y completa:
- `slack_webhook_url`
- `slack_channel`
- `bitbucket_repo` (ej: `git@bitbucket.org:rodrihgod/ansible.git`)

## 1) Levantar contenedores
Desde la raíz del proyecto:
1. Construir (primera vez o cambios en imágenes): `./bin/build.sh`
2. Levantar: `./bin/start.sh`

## 2) Verificar y entrar a máquinas
1. Ver estado: `docker compose ps`
2. Entrar:
   - A: `./bin/entrar-maq.sh a`
   - B: `./bin/entrar-maq.sh b`

## 3) Conectividad A -> B (check rápido)
Dentro de la Máquina A:
- `su - deploy`
- `ping 10.10.1.20`
- `ssh deploy@10.10.1.20` (password: `deploy`)

## 4) Ejecutar Ansible
Los playbooks usan:
- `inventory/hosts.ini` para mapear `production` (maquina-a) y `staging` (maquina-b)
- `vault/secrets.yml` para obtener `bitbucket_repo` y el webhook de Slack

Opciones:
1. Sintaxis: `ansible-playbook playbooks/deploy.yml --syntax-check`
2. Dry run completo (staging + producción): `ansible-playbook playbooks/deploy.yml --check`
3. Ejecución real (staging + producción): `ansible-playbook playbooks/deploy.yml`

Nota: `./bin/run-ansible.sh` ejecuta solo `playbooks/production.yml` (solo producción). Si quieres validar staging también, usa `playbooks/deploy.yml`.

## Workaround (git safe.directory)
`git config --global --add safe.directory /var/www/ansible`