# Subnetting IP Addresses
**Notes before beginning**:
+ Understanding binary will help in understanding the concepts of this guide. Please read the binary.md guide posted above.

+ I use the term **octet** to refer to each portion of an IP address. So in 192.168.1.0, 168 would be the second octet and 1 would be the third. You could use the term **byte** if you want, but I prefer octet.

## What is subnetting?
In short, subnetting is splitting a network into smaller sub-networks. At a lower level, subnetting is splitting up a set of IP addresses so each set refers to a different subnetwork. This can be done for many different reasons - access control is one example - but the why do it is outside the scope of this guide. This guide aims at helping you understand the subnet mask, the CIDR notation, and to compute the relevant subnet information from an IP/CIDR notation.

## What is the subnet mask?
The subnet mask is a 32-bit (4-byte) value, which - similar to an IP address - is typically displayed in an a.b.c.d format. The subnet mask differentiates between the **host portion** of the IP address, which is the portion that can be used to identify host machines on the subnet, and the **network portion**, which is the portion referring to the upstream network and subnet. This helps a machine sending a packet to another maching determine whether the recipient machine is on the same (sub)network. 

The subnet mask means little by itself. Instead, it is meant to be paired with an IP address to tell you what portions of that IP address mean what. This is because **the network portion of the subnet mask is where the bits of the subnet mask equal 1**. This means that if you compare two IP addresses, and one is different from the other only where the subnet mask is zero, they should be on the same subnetwork. If, however, they differ wherever the subnet mask is 1, they should be on different subnetworks. Because of this, viewing the IP addresses and subnet masks in binary should make understanding the concept easier.

Take the following example:
```
IP Address1: 192.168.1.25
Subnet Mask: 255.255.255.0

Convery to binary:
IP Address1: 11000000.10101000.00000001.00011001
Subnet Mask: 11111111.11111111.11111111.00000000

Network portion of IP is where subnet mask is a 1.
Host portion is where subnet mask is a 0.

Network:  11000000.10101000.00000001.00000000
Host:     00000000.00000000.00000000.00011001

Network ID: 192.168.1.0
```
This means all of the first three octets contain our network address, and the last octet is reserved solely for host IP addresses (with the exception of two, which I will address below). So any IP address between 192.168.1.0 and 192.168.1.255 is on this machine's subnetwork. But if you change the third octet to 192.168.2.25, you are now dealing with a different subnetwork. This is because every bit in the first three octets is used to identify our subnetwork, and now that's changed from 00000001 to 00000010 as shown below.

```
IP Address1: 192.168.1.25
Subnet Mask: 255.255.255.0

IP2: 192.168.2.25
IP3: 192.168.1.254

IP1 Binary: 11000000.10101000.00000001.00011001
SNM Binary: 11111111.11111111.11111111.00000000
IP2 Binary: 11000000.10101000.00000010.00011001
IP3 Binary: 11000000.10101000.00000001.11111110
```
Notice where subnet mask equals 1, IP1 and IP2 do not match. They are, therefore on different subnetworks. IP3, though having many more bits differing from IP1, is exactly the same where the subnet mask is equal to 1. They are, thus, on the same subnetwork.

If, for example, our subnet mask was 255.255.0.0, then our network ID would be 192.168.0.0 and the remaining two octets would be dedicated to host addresses. This would mean IP1, IP2 and IP3 *would* all be on the same subnetwork. However, 192.169.1.1 would be on a different subnetwork from all three.

Before we go on, I should point out the every subnetwork has both a **network ID**, which is the very first IP of the subnet (all host bits set to zero), and a **broadcast ID**, which is the very last value (all host bits set to one). So while the 255.255.255.0 subnet mentioned above allows for 256 IP addresses, ranging from 0 to 255, two addresses per subnet will be reserved. This means 254 IP addresses are available to be assigned to host machines.

## Subnet mask characteristics
Aside from what it means, the most important things to know about a subnet mask are:
1. The network portion is filled in from left to right, and
2. The ones making up the network portion must be consecutive.

In other words, 1101111.... is not a valid subnet mask. Once the ones stop, the rest will be all zeros and will identify the host postion of the associated IP address. Understanding this allows us to more easily understand **CIDR notation**.

## CIDR notation
If you've seen an IP address followed by /x, where x is a number between 1 and 32, you've seen CIDR notation. The number after the / means **how many ones are in the subnet mask**. That's it. That's all there is to CIDR. Well, except for one instance which is unique unto itself: x.x.x.x/32 means the IP address x.x.x.x. It is not used to denote a subnet. This is seen in access control lists, IDS/IPS rules, etc.

The easiest way to remember CIDR values is by understanding that every multiple of 8 (/8, /16, /24, /32) means another octet full of ones. In other words:
```
/8  = 255.0.0.0
/16 = 255.255.0.0
/24 = 255.255.255.0
/32 = 255.255.255.255
```
But you will see other CIDR numbers as well, such as /19 or /27. Just as noted above, this means the subnet mask contains 19 or 27 consecutive ones followed by zeros until reaching a total of 32 bits. Converting the subnet to decimal notation is simple because it's just a binary to decimal conversion.
```
Using the same IP1 as above:
IP and CIDR:   192.168.1.25/19
IP in binary:  11000000.10101000.00000001.00011001
Subnet binary: 11111111.11111111.11100000.00000000
Subnet in dec:    255  .   255  .  x     . 0

To solve for x, you add up the bit values. 128 + 64 + 32 = 224
Subnet in dec: 255.255.224.0

IP and CIDR:   192.168.1.25/27
IP in binary:  11000000.10101000.00000001.00011001
Subnet binary: 11111111.11111111.11111111.11100000
```
The first three bits of octet 4 add up to the same as the first three bits in octet 3, which is 224. The only difference here is we have 3 octets of all ones. So the subnet mask here for /27 is 255.255.255.224

Note that /19 is three more than /16, and /27 is three more than /24. This pattern is the same as the /8, /16, /24 and /32 pattern above, and it holds for all numbers as follows:
```
/1 to /8:   Number of ones in the first octet
            Masks are 128.0.0.0 to 255.0.0.0

/9 to /16:  All ones in first octet. N - 8 ones in second octet
            Masks are 255.128.0.0 to 255.255.0.0

/17 to /24: All ones in first two octets. N - 16 ones in third
            Masks are 255.255.128.0 to 255.255.255.0

/25 to /32: All ones in first three octets. N - 24 ones in fourth
            Masks are 255.255.255.128 to 255.255.255.255
```
In a way, this can be seen as kind of a loop. Every CIDR /x adds one bit to the mask from left to right, increasing the decimal representation of the octet by the bit value being set, until it hits a multiple of eight, at which point the octet is maxed out. After that it starts over again at the next octet. Anyone whose written a program involving modulo operators should be able to relate this quickly to that concept.

## Calculating subnet values
For each subnetwork, you will want to understand a few key pieces of information for most exams:
1. The network ID
2. The broadcast ID
3. How many subnetworks are created
4. How many total IP addresses on each subnet
5. How many host IP addresses on each subnet

### Network and Broadcast IDs
The network ID was already covered. You keep the network bits undisturbed, and set all host bits to zero, and you have your network ID. The broadcast ID is taking the same concept, but setting all host bits to one instead. Using the /19 CIDR from the example above:
```
IP and CIDR:   192.168.1.25/19
IP in binary:  11000000.10101000.00000001.00011001
Subnet binary: 11111111.11111111.11100000.00000000

For network ID, keep all bits in the IP where subnet is 1. Set all other bits to zero.

Network ID: 11000000.10101000.00000000.00000000
ID to dec:  192.168.0.0

And for broadcast ID we set all host bits to 1.

Broadcast:  11000000.10101000.00011111.11111111
BC to dec:  192.168.31.255
```
So this subnetwork has IP addresses ranging from 192.168.0.0 to 192.168.31.255, but the first and last are reserved as the network ID and broadcast ID.

### How many subnets are created?
You might see a question on, for example, the Network+ exam asking you to create a certain number of subnetworks. If you think of each octet separately, the number of subnetworks created is 2^N where N is the number of subnet bits in the octet. This goes along with the idea that the subnet mask is filled in from left to right.

As we know, a single bit gives you two values: 0 and 1. CIDR of /1, /9, /17 or /25 mean one subnet bit goes into the corresponding octet. So this would create two subnetworks: one where the subnet bit in the octet is set to zero, and one where it is set to one. Two bits in the octet (/2, /10, /18, /26) gives you four options (00, 01, 10, 11) and so on. To view it more easily:
```
1/9/17/25:  One subnet bit set in octet. 2^1 = 2
2/10/18/26: Two subnet bits set. 2^2 = 4
3/11/19/27: Three bits = 8
4/12/20/28: Four bits = 16
...
8/16/24/32: Eight bits = 256
```

### How many IP addresses per subnet?
With 32 total bits for an IP address, and CIDR bits dedicated to the network + subnet address, this leaves 32 - CIDR bits for IP addresses. Thus, each subnet will have 2^(32 - CIDR) IP addresses. Note: this means it will double each time CIDR decreases by one, as shown below:
```
/32 = 2 ^ (32 - 32) = 2 ^ 0 = 1 IP address
/31 = 2 ^ (32 - 31) = 2 ^ 1 = 2 IP addresses
/30 = 2 ^ (32 - 30) = 2 ^ 2 = 4 IP addresses
/29 = 8
/28 = 16
/27 = 32
...
/24 = 256
/25 = 512
...
/19 = 8192
```
This can also be computed a different way. The total value of all of the bits covering all four octets is 256^4, which makes sense because each octet has 256 combinations. So to figure this out, you:
1. Raise 256^N, where N is the number of subnet octets not equal to 255, and
2. Divide by total number of subnets created

