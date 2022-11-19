How * works!

**Moodle Image/Container**

The moodle image shoud be using `bitnami/moodle:4` instead of `bitnami/moodle`, because the `:latest` tag refers to version 3.x. When using the `bitnami/moodle`, the container exit after `Running Moodle install script`.

Moodle installation and Moodle data persisted in external volume instead of persisted internally in compose. This will make easier to deploy new container in case we got over-loaded.

Create volumes first before up-ing the compose. The main compose which is docker-compose.yml contains a Moodle web and a MariaDB.

MariaDB port is also exposed. This will make us easier to deploy new container in case we got over-loaded. The containers of moodle can access through docker gateway ip `172.17.0.1`.

**Fresh Start**

Make sure have created needed volumes, those are `vol_moodle` and `vol_moodledata`.

Just start the compose like we usually do.
```shell
$ docker compose up # -d 
```
Wait until installation complete (see the logs, if it says `Running Moodle install script`, means still installing). Then we can start more containers. 

**Caveats/Notes**

Main (docker-compose.yml) has it's own network. When creating new cluster like on the example (cluster2.yml), it should have it's own network too. This is mandatory because compose by default creating `foldername_default` network if we don't assign network to container, and this is dangerous.

Imagine this scenario. We have 2 clusters, `cluster2.yml` and `cluster3.yml` . They don't use any network in config. Let's say `cluster2.yml` died (due to SIGKILL maybe), then `cluster3.yml`'s network will die too, making all containers exits.

If starting without `-d` or detach, be careful, CTRL+C can kill the whole containers even if it's executed using cluster config. So **Make Sure** to detach container when starting new cluster, then see log using `logs` command.

Also create new hostname for each container when creating new cluster config.

Stopping/Downing main compose (docker-compose.yml) will kill all containers (including in cluster config).

**Go Productive**

I know it's inconvinient to type `docker compose -f cluster2.yml ...`. Well okay. Just use `./dc {cluster_number} {docker compose args}`.

```shell
docker compose -f cluster2.yml up
```

```shell
./dc 2 up
```

Make sure you're using this convention.
