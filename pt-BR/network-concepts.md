---
order: 80
icon: ":globe_with_meridians:"
---
# Network Concepts

The physical network in System z consists of devices known as Open
Systems Adapters (OSAs). Several varieties are available, such as the
OSA-Express4S and OSA-Express5S. These are capable of handling up to 640
TCP/IP stacks simultaneously, including HiperSockets for inter-LPAR
communication. An IBM System zEC12 provides up to 96 OSA-Express5S ports
for external network communications. The Open Systems Adapter supports
both copper and fiber Ethernet connections at speeds of up to 10 Gb.

As might be expected, the z/VM feature to access the Internet Protocol
network is TCP/IP for z/VM. OSA-Express devices can be virtualized
through a virtual switch (VSWITCH) device to many Linux guests. It is
available using special z/VM machines known as VSWITCH controllers. Each
Linux guest connects using a virtual device controlled by the qeth
module to a virtual switch system in a z/VM LPAR.

An important benefit of the VSWITCH system is that it can be set up with
redundant OSA devices that provide a failover network system on z/VM

HiperSockets provide high-speed interconnectivity among guests running
on a System z. This technology does not require any special physical
device configurations or cabling. The guests simply communicate with one
anot her internally via the in-memory capabilities of the PR/SM
hypervisor. HiperSockets, however, are not intended to be used for
sophisticated networking and should not be used for external traffic.

Both OSA-Express and HiperSockets use the Queue Direct I/O (QDIO)
mechanism to transfer data. This mechanism improves the response time
using system memory queues to manage the data queue and transfer between
z/VM and the network device

## Network Facilities

On the Mainframe, quite a number of different network devices are
available for use. Many of these come from a historical background, and
should not be used for new implementations. They commonly stay, however,
to continue the support of previous installations on newer hardware.
Linux on System z can operate using all common network interfaces but
for new installations, there are recommended methods for operation
depending on the use case.

The following are some technologies that you will find in the System z
wo rld that are not used or even seen on x86 systems. This section
clarifies some new facilities that you are going to find when you are
migrating from x86 to System z. We provide some brief information that
you can use to start your network planning. In each subsection, you can
find a reference for more detailed information.

### The Open Systems Adapter (OSA)

The Open Systems Adapter (OSA) is a hardware network controller. It is
installed in a Mainframe I/O cage and provides connectivity to clients
on local area networks (LANs) or wide area networks (WANs). It ca n be
directly attached on Linux but will typically be attached to virtual
switches (read more in the "Virtual switch" section below). You can find
more technical information about OSA cards on IBM zEnterprise EC12
Technical Guide, SG24-8049.

### OSA with Link Agregation

You can aggregate multiple physical OSA cards in to a single logical
link, which is called a link aggregation group (LAG). This configuration
increases the bandwidth and provides nondisruptive failover. How to co
nfigure it is well described on Advanced Networking Concepts Applied
Using Linux on IBM System z, SG24-7995.

### HiperSockets

HiperSockets is a microcode implementation that emulates a Logical Link
Control Layer of an OSA interface. HiperSockets provides near zero
latency at memory speed communications between servers running in
different LPARs. When connecting a Linux guest to an IBM z/OS system on
the Mainframe, the HiperSockets network in Layer 3 mode is the method to
use. HiperSockets must be configured in the I/O configuration of the
Mainframe. HiperSockets do not provide external connections. If an
external connection is required, either a HiperSockets bridge must be
implemented by using a VSWITCH, or a Linux guest must be set up as a
router.

HiperSockets provide a very fast connection between LPARs. They provide
an easy way to connect many Linux servers to a z/OS system in the same
Mainframe. This direct connection without involving real hardware is an
important factor to simplify setups with many Linux systems that must be
connected to z/OS. Some benefits are explained in Set up Linux on IBM
System z for Production, SG24-8137.

### Virtual Switch

A virtual switch (VSWITCH) is a software program that enables one
virtual host to communicate with another virtual host within a computer
system. Virtual switches typically emulate functions of a physical
Ethernet switch. In Linux on System z, a VSWITCH provides direct
attachment of z/VM guests to the local physical network segment. The
VSWITCH allows IP network architects and network administrators to treat
z/VM guests as a server in the network.

The switched network inside a z/VM Operating System commonly is
implemented with a VSWITCH. When running the VSWITCH as Layer 2, it
behaves similar to a real switch just between virtual machines.

The actual speed of a connection with a VSWITCH depends on a number of
different variables. The type of traffic is as important as the real
underlying hardware and the maximum transmission unit (MTU), which is
the maximum size (in bytes) of one packet of data that can be
transferred in a network. Common to all of those solutions is that the
VSWITCH is faster than a real switch connected to the Mainframe would
be.

VSWITCHes do not need a connection to an OSA card to operate. They can
also provide purely virtual networks. This also simplifies the setup of
private interconnects between guest systems. When creating private
interconnects in an SSI with LGR enabled, the use of dedicated VLANs
with external interface is recommended. This is necessary to accomplish
the private connection between guests that run on different nodes in the
SSI.

Implementing VLANs also helps if different guests run in different
security zones of a network. It is easy to configure network interfaces
to Linux guests that provide only selected VLANs to the guest. These can
be configured either as tagged VLANs or as single untagged VLAN on an
interface.

The VSWITCH infrastructure provides two basic configuration options. One
configures user-based access, the other configures port-based access.
From the possibilities, both are equivalent, just the configurations
differs.

You can read more about VSWITCH benefits on Set up Linux on IBM System z
for Production, SG24-8137, and technical information about Advanced
Networking Concepts Applied Using Linux on IBM System z, SG24-7995.
