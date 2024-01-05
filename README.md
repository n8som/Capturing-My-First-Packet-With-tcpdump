# Capturing-My-First-Packet-With-tcpdump

<h2>Activity overview</h2>

As a security analyst, it’s important to know how to capture and filter network traffic in a Linux environment. I’ll also need to know the basic concepts associated with network interfaces.

In this lab activity, I’ll perform tasks associated with using tcpdump to capture network traffic. I’ll capture the data in a packet capture (p-cap) file and then examine the contents of the captured packet data to focus on specific types of traffic.

<h2>Scenario</h2>

I’m a network analyst who needs to use ```tcpdump``` to capture and analyze live network traffic from a Linux virtual machine.

The lab starts with my user account, called analyst, already logged in to a Linux terminal.

My Linux user's home directory contains a sample packet capture file that I will use at the end of the lab to answer a few questions about the network traffic that it contains.

Here’s how I’ll do this: First, I’ll identify network interfaces to capture network packet data. Second, I’ll use ```tcpdump``` to filter live network traffic. Third, I’ll capture network traffic using ```tcpdump```. Finally, I’ll filter the captured packet data.

<h2>Task 1. Identify network interfaces</h2>

In this task, I must identify the network interfaces that can be used to capture network packet data.

1. Use ```ifconfig``` to identify the available interfaces:

```sudo ifconfig```

This command returns output similar to the following:

