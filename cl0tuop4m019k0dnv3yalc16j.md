## VoIP calls and TLS security

After a while, here I am again, talking about VoIP.  
The last article is about [audio problems and the quality of a VoIP call](https://blog.giovannitommasini.info/voip-fundamentals-of-sip-and-related-topics-part-6-audio-problems-and-the-quality-of-a-voip-call). In this new article I talk about the VoIP over TLS and in particular how to configure a SIP server with self-renewing TLS certificates.

This is an article accompanying code found in this [github repo](https://github.com/evoseed/kamailio-tls-letsencrypt).  

## The idea and the purpose

The purpose of this article is to explain how to deal with SIP over TSL.
In the [this github repo](https://github.com/evoseed/kamailio-tls-letsencrypt) the [evoseed](https://evoseed.io) team explain a simple way to deploy a Kamailio server in a docker container with TLS enabled using Let's Encrypt.

Thanks to Fred Posner's [article](https://www.fredposner.com/1836/kamailio-tls-and-letsencrypt/) we have understood that there are 2 ways to deal with TLS in Kamailio:

*quoting Fred Posner*:

>1. Use a purchased certificate
>    - Pro: easy to deploy on client side
>    - Cons: expensive, painful acquisition model
>1. Use a self-signed certificate
>    - Pro: free
>    - Cons: difficult to deploy on client side

- if you use a **self-signed certificate** (not certified by any authority) on your SIP Server, necessarily on client-side you must use its `calist.pem`; so *free but difficult to deploy on client side*.  
If you want to go down this road [here](doc/SELF-SIGNED-CERT.md) you can find my personal guide. You can also find the procedure here [here](https://www.kamailio.org/dokuwiki/doku.php/tls:create-certificates) and [here](https://kamailio.org/docs/modules/devel/modules/tls.html) on [Kamailio](https://kamailio.org)'s site):

- if you use a **certificate trusted** by a guarantor authority recognized by the client then client-side should not need to load the `calist.pem`; so *easy to deploy on client side but expensive*.  
If you want to go down this road you have to buy a certificate and the put it in you Kamailio installation following the same procedure describe for self-signed certificates.

There is, however, an **alternative** that is free, automatable, easy to use and requires no client-side configuration: `Let’s Encrypt`.

I wrote this article and created this [github repo](https://github.com/evoseed/kamailio-tls-letsencrypt) to take a step forward from Fred's excellent article [https://www.fredposner.com/1836/kamailio-tls-and-letsencrypt/](https://www.fredposner.com/1836/kamailio-tls-and-letsencrypt/).  
Fred explains how to install Let's Encrypt and create a certificate (using `letsencrypt-auto`, which unfortunately is no longer supported) and how to configure the certificate in kamailio.  
I want to go one step further and explain how to have a `recipe` and a ready-to-use `docker-compose` that allows you to:

- pre-configure the TLS certificate for your domain
- pre-configure kamailio to use the certificate
- have a container that checks the validity of the certificate and autonomously performs the renewal when necessary
- have, ready-to-use, an nginx reverse proxy that uses the certificate: `HTTP 80 -> HTTPS`

---

## The project structure

```bash
/doc
/modules/db
/modules/kamailio` 
/modules/nginx
env-template
docker-compose.yml
```

---

### Step to deploy

1. clone the repository in your deploy machine
    `git clone https://github.com/evoseed/kamailio-tls-letsencrypt .`
1. pre-configure the TLS certificate for our domain
    - [Install docker-compose](https://docs.docker.com/compose/install/#install-compose).
    - Modify configuration of nginx  
        Of course change `your.domain.com` with you real domain and change `1.2.3.4` with your real IP address.

        - **Linux users**

            ```bash
            cd modules/nginx/nginx-certbot
            sed -i 's/${DOMAIN}/your.domain.com/g' init-letsencrypt.sh
            sed -i 's/${DOMAIN}/your.domain.com/g' data/nginx/app.conf
            sed -i 's/${IP}/1.2.3.4/g' data/nginx/app.conf
            ```

        - **MacOS users**

            ```bash
            cd modules/nginx/nginx-certbot
            sed -i '' 's/${DOMAIN}/your.domain.com/g' init-letsencrypt.sh
            sed -i '' 's/${DOMAIN}/your.domain.com/g' data/nginx/app.conf
            sed -i '' 's/${IP}/1.2.3.4/g' data/nginx/app.conf
            ```

    - Run the init script and follow the Let's encrypt instructions:
        ```bash
        ./init-letsencrypt.sh
       ```
        **NB** It's quite important to compile correctly the conf in this way; in particular `Organization Name` and `Common Name` with your domain

1. return in the root of the project and compile the `.env` file following the `env-template` file
    ```bash
    cd ../../.. && cp env-template .env
    ``` 
    and complete the `.env` file

1. start all the containers
    ```bash
     docker-compose up -d
    ```

---

### The running project

When everything has started, what you will see is more or less this:

```bash
CONTAINER ID   IMAGE                    COMMAND                  CREATED       STATUS       PORTS     NAMES
48838aee0057   kamailio-test_kamailio   "/etc/kamailio/boots…"   2 hours ago   Up 1 hour                kamailio
8103e16bd845   kamailio-test_db         "docker-entrypoint.s…"   2 hours ago   Up 1 hour                db
e212e6c8241f   certbot/certbot          "/bin/sh -c 'trap ex…"   2 hours ago   Up 1 hour                certbot
3c101597b160   nginx:1.15-alpine        "/bin/sh -c 'while :…"   2 hours ago   Up 1 hour                nginx
```

The `kamailio` container, your SIP Server  
the `db` container  
the `nginx` container as reverse proxy for HTTP to HTTPS
and the `certbot` container who will check every 12 hours whether certificates are still valid or need to be renewed (and will renew them independently if necessary)

---

### Technical details

1. **NB** this project is designed for **dev** purpose, so it is not optimized for production.
    - `docker-compose` for example use the build stage

        ```bash
        ...
        build:
          context: ./modules/db
          dockerfile: Dockerfile
        ...
        ```

        so if you want to have a production ready version you can use built image:

        ```bash
        ...
        image: your.registry.com/project-name/db:latest
        ...
        ```

1. **NB** this project is designed to run on a VM to which the domain you configured in `init-letsencrypt.sh` and in the `.env` points and with a correct domain.

    If you run the `init-letsencrypt.sh` on your local private machine and you set `${DOMAIN} = your.domain.com` you'll receive an error like this

    ```bash
    Requesting a certificate for your.domain.com

    Certbot failed to authenticate some domains (authenticator: webroot). The Certificate Authority reported these problems:
    Domain: your.domain.com
    Type:   dns
    Detail: no valid A records found for your.domain.com; no valid AAAA records found for your.domain.com

    Hint: The Certificate Authority failed to download the temporary challenge files created by Certbot. Ensure that the listed domains serve their content from the provided --webroot-path/-w and that files created there can be downloaded from the internet.

    Some challenges have failed.
    Ask for help or search for solutions at https://community.letsencrypt.org. See the logfile /var/log/letsencrypt/letsencrypt.log or re-run Certbot with -v for more details.
    ERROR: 1
    ```

    This happens because Let's Encrypt need to test if you are the admin of the domain.


1. How is the creation of certificates and their use automated in kamailio?

    As you can see in the `docker-compose.yaml` file, the `certbot` container and the `kamailio` container mount both volumes with the same source directory: `./module/nginx/nginx-certbot/data/certbot/conf/`.  
    So the `certbot` create (and renew) the certificates and `kamailio` use them.

---

### Thanks to

- [Fred Posner](https://www.fredposner.com) for your [article](https://www.fredposner.com/1836/kamailio-tls-and-letsencrypt/)
- [Kamailio](https://www.kamailio.org/w/) SIP Server and all your community
- [Philipp Schmieder](https://github.com/wmnnd) for your [nginx-certbot](https://github.com/wmnnd/nginx-certbot) repo
- my [evoseed](https://evoseed.io) team

