---
title: "Koji build docker images"
date: 2023-07-12T13:35:27+02:00
draft: false
---

### Build docker images using koji

Koji is fedora projects build system its used to build packages and other artifacts for Fedora Linux releases. Docker image is basically a specific snapshot of the state of some machine defined by Docker/Container file. In koji we cannot dirrectly build from COntainer files so we need to translate the file into a kickstart. For this example we will use https://pagure.io/fedora-kickstarts/blob/main/f/fedora-container-toolbox.ks

We will use ```$koji image-build``` command, it can get really long so for cleanliness we will use a config file to pass all options to the koji task.

Here is example of such file:
```
[image-build]
name = Fedora-Container-Toolbox
version = Rawhide
target = f39
install_tree =  https://kojipkgs.fedoraproject.org/compose/rawhide/Fedora-Rawhide-20230712.n.0/compose/Everything/$arch/os
arches =  aarch64,ppc64le,s390x,x86_64
release = 20230712.n.0
format = docker
distro = Fedora-22
repo = https://kojipkgs.fedoraproject.org/compose/rawhide/Fedora-Rawhide-20230712.n.0/compose/Everything/$arch/os
disk_size = 5
kickstart = fedora-container-toolbox.ks
ksurl = git+https://pagure.io/fedora-kickstarts.git?#b41eb6bd15603aca23952f89904b0d771ada5445
optional_arches = aarch64,ppc64le,s390x,x86_64

[factory-parameters]
dockerversion = 1.10.1
docker_cmd = [ "/bin/bash" ]
docker_env = [ "DISTTAG=f39container", "FGC=f39", "container=oci" ]
docker_label = { "name": "fedora", "license": "MIT", "vendor": "Fedora Project", "version": "39"}

```

The koji command will look like this:
```$ koji image-build --scratch --config=fedora-container-toolbox.conf ```