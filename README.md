# Guide to Onion Services
### Alec Muffett

How to make and use Onion Services

# Why build an Onion Site / use Onion Services?

Onion networking and onion services are probably best considered as an alternative network stack, layered on top of TCP; Onion networking feels like TCP to use -- all connections are "stream" connections, connections are made from a client to a server's {address, port} pair, and the way that Tor implements onion services means that onion connections present themself to traditional TCP/IP networking in familiar ways: SOCKS5 outbound for client connections, and inbound TCP connections (via a reverse proxy) for servers.
But what are the benefits?

## Simplicity of Architecture

Once you are good at it - and Onion networking is pretty easy to get used to - then you can directly connect two machines, anywhere in the world, even if they are behind firewalls. It's as-if all machines that are equipped with Onion addresses live on the same, flat, local network - this is an easy model to reason about regarding security and trust, and it is also helpful for deployment of some architectures (e.g. IoT) where firewalls can add complexity.

## Disintermediation 

Because Onion networking is a flat "over the top meta-network" which lives on top of TCP/IP, it doesn't require you to deal with NAT, DNS, Dynamic DNS, port-forwarding, firewall configuration, Internet Service provider address-ranges, or anything like that. 

This probably sounds weird, but so long as the server that you run your Onion services upon has got some kind of outbound internet connection, then it can act as a server and accept inbound connections over its Onion address -  much like the internet used to work before the invention of the firewall and of NAT.

## Availability and Robustness 

The TCP/IP internet is mired with configurational issues, trust issues, and availability issues; firewalls get reconfigured and swapped-out, "BGP" routes get hijacked and traffic is routed through unexpected countries (and their political restrictions / surveillance apparatus) in pursuit of speed, efficiency, and lower cost, and this is fine.

Tor Onion networking makes a performance tradeoff: you lose a little speed, and you lose some features (eg: UDP/datagrams, "Anycast", etc) but if you have a server which is equipped with both an Onion address and a reasonably stable and functional internet connection, then your server will be reachable by the world at that Onion address. 

If your network goes down, roams/changes IP address, or even changes ISP, then eventually your Onion service will self-heal and connectivity will be restored.

## Lots of Trust and Privacy

All of the above is technical benefit, but there is also qualitative benefit to Onion networking: Onion addresses can be trusted. 

There is no ARP-spoofing, no DNS-poisoning, no DNS-blocking, no deep packet inspection and no firewall-blocking in Onion networking; if you have typed-in the address of the site correctly, and if your connection is made, then you are cryptographically certain to be connected, privately and with integrity, to the server which you intended.

There is a proviso, here: there is no corresponding "trust anchor" for clients, so you are still going to have to implement some form of client authentication, just like the internet does.

There is also a question of "scale" - that if a million people globally are connected to a single Onion address, then surely there must be performance challenges? The answer is "yes, but performance is much better than you would expect in these circumstances, and traditional load-balancing helps, plus there are technologies like Alt-Svc and OnionBalance to help with this."

Step 1, of course, is to get a million users.

## Reasonable amounts of Server Anonymity

The Tor Project sets out "anonymity" as a major feature of Onion networking; compared to all the above I consider anonymity to be a minor aspect, one that is subject to easy misunderstanding, nitpicking, or compromise. 

For instance: if your Onion site offers "anonymity" but someone logs-in and posts "My name is Alice and my email address is alice.smith@gmail.com" then it's obvious how clients can easily surrender their "anonymity" over Onion networking. This kind of anonymity is not a strong value proposition.

What Onion networking does offer, though, is some degree of "server anonymity" - that is: server privacy and protection. BLAH BLAH BLAH

## Reasonable amounts of Client Discretion

PICK UP ON THE CLIENT ANONYMITY STUFF ABOVE

## Fun!

# What is an Onion Address?

- over-the-top meta network
- physical address
- cute namespace hack: www.nytimes3xbfgragh.onion
- HSDir is an ARP-like mechanism which maps Onion Addresses to IP addresses, through which connections may be initiated.
- Enable "Onion Services", viz: "SSH over Onion", "HTTPS over Onion", etc…

## "Version 2" versus "Version 3" Onion Addresses

# Onion Address Mechanics: How do I use them?

## tl;dr - if you're a building a client 

You run a local tor daemon and use SOCKS5, via that daemon, to talk to services on Onion addresses.

## tl;dr - if you're building a server

You run a local tor daemon and configure it to forward traffic for an Onion address/port tuple, to an IP address/port tuple. The daemon will create a fresh Onion address if you don't already have one. Onion addresses are essentially permanent, until you choose to throw them away.

Onion services are conceptually very similar to "ssh" tunnelling, but they run for the entire period that your tor daemon is connected to the rest of the tor network - they don't require special connections to be set up. 

The IP address/port can be:

- local to the machine running Tor: `localhost:80`, `localhost:22`
- or on another machine entirely: `www.example.com:80, 10.1.2.3:443`
- or on a remote load-balancer or "Virtual IP": `elb1234.aws.com:443`

A single tor daemon can simultaneously forward multiple ports for "your" Onion address, to IP addresses/ports on arbitrary other machines:

- `exampleonionaddr.onion:80` -> `legacy.example.com:8081`
- `exampleonionaddr.onion:443` -> `www.example.com:443`
- `exampleonionaddr.onion:22` -> `ssh-gateway.example.com:22`

## What about subdomains and hostnames?

Hostnames and subdomains (http://www.prod.exampleonionaddr.onion) are merely "advisory" in Onion networking; the above example will still connect to exampleonionaddr.onion on port 80 (because "http://") - but the webserver on that machine will still receive a header like:

```
Host: www.prod.exampleonionaddr.onion
```

...because the hostname ("www") and subdomain ("prod") can be transported as part of the HTTP protocol; hopefully that webserver will know what to do with that extra information.

## If you remember one thing…

Onion addresses may look like DNS names, but it's better to think of them as network addresses, so that subdomains and hostnames are implemented as a hack, where "www.prod.192.168.1.1"  would still refer to "192.168.1.1".

## Why do I have to run a local Tor daemon, why can't I run just one centrally?

trust boundaries go here

# Building a "toy" Onion Site

# Adding more services to your "toy" Onion site

# Building a "high-bandwidth" Onion Site

# Building an "anonymous" Onion Site

# Adding an Onion Address to your public website

# SSL / TLS

