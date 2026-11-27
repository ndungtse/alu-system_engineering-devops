# Web stack debugging 2

Running commands and services as a less privileged user instead of root.

## Tasks

| File | Description |
| ---- | ----------- |
| [0-iamsomeoneelse](0-iamsomeoneelse) | Runs `whoami` as the user passed as the first argument |
| [1-run_nginx_as_nginx](1-run_nginx_as_nginx) | Runs Nginx as the `nginx` user, listening on port 8080 of all active IPs |

## 0. Run software as another user

```
# whoami
root
# ./0-iamsomeoneelse www-data
www-data
# whoami
root
```

`sudo -u USER` runs one command as `USER`. It works for accounts with a
`nologin` shell, such as `www-data` and `nginx`.

## 1. Run Nginx as Nginx

By default the Nginx master process runs as root and only its workers drop to
`www-data`. Starting Nginx as the `nginx` user makes every process run as
`nginx`, so a break-in is limited to that user's permissions.

The script:

1. stops Apache, which holds port 8080, and the root Nginx
2. changes the default site from port 80 to 8080, for IPv4 and IPv6
3. makes `nginx.conf` readable by everyone
4. gives `nginx` the log directory, the temp directory and the pid file, which
   it cannot create in `/run` itself
5. starts Nginx as `nginx`

A user other than root cannot bind ports below 1024, which is why Nginx listens
on 8080.

```
# ps auxff | grep ngin[x]
nginx      143  ...  Ss   11:59   0:00 nginx: master process /usr/sbin/nginx
nginx      144  ...  S    11:59   0:00  \_ nginx: worker process
# nc -z 0 8080 ; echo $?
0
```

`pkill -9` stops the old Nginx without letting it delete its pid file, so the
`touch` and `chown` that follow cannot race with it.
