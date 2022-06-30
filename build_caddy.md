# Build caddy step by step

## 1.安装 Go1.18.3

```
$ curl -OL https://go.dev/dl/go1.18.3.linux-amd64.tar.gz
$ tar -C /usr/local -xvf go1.18.3.linux-amd64.tar.gz
```
```
$ vim ~/.profile
# export PATH=$PATH:/usr/local/go/bin
```
```
$ source ~/.profile
$ go version
```

[How To Install Go on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-install-go-on-ubuntu-20-04)

## 2.安装 xcaddy

### Method 1

```
$ wget https://github.com/caddyserver/xcaddy/releases/download/v0.3.0/xcaddy_0.3.0_linux_amd64.tar.gz
$ tar -xvf xcaddy_0.3.0_linux_amd64.tar.gz
```

### Method 2

```
$ go install github.com/caddyserver/xcaddy/cmd/xcaddy@latest
```

## 3. 编译 caddy

```    
./xcaddy build \
	--with github.com/caddy-dns/cloudflare \
        --with github.com/imgk/caddy-trojan \
        --with github.com/mholt/caddy-webdav \
        --with github.com/caddyserver/forwardproxy@caddy2=github.com/klzgrad/forwardproxy@naive
```

## Other

```
# chmod +x caddy
$ ./caddy list-modules
$ caddy version
$ mv caddy /usr/local/bin/
```

### Keep caddy running as Linux service
```
cat > /etc/systemd/system/caddy.service <<EOF
[Unit]
Description=Caddy
Documentation=https://caddyserver.com/docs/
After=network.target network-online.target
Requires=network-online.target

[Service]
Type=notify
User=root
Group=root
ExecStart=/usr/local/bin/caddy run --environ --config /etc/caddy/caddy.json //如是Caddyfile配置，直接修改caddy.json为Caddyfile即可。
ExecReload=/usr/local/bin/caddy reload --config /etc/caddy/caddy.json --force //如是Caddyfile配置，直接修改caddy.json为Caddyfile即可。
TimeoutStopSec=5s
LimitNOFILE=1048576
LimitNPROC=512
PrivateTmp=true
ProtectSystem=full
AmbientCapabilities=CAP_NET_BIND_SERVICE

[Install]
WantedBy=multi-user.target
EOF
```

```
$ systemctl daemon-reload
$ systemctl enable --now caddy
$ systemctl stop caddy
$ systemctl status caddy
$ systemctl reload caddy
$ systemctl edit caddy
```

1. [caddyserver/xcaddy](https://github.com/caddyserver/xcaddy)
2. [imgk/caddy-trojan](https://github.com/imgk/caddy-trojan)
3. [klzgrad/forwardproxy](https://github.com/klzgrad/forwardproxy)
4. [caddy-dns/cloudflare](https://github.com/caddy-dns/cloudflare)
5. [mholt/caddy-l4-Layer 4 (TCP/UDP) app for Caddy](https://github.com/mholt/caddy-l4)
6. [mholt/caddy-dynamicdns](https://github.com/mholt/caddy-dynamicdns)
7. [mholt/caddy-webdav](https://github.com/mholt/caddy-webdav)
8. [pieterlouw/caddy-net](https://github.com/pieterlouw/caddy-net)
9. [mastercactapus/caddy2-proxyprotocol](https://github.com/mastercactapus/caddy2-proxyprotocol)
