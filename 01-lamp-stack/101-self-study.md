# Self study (module 01)

## SDLC

SDLC means Software Development Life Cycle. It is the set of steps a piece of software goes through, from the idea to its use and its maintenance.

1. **Planning**: define the need, the budget and the team.
2. **Analysis**: define what the software must do.
3. **Design**: draw the architecture.
4. **Development**: write the code.
5. **Testing**: find and fix bugs.
6. **Deployment**: put the software online.
7. **Maintenance**: fix, update and monitor.

There are different methodologies depending on the project:
- **Waterfall**: each phase is finished before the next one starts.
- **Agile**: short cycles (sprints) that are repeated.
- **Spiral model and V-model**.
- **CI/CD** (DevOps): continuous automation of development, tests and deployment.

## LAMP

LAMP (Linux, Apache, MySQL, PHP) is a set of 4 free technologies used to build, host and run dynamic websites and applications.

- **Linux**: the operating system that is the base and manages the resources of the server.
- **Apache**: the HTTP server that receives requests from browsers and sends back web pages.
- **MySQL**: the relational database management system that stores the data.
- **PHP** (or Perl or Python): the scripting language that generates the dynamic content.

A request goes from the browser to Apache, which passes the code to PHP, which queries MySQL; then the page goes back to the browser.

## chmod and chown

See [101-permissions.md](101-permissions.md).

## TCP and UDP

For two machines to talk on a network, data travels in small packets. TCP and UDP are two ways to send them.

**TCP** (Transmission Control Protocol)
- Reliable: it checks that everything arrives, in order. If a packet is lost, it sends it again.
- With a connection: both machines agree before exchanging (the 3-step "handshake": SYN, SYN-ACK, ACK).
- Slower, but safe.
- Used for: the web, email, file transfers, SSH.

**UDP** (User Datagram Protocol)
- Fast, no guarantee: it sends without checking that the data arrives.
- Without a connection: no handshake.
- A few lost packets are not a problem.
- Used for: live streaming, video calls, online games, DNS.

Image: TCP is a parcel with a delivery receipt. UDP is a postcard dropped in a mailbox.

## Ports

An IP address identifies a machine. A port identifies the service running on that machine.

| Service | Port | Protocol | Role |
|---|---|---|---|
| HTTP | 80 | TCP | Web, not encrypted |
| HTTPS | 443 | TCP | Web, encrypted |
| SSH | 22 | TCP | Secure connection to a server |
| FTP | 21 | TCP | File transfer (not secure) |
| SFTP | 22 | TCP | File transfer over SSH |
| Telnet | 23 | TCP | Remote connection, not encrypted (to avoid) |
| DNS | 53 | UDP (and TCP) | Domain name to IP |
| MySQL | 3306 | TCP | Database |

Live streaming and calls usually use UDP (lost pieces: the image jumps). On-demand video (YouTube, Netflix) uses TCP over HTTP/HTTPS and loads the video in advance in a buffer: when it stops, the circle spins because the buffer is empty and the network is slow.

To see the open ports on a machine:

```bash
sudo ss -tulnp
```

- `-t`: TCP ports
- `-u`: UDP ports
- `-l`: only the ports that are listening
- `-n`: show numbers instead of names
- `-p`: show the program that uses the port

## Buffer vs cache

| | Buffer | Cache |
|---|---|---|
| Goal | Absorb speed differences between two steps | Avoid redoing work that is already done |
| Content | Data **in transit**, used once | Data **already used**, that can be reused |
| Lifetime | Very short: read, then thrown away | Longer: kept for next time |
| Example | The video loaded a few seconds in advance | A web page already downloaded and kept by the browser |
