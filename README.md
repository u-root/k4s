# kuties

k4s is short for KUTIES, which is Kubernetes plus U-root utiliTIES. 

![The KUTIES logo](kuties.svg)

## How it works

Kubernetes tools (e.g. runc) are integrated via the
[gobusybox](https://github.com/u-root/gobusybox), 
which converts Go programs to Go packages, and then compiles them as one
program (this process takes around 20 seconds). 

In standard [u-root](https://u-root.org/), this allows over 180 commands to be
built into a single binary smaller than 20MiB. 

## Requirements

To be used in k4s, Go programs must be Go buildable, statically linked, and hence pure Go.

This will require us to revisit projects, such as k3s and runc, that have evolved over the years and brought in
dependencies on external, non-Go libraries (e.g. [libpathrs](https://github.com/cyphar/libpathrs/), used in runc);
external non-Go programs (e.g. [nsenter](https://github.com/opencontainers/runc/tree/main/libcontainer/nsenter)); 
systemd; and embedded binaries.
These dependencies can be made compile-time dependent. 

## Kubernetes components

As described in the architecture overview, Kubernetes consists of multiple
[components](https://kubernetes.io/docs/concepts/overview/components/).
Even projects claiming to be _single binary_ are, in fact, embedding multiple
other binaries. They effectively contain multiple Go runtimes, using
techniques such as the [`//go embed` directive](https://pkg.go.dev/embed).

However, with u-root, we build file system images, typically cpio, to contain
symlinks to one BusyBox-style binary with a single, shared Go runtime.

In the following, we look at what kinds of components we need, what multiple
Kubernetes distros use, and which implementations are the best candidates for
inclusion in k4s. The main criteria are whether they are pure Go, and how they
affect the eventual binary size. As usual, we split between control plane and
[nodes](https://kubernetes.io/docs/concepts/architecture/#node-components).

Note that we build [minimal](https://eevans.co/blog/minimum-viable-kubernetes/)
systems, so we omit optional components and addons, because we need the binary
to fit in a flash part.

### Control Plane

| kind              | implementations         | notes |
| ----------------- | ----------------------- | ----- |
| front-end (API)   | kube-apiserver          |       |
| backing store     | etcd                    |       |
| scheduler         | kube-scheduler          |       |
| supervisor        | kube-controller-manager |       |

### Node

| kind              | implementations         | notes |
| ----------------- | ----------------------- | ----- |
| runner agent      | kubelet                 |       |
| container runtime | CRI-O, containerd       |       |

### k0s

- [architecture](https://docs.k0sproject.io/stable/architecture/)

more tbd

### k3s

tbd

### KubeSolo

tbd

### Talos

tbd

## On u-root

u-root is a core firmware component for 10s of millions of server systems around the world, providing a 
compact, safe userland for LinuxBoot systems. 

It has been part of the standard server firmware at Google since 2021; ByteDance adopted it a few years later.
k4s will allow us to integrate Kubernetes capabilities into firmware images, enabling the creation of diskless Kubernetes
appliances.
