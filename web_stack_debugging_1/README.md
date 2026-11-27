# Web stack debugging 1

Finding out why Nginx in an Ubuntu container does not listen on port 80, and
automating the fix.

## The problem

```
# curl 0:80
curl: (7) Failed to connect to 0 port 80: Connection refused
```

Nginx loads its sites from `/etc/nginx/sites-enabled/*`. The default site lives
in `/etc/nginx/sites-available/default` and is enabled by a symlink. Nginx
does not listen on port 80 when:

- the `sites-enabled/default` symlink is missing, so no server block is loaded
- the default site listens on another port (such as `8080`) or only on one
  address (such as `127.0.0.1`)

## The fix

[0-nginx_likes_port_80](0-nginx_likes_port_80) runs three commands:

1. `sed` rewrites the IPv4 `listen` line of the default site to
   `listen 80 default_server;`, which binds every IPv4 address
2. `ln -sf` enables the default site again
3. `service nginx restart` loads the new configuration

```
# ./0-nginx_likes_port_80 > /dev/null 2>&1
# curl -sI 0:80 | head -1
HTTP/1.1 200 OK
```

## Testing

The script was tested in `ubuntu:20.04` containers broken in each way above,
and on a healthy one. After it runs, `ss -ltn` shows Nginx on `0.0.0.0:80`.

Run test containers with `/bin/bash` as the main process (`docker run -dit
image /bin/bash`). With `sleep infinity` as PID 1, the old Nginx process is
never reaped on `restart`, and the init script reports a failure.

## Tasks

| File | Description |
| ---- | ----------- |
| [0-nginx_likes_port_80](0-nginx_likes_port_80) | Makes Nginx listen on port 80 of all the server's active IPv4 IPs |