![image](https://github.com/n8som/Capturing-My-First-Packet-With-tcpdump/assets/110139109/67c56202-e196-4fc2-a9a7-e692b122245c)

The Ethernet network interface is identified by the entry with the ```eth``` prefix.

So, in this lab, I'll use ```eth0``` as the interface that I will capture network packet data in the following tasks.

2. Use ```tcpdump``` to identify the interface options available for packet capture:

```sudo tcpdump -D```

This command will also allow me to identify which network interfaces are available. This may be useful on systems that do not include the ```ifconfig``` command.

![image](https://github.com/n8som/Capturing-My-First-Packet-With-tcpdump/assets/110139109/f2894d0c-9954-4036-bb48-09e2ec4f8996)

<h2>Task 2. Inspect the network traffic of a network interface with tcpdump</h2>

In this task, I must use ```tcpdump``` to filter live network packet traffic on an interface.

- Filter live network packet data from the ```eth0``` interface with ```tcpdump```:

```sudo tcpdump -i eth0 -v -c5```

This command will run ```tcpdump``` with the following options:

- ```-i eth0```: Capture data specifically from the eth0 interface.
- ```-v```: Display detailed packet data.
- ```-c5```: Capture 5 packets of data.

Now, let's take a detailed look at the packet information that this command has returned.

Some of my packet traffic data will be similar to the following:

![image](https://github.com/n8som/Capturing-My-First-Packet-With-tcpdump/assets/110139109/00c99c1c-2c76-4d25-af0a-3291804c1c45)

The specific packet data in my lab may be in a different order and may even be for entirely different types of network traffic. The specific details, such as system names, ports, and checksums, will be different. I can run this command again to get different snapshots to outline how data changes between packets.

<h3>Exploring network packet details</h3>

In this example, I’ll identify some of the properties that ```tcpdump``` outputs for the packet capture data I’ve just seen.

1. In the example data at the start of the packet output, ```tcpdump``` reported that it was listening on the ```eth0``` interface, and it provided information on the link type and the capture size in bytes:

![image](https://github.com/n8som/Capturing-My-First-Packet-With-tcpdump/assets/110139109/0f922871-11c7-427c-ab7e-2581ba7793a4)

2. On the next line, the first field is the packet's timestamp, followed by the protocol type, IP:

![image](https://github.com/n8som/Capturing-My-First-Packet-With-tcpdump/assets/110139109/ca9ed6a2-91e6-4dbc-b61b-7fad0f845cea)

3. The verbose option, ```-v```, has provided more details about the IP packet fields, such as TOS, TTL, offset, flags, internal protocol type (in this case, TCP (6)), and the length of the outer IP packet in bytes:

![image](https://github.com/n8som/Capturing-My-First-Packet-With-tcpdump/assets/110139109/d519d4a5-6f51-4a14-b320-67a23172f9a9)

The specific details about these fields are beyond the scope of this lab. However, I should know that these are properties that relate to the IP network packet.

4. In the next section, the data shows the systems that are communicating with each other:

![image](https://github.com/n8som/Capturing-My-First-Packet-With-tcpdump/assets/110139109/8acb6ebf-a7c2-4f48-9f40-37b8f2616594)

By default, ```tcpdump``` will convert IP addresses into names. The name of my Linux virtual machine also included in the command prompt, appears here as the source for one packet and the destination for the second packet. 

The direction of the arrow (>) indicates the direction of the traffic flow in this packet. Each system name includes a suffix with the port number, which is used by the source and the destination systems for this packet.

5. The remaining data filters the header data for the inner TCP packet:

![image](https://github.com/n8som/Capturing-My-First-Packet-With-tcpdump/assets/110139109/6bb0fb57-0d7e-4c48-9574-1ada2c3466b6)

![image](https://github.com/n8som/Capturing-My-First-Packet-With-tcpdump/assets/110139109/6b0d368e-fb68-4c5e-a47e-b89cbc9b1bc0)

The flags field identifies TCP flags. In this case, the P represents the push flag and the period indicates it's an ACK flag. This means the packet is pushing out data.

The next field is the TCP checksum value, which is used for detecting errors in the data.

This section also includes the sequence and acknowledgment numbers, the window size, and the length of the inner TCP packet in bytes.

<h2>Task 3. Capture network traffic with tcpdump</h2>

In this task, I will use ```tcpdump``` to save the captured network data to a packet capture file.

In the previous command, I used ```tcpdump``` to stream all network traffic. Here, I will use a filter and other ```tcpdump``` configuration options to save a small sample that contains only web (TCP port 80) network packet data.

1. Capture packet data into a file called capture.pcap:

```sudo tcpdump -i eth0 -nn -c9 port 80 -w capture.pcap &```

![image](https://github.com/n8som/Capturing-My-First-Packet-With-tcpdump/assets/110139109/1615f476-58f3-4731-a68c-c625c80401d3)

I must press the ENTER key to get my command prompt back after running this command.

This command will run ```tcpdump``` in the background with the following options:

- ```-i eth0```: Capture data from the eth0 interface.
- ```-nn```: Do not attempt to resolve IP addresses or ports to names. This is best practice from a security perspective, as the lookup data may not be valid. It also prevents malicious actors from being alerted to an investigation.
- ```-c9```: Capture 9 packets of data and then exit.
- ```port 80```: Filter only port 80 traffic. This is the default HTTP port.
- ```-w capture.pcap```: Save the captured data to the named file.
- ```&```: This is an instruction to the Bash shell to run the command in the background.

This command runs in the background, but some output text will appear in my terminal. The text will not affect the commands when I follow the steps for the rest of the lab.

2. Use ```curl``` to generate some HTTP (port 80) traffic:

```curl opensource.google.com```

When the ```curl``` command is used like this to open a website, it generates some HTTP (TCP port 80) traffic that can be captured.

![image](https://github.com/n8som/Capturing-My-First-Packet-With-tcpdump/assets/110139109/93e147f4-1911-4193-ab48-6362438b9d04)

3. Verify that packet data has been captured:

```ls -l capture.pcap```

Note: The "Done" in the output indicates that the packet was captured.

![image](https://github.com/n8som/Capturing-My-First-Packet-With-tcpdump/assets/110139109/915d2586-501d-4dee-87a7-7c9aa3bf07ac)

<h2>Task 4. Filter the captured packet data</h2>

In this task, use ```tcpdump``` to filter data from the packet capture file I saved previously.

1. Use the ```tcpdump``` command to filter the packet header data from the ```capture.pcap``` capture file:

```sudo tcpdump -nn -r capture.pcap -v```

This command will run ```tcpdump``` with the following options:

- ```-nn```: Disable port and protocol name lookup.
- ```-r```: Read capture data from the named file.
- ```-v```: Display detailed packet data.

I must specify the ```-nn``` switch again here, as I want to make sure tcpdump does not perform name lookups of either IP addresses or ports, since this can alert threat actors.

This returns output data similar to the following:

![image](https://github.com/n8som/Capturing-My-First-Packet-With-tcpdump/assets/110139109/c26b79b2-b162-45e5-93ee-caa200e8248e)

As in the previous example, I can see the IP packet information along with information about the data that the packet contains.

2. Use the ```tcpdump``` command to filter the extended packet data from the ```capture.pcap``` capture file:

```sudo tcpdump -nn -r capture.pcap -X```

This command will run ```tcpdump``` with the following options:

- ```-nn```: Disable port and protocol name lookup.
- ```-r```: Read capture data from the named file.
- ```-X```: Display the hexadecimal and ASCII output format packet data. Security analysts can analyze hexadecimal and ASCII output to detect patterns or anomalies during malware analysis or forensic analysis.

Note: Hexadecimal, also known as hex or base 16, uses 16 symbols to represent values, including the digits 0-9 and letters A, B, C, D, E, and F. American Standard Code for Information Interchange (ASCII) is a character encoding standard that uses a set of characters to represent text in digital form.

![image](https://github.com/n8som/Capturing-My-First-Packet-With-tcpdump/assets/110139109/3a4976b7-1c90-4724-96e1-b4880407b2c4)

<h3>Testing my understanding</h3>

What command would I use to capture 3 packets on any interface with the verbose option?

- ```sudo tcpdump -c3 -i any -v```

What does the ```-i``` option indicate?

- The network interface to monitor

What type of information does the ```-v``` option include?

- Verbose information

What ```tcpdump``` command can I use to identify the interfaces that are available to perform a packet capture on?

- ```sudo tcpdump -D```

<h2>Conclusion</h2>

I have gained practical experience to enable me to

- identify network interfaces,
- use the ```tcpdump``` command to capture network data for inspection,
- interpret the information that ```tcpdump``` outputs regarding a packet, and
- save and load packet data for later analysis.

I’m well on my way to capturing my first packet.
