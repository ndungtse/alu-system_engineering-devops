# Web stack debugging 3

Finding why a WordPress site on a LAMP stack returns a `500` error, using
`strace`, and automating the fix with Puppet.

## Tasks

| File | Description |
| ---- | ----------- |
| [0-strace_is_your_friend.pp](0-strace_is_your_friend.pp) | Puppet manifest that fixes the typo in `wp-settings.php` |

## The problem

```
# curl -sI 127.0.0.1
HTTP/1.0 500 Internal Server Error
```

PHP errors are not displayed or logged, so the Apache logs give no clue.

## Finding it with strace

Attach `strace` to the running Apache processes, then send a request from a
second window (for example with `tmux`):

```
# strace -f -e trace=file $(pgrep apache2 | sed 's/^/-p /') 2>&1 | grep ENOENT
# curl -sI 127.0.0.1          # in the other window
```

Among the expected misses (`.htaccess`, optional WordPress files), one stands
out:

```
openat(AT_FDCWD, "/var/www/html/wp-includes/class-wp-locale.phpp", O_RDONLY) = -1 ENOENT (No such file or directory)
```

`wp-settings.php` requires `class-wp-locale.phpp`, a file that does not exist.
PHP stops with a fatal error and Apache answers `500`.

## The fix

```puppet
exec { 'fix-wordpress':
  command => 'sed -i s/\.phpp/\.php/g /var/www/html/wp-settings.php',
  path    => '/usr/local/bin/:/bin/',
}
```

```
# puppet apply 0-strace_is_your_friend.pp
Notice: /Stage[main]/Main/Exec[fix-wordpress]/returns: executed successfully
# curl -sI 127.0.0.1 | head -1
HTTP/1.1 200 OK
```

Running the manifest again changes nothing, since no `.phpp` is left.

## Testing

- `puppet-lint` 2.1.1 and `puppet apply` with Puppet 3.4.3 on Ubuntu 14.04
  (`holbertonschool/292`)
- The bug reproduced on a WordPress + MySQL stack with PHP errors hidden:
  `500` before the manifest, `200` with the `Holberton` title after
