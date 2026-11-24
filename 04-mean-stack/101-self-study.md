# Self study (module 04: MEAN)

## 1. OSI model

The OSI (Open Systems Interconnection) model splits network communication in **7 layers**. Each layer has one job and talks to the layer above and the layer below. Data goes **down** the layers on the sender side (7 to 1) and **up** on the receiver side (1 to 7).

| # | Layer | Role | Examples |
|---|---|---|---|
| 7 | Application | services used directly by applications and users | HTTP, FTP, SMTP, DNS |
| 6 | Presentation | format, encryption, compression of the data | TLS/SSL, MIME |
| 5 | Session | opens, keeps and closes a conversation between two applications | RPC, session management |
| 4 | Transport | reliable end-to-end delivery: segments, flow control, error recovery | TCP, UDP |
| 3 | Network | logical addresses and routing between networks (packets) | IP, routers |
| 2 | Data link | delivery between two neighbour devices (frames) | Ethernet, switches, MAC addresses |
| 1 | Physical | bits on the medium: cables, radio, electrical signals | cables, hubs, network cards |

A trick to remember the order from 7 to 1: **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing.

Why it is useful:
- **Troubleshooting**: I can locate the layer of a problem. A cable unplugged is layer 1, a wrong IP or route is layer 3, a closed port or a TCP problem is layer 4, a bad HTTP answer is layer 7.
- **Standard vocabulary**: "a layer 7 load balancer" has the same meaning for everybody.
- **Interoperability**: each layer has clear boundaries, so devices and software from different vendors work together.

**OSI vs TCP/IP.** The Internet really works with the TCP/IP model, which has 4 layers: Application (OSI 5 to 7), Transport (4), Internet (3), Network access (1 and 2). OSI is the reference to understand and talk, TCP/IP is what is actually implemented.

Where I met these layers in my projects:

| Situation | Layer |
|---|---|
| Security group rule for port 22, 80, 3000 | 4 (TCP ports) |
| Public IP of the instance, route to the Internet | 3 |
| `curl` to `/api/todos`, HTTP status `400` | 7 |
| HTTPS certificate (coming later in the course) | 6 (TLS) |

## 2. Load balancing

A **load balancer** distributes the incoming traffic across a group of servers (a pool). No server is overloaded, users get fast answers, and if one server fails the traffic goes to the others. It is like several checkout lanes in a supermarket.

**Algorithms**

| Algorithm | How it chooses the server | Good for |
|---|---|---|
| Round robin | one after the other, in order | identical servers, simple |
| Weighted round robin | like round robin, but stronger servers get more requests | servers with different capacity |
| Least connections | the server with the fewest active connections | long requests, uneven load |
| Resource-based | looks at the CPU and memory of each server | heavy and varied workloads |
| DNS-based | the DNS answer sends the user to the closest server | users in several regions |
| Content-based | routes by what is requested (for example `/images` to a static pool) | separate static and dynamic content |

**Types and techniques**
- **Hardware** load balancer: a dedicated device, very fast, expensive.
- **Software** load balancer: a program on a server (Nginx, Apache, HAProxy). I will build one in modules 08 and 10.
- **Cloud** load balancer: a managed service, for example the AWS Elastic Load Balancer.
- **Layer 4** balancing works on IP and port (TCP). **Layer 7** balancing reads the HTTP request (URL, headers), so it can route by content.

**Benefits**: performance, high availability (no single point of failure), scalability (add servers to the pool), redundancy, protection against some DDoS attacks, better use of the hardware, and **health checks** (a failing server is removed from the pool).

## 3. HTML, CSS and JavaScript: a web form

The three languages of the web page, each with one job.

| Language | Role | Example |
|---|---|---|
| **HTML** | the structure and content | `<input type="text" id="name">` |
| **CSS** | the appearance | `button { background: #007bff; }` |
| **JavaScript** | the behaviour, in the browser | check that a field is not empty |

A small contact form with validation, in one file (HTML, CSS and JavaScript together):

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Contact form</title>
  <style>
    body { font-family: Arial, sans-serif; background: #f4f4f4; }
    .container { max-width: 400px; margin: 0 auto; padding: 20px; background: #fff; border-radius: 5px; }
    label { display: block; margin-bottom: 5px; }
    input, textarea { width: 100%; padding: 10px; border: 1px solid #ccc; border-radius: 5px; box-sizing: border-box; }
    button { background: #007bff; color: #fff; border: none; padding: 10px 20px; border-radius: 5px; cursor: pointer; }
    button:hover { background: #0056b3; }
    .error { color: red; font-size: 12px; }
  </style>
</head>
<body>
  <div class="container">
    <h2>Contact us</h2>
    <form id="contactForm">
      <label for="name">Name</label>
      <input type="text" id="name">
      <span class="error" id="nameError"></span>

      <label for="email">Email</label>
      <input type="email" id="email">
      <span class="error" id="emailError"></span>

      <label for="message">Message</label>
      <textarea id="message" rows="4"></textarea>
      <span class="error" id="messageError"></span>

      <button type="submit">Send</button>
    </form>
    <div id="status"></div>
  </div>

  <script>
    document.getElementById('contactForm').addEventListener('submit', function (event) {
      event.preventDefault();                       // stop the page from reloading
      const fields = ['name', 'email', 'message'];
      let valid = true;
      fields.forEach(id => {
        const empty = document.getElementById(id).value.trim() === '';
        document.getElementById(id + 'Error').innerText = empty ? 'This field is required' : '';
        if (empty) valid = false;
      });
      if (valid) {
        document.getElementById('status').innerText = 'Message sent (simulation)';
        this.reset();
      }
    });
  </script>
</body>
</html>
```

- `<label for="name">` is linked to the field with the same `id`: clicking the label selects the field.
- `event.preventDefault()` stops the default behaviour of the form (reloading the page), so JavaScript can validate first.
- The validation here runs **in the browser only**: it improves the user experience, but it is not a security. The server must check the data again (in module 03 the `400` answer of the API did it).
- The Book Register of this module is also a form: name, ISBN, author, pages.

## 4. Extra: MEAN vs MERN

MEAN is MERN with **Angular** instead of React. The rest is identical.

| Letter | Technology | Role | Same as MERN? |
|---|---|---|---|
| M | MongoDB | document database | yes |
| E | Express | backend framework (on Node.js) | yes |
| A | Angular | frontend framework in the browser | no (React in MERN) |
| N | Node.js | runtime that executes JavaScript on the server | yes |

| | React | Angular |
|---|---|---|
| Type | a library for the interface | a complete framework |
| Language | JavaScript (JSX) | TypeScript |
| Included | the view only (routing, HTTP, forms come from other libraries) | routing, HTTP client, forms, dependency injection |
| HTTP calls | `fetch` | `HttpClient` (returns an Observable) |

The course guide uses **AngularJS 1.6**, the very first version of Angular. It is a different framework from today's Angular and it is **no longer maintained** (end of life since 2022). The concepts are the same: a controller holds the data, the HTML shows it with `ng-repeat` and `{{ }}`, and `$http` calls the API.

## References

- [OSI model (TechTarget)](https://www.techtarget.com/searchnetworking/definition/OSI)
- [What is load balancing (NGINX)](https://www.nginx.com/resources/glossary/load-balancing/)
- [MDN: HTML forms](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Forms)
- [Angular documentation](https://angular.dev/)
