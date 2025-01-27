### Setup Docker Network ranges

From https://straz.to/2021-09-08-docker-address-pools/
Add the following to `/etc/docker/daemon.json`:

```
{
  "default-address-pools" : [
    {
      "base" : "172.17.0.0/12",
      "size" : 20
    },
    {
      "base" : "192.168.0.0/16",
      "size" : 24
    }
  ]
}
```

### Create a temporary docker network container to debug a network

Join an existing network

```
docker run --rm --network=cloudflared-tunnel_default -it jonlabelle/network-tools
```

Join the network namespace of a container
```
docker run --rm --network=container:cloudflared-tunnel -it jonlabelle/network-tools
```
