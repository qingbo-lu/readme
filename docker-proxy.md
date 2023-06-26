# Docker registry proxy

### Main purpose:

*   avoiding DockerHub Pull Rate Limits with Caching;
*   faster internet image pull, faster container startup;
*   save bandwidth;
*   cache images unreachable for aliyun;
*   centralized management of Docker registry credentials

### Link:

<https://github.com/rpardini/docker-registry-proxy>

# What?

Essentially, it's a [man in the middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack): an intercepting proxy based on `nginx`, to which all docker traffic is directed using the `HTTPS_PROXY` mechanism and injected CA root certificates.



# Deployment.

can easily be deployed to kubernetes  cluster.



# How to user proxy

*   [Configure Docker proxy](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) pointing to the caching server  (docker.service  Environment="HTTP\_PROXY=<http://127.0.0.1:3128/>" Environment="HTTPS\_PROXY=<http://127.0.0.1:3128/>")
*   Add the caching server CA certificate to the list of system trusted roots.
*   Restart `docker`d

<!---->

    ### CENTOS
    # Get the CA certificate from the proxy and make it a trusted root.
    curl http://192.168.66.72:3128/ca.crt > /etc/pki/ca-trust/source/anchors/docker_registry_proxy.crt
    update-ca-trust
    ###

    # Reload systemd
    systemctl daemon-reload

    # Restart dockerd
    systemctl restart docker.service



### Other way than config docker proxy.

*   dns-hijack a special registry, (for example quay.io, dockerhub.com, change node resolver to kube-dns, adding a dns recored for registry on kube-dns）
*   Add the caching server CA certificate to the list of system trusted roots.



## Ways to config kubernetes nodes.

*   cloud-init to config node resolve, docker, containerd proxy...( gcp instance metadata, aliyun node pool cloud-init)


*   to create a privileged DaemonSet putting self-signed certificates into the host nodes' Docker certificate stores and editing resolv.conf to include the kube-dns nameserver.

# Issues

*   cache proxy self HA.
*




