<a href="https://github.com/josuapsianturi/velflix"> <h1 align="center">Velflix</h1></a>
<p align="center"><a href="https://github.com/josuapsianturi/velflix/blob/main/LICENSE"><img src="https://poser.pugx.org/cpriego/valet-linux/license.svg" alt="License"></a>
</p>

## About

Velflix is a Laravel [Netflix](https://netflix.com) clone project using TALL stack ([Tailwindcss](https://tailwindcss.com/), [Alpinejs](https://github.com/alpinejs/alpine/), [Laravel](https://laravel.com/), [Livewire](https://laravel-livewire.com/) ).

> **Note**
> Work in Progress

## Table of Contents

* [Screenshots](#screenshots)
* [Requirements](#requirements)
* [Installation](#installation)
* [Testing](#testing)
* [Contributing](#contributing)
* [License](#license)

<a name="screenshots"></a>
## Screenshots

![home page](https://raw.githubusercontent.com/josuapsianturi/velflix/main/public/img/home.png)

see full page [here](https://raw.githubusercontent.com/josuapsianturi/velflix/main/public/img/home-full-page.png)

![movies header](https://raw.githubusercontent.com/josuapsianturi/velflix/main/public/img/movies-header.png)

![movies](https://raw.githubusercontent.com/josuapsianturi/velflix/main/public/img/movies.png)

see full page [here](https://raw.githubusercontent.com/josuapsianturi/velflix/main/public/img/movies-full-page.png)

![Detail movies](https://raw.githubusercontent.com/josuapsianturi/velflix/main/public/img/details-movie.png)

<a name="requirements"></a>
## Requirements

Package | Version
--- | ---
[Node](https://nodejs.org/en/) | V14.19.1+
[Npm](https://nodejs.org/en/)  | V6.14.16+ 
[Composer](https://getcomposer.org/)  | V2.2.6+
[Php](https://www.php.net/)  | V8.0.17+
[Mysql](https://www.mysql.com/)  |V 8.0.27+

<a name="installation"></a>
## Installation


To deploy your Laravel project using Nginx, MySQL, and an SSL certificate on an Ubuntu server, and then upload a README file to your GitHub repository, follow these steps:

### 1. **Set Up Your Server**
- **Update your server:**
  ```bash
  sudo apt update && sudo apt upgrade -y
  ```

- **Install necessary packages:**
  ```bash
  sudo apt install nginx mysql-server php-fpm php-mysql git -y
  ```

- **Secure MySQL:**
  ```bash
  sudo mysql_secure_installation
  ```
  Follow the prompts to set a root password, remove anonymous users, disallow root login remotely, and remove the test database.

### 2. **Set Up MySQL Database**
- **Login to MySQL:**
  ```bash
  sudo mysql -u root -p
  ```
  
- **Create a database and user for your Laravel project:**
  ```sql
  CREATE DATABASE laravel_db;
  CREATE USER 'laravel_user'@'localhost' IDENTIFIED BY 'password';
  GRANT ALL PRIVILEGES ON laravel_db.* TO 'laravel_user'@'localhost';
  FLUSH PRIVILEGES;
  EXIT;
  ```

### 3. **Set Up Laravel**
- **Clone your Laravel project:**
  ```bash
  cd /var/www/html
  sudo git clone https://github.com/yourusername/your-laravel-project.git
  cd your-laravel-project
  ```

- **Install Composer dependencies:**
  ```bash
  sudo apt install composer -y
  composer install
  ```

- **Set up environment file:**
  ```bash
  cp .env.example .env
  nano .env
  ```
  Update the `.env` file with your database and other configuration details:
  ```env
  DB_DATABASE=laravel_db
  DB_USERNAME=laravel_user
  DB_PASSWORD=password
  ```

- **Generate an application key:**
  ```bash
  php artisan key:generate
  ```

### 4. **Set Up Nginx**
- **Create a new Nginx configuration file:**
  ```bash
  sudo nano /etc/nginx/sites-available/laravel
  ```

- **Add the following configuration:**
  ```nginx
  server {
      listen 80;
      server_name your_domain_or_IP;
      root /var/www/html/your-laravel-project/public;

      index index.php index.html index.htm index.nginx-debian.html;

      location / {
          try_files $uri $uri/ /index.php?$query_string;
      }

      location ~ \.php$ {
          include snippets/fastcgi-php.conf;
          fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
          fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
          include fastcgi_params;
      }

      location ~ /\.ht {
          deny all;
      }
  }
  ```

- **Enable the configuration and restart Nginx:**
  ```bash
  sudo ln -s /etc/nginx/sites-available/laravel /etc/nginx/sites-enabled/
  sudo nginx -t
  sudo systemctl restart nginx
  ```

### 5. **Set Up SSL with Certbot**
- **Install Certbot and the Nginx plugin:**
  ```bash
  sudo apt install certbot python3-certbot-nginx -y
  ```

- **Obtain and install the SSL certificate:**
  ```bash
  sudo certbot --nginx -d your_domain_or_IP
  ```

- **Follow the prompts to configure the SSL certificate.**

### 6. **Set Up Permissions**
- **Give Nginx ownership of the Laravel directory:**
  ```bash
  sudo chown -R www-data:www-data /var/www/html/your-laravel-project
  sudo chmod -R 755 /var/www/html/your-laravel-project
  ```

### 7. **Finalize the Laravel Setup**
- **Run migrations:**
  ```bash
  php artisan migrate
  ```

- **Optimize Laravel:**
  ```bash
  php artisan optimize
  ```



1. Visit `localhost:8000` in your favorite browser.     

    > Make sure to follow your Laravel local Development Environment.

1. Newsletter feature configuration (optional)
 - Go to [mailchimp](https://mailchimp.com)
 - Register your account, get API key and paste it into `.env` file. If you need help, you can follow these steps:
    - Click Sign Up Free
    - Enter your data, check your email and verify
    - select Free, Next
    - Do you have a list of contacts? (NO)
    - Do you sell products or services online? (Neither, Products)
    - continue
 - Go to Profile > Extras > API keys
 - Create a key and copy API key
 - open the velflix project, go to `.env` file and paste it into `MAILCHIMP_KEY=paste API key here`
 - Go to web.php and paste this code at the bottom or you can follow the documentation [here](https://mailchimp.com/developer/marketing/api/lists/get-lists-info/)
 ```php
    Route::get('ping', function() {
    $mailchimp = new MailchimpMarketing\ApiClient();
    $mailchimp->setConfig([
        'apiKey' => config('services.mailchimp.key'),
        'server' => 'us5',
    ]);

    $response = $mailchimp->lists->getAllLists();
    ddd($response);
    });
 ```

 > make sure you fill in the `server` correctly, check the link at the top of your admin Mailchimp, for me its `https://us5.admin.mailchimp.com/account/api/` so i give the value of server is `us5`. if you get us6, change the server value to be `us6`.

- visit `localhost:8000/ping` or `velflix.test/ping` and copy value of id in the ` "lists" > 0 > "id"`
- open project, in .env file paste the id into `MAILCHIMP_LIST_SUBSCRIBERS=paste id here` and we ready to go
- visit `localhost:8000` or `velflix.test` test email for subscribing , and refresh your admin mailchimp it should be Your audience has increased 1 contact. 

14. Setup Laravel Socialite login with Google account (optional)
 - Go to the [Google Developers Console](https://console.cloud.google.com/apis) get "GOOGLE_CLIENT_ID" and "GOOGLE_CLIENT_SECRET". paste it into `.env` file.
 if you need help, you can follow these steps:
 - Click Credentials menu, click "select a project" at the navbar > ALL > No organization > new project.
 - project name 'velflix', location should be no organization > Create.
 - Go to OAuth consent screen menu > Select External and Create
 - App Information > app name 'velflix' choose user support email, fill email in developer contact information, save and continue
 - Go to Credentials menu > click `+Create Credentials` at the top > select "OAuth Client ID" > select Application type "Web Application" > Name 'velflix'
 - At the Authorized redirect URIs > +ADD URI > paste this into it `http://127.0.0.1:8000/login/google/callback` > Create.

 > NOTE: you can change the port to be `8080` or others, but make sure when you run `php artisan serve`, your project run in the same port.

 -  Copy `Your Client ID` and `Your Client Secret` 
 - Open velflix project, go to `.env` file and paste it in `GOOGLE_CLIENT_ID=paste_here` and `GOOGLE_CLIENT_SECRET=paste_here` and we ready to go
    ```sh
    php artisan serve
    ```
 - let's test, visit the project in your browser > Login > Login Google > choose account > and if success, it should be redirect to the movies page. 
 
 > Let me know if you get in trouble.

<a name="testing"></a>
## Testing

### <a href="https://pestphp.com/">Pest</a>
1. To run PHP testing for Laravel
    > **Warning**
    > Every time you run testing, you should run `php artisan db:seed` first

```sh
vendor/bin/pest
```

### <a href="https://www.cypress.io/">Cypress</a>


2. To run E2E testing
```sh
npx cypress run
```

### <a href="https://laravel.com/docs/9.x/pint">Laravel Pint</a>

3. To run coding style checks
```sh
vendor/bin/pint
```
### <a href="https://psalm.dev/">Laravel Psalm</a>

4. To run static analysis with Psalm
```sh
vendor/bin/psalm
```
### <a href="https://github.com/nunomaduro/larastan">Larastan </a>

5. To run static analysis with PHPStan
```sh
vendor/bin/phpstan analyse
```

<a name="contributing"></a>
## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

<a name="license"></a>
## License
Velflix is an open-sourced software licensed under [the MIT license](https://github.com/josuapsianturi/velflix/blob/main/LICENSE)
