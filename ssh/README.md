# SSH

Connecting to a remote Ubuntu server with `ssh` using an RSA key pair.

## Tasks

| File | Description |
| ---- | ----------- |
| [0-use_a_private_key](0-use_a_private_key) | Connects to the server as user `ubuntu` using the private key `~/.ssh/school` |
| [1-create_ssh_key_pair](1-create_ssh_key_pair) | Creates a 4096-bit RSA key pair named `school`, protected by the passphrase `betty` |
| [2-ssh_config](2-ssh_config) | SSH client configuration that uses the private key `~/.ssh/school` and refuses password authentication |
| 3. Let me in! | The provided public key was added to `~/.ssh/authorized_keys` of the `ubuntu` user on the server (no file) |
| [100-puppet_ssh_config.pp](100-puppet_ssh_config.pp) | Puppet manifest that sets `IdentityFile ~/.ssh/school` and `PasswordAuthentication no` in `/etc/ssh/ssh_config`. Requires `puppetlabs-stdlib` for `file_line`. Usage: `sudo puppet apply 100-puppet_ssh_config.pp` |
