# Web stack debugging 4

Load testing Nginx with ApacheBench, finding why many requests fail, and
fixing it with Puppet.

## Tasks

| File | Description |
| ---- | ----------- |
| [0-the_sky_is_the_limit_not.pp](0-the_sky_is_the_limit_not.pp) | Raises the Nginx open file limit and restarts Nginx |

## The problem

2000 requests, 100 at a time:

```
# ab -c 100 -n 2000 localhost/
Document Length:        201 bytes
Failed requests:        943
Non-2xx responses:      1057
```

The Nginx error log shows why:

```
[crit] accept4() failed (24: Too many open files)
[crit] open() "/usr/share/nginx/html/index.html" failed (24: Too many open files)
```

Every connection and every file Nginx serves uses a file descriptor. On Ubuntu
14.04 the init script applies `ULIMIT` from `/etc/default/nginx`, and it is set
far too low (`ULIMIT="-n 15"`). Under load Nginx runs out of descriptors and
answers `500` (the 201-byte error page).

## The fix

The manifest sets `ULIMIT="-n 4096"` in `/etc/default/nginx`, then restarts
Nginx so the new limit applies:

```
# puppet apply 0-the_sky_is_the_limit_not.pp
Notice: /Stage[main]/Main/Exec[fix--for-nginx]/returns: executed successfully
Notice: /Stage[main]/Main/Exec[restart-nginx]/returns: executed successfully
# ab -c 100 -n 2000 localhost/
Document Length:        612 bytes
Failed requests:        0
```

Check the limit of the running Nginx processes with:

```
# grep "open files" /proc/$(pgrep -o nginx)/limits
Max open files            4096                 4096                 files
```

## Testing

Ubuntu 14.04 with Nginx 1.4.6 and Puppet 3.4.3 (`holbertonschool/292`):

- `puppet-lint` 2.1.1 reports no errors
- with `ULIMIT` at 30 or 60, `ab` failed hundreds of requests; after the
  manifest, 0 failed
- with `ULIMIT="-n 15"` Nginx could not start at all; after the manifest it
  served all 2000 requests
- running the manifest twice gives the same result
