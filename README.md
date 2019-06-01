# [Restic](https://github.com/restic/restic)

[![badge](https://images.microbadger.com/badges/image/hotio/restic.svg)](https://microbadger.com/images/hotio/restic "Get your own image badge on microbadger.com")
[![badge](https://images.microbadger.com/badges/version/hotio/restic.svg)](https://microbadger.com/images/hotio/restic "Get your own version badge on microbadger.com")
[![badge](https://images.microbadger.com/badges/commit/hotio/restic.svg)](https://microbadger.com/images/hotio/restic "Get your own commit badge on microbadger.com")

## Donations

NANO: `xrb_1bxqm6nsm55s64rgf8f5k9m795hda535to6y15ik496goatakpupjfqzokfc`  
BTC: `39W6dcaG3uuF5mZTRL4h6Ghem74kUBHrmz`  
LTC: `MMUFcGLiK6DnnHGFnN2MJLyTfANXw57bDY`

## Starting the container

Just the basics to get the container running:

```shell
docker run --rm --name restic -v /tmp/restic:/config -e TZ=Etc/UTC hotio/restic
```

```yaml
restic:
  container_name: restic
  image: hotio/restic
  volumes:
    - /tmp/restic:/config
  environment:
    - TZ=Etc/UTC
```

The environment variables `PUID`, `PGID`, `UMASK` and `BACKUP` are all optional, the values you see below are the default values.

```shell
-e PUID=1000
-e PGID=1000
-e UMASK=022
-e BACKUP=yes
```

```yaml
environment:
  - PUID=1000
  - PGID=1000
  - UMASK=022
  - BACKUP=yes
```

## Configuration

There are 3 files you can create. The file `/config/app/backups` containing your restic backup commands (it's a bash script basically). The file `/config/app/interval` that should contain an integer representing the amount of seconds between backups. Finally you can also create the empty file `/config/app/onstart` if you wish to create a backup on the start of the container. Removing `/config/app/interval` will only create a backup on start of the container. If you do not wish to do any automated stuff, don't create any of these files and execute the container like this `docker run --rm --name restic -v /tmp/restic:/config -e TZ=Etc/UTC hotio/restic restic version`.

## Backing up the configuration

By default on every docker container shutdown a backup is created from the configuration files. You can change this behaviour.

```shell
-e BACKUP=no
```

```yaml
environment:
  - BACKUP=no
```

## Using a rclone mount

Mounting a remote filesystem using `rclone` can be done with the environment variable `RCLONE`. Use `docker exec -it --user hotio CONTAINERNAME rclone config` to configure your remote when the container is running. Configuration files for `rclone` are stored in `/config/.config/rclone`.

```shell
-e RCLONE="remote1:path/to/files,/localmount1|remote2:path/to/files,/localmount2"
```

```yaml
environment:
  - RCLONE=remote1:path/to/files,/localmount1|remote2:path/to/files,/localmount2
```

## Using a rar2fs mount

Mounting a filesystem using `rar2fs` can be done with the environment variable `RAR2FS`. The new mount will be read-only. Using a `rar2fs` mount makes the use of an unrar script obsolete. You can mount a `rar2fs` mount on top of an `rclone` mount, `rclone` mounts are mounted first.

```shell
-e RAR2FS="/folder1-rar,/folder1-unrar|/folder2-rar,/folder2-unrar"
```

```yaml
environment:
  - RAR2FS=/folder1-rar,/folder1-unrar|/folder2-rar,/folder2-unrar
```

## Extra docker privileges

In most cases you will need some or all of the following flags added to your command to get the required docker privileges when using a rclone or rar2fs mount.

```shell
--security-opt apparmor:unconfined --cap-add SYS_ADMIN --device /dev/fuse
```

```yaml
security_opt:
  - apparmor:unconfined
cap_add:
  - SYS_ADMIN
devices:
  - /dev/fuse
```
