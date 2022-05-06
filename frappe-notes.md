# frappe library tutorial
deshabilitar gui debian
```bash
sudo systemctl set-default multi-user.target
sudo reboot
```
volver habilitar
```bash
sudo systemctl set-default graphical.target
```

# Preparacion/instalacion
```bash
apt install git python-dev python-pip redis-server
apt-get install software-properties-common
apt-get install supervisor
pip3 install bench
```
mariadb
```bash
### versiones anteriores a ubuntu 20.04
apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0xF1656F24C74CD1D8
add-apt-repository 'deb [arch=amd64,i386,ppc64el] http://ftp.ubuntu-tw.org/mirror/mariadb/repo/10.3/ubuntu xenial main'
```
```bash
apt-get update
apt-get install mariadb-server-10.3
mysql_secure_installation
apt-get install mariadb-client-10.3
mysql_secure_installation
```
```bash
editar el archivo
nano /etc/mysql/my.cnf
```
añadir lo siguiente
```bash
[mysqld]
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci

[mysql]
default-character-set = utf8mb4
```
reiniciar el servicio
```bash
systemctl restart mysql
```
### instalacion de nodejs
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
```
```bash
exportar
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```
```bash
nvm install 14
npm install -g yarn
# or 
sudo apt install yarn -y
sudo apt-get install xvfb libfontconfig wkhtmltopdf
```
# iniciando el proyecto
crear el directorio
```bash
bench init frappe-bench
```
# crear un usuario para frappe en caso de querer trabajar con otro usuario
```bash
sudo su -
nano /etc/sudoers
admin_dev ALL=(ALL:ALL) NOPASSWD: ALL
```
```bash
sudo apt install nvm
nvm install 14
nvm use 14
```
iniciar el sitio 
```bash
bench start
```
en este punto no hay nada, solo se ejecuta el server

crear una app
```bash
bench new-app library_management
```

crear un site
```bash
bench new-site library.test --db-name 'nombre_de_la_db'
```

al instalar bench se baja la ultima version 14, para el proposito de pruebas y que este similar a la app
en produccion hay que volver a una version anterior v13.
```bash
git remote set-branches upstream '*'
git fetch --all --unshallow
```
se trae todas las versiones

obtener la v13
```bash
# es mejor esto
bench switch-to-branch 'nombre de la rama'
git checkout -f version-13
bench setup requirements
bench update
```
```bash
bench update --no-backup --reset
```
--reset cambia todos los cambios realizados por del dev

con bench start carga el sitio y realiza un rollup

# añadir el sitio creado para poder correr la app
crear un currentsite.txt en sites/
dentro del archivo añadir el nombre del proyecto
```bash
nano sites/currentsite.txt
```

modificar json para evitar mostrar la pantalla de mantenimiento en el navegador
```bash
nano sites/library.test/site_config.json
{
 "db_name": "_ad03fa1a016ca1c4",
 "db_password": "6w83Hgp1q2JCSop8",
 "db_type": "mariadb",
 "maintenance_mode": 0, # nueva linea
 "pause_sheduler": 0 # nueva linea
}
# or

bench set-config -g developer_mode true
```

# login
user: Adminstrator
pass: (la que solicita al crear el site)

# añadir varios sitios y acceder a ellos con el mismo puerto.
eliminar el currentsite.txt

```bash
rm -f sites/currentsite.txt
```
añadir el sitio al host
```bash
bench --sites library.test add-to-hosts
```

# instalar app del sitio
```bash
bench --site library.test install-app library_management
```

confirmar la instalacion de la app
```bash
bench --site library.test list-apps

```

pasar frape a produccion
```bash
sudo bench setup production 'user'
```

si marca error con alguina libreria hacer:
```bash
bench build
```
al iniciar sesion se podra ver el asistente de configuracion

# hacer un push de la app a git
si es el master solo hacer proceso normal.

```bash
cd apps
git checkout -b version-13 # opcional si se trabaja con una rama diferente para hacer un pull request y despues un merge
git init
git add .
git commit -m 'feat(app): push app'
git push origin version-13
```
cargado el modulo a git se puede extraer para cargar en el directorio actual de bench
```bash
# por defecto baja el master
bench get-app library_management git@github.com:taquero-programador/erp-frappe.git
# par obtener una rama diferente
bench get-app library_management git@github.com:taquero-programador/erp-frappe.git branch 'nombre de la rama'
bench update
# si marca error
bench update --no-backup --reset
bench new-site library.test --db-name 'nombre_de_la_db'
bench --site library.test install-app library_management
```

# modo cosola de frappe (pendiente)

# Doctypes

define propiedades y el comportamiento del modelo.

crear un doctype Articulo.
```bash
bench --site library.test mariadb
desc tabArticulo; # es la table del doctype creado
```
los datos pasados como Title automaticamente se pasan con minuscula y separado por `_`

