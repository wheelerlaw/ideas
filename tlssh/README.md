# tlssh

_An SSH client and server that uses TLS as the underlying communication protocol rather than the custom one of SSH's._

## Background

One day while doing some on-site consulting, I encountered some difficulty establishing an SSH connection to a 
remote server on port 443. It was slow day and I didn't have anything else to do, so I thought I would a spend a non-billable
hour or two doing some network security analysis, just to see the the extent my traffic was being monitored—purely 
for academic reasons, and potentially to offer the client some security recommendations. 

I was connected to the client's guest network, which was surprisingly capable; I encountered 
practically no issues accessing the internet, and for a guest network, the speeds were pretty quick, sometimes reaching
nearly 2-3 mega*bytes* per second. The guest network didn't appear to be using an explicitly defined web proxy, since
I was able to access the internet without having to manually configure the proxy settings on my machine. This was a relief,
since explicit web proxies are well known productivity inhibitors. The network gateway also didn't appear to be 
doing any sanctioned man-in-the-middle attacks—a common practice in corporate networks—to listen in on my 
encrypted traffic, since all of the certificates were signed signed by reputable certificate authorities rather than an 
internal corporate one.

The remote server was a virtual appliance I had spun up on [Vultr's](https://vultr.com) web interface. 
It was the second least expensive option they offered that came with a public IPv4 address. In many cases corporate
networks are unable to reach IPv6-only devices, since their networks sometimes lack an IPv6 component of their network stack.
I configured the SSH server daemon (`sshd`) to listen for connections on port 443, create a new DNS record to point a 
subdomain to that machine, and tried to connect using `ssh`.  

At this point, you might be wondering why I was using port 443. 
Well, I had feared that outbound connections to port 22 would be
blocked, as is common on corporate networks—even guest ones. This actually turned out not to be the case, but at this
point I just wanted to see if connecting to remote machines on this port using a protocol other than TLS would be possible. After all,
I was able to navigate to TLS-secured websites with no issue, so theoretically, I should be able to use SSH over the same
port, right? Well, not necessarily, as we'll find out. 

## Investigation & Analysis

With the server ready and waiting, the DNS records configured, I typed out the `ssh` command—remembering to specify 
the port with `-p 443`—and hit Enter. `ssh_exchange_identification: read: Connection reset by peer` the output read. At
first, I wasn't certain what meant, only that it had something to do with SSH handshake between the client and the server. 

## Resources

- https://blog.anvileight.com/posts/simple-python-http-server/
- https://www.binarytides.com/python-socket-server-code-example/
- https://superuser.com/questions/226192/avoid-password-prompt-for-keys-and-prompts-for-dn-information
- https://stackoverflow.com/questions/15101333/is-there-a-way-to-listen-to-multiple-python-sockets-at-once