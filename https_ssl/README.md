# HTTPS SSL project

Files for the `alu-webstack/https_ssl` assignment.

## DNS audit script

`0-world_wide_web DOMAIN [SUBDOMAIN]` queries DNS with `dig`. With no subdomain it reports `www`, `lb-01`, `web-01`, and `web-02` in that order. It assumes the zone has the corresponding A records configured. Run it on Ubuntu/Linux after copying the file:

```sh
chmod +x 0-world_wide_web
./0-world_wide_web example.com
./0-world_wide_web example.com web-02
```

Configure DNS A records to these assignment server addresses:

| Name | Address |
| --- | --- |
| www | 3.10.198.197 (load balancer) |
| lb-01 | 3.10.198.197 |
| web-01 | 18.171.162.21 |
| web-02 | 18.171.164.153 |

DNS must be changed at the authoritative DNS provider for the actual domain; its name is not included in the assignment text.

## HAProxy TLS files

`1-haproxy_ssl_termination` enables HTTPS on port 443. `2-redirect_http_to_https` additionally redirects HTTP requests with status 301. Both balance requests across the provided web servers on port 80.

For each config, obtain a certificate for `www.YOUR_DOMAIN` and create the HAProxy PEM bundle (private key followed by the full chain) at `/etc/haproxy/certs/www.pem`, then install the selected config as `/etc/haproxy/haproxy.cfg` and reload HAProxy. Example Certbot flow on the load balancer (adjust the domain and email, and ensure DNS already points to it):

```sh
sudo certbot certonly --standalone -d www.YOUR_DOMAIN
sudo install -d -m 700 /etc/haproxy/certs
sudo sh -c 'cat /etc/letsencrypt/live/www.YOUR_DOMAIN/privkey.pem /etc/letsencrypt/live/www.YOUR_DOMAIN/fullchain.pem > /etc/haproxy/certs/www.pem'
sudo chmod 600 /etc/haproxy/certs/www.pem
```

The two configs are assignment answer files and need the real domain's certificate installed at the path above before HAProxy can start with TLS. The backend web servers must already serve the required `Holberton School` page at `/`.
