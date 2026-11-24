# Self study (module 03: MERN)

## 1. Types of databases, and SQL vs NoSQL

A database management system (DBMS) stores data and lets applications read and change it. There are several families, each good at a different job.

| Type | How data is stored | Good for | Examples |
|---|---|---|---|
| Relational (SQL) | Tables with rows and columns, linked by relationships | Structured data, transactions (banking, orders) | MySQL, PostgreSQL |
| Document (NoSQL) | JSON-like documents in collections | Flexible data, modern web apps | MongoDB |
| Key-value (NoSQL) | A key and its value | Cache, sessions, very fast reads | Redis |
| Column (NoSQL) | Data grouped by columns | Huge volumes spread over many servers | Cassandra |
| Graph (NoSQL) | Nodes and the links between them | Social networks, recommendations | Neo4j |

### SQL vs NoSQL

| | Relational (SQL) | NoSQL |
|---|---|---|
| Structure | Tables, rows, columns | Documents, key-values, columns or graphs |
| Schema | Fixed: I define the columns before inserting data | Flexible: two documents can have different fields |
| Query language | SQL | Depends on the database (for MongoDB: methods such as `find` and `insert`) |
| Consistency | ACID transactions: strong guarantees | Often favours availability and speed |
| Scaling | Mostly vertical (a bigger server) | Designed for horizontal scaling (more servers) |

How I understand it: a relational database is like a spreadsheet with strict columns, a NoSQL document database is like a folder of JSON files that do not all have to look the same. The choice depends on the application: strict, linked data and transactions go to SQL; flexible, fast-changing or very large data goes to NoSQL.

In this project I use **MongoDB**: a To-Do item is a small document, which matches the JSON format used everywhere in JavaScript.

```json
{ "_id": "65f1c2...", "action": "Buy milk" }
```

## 2. Web application frameworks

A web application framework is a toolbox that provides ready-made building blocks (routing, handling requests, rendering the interface), so I do not have to write everything from scratch. A web application has two halves that talk to each other.

| | Server side (backend) | Client side (frontend) |
|---|---|---|
| Where it runs | On the server (my EC2 instance) | In the browser of the user |
| Role | Receive requests, talk to the database, apply the rules (authentication, permissions), send data back | Display the interface, react to clicks, call the server |
| Examples | Express (Node.js), Django, Laravel, Flask | React, Angular, Vue |
| In this project | **Express** | **React** |

How they work together in the To-Do app:
1. The browser loads the **React** application.
2. React sends a request to the API: `GET /api/todos`.
3. **Express** receives the request, reads MongoDB and sends back JSON.
4. React displays the list.

The server does not care about the look, and the client never touches the database directly. This is also a security point: the database password stays **on the server** (in the `.env` file), never in the browser code.

- **Server-side rendering (SSR)**: the server builds the HTML page. This is what PHP did in the LAMP and LEMP projects.
- **Single-page application (SPA)**: the browser loads the application once, then only JSON travels between the browser and the server. This is the case of React.

## 3. JavaScript basics

JavaScript is the language of the whole MERN stack: MongoDB documents look like JSON, Express and Node.js run JavaScript on the server, and React builds the interface in JavaScript. **Node.js** is what lets JavaScript run outside the browser, on a server (`node index.js`).

| Element | Example |
|---|---|
| Variables | `let message = "Hi";` (can change), `const age = 25;` (cannot be reassigned) |
| Data types | number, string, boolean, array `[1, 2]`, object `{ action: "Buy milk" }` |
| Comments | `// one line` and `/* several lines */` |
| Operators | `+ - * /`, `=`, `+=`, `==` / `===`, `!=`, `<`, `>` |
| Condition | `if (...) { } else { }` |
| Loops | `for` and `while` |
| Function | `function add(a, b) { return a + b; }` |

Three things I will meet in the project code:

```javascript
// 1. Arrow function (used everywhere in the Express and React code)
const add = (a, b) => a + b;

// 2. Object and array (a to-do is an object, the list is an array)
const todos = [{ action: "Buy milk" }, { action: "Call mom" }];

// 3. Promise: an operation that takes time (calling the database or the API)
fetch("/api/todos")
  .then(res => res.json())
  .then(data => console.log(data))
  .catch(err => console.log(err));
```

- A **promise** represents a result that is not ready yet. JavaScript does not wait: it keeps running, and calls `.then(...)` when the answer arrives, or `.catch(...)` if there is an error. The routes of the project use it (`Todo.find().then(...).catch(next)`).
- `require('express')` loads a module, and `process.env.PORT` reads an environment variable (the ones I put in the `.env` file).

## 4. RESTful API

An API is the "door" through which one program talks to another. A **RESTful** API uses the HTTP protocol with simple rules:
- every thing is a **resource**, identified by a URL (here `/api/todos`);
- the action is given by the **HTTP method**;
- data travels as **JSON**.

| Method | Action (CRUD) | Example in this project |
|---|---|---|
| `GET` | Read | `GET /api/todos`: list of tasks |
| `POST` | Create | `POST /api/todos` with `{"action": "Buy milk"}` |
| `PUT` | Update | not used in this To-Do app |
| `DELETE` | Delete | `DELETE /api/todos/<id>` |

Main principles:
- **Client-server**: the client and the server are separate (React and Express), so each one can change independently.
- **Stateless**: each request contains everything the server needs. The server does not remember the client between two requests.
- **Uniform interface**: always the same methods and the same URL style.
- **Cacheable**: a response can be marked as reusable, to go faster.
- **Layered system**: there can be intermediaries (proxy, load balancer) between the client and the server, invisible to the client.

Common HTTP status codes:

| Code | Meaning |
|---|---|
| `200` | OK |
| `201` | Created |
| `400` | Bad request (client error) |
| `404` | Resource not found |
| `500` | Server error |

Benefits: scalable, simple, and compatible with any language or platform because it relies on standard HTTP and JSON. REST APIs are used between a single-page application and its backend, between microservices, for mobile apps, and for third-party services (payment, social networks).

## 5. CSS

CSS (Cascading Style Sheets) describes the **appearance** of HTML: colours, fonts, spacing, layout, responsive design for different screen sizes, and animations. A rule is made of a **selector** (which elements) and **declarations** (property: value).

```css
/* CSS comment */
h1 {
  color: #333333;
  font-size: 24px;
}
```

Common properties: `font-family`, `font-size`, `color`, `background-color`, `width`, `height`, `margin`, `padding`, `display`, `position`, `text-align`, `border`, `border-radius`.

In this project, the style of the To-Do page is in the `App.css` file of the React frontend.

## References

- [MDN: JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
- [RESTful API](https://restfulapi.net/)
- [W3C: CSS](https://www.w3.org/Style/CSS/Overview.en.html)
