# tor-box

This is an image/container build for a transparently tor proxied host

## build

This uses [mkosi](https://github.com/systemd/mkosi) which is packaged for some distros, or easy-enough to install from source.

```shell
git clone git://github.com/vbatts/tor-box
cd ./tor-box
sudo mkosi
```

## Usage

```shell
systemd-nspawn -bni $(pwd)/image.raw -M tor-box
```

This boots the container up, but gives you a login prompt with no root passwd set.
The container is on private network with a veth to the host.
This expects `systemd-networkd.service` on the host to be configured and running.
Also, this may conflict with the `firewalld.service`, so disable that or tamper at your own will.

To get a shell inside this container, do:

```shell
machinectl shell tor-box
Connected to machine tor-box. Press ^] three times within 1s to exit session.
sh-4.4# curl ifconfig.co/json
{"ip":"91.223.82.156","ip_decimal":1541362332,"country":"Netherlands","city":"Unknown","hostname":"hosted-by.iws.co"}sh-4.4#
```

From here you can run `passwd` if you'd like, to set a password for root.


## Start on boot

The resulting `image.raw` and `image.nspawn` files can be installed and enabled like services on a host.

```shell
sudo mv ./image.raw /var/lib/machines/tor-box.raw
sudo mkdir -p /etc/systemd/nspawn
sudo mv ./image.nspawn /etc/systemd/nspawn/tor-box.raw.nspawn
sudo sudo systemctl start systemd-nspawn@tor-box.raw
sudo sudo systemctl status systemd-nspawn@tor-box.raw
```

Likewise you can enable it to start on-boot with:

```shell
sudo sudo systemctl enable systemd-nspawn@tor-box.raw
```

(this requires `systemd-machined` so `systemctl enable --now machines.target` on the host.)


## Props and References

* https://blog.jessfraz.com/post/routing-traffic-through-tor-docker-container/
* https://trac.torproject.org/projects/tor/wiki/doc/TransparentProxy
