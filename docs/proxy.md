# Proxy plugin

> New as of 0.5.0

As of dokku 0.5.0, the proxy functionality has been decoupled from the nginx-vhosts plugin into the proxy plugin. In the future this will allow other proxy software (HAproxy for example) to be used instead of nginx.

```
proxy <app>                                                                                              Show proxy settings for app
proxy:disable <app>                                                                                      Disable proxy for app
proxy:enable <app>                                                                                       Enable proxy for app
proxy:ports <app>                                                                                        List proxy port mappings for app
proxy:ports-add <app> <scheme>:<host-port>:<container-port> [<scheme>:<host-port>:<container-port>...]   Set proxy port mappings for app
proxy:ports-clear <app>                                                                                  Clear all proxy port mappings for app
proxy:ports-remove <app> <host-port> [<host-port>...]                                                    Unset proxy port mappings for app
proxy:set <app> <proxy-type>                                                                             Set proxy type for app
```

## Container network interface binding

By default, the deployed docker container running your app's web process will bind to the internal docker network interface (i.e. `docker inspect --format '{{ .NetworkSettings.IPAddress }}' $CONTAINER_ID`). This behavior can be modified per app by disabling the proxy (i.e. `dokku proxy:disable <app>`). In this case, the container will bind to an external interface (i.e. 0.0.0.0) and your app container will be directly accessible by other hosts on your network.

> If a proxy is disabled, dokku will bind your container's port to a random port on the host, e.g. `0.0.0.0:32771->5000/tcp`.

```shell
# container bound to docker interface
root@dokku:~/dokku# docker ps
CONTAINER ID        IMAGE                      COMMAND                CREATED              STATUS              PORTS               NAMES
1b88d8aec3d1        dokku/node-js-app:latest   "/bin/bash -c '/star   About a minute ago   Up About a minute                       node-js-app.web.1

root@dokku:~/dokku# docker inspect --format '{{ .NetworkSettings.IPAddress }}' node-js-app.web.1
172.17.0.6

# container bound to all interfaces
root@dokku:/home/dokku# docker ps
CONTAINER ID        IMAGE                      COMMAND                CREATED              STATUS              PORTS                     NAMES
d6499edb0edb        dokku/node-js-app:latest   "/bin/bash -c '/star   About a minute ago   Up About a minute   0.0.0.0:49153->5000/tcp   node-js-app.web.1
```
