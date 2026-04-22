# kuties

k4s is short for KUTIES, which is Kubernetes plus U-root utiliTIES. 

![The KUTIES logo](kuties.svg)

## How it works

Kubernetes tools (e.g. runc) are integrated via the
[gobusybox](https://github.com/u-root/gobusybox), 
which converts Go programs to Go packages, and then compiles them as one
program (this process takes around 20 seconds). 

In standard u-root, this allows over 180 commands to be built into a single binary smaller than 20MiB. 

## Requirements

To be used in k4s, Go programs must be Go buildable, statically linked, and hence pure Go.

This will require us to revisit projects, such as k3s and runc, that have evolved over the years and brought in
dependencies on external, non-Go libraries (e.g. libpathrs); external programs (e.g. nsenter); 
systemd; and embedded binaries.
These dependencies can be made compile-time dependent. 

## On u-root

u-root is a core firmware component for 10s of millions of server systems around the world, providing a 
compact, safe userland for LinuxBoot systems. 

It has been part of the standard server firmware at Google since 2021; ByteDance adopted it a few years later.
k4s will allow us to integrate Kubernetes capabilities into firmware images, enabling the creation of diskless Kubernetes
appliances.
