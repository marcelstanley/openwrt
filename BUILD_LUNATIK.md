<!-- markdownlint-disable MD013 -->
# Build OpenWrt with Lunatik

## Clone openwrt fork

Clone branch `marcel_feed_openwrt_v23.05.5` from [this openwrt fork](https://github.com/marcelstanley/openwrt).
This branch adds a custom feed to the tree, which is available at <https://github.com/marcelstanley/openwrt_feed>.

```sh
git clone --single-branch --branch marcel_feed_openwrt_v23.05.5 https://github.com/marcelstanley/openwrt
```

> [!NOTE]
> This branch relies on a custom feed on a custom [OpenWrt package feed for luainkernel](https://github.com/marcelstanley/openwrt_feed/blob/main/README.md) included in [`openwrt/feeds.conf.default`](.openwrt/feeds.conf.default).

## Build

### Install packages

Update and install all packages from the feeds:

```sh
cd openwrt
./scripts/feeds udpate -a
./scripts/feeds install -a
```

> [!TIP]
> During development cycles, the feeds might get outdated.
> In such cases, make sure to force package installation by `./scripts/feeds install -a -f`.
> Check [Working with Feeds](https://openwrt.org/docs/guide-developer/feeds#working_with_feeds) for more about feeds.

### Configure buildroot

Setup the target platform configuration as usual but make sure to select `kmod-lunatik` under the following path:

```txt
Kernel modules --->
    Other modules  --->
        <*> kmod-lunatik
```

> [!IMPORTANT]  
> Some sample configuration for various platforms are available under [`./sample_configs`](./sample_configs).

## Build the image

```sh
time make -j$(nproc) V=s
```
