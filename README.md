# Wireguard
Configure Wireguard for 3 sites with a Dual Stack network

For the Wireguard interface generate at https://network00.com/NetworkTools/IPv6LocalAddressRangeGenerator a local ipv6 network and choose a private ipv4 network, we use: **172.16.0.0/24** and **fde2:e0e8:a244:8c60::/64**

Network overview:

![Wireguard](https://user-images.githubusercontent.com/40859756/145683350-8ec4b24f-0fa0-4c04-b090-4ae33a2547ad.png)

Generate private/public keys for every site:
```
mkdir -p ~/wireguard/site-{01,02,03}

cd ~/wireguard/site-01
wg genkey | tee privatekey | wg pubkey > publickey

cd ~/wireguard/site-02
wg genkey | tee privatekey | wg pubkey > publickey

cd ~/wireguard/site-03
wg genkey | tee privatekey | wg pubkey > publickey
```
Generate shared keys for every site combination:
```
cd ~/wireguard
wg genpsk > presharedkey-site-01-02
wg genpsk > presharedkey-site-01-03
wg genpsk > presharedkey-site-02-03
````

Edit for each site **/etc/wireguard/wg0.conf**:

**Site-01**
```
[Interface]
privatekey = kHZL5QzKc2wv/fLjDdZNUSH99tJRJlxuwGhFkcpvUUo=
listenport = 47672
Address = 172.16.0.1/32, fde2:e0e8:a244:8c60::1/128

[Peer]
# Site-02
publickey = fLVyLC8/+IBHUCVGFpxH01Z72w9hCIb7+YFCgBooSUA=
presharedkey = XaZgt12ccgH3UVGha00h2lslS/vCSA8z4fmmmmi/BHA=
allowedips = 10.99.20.0/24, 2001:1c01:a:b:c::/64, 172.16.0.2/32, fde2:e0e8:a244:8c60::2/128
persistentkeepalive = 25

[Peer]
# Site-03
publickey = qDJDiZPIPFt46llqO8zx7CXBXgSFBKU13vrfa//M/Q8=
presharedkey = jZgY59TEnF6YDjbzor9Acdp0RS85JYLPDzuoKEhYHHI=
allowedips = 10.99.30.0/24, 2a0f:2980:a:b:c::/64, 172.16.0.3/32, fde2:e0e8:a244:8c60::3/128
persistentkeepalive = 25
```
**Site-02**
```
[Interface]
privatekey = MBbz1+ucKGPrsUFn+jadEhAAXsOhX8+hGZ8xqV3dHlc=
listenport = 47672
address = 172.16.0.2/32, fde2:e0e8:a244:8c60::2/128

[Peer]
# Site-01
publickey = naMhW/M1LlxQhIlZTJN+TGwZdIs7CjKiEnq/B394+is=
presharedkey = XaZgt12ccgH3UVGha00h2lslS/vCSA8z4fmmmmi/BHA=
endpoint = <public-ip4-address-site-01>:47672
allowedips = 10.99.10.0/24, 2001:41f0:a:b:c::/64, 172.16.0.1/32, fde2:e0e8:a244:8c60::1/128
persistentkeepalive = 25

[Peer]
# Site-03
publickey = qDJDiZPIPFt46llqO8zx7CXBXgSFBKU13vrfa//M/Q8=
presharedkey = 89YgkeyDSeVdyI3fU3wBeluV1x7TAa0BtQ9RXvQxu5U=
allowedips = 10.99.30.0/24, 2a0f:2980:a:b:c::/64, 172.16.0.3/32, fde2:e0e8:a244:8c60::3/128
persistentkeepalive = 25

```
**Site-03**
```
[Interface]
privatekey = EBJsF2yKN9zJ4opYzVEKse5KHb8CHB/SQBx+3Hx4Vlk=
listenport = 47672
address = 172.16.0.3/32, fde2:e0e8:a244:8c60::3/128

[Peer]
# Site-01
publickey = naMhW/M1LlxQhIlZTJN+TGwZdIs7CjKiEnq/B394+is=
presharedkey = jZgY59TEnF6YDjbzor9Acdp0RS85JYLPDzuoKEhYHHI=
endpoint = <public-ip4-address-site-01>:47672
allowedips = 10.99.10.0/24, 2001:41f0:a:b:c::/64, 172.16.0.1/32, fde2:e0e8:a244:8c60::1/128
persistentkeepalive = 25

[Peer]
# Site-02
publickey = fLVyLC8/+IBHUCVGFpxH01Z72w9hCIb7+YFCgBooSUA=
presharedkey = 89YgkeyDSeVdyI3fU3wBeluV1x7TAa0BtQ9RXvQxu5U=
endpoint = <public-ip4-address-site-02>:47672
allowedips = 10.99.20.0/24, 2001:1c01:a:b:c::/64, 172.16.0.2/32, fde2:e0e8:a244:8c60::2/128
persistentkeepalive = 25
```
