# zfs-tools
Some useful ZFS dataset management scripts

## zfs-pull / zfs-push

![](https://raw.githubusercontent.com/johnko/zfs-tools/master/img/screenshot.png)


## zfs-sync within a LAN

To pull in a remote dataset's new snapshots to our local set, overwriting our modifications.

This expects the dataset to be mounted at /$DATASET and looks for /$DATASET/.zfs/snapshot/* on local and remote host.

```
zfs-sync-pull  tank/urep  remote_hostname  tank
```

## zfs-sync over WAN

To pull in a remote dataset's new snapshots to our local set, overwriting our modifications.

This does a zfs send to tmp file and rsync over WAN.

*This is a workaround until zfs send/recv gets resume.*

```
local$  mkdir -p /scratch/urep
remote$  mkdir -p /scratch/urep
local$  zfs-sync-xz-pull  tank/urep  remote_hostname  tank  /scratch/urep

# if interrupted, you can export manually on remote while in tmux
remote$  zfs-xz-multi-export  tank/urep@$START_AT_SNAPSHOT  /scratch/urep  999

# then loop rsync
local$  zfs-xz-rsync-pull  remote_hostname  /scratch/urep

# when rsync is done
local$  zfs rollback tank/urep@$START_AT_SNAPSHOT
local$  zfs-xz-multi-import  /scratch/urep  tank
```
