# pihole-dnscrypt
Run pihole with dnscrypt-proxy to encrypt your network's DNS traffic with minimal setup.

## Overview
This is a simple docker-compose file for deploying a pihole container that uses dnscrypt-proxy to encrypt DNS requests/responses. [PiHole](https://pi-hole.net/) acts as a DNS server and "blackholes", or blocks, known ad/tracking URLs (or anything you want) for your local network. [dnscrypt-proxy](https://github.com/DNSCrypt/dnscrypt-proxy) makes it easy to redirect regular, unencrypted, DNS queries/responses over an encrypted connection using [DNSCrypt](https://www.dnscrypt.org/).

## Deploying
This docker-compose file is about 95% complete for getting up and running right away, but there are a few steps to complete:

1. Update the `WEBPASSWORD` environment variable - this is what you use to login to the PiHole web admin portal. _Optionally: comment it out to get a random password and fetch it from the container logs._
2. Grab a copy of [example-dnscrypt-proxy.toml](https://github.com/DNSCrypt/dnscrypt-proxy/blob/master/dnscrypt-proxy/example-dnscrypt-proxy.toml), which is the config file to tell dnscrypt-proxy what servers to forward requests to. Rename it to `dnscrypt-proxy.toml` and place it in `/etc/dnscrypt` on the docker host.
3. Uncomment the `server_names` setting and add/remove the servers you want. Use as many or as few as you want; I typically just use `cloudflare`.
4. Issue `docker compose up` to deploy the containers
5. If you have a firewall enabled (you should), allow incoming traffic on docker host ports `53, 80` to allow DNS requests and web admin portal traffic.
6. Login in to your router and set your DNS server to the address of the docker host.
7. Test your setup - navigate to a website, or use `dig` on a linux machine: `dig google.com`

## Tips
* If you run into issues resolving hosts, you can add DNS servers to the `PIHOLE_DNS_` environment variable but be advised this will expose your DNS traffic on the internet. This is a semicolon-delimited list, so to add Cloudflare temporarily you could set it to `1.1.1.1;dnscrypt#5053`
* In your router's configuration, typically under an "addresses" tab, the "primary DNS server" address should be set to the IP address of the docker host, not the container.
* The maintainers of PiHole recommend *against* automatically updating your deployments in case there is a breaking change that prevents your deployment from working. With the docker container version of PiHole, updating is as simple as pulling and deploying that in place of your current deployment. You should stop the current container, pull, then deploy to avoid any issues.
* You should really enable a firewall on the host running this stack. At a minimum, you need to allow ports 53 and 80 - 53 for DNS, and 80 for the admin portal. You may want to also allow port 22 to SSH in for troubleshooting.
* If you run into additional problems, feel free to reach out to me!
  * irc.libera.chat:6697 #pihole @Nullifi3d
  * https://github.com/jropella
