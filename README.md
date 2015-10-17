# owner.sh

Ownership wrangling for poorly behaved systems. Plays nice with busybox.

## Usage

```
owner get <path>
```
Prints the name of the user that owns `<path>`

```
owner check <user> <path>
```
Checks if <user> owns <path>.

```
owner set <user> <path>
```
Attempts to set the owner of `<path>` to `<user>` and confirms it actually did.

## Why?

I wrote this because of a quirk mounting volumes from an OSX host to a Docker
container on a VirtualBox VM using VirtualBox Shared Folders. If it sounds
awkwardly specific, this scenario comes up more often than might be expected,
as the default driver for `docker-machine` is `virtualbox`, and the default
behaviour when mounting volumes from the host uses VirtualBox Shared Folders.

  * The mount type is `vboxsf`.
  * The UID/GID of the shared folder is determined by the guest VM (UID 1000 / GID 50).
  * In the container, `chown <user> /path/to/vboxsf_mount` returns a zero exit status and prints no error.

Remove any one of these factors, and the issue doesn't happen.

### `virtualbox` driver, volume mounted from Guest VM.
The mount type is `tmpfs`, and `chown` works as expected. This provides a workaround for some cases, but does not solve the issue of mounting volumes from the Host.

### `vmwarefusion` driver, volume mounted from Host.
The mount type is `vmhgfs` and `chown` behaves as expected, exiting with a
non-zero status and an error message when permissions cannot be changed. This is fine, but doesn't resolve the issue for anyone not using `vmwarefusion`.

### `virtualbox` driver, volume mounted from Host using NFS.
Technically works, but brings a new set of problems, in the form of needing to
care about UID/GID inconsistencies between Host/Guest/Container. This is
requires awkward coupling between Host/Guest/Container. This is fragile,
cumbersome, and undermines the purpose of containerization.

# License
[MIT](https://tldrlegal.com/license/mit-license)

# Contributors
  * [Chris Olstrom](https://colstrom.github.io/) | [e-mail](mailto:chris@olstrom.com) | [Twitter](https://twitter.com/ChrisOlstrom)
