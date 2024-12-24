Molecules
===============


***Contents:***

- [Technologies](#technologies)
- [Getting Started](#getting-started)
  - [Server Setup](#server-setup)
  - [Configuring Git](#configuring-git)
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



### Configuring Git <a name="configuring-git"></a>
---

1. Install Git
    ```bash
    sudo apt update
    sudo apt install git
    ```

1. Generate an SSH Key

    Create an SSH key for authentication with GitHub:
    ```bash
    ssh-keygen -t ed25519 -C "your_email@example.com"
    ```
    Follow the instructions and save the key in the default path (`~/.ssh/id_ed25519`).

1. Add the SSH Key to GitHub
    - Copy the contents of the public key:
        ```bash
        cat ~/.ssh/id_ed25519.pub
        ```

    - Go to your GitHub account settings:
        - Open **Settings** → **SSH and GPG keys**.
        - Click **New SSH Key**.
        - Paste the key and save it.

1. Test the Connection to GitH

    Ensure the server can connect to GitHub:
    ```bash
    ssh -T git@github.com
    ```
    You should see a message like this:
    ```
    Hi username! You've successfully authenticated, but GitHub does not provide shell access.
    ```

1. Configure Git to Use SSH

    To avoid issues with HTTPS in submodules, run the following command:
    ```bash
    git config --global url."git@github.com:".insteadOf "https://github.com/"
    ```

    This command automatically replaces `https://github.com/` with `git@github.com:` when interacting with GitHub, including when working with submodules.




### Project Setup <a name="project-setup"></a>
---

#### <ins>Database Setup</ins> <a name="database-setup"></a>

Create `db_nftmolecules` database according to the [instructions](https://github.com/Unker/cloud_storage_server/tree/main?tab=readme-ov-file#database-setup)  and [provide access](https://github.com/Unker/cloud_storage_server/tree/main?tab=readme-ov-file#database-access).

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

1. Work with Submodules

    After applying the [Configuring Git](#configuring-git), initialize and update the submodules:
    ```bash
    git submodule update --init --recursive
    ```

1. To verify that everything is working, run:
    ```bash
    git submodule status
    ```
    Ensure that all submodules are correctly initialized.


1. Unpack `jmol` lib:  
    ```bash
    unzip ./molecules--web/jmol.zip -d ./molecules--web/
    unzip ./molecules--web/jmol/jsmol.zip -d ./molecules--web/jmol/
    ```

1. Verify the Setup:  
    Ensure the `jmol` directory is correctly located:
    ```bash
    ls /var/www/molecules/molecules--deploy/molecules--web/
    ```
    You should see a folder named `jmol`.

1. Collect media files:  
    Copy media files from your desktop to the server
    ```bash
    rsync -avz --progress --ignore-errors --bwlimit=10000 --delete-before -e "ssh -T -c aes128-gcm@openssh.com -o Compression=no -x" data_media/ <user_name>@109.71.245.96:/var/www/molecules/molecules--deploy/data_media/
    ```
    replace <user_name> and `109.71.245.96` with your server's address.

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
    DB_HOST=172.17.0.1
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
    URL_SERVER='http://109.71.245.96:8001'
    ```
***Replace `109.71.245.96` with your server's address.***

## Running the Project with Docker Compose <a name="run-with-docker"></a>
```bash
cd /var/www/molecules/molecules--deploy
sudo docker compose up -d --build
```
Check status
```bash
sudo docker ps
```

## Migrations and etc

1. Migrations, create super user and collect static files
    ```bash
    sudo docker exec -it molecules--deploy-molecules_backend-1 bash
    python3 manage.py migrate
    python3 manage.py createsuperuser
    python3 manage.py collectstatic
    python3 create_object_from_json.py
    ```

## Useful scripts <a name="useful-scripts"></a>
###
1. Generate a SECRET_KEY for Django:
   ```bash
   sudo docker exec -it molecules--deploy-molecules_backend-1 bash
   python3 manage.py shell
   from django.core.management import utils
   utils.get_random_secret_key()
   exit()
   ```


## To Do <a name="to-do"></a>

+ Connect a domain name.
+ Set up CI/CD.