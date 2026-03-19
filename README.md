# Pasos

# 0.a Copia o renombra keys_template en keys
# 0.b Define las keys ssh de las maquinas A y B usando el keygen.sh
# 0.c Copia o renombra el yml en vault/template.yml en vault/secrets.yml y rellena las variables

# 1. Levantar los contenedores
cd docker
docker compose up -d --build

# 2. Verificar que ambos están corriendo
docker compose ps

# 3. Entrar a Máquina A (el controller)
docker exec -it maquina-a bash

# 4. Desde dentro de Máquina A, probar conectividad a B
su - deploy
ping 10.10.1.20
ssh deploy@10.10.1.20   # password: deploy

# 5. a En maquina B, clonar un repositorio a desplegar
ej: 
- cd /var/www
- git@bitbucket.org:rodrihgod/ansible.git

# 5.b Levantar la mini app de express con 
- cd /var/www/express-health-app
- npm install 
- npm run dev

# 6. Probar sintaxis del playbook
cd /opt/ansible-deploy
ansible-playbook playbooks/deploy.yml --syntax-check

# 7. Dry run completo
ansible-playbook playbooks/deploy.yml --check

# 8. Ejecución real
ansible-playbook playbooks/deploy.yml

# Comandos
1. Entrar maquina ./bin/entrar-maq.sh <maquina>
Ej: ./bin/entrar-maq.sh a
    ./bin/entrar-maq.sh b

2. Iniciar contenedores ./bin/start.sh
3. Construir contenedores ./bin/build.sh
4. Iniciar Claude ./bin/run-ollama.sh
5. Ejecutar ansible scripts ./run-ansible.sh

# Workarounds

git config --global --add safe.directory /var/www/ansible