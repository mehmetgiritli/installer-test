# Millie API

## About

Millie API is an interface for managing clients, scopes and access to one of the APIs created for the Online Education Management System (OEMS). This API allows for management of users and applications via the OEMS, and exposes course, course fee and HESA lookup data.

## Setting up your Local Development Environment

1. Download a copy of this repository.

    ```sh
    $ git clone https://github.com/uofa/millie-api.git
    ```

2. Set up your `laravel/.env` file.
   1. This file contains all the sensitive configuration information for a specific environment.
   1. A `laravel/.env.example` is supplied to get you started. This should be copied and renamed to `laravel/.env`
   1. Update this file with the appropriate settings for your current working environment.

3. Set up the database.
   1. It is recommended to connect to the dev or test database, unless you are making changes to Oracle packages or procedures.
   1. If connecting to your own Oracle schema for the first time, you will need to:
       - Create `millie_web` and `millie_api` schemas (ask a DBA)
       - Create `oems_web` and `oems_owner` schemas (ask a DBA)- note you may need a database link between your schema and `OSS`, as this is where `oemsapi_owner` procedures reside.
       - Enter your credentials into the `API_*` and `OEMS_*` sections in your `laravel/.env` file.
       - Create all relevant database objects - run the scripts below in the order below:

           ```
           In sql/millie_api/:
             scopes.sql
             oauth_clients.sql
             oauth_access_tokens.sql
             client_scopes.sql

           In sql/oems-owner/:
             oems_errors.sql
             oems_users.sql
             ph_oems_shared.sql
             pb_oems_shared.sql
             ph_oems_account.sql
             pb_oems_account.sql
           ```
   1. If you are reusing an existing schema, so long as the credentials are included in your `laravel/.env` file you do not need to complete the above step.

4. Install third-party assets and generate application key.
   1. It is recommended to use Docker. Open a terminal at the root:
       ```sh
       $ docker-compose up -d
       -- to retrieve the workspace name (`millie-api_workspace_1` is default)
       $ docker ps
       $ docker exec -it <workspace_container_name> bash
       $ cd laravel
       $ composer install
       $ php artisan key:generate
       ```
   1. Run the following commands from the `./laravel` directory, outwith the Docker container:
       ```sh
       $ npm install
       ```

5. Compile assets and run the application.
   1. Transforms all the `.scss` files into `.css`
   1. Optimises the JavaScript and merges all the dependencies
   1. Run the following command from the `./laravel` directory, outwith the Docker container:

       ```sh
       -- to prep for dev
       $ npm run dev

       -- to prep for production
       $ npm run prod
       ```

## Phan

Phan is included in the Docker `*_workspace` container.

When Docker is running, it can be set up as follows:

```

-- launch the *_workspace container
docker exec -it millie-api_workspace_1 bash

cd laravel

-- if you are running this for the first time...

-- remove the compiled services file
php artisan clear-compiled

-- generate an `_ide_helper.php` file in the `laravel/` directory.
php artisan ide-helper:generate

-- move this file to `laravel/.phan/stubs/`.
mv /var/www/laravel/_ide_helper.php /var/www/laravel/.phan/stubs/

-- create empty file for the report
touch .phan/reports/analysis.txt

--
-- to run phan and output to `.phan/reports/analysis.txt`:
/usr/local/bin/phan --progress-bar -o .phan/reports/analysis.txt

```

Phan settings can be tweaked in `laravel/.phan/config.php`.
