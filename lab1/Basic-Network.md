# Lab 1: Basic Network Setup

## Objective

To configure static and dynamic routing between two networks.

## Topology

In this case, I used three 1841 routers, which I named R1, R2, and R3. I also used two PCs, which I designated P1 and P2, respectively.

![Screenshot (137).png](images/8fd7fa70-621a-4e3a-8ca2-392a991880cd.png)

## Device Configuration Table

| **Device** | **Interface** | **IP Address** | **Subnet Mask** | Connected To | Network |
| ---------- | ------------- | -------------- | --------------- | ------------ | ------- |
| P1         | FastEthernet0 | 10.0.0.2       | 255.0.0.0       | R1 f0/0      | A       |
| R1         | f0/0          | 10.0.0.1       | 255.0.0.0       | P1           | A       |
| R1         | f0/1          | 20.0.0.1       | 255.0.0.0       | R2 f0/0      | B       |
| R2         | f0/0          | 20.0.0.2       | 255.0.0.0       | R1 f0/1      | B       |
| R2         | f0/1          | 30.0.0.1       | 255.0.0.0       | R3 f0/0      | C       |
| R3         | f0/0          | 30.0.0.2       | 255.0.0.0       | R2 f0/1      | C       |
| R3         | f0/1          | 40.0.0.1       | 255.0.0.0       | P2           | D       |
| P2         | FastEthernet0 | 40.0.0.2       | 255.0.0.0       | R3 f0/1      | D       |

### Step 1: Cable connection

Use copper cross cables to link all of the PCs and routers.

![Screenshot (138).png](images/988961ac-a48d-4aaf-ba9b-e4bf88608484.png)

I've connected all of the PCs and routers. However, we are unable to send data from P1 to P2 because they are not yet configured.

![Screenshot (139).png](images/b29ca8ea-3b2b-47fc-bbaa-5614fcfe0902.png)

### Step 2: PC IP Configuration

Click P1, then go to Desktop > IP Configuration, and enter the IPv4 address, Subnet Mask, and Default Gateway as shown in the picture.

![Screenshot (140).png](images/efaa22f9-5c66-4859-a01b-30bc75ea8fa1.png)

Next, click P2, then go to Desktop > IP Configuration, and enter the IPv4 address, Subnet Mask, and Default Gateway as shown in the picture.

![Screenshot (141).png](images/e4c38fb0-d219-41cf-af8f-68904a1394e8.png)

### Step 3: Start the routers

When you click R1 and navigate to CLI, the following appears. To enter the user mode, just type ‘no’ and hit ENTER.

![Screenshot (143).png](images/acb7817b-5054-4d40-b985-501b6a9f2857.png)

## Key Commands and Configurations

User Mode (Router>)

```bash
enable
```

Priviledge Mode (Router#)

```bash
configure terminal | config terminal | conf t
```

(OPTIONAL): We can change Router(config)# to R1(config)# and add a password using the following commands:

```bash
hostname R1
enable password pass1example
```

To enter the interface use

```bash
interface f0/0 | int f0/1
```

After entering IP address, use

```bash
no shutdown
```

And use the following command to exit the interface

```bash
exit
```

Save the config file in NVRAM by using the command after providing the IP addresses.

You can only use this command after exiting the Priviledge mode using the above “exit” command.

```bash
copy run start
```

## Router IP Configuration

R1(config-if)#:

```bash
ip address 10.0.0.1 255.0.0.0
ip address 20.0.0.1 255.0.0.0
```

![Screenshot (144).png](images/2fe32df4-5e39-4550-b8fa-e76831b7a06f.png)

R2(config-if)#:

```bash
ip address 20.0.0.2 255.0.0.0
ip address 30.0.0.1 255.0.0.0
```

![Screenshot (145).png](images/14cec3a3-777e-4beb-8c96-8c15bbed93ec.png)

R3(config-if)#:

```bash
ip address 30.0.0.2 255.0.0.0
ip address 40.0.0.1 255.0.0.0
```

![Screenshot (146).png](images/11507ca0-633a-4c9a-924a-eb32e9df7cc4.png)

Then, since the connection is now green, you can see that every device has joined properly.

![Screenshot (147).png](images/aee47202-e760-41e3-b043-ec253228365b.png)

Let's attempt to ping P2 from P1. Just click on P1 and navigate to Command Prompt.

```bash
ping 40.0.0.2
```

![Screenshot (148).png](images/b5e77310-25d6-4873-a51b-101bd9d358a8.png)

Because P1 lacks a gateway to reach P2, it will respond with "Request timed out.”

## Static Routing

Let's see the IP addresses in R1 by using the command

```bash
show ip route
```

![Screenshot (149).png](images/60cece8f-beb3-4c89-a91d-b763e3e8610b.png)

We will assign IP addresses to each router individually in order to add the gateways to every router. To accomplish that, follow the instructions in the image.

R1(config-if)#:

```bash
ip route 30.0.0.0 255.0.0.0 20.0.0.2
ip route 40.0.0.0 255.0.0.0 20.0.0.2
```

![Screenshot (150).png](images/526f5996-f02b-42ff-ad63-3d9dd6db2407.png)

R2(config-if)#::

```bash
ip route 10.0.0.0 255.0.0.0 20.0.0.1
ip route 40.0.0.0 255.0.0.0 30.0.0.2
```

![Screenshot (151).png](images/e6d3829b-a779-4201-8848-1ce1546b6cf8.png)

R3(config-if)#:

```bash
ip route 10.0.0.0 255.0.0.0 30.0.0.1
ip route 20.0.0.0 255.0.0.0 30.0.0.1
```

![Screenshot (152).png](images/4b2b21a0-b547-4c8a-939f-b2457fa36deb.png)

Now let's try to ping P1 from P2 using the command

```bash
ping 10.0.0.2
```

![Screenshot (153).png](images/77d2c18d-78f2-4f4b-a8ff-b3efd1cc9f5c.png)

We've successfully set up a static connection between the routers and the PCs.

## Dynamic Routing

This is limited to the topology's edge routers, which in this instance are R1 and R3.

R1(config-if)#:

```bash
no ip route 30.0.0.0 255.0.0.0 20.0.0.2
no ip route 40.0.0.0 255.0.0.0 20.0.0.2

#add default static route
ip route 0.0.0.0 0.0.0.0 20.0.0.2
```

![Screenshot (154).png](images/25219888-87ec-4ac2-879d-c28763788292.png)

R3(config-if)#:

```bash
no ip route 10.0.0.0 255.0.0.0 30.0.0.1
no ip route 20.0.0.0 255.0.0.0 30.0.0.1

#add default static route
ip route 0.0.0.0 0.0.0.0 30.0.0.1
```

![Screenshot (155).png](images/2f8fc10e-640e-4d60-8281-901343ac4192.png)
