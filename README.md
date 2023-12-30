# linux-ns-networking-1


## Linux Network Stack

![network-stack](https://lab-bucket.s3.brilliant.com.bd/labthumbnail/482ee565-aff7-4853-90de-13401024239f.png)

In Linux networking, within the Linux network stack, routes define traffic paths, iptables configures packet filtering, lo is a local loopback interface for testing, and eth0 is the primary Ethernet interface for external connections. Let's inspect the network stack, in short.

Network interfaces allow us to establish communication between a network and a device.
```bash
ip link list
```

![network-interfaces](https://lab-bucket.s3.brilliant.com.bd/labthumbnail/c6273201-9f34-47f5-b6ea-639f86afd653.png)

These commands provide information about the configuration and status of the `ens3` (my primary Ethernet interface) and docker0 (Docker bridge) interfaces. In the case of running Docker containers, the `docker0` bridge is used for communication between them and the host system.

`lo` is the loopback interface, allowing local network communication within a device without external network involvement. Verify the loopback interface is up

```bash
ifconfig lo
```

![loopback-interface](https://lab-bucket.s3.brilliant.com.bd/labthumbnail/33dea05f-52b0-4cf1-b5e0-147b9569701a.png)

A `route` in networking specifies the path for network traffic from source to destination. View the routing table:

```bash
ip route show
```
![route](https://lab-bucket.s3.brilliant.com.bd/labthumbnail/9d366861-c6a9-4dc1-8965-29712554f6a8.png)

`iptables` is a user-space utility for configuring packet filter rules in the Linux kernel's Netfilter framework. View `iptables` rules:

```bash
iptables -L
```
![iptables](https://lab-bucket.s3.brilliant.com.bd/labthumbnail/1c910ea8-e9ed-44b4-94e4-da755eb561fa.png)


## Create Custom Network Namespace

Let's create a custom namespace using `ip netns add` utiliy.

![ns](https://lab-bucket.s3.brilliant.com.bd/labthumbnail/d90d5bb0-0f7b-408e-89d8-a1403da7d356.png)


```bash
sudo ip netns add ryuzaki
sudo ip netns list
```

![ns](https://lab-bucket.s3.brilliant.com.bd/labthumbnail/aa03c245-42f3-48d5-90bb-67c9183038a3.png)

Now, entering a network namespace in Linux:

`ip netns exec`

It is part of the iproute2 package and is often used for managing network namespaces. 

```bash
sudo ip netns exec ryuzaki bash
```

`nsenter`

The `nsenter` utility is commonly used to enter into namespaces in Linux, including network namespaces.

```bash
sudo nsenter --net=/var/run/netns/ryuzaki bash
```

![ns-exec](https://lab-bucket.s3.brilliant.com.bd/labthumbnail/4c97214b-8977-48d2-9739-ff1790336f58.png)


Now, check the network interfaces inside the new ns.

```bash
ip link show
```

![ip-link-list](https://lab-bucket.s3.brilliant.com.bd/labthumbnail/f7a0695f-9fea-4261-9fe8-c985ecf7aa7b.png)

Let's check for `iptable` rules for custom ns.

![ns-iptables](https://lab-bucket.s3.brilliant.com.bd/labthumbnail/21eecbb6-60d8-4181-bab8-f788259564e2.png)

```bash
ifconfig lo
```
![lo](https://lab-bucket.s3.brilliant.com.bd/labthumbnail/8d4d30c7-f834-482c-b342-7c4aa8aa09b1.png)