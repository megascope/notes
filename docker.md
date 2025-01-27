# Create a temporary docker network container to debug a network

Join an existing network
`docker run --rm --network=cloudflared-tunnel_default -it jonlabelle/network-tools`

Join the network namespace of a container
`docker run --rm --network=container:cloudflared-tunnel -it jonlabelle/network-tools`