Let's show an example using /19:
```
/19 gives a subnet of 255.255.224.0, so two non-255 octets
256^2 = 65536 total IP addresses covered by these octets
/19 puts 3 bits into the third octet, which means it creates 8 subnetworks.
65536 / 8 = 8192 IP addresses per subnet.

Validation using the 192.168.1.25/19 address used above:
Gave us IP addresses ranging from 192.168.0.0 to 192.168.31.255, which means:

192.168.0.0 to 192.168.0.255 [256]
192.168.1.0 to 192.168.1.255 [256]
...
192.168.31.0 to 192.168.31.255 [256]

32 * 256 = 8192 addresses in this subnetwork.
```

### What are the subnetwork addresses?
Finally, we have to ask how the individual subnetworks are broken down. We know the example above ended at 192.168.31.255, but what happens after that? What are the addresses of the other subnetworks? The short answer is: you simply have the same pattern extending until you reach 192.168.255.255.

Let's go back to the subnet bits example with /19, and look at the possible values this gives for our third octet.
```
IP and CIDR:   192.168.1.25/19
IP in binary:  11000000.10101000.00000001.00011001
Subnet binary: 11111111.11111111.11100000.00000000
Network ID   : 11000000.10101000.00000000.00000000

Zooming in on the third octet, which is the first non-255 subnet octet, we see
IP of network ID: 00000000
Subnet mask:      11100000

Before we said changing any of the IP bits associated with a zero in the subnetmask 
means changing host addresses, but changing the bits associated with a 1 in the subnet 
mask means changing the subnetwork address. This means the following values are 
subnetwork ID values for the third octet:

00000000 = 0  (the one seen above)
00100000 = 32 (.32.0 is the first value after .31.255)
01000000 = 64
01100000 = 96
10000000 = 128
10100000 = 160
11000000 = 196
11100000 = 224

And that's where the pattern ends because we have only 3 network bits to change.

Notice a pattern here? The network ID keeps increasing by 32, which just so happens 
to be the bit value of the lowest bit associated with the network ID. So this means 
our subnets created by the /19 CIDR are as follows:

192.168.0.0   - 192.168.31.255
192.168.32.0  - 192.168.63.255
192.168.64.0  - 192.168.95.255
192.168.96.0  - 192.168.127.255
192.168.128.0 - 192.168.159.255
192.168.160.0 - 192.168.191.255
192.168.192.0 - 192.168.223.255
192.168.224.0 - 192.168.255.255
```
Similarly with a /17, which puts one bit in the non-255 subnet octet, the value of the network id bit is 128, and 2^1 is 2. So this would create two subnetworks starting in the third octet witn 65536 / 2 = 32768 IP addresses per subnet, and 32766 host IPs. Subnet 1 goes from 192.168.0.0 to 192.168.127.255, and subnet 2 goes from 192.168.128.0 to 192.168.255.255.

Finally, an example in the fourth octet using /26:
```
IP and CIDR:   192.168.1.25/26
IP in binary:  11000000.10101000.00000001.00011001
Subnet binary: 11111111.11111111.11111111.11000000

- Non-255 octet = octet 4, which means 256 total IP addresses
- 2 subnet bits in the non-255 octet, which means 2^2 = 4 subnetworks
- 256 / 4 = 64 IP addresses per subnetwork (minus 2 = 62 of which can be host IDs)
- Lowest network bit value in octet = 64

Network IDs           Broadcast IDs
192.168.1.0           192.168.1.63
192.168.1.64          192.168.1.127
192.168.1.128         192.168.1.191
192.168.1.192         192.168.1.255
```
And using the second octet with /13
```
IP and CIDR:   192.168.1.25/13
IP in binary:  11000000.10101000.00000001.00011001
Subnet binary: 11111111.11111000.00000000.00000000

- Non-255 octet = octet 2, which means 256^3 = 16,777,216 total IP addresses
- 5 subnet bits in the non-255 octet, which means 2^5 = 32 subnetworks
- 16,777,216 / 32 = 524,288 IP addresses per subnetwork [524,286 host]
- Lowest network bit value in octet = 8

Network IDs           Broadcast IDs
192.0.0.0             192.7.255.255
192.8.0.0             192.15.255.255
192.16.0.0            192.23.255.255
...
192.224.0.0           192.231.255.255
192.232.0.0	          192.239.255.255
192.240.0.0	          192.247.255.255
192.248.0.0	          192.255.255.255
```
## Final tips
When you see CIDR notation, unless you're being asked a specific question about the IP address in the notation, do not concern yourself with the actual IP address portion of the notation beyond determining the ID of the network being subnetted.

For instance, I repeatedly used 192.168.1.25 in examples throughout this explanation, but 192.168.1.25/24 would mean the same thing as 192.168.1.1/24. That's because the first non-255 subnet octet is the last one, so the network we are solving for is 192.168.1.X in both instances. Similarly, 192.168.1.25/19 means the same thing as 192.168.0.1/19 because the first non-255 subnet octet is octet 3, so the network we are solving for is 192.168.X.Y.

So, if you're being asked to, for example, identify the network ID or broadcast ID, you would need to use a portion of the IP address supplied. If you're being asked whether the IP address is a valid one on the subnet, then you need to use the IP supplied. If you're being asked how many subnets are created by the CIDR, the actual IP supplied is largely useless. Do not let it distract you.
