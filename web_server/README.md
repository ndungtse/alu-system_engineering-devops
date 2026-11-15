# Web server

Scripts to publish files to a remote Ubuntu server and to install and configure
the Nginx web server on it.

## Requirements

- Ubuntu 20.04 LTS
- Bash scripts start with `#!/usr/bin/env bash`, followed by a comment explaining what they do
- All scripts pass `shellcheck` without errors
- `systemctl` is not used to restart processes (`service` is used instead)

## Tasks

| File | Description |
| ---- | ----------- |
| [0-transfer_file](0-transfer_file) | Transfers a file to the home directory (`~/`) of a server with `scp`, strict host key checking disabled. Usage: `./0-transfer_file PATH_TO_FILE IP USERNAME PATH_TO_SSH_KEY` |
| [1-install_nginx_web_server](1-install_nginx_web_server) | Installs Nginx listening on port 80 and serving a page that contains `Holberton School` |
| [2-setup_a_domain_name](2-setup_a_domain_name) | Domain name pointing (A record) to the web-01 server |
| [3-redirection](3-redirection) | Same as task 1, plus `/redirect_me` returning a `301 Moved Permanently` |
| [4-not_found_page_404](4-not_found_page_404) | Same as task 3, plus a custom 404 page containing `Ceci n'est pas une page` |
| [5-design_a_beautiful_404_page.html](5-design_a_beautiful_404_page.html) | A designed 404 page that still contains `Ceci n'est pas une page` |

## Usage

The Nginx scripts are run on the server itself, as root:

```bash
sudo ./4-not_found_page_404
curl -sI localhost/redirect_me   # 301 Moved Permanently
curl localhost/xyz               # Ceci n'est pas une page
```
