Molecules
===============


***Contents:***

- [Technologies](#technologies)
- [Getting Started](#getting-started)
  - [Server Setup](#server-setup)
  - [Project Setup](#project-setup)
    - [Database Setup](#database-setup)
    - [Cloning the Project](#cloning-the-project)
    - [Setting Backend Environment Variables](#env-backend)
    - [Setting Frontend Environment Variables](#env-frontend)
  - [Running the Project with Docker Compose](#run-with-docker)
- [To Do](#to-do)

# Technologies <a name="technologies"></a>

### Backend
<img src="./assets/img/Django_logo.png" alt="Django" title="Django" height="50" style="margin: 10px; background: #FFFFFF">
<img src="https://www.postgresql.org/media/img/about/press/elephant.png" alt="PostgreSQL" title="PostgreSQL" height="50" style="margin: 10px;">
<img src="https://nginx.org/nginx.png" alt="nginx" title="nginx" height="50" style="margin: 10px;">
<img src="https://gunicorn.org/images/logo.jpg" alt="gunicorn" title="gunicorn" height="70" style="margin:0 10px;">



### Frontend
<img src="./assets/img/TypeScript_logo.png" alt="TypeScript" title="TypeScript" height="50" style="margin: 10px;">
<img src="https://vitejs.dev/logo-with-shadow.png" alt="Vite" title="Vite" height="50" style="margin: 10px;">
<img src="./assets/img/React_logo_light.svg" alt="React" title="React" height="50" style="margin: 10px;">
<img src="./assets/img/Tailwind_CSS_logo.png" alt="Tailwind" title="Tailwind" height="25" style="margin: 10px; background: #FFFFFF; padding: 10px;">

<img src="./assets/img/Redux_Logo.png" alt="Redux Toolkit" title="Redux Toolkit" height="25" style="margin: 10px; background: #FFFFFF; padding: 10px;">
<img src="./assets/img/react-router-color.svg" alt="React Router" title="React Router" height="25" style="margin: 10px; background: #FFFFFF; padding: 10px;">

# Getting Started <a name="getting-started"></a>

### Server Setup <a name="server-setup"></a>
1. Connect to the server as the root user:
    ```bash
    ssh <ip>
    ```
    where `<ip>` is the server's IP address, for example:
    ```bash
    ssh 109.71.245.96
    ```

1. Install PostgreSQL:
    ```bash
    apt install postgresql
    ```

1. Install Docker:

    https://docs.docker.com/engine/install/ubuntu/

1. Create a user and grant them admin privileges:
    ```bash
    adduser <user>
    usermod <user> -aG sudo
    ```
    where `<user>` is the username.

1. Switch to the newly created user:
    ```bash
    su - <user>
    ```

1. Create the project directory and reassign ownership:
    ```bash
    sudo mkdir -p /var/www/molecules
    sudo chown <user>:<user> -R /var/www/molecules/
    ```

### Project Setup <a name="project-setup"></a>

#### <ins>Database Setup</ins> <a name="database-setup"></a>

Set up the database according to the [instructions](https://github.com/Unker/cloud_storage_server/tree/main?tab=readme-ov-file#database-setup) and [provide access](https://github.com/Unker/cloud_storage_server/tree/main?tab=readme-ov-file#database-access).

#### <ins>Cloning the Project</ins> <a name="cloning-the-project"></a>

1. Switch to the previously created user, if not already done:
    ```bash
    su - <user>
    ```

1. Navigate to the project directory and clone it from the repository:
    ```bash
    cd /var/www/molecules
    git clone https://github.com/Unker/molecules--deploy.git
    cd molecules--deploy
    ```

1. Download the submodules:
    ```bash
    git submodule update --init --recursive
    ```


1. Download `jmol lib` from https://sourceforge.net/projects/jmol/  
    ```
    unpack to /var/www/molecules/molecules--deploy/molecules--web/
    Then rename it to `jmol`
    ```

1. Copy molecules descriptors
    ```
    to /var/www/molecules/molecules--deploy/molecules--server/molecules/static/molecules/
    ```

#### <ins>Setting Backend Environment Variables</ins> <a name="env-backend"></a>

1. Create a `.env` file and open it for editing:
   ```bash
   touch ./molecules--server/.env
   nano ./molecules--server/.env
   ```

1. Populate the `.env` file with the following content:
    ```
    SECRET_KEY_DJANGO='django-insecure-xxx'
    CORS_ALLOWED_DOMEN='109.71.245.96,localhost,127.0.0.1,molecules_backend,molecules_backend:8000,molecules_frontend,molecules_frontend:3000'
    HTTP_WEB3_PROVIDER='http://192.168.0.112:8545'
    DB_NAME=db_nftmolecules
    DB_HOST=172.18.0.1
    DB_PORT=5432
    DB_USER=<superuser_name>
    DB_PASSWORD=<superuser_password>
    DATABASE=postgres
   ```
    - Replace `xxx` in `SECRET_KEY_DJANGO` with your key.
    - For `CORS_ALLOWED_HOSTS` and `ALLOWED_HOSTS`, replace `109.71.245.96` with your server's address.
    - For `HTTP_WEB3_PROVIDER`, replace 
    - For `DB_HOST`, use the current server address.
    - For `DB_USER`, use your superuser name.
    - For `DB_PASSWORD`, use your superuser password.

#### <ins>Setting Frontend Environment Variables</ins> <a name="env-frontend"></a>
1. Create a `.env` file and open it for editing:
    ```bash
    touch ./molecules--web/.env
    nano ./molecules--web/.env
    ```

1. Populate the `.env` file according to `.env.example` and set:
    ```
    URL_SERVER='http://109.71.245.96/api'
    ```
***Replace `109.71.245.96` with your server's address.***

## Running the Project with Docker Compose <a name="run-with-docker"></a>
```bash
cd /var/www/molecules/molecules--deploy
docker compose up -d --build
```

## Migrations and etc

1. Migrations, create super user and collect static files
    ```bash
    sudo docker compose exec -it molecules--deploy-molecules_backend bash
    python3 manage.py migrate
    python3 manage.py createsuperuser
    python3 manage.py collectstatic
    ```

## Useful scripts <a name="useful-scripts"></a>
###
1. Generate a SECRET_KEY for Django:
   ```bash
   sudo docker compose exec -it molecules--deploy-molecules_backend bash
   python3 manage.py shell
   from django.core.management import utils
   utils.get_random_secret_key()
   exit()
   ```


## To Do <a name="to-do"></a>

+ Connect a domain name.
+ Set up CI/CD.