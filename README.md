# Erbarbar's WEb App Template

## Start coding with minimal setup

<img src=https://icon-library.com/images/docker-icon/docker-icon-15.jpg  width="16%" /><img src="https://i0.wp.com/www.primefaces.org/wp-content/uploads/2017/09/feature-react.png?fit=260%2C260&ssl=1"  width="16%" /><img src="https://icon-library.com/images/django-icon/django-icon-0.jpg"  width="16%"/><img src="https://icon-library.com/images/postgres-icon/postgres-icon-7.jpg"  width="16%"/><img src="https://www.juliosblog.com/content/images/size/w2000/2016/06/nginx-smalllogo.png"  width="16%"/><img src="https://it.izero.fr/wp-content/uploads/2019/01/img_5c3237287a97a.png"  width="16%"/>

---

    git clone git@github.com:Erbarbar/template.git

# Development environment

#### React and Django file changes are detected by the containers and changes take effect after reloading the browser.

Access app by going to your [localhost](http://localhost).

---

- Start containers

```
    docker-compose -f development.yml up -d
```

- Run command on container

```
    docker-compose -f development.yml run --rm django python manage.py createsuperuser
```

# Production environment

- Create the production environment variables file.

```
nano /env/production.env
```

- Copy these, and add the missing values at the end.

```
DEBUG=0
SQL_HOST=database
SQL_PORT=5432
DATABASE=postgres
SQL_ENGINE=django.db.backends.postgresql

SECRET_KEY=
DJANGO_ALLOWED_HOSTS=
SQL_DATABASE=
SQL_USER=
SQL_PASSWORD=
```

- Change the domain name on these files:

  - `letsencrypt.sh`

  ```
  ...
  domains=(change.me www.change.me)            # HERE
  rsa_key_size=4096
  data_path="./nginx/production/certbot"
  ...
  ```

  - `nginx/production/production/nginx.conf`

  ```
  ...
  server {

    listen 80;
    server_name change.me;    HERE

    ...

  }
  server {

    listen 443 default_server ssl http2;
    ssl_certificate /etc/letsencrypt/live/change.me/fullchain.pem;          HERE
    ssl_certificate_key /etc/letsencrypt/live/change.me/privkey.pem;        HERE
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

    server_name change.me;                                              HERE
    ...
  ```

- Add the certificate manually for the 1st time:

```
chmod +x letsencrypt.sh
```

```
./letsencrypt.sh
```

---

Optional: Automatically deploy changes on server when modifications are pushed to github.

- Create a ssh key pair on your server:

```
ssh-keygen
```

- Add the public ssh key to your guthub [PROFILE](https://github.com/settings/keys), so that the server can pull from github using ssh:

```
cat ~/.ssh/id_rsa.pub
```

Should be something like:

```
ssh-rsa Hf9283hF928hf98fH[...]n9f3qf9Q38Gf9q38hf9q3Hf9q3= user@host
```

- Go to your REPOSITORY -> Settings -> Secrets -> New Repository Secret, and add these 3 secrets:

  - Name: SSH_HOST

    Value: the ip address of your host

  - Name: SSH_USERNAME

    Value: the user you use to ssh into the host

  - Name: SSH_KEY

    For the value, copy the servers private key by revealing it with this command:

    ```
    cat ~/.ssh/id_rsa
    ```

    Should be something like this:

    ```
    -----BEGIN OPENSSH PRIVATE KEY-----
    MDfeIhNhMBhJr12LovLb9vZsMfiPxBVaxnx9yqrn/LtU
    g2ErKhKeGyOSnHmQQ3D8f[...]P64nQYVHH+B9OOMdCi
    d1WzN6tOnN9/rl7q14r59FUDwAQ4VAAAFgEgMIehErY6
    kC/pyozaXKVbyX/TxfGScsqn9yW0=
    -----END OPENSSH PRIVATE KEY-----
    ```

- Change the line on `/github/workflows/deploy.yml` into the folder you cloned your repository to:

```
script: |
    cd template                 ; change this line
    git pull origin master
```

---

# Happy coding
