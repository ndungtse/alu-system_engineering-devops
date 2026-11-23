# Web stack debugging 0

Getting Apache to serve a page from the `holbertonschool/265-0` Docker
container.

## The problem

```
$ docker run -p 8080:80 -d -it holbertonschool/265-0
$ curl 0:8080
curl: (52) Empty reply from server
```

Inside the container, Apache is installed and `/var/www/html/index.html`
already contains `Hello Holberton`, but the server is not running:

```
$ service apache2 status
 * apache2 is not running
```

The container starts `/bin/bash`, not Apache, so nothing listens on port 80.

## The fix

```
$ service apache2 start
$ curl 0:8080
Hello Holberton
```

## Tasks

| File | Description |
| ---- | ----------- |
| [0-give_me_a_page](0-give_me_a_page) | Starts Apache in the container so the root page returns `Hello Holberton` |
