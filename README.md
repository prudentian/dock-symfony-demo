Symfony Demo Application
========================

The "Symfony Demo Application" is a reference application created to show how
to develop applications following the [Symfony Best Practices][1].

This is a Dockerized PHP8 NGINX base container for symfony. 

If not already done, [install Docker Compose][2] (v2.10+)

Components
------------

  * Alpine 3.18 
  * PHP 8.2.0 
  * PDO-SQLite PHP extension enabled;
  * Nginx 1.25 or higher;
  * Node 20.5.1 or higher;

Installation
------------

1. Clone this repo. Go to project folder
2. Run `docker compose build --no-cache` to build fresh images
3. Run `docker compose up --pull --wait` to start the application 

Wait for about a minute for the application to be initialized

Usage
-----

Access the application in your browser at the given URL (<//http://localhost:8080/> ).

You should see the demo landing page ....
`Welcome to the Symfony Demo application`

#### Stop application
Run `docker compose down --remove-orphans` to stop the Docker containers


License
-------
Symfony Docker is available under the MIT License.

Credits
-------
Adopted from [Symfony Demo][3].


[1]: https://symfony.com/doc/current/best_practices.html
[2]: https://docs.docker.com/compose/install/
[3]: https://github.com/symfony/demo
