# 03. MERN Web Stack

- [ ] 101
- [ ] 102
- [ ] 103
- [ ] 104
- [ ] 105
- [ ] 106
- [ ] 107
- [ ] 108  (assignment)

## Notes

- Self study (databases, SQL vs NoSQL, web frameworks, JavaScript, REST API, CSS): [101-self-study.md](101-self-study.md)
- EC2 instance `mern-server`, dedicated security group and SSH connection: [102-ec2-mern.md](102-ec2-mern.md)
- Installing Node.js (backend, step 1): [103-nodejs.md](103-nodejs.md)
- Backend with Express (steps 2 and following): [104-express-backend.md](104-express-backend.md)
- MongoDB Atlas, database user, network access, `.env` and connection (backend, step 3): [105-mongodb-atlas.md](105-mongodb-atlas.md)
- Testing the API with curl, and the `Server error` problem (backend, step 4): [106-api-tests.md](106-api-tests.md)
- React frontend with Vite, proxy to the API, components (step 5): [107-react-frontend.md](107-react-frontend.md)

## Errors and fixes

| Error | Fix |
|---|---|
| `Could not connect to any servers in your MongoDB Atlas cluster ... IP that isn't whitelisted` | The Atlas IP list contained the IP of my PC instead of the public IP of `mern-server`. I replaced it (the server can show its own IP with `curl -s https://checkip.amazonaws.com`) |
| A `#` in the database password breaks the connection string | In a URL the special characters must be encoded (`#` becomes `%23`), and a `#` starts a comment in a `.env` file. I generated a password without special characters |
| The connection string had no database name | I added `/todo` before the `?`: without it Mongoose uses the `test` database, where the user has no rights |
| `GET /api/todos` answered `{"error":"Server error"}` while the database was connected | An old `node index.js` was still running and answered on port 5000. I stopped it with `pkill -f "node index.js"` and restarted. The `.env` file is only read at startup, so I always restart after changing it |
| `Cannot GET /` in the browser | Normal: the welcome page was removed, the API is under `/api` (for example `/api/todos`) |
| The React page did not load: Vite showed `localhost:5173` and `use --host to expose` | My `vite.config.js` changes had not been saved. I added `host: true`, `port: 3000` and the proxy, checked with `cat`, and restarted |
| `Port 3000 is in use, trying another one...` | An old Vite process still held the port. I found it with `sudo ss -ltnp \| grep ':3000'` and stopped it with `kill <PID>` |
| `kill 511`: `No such process` | `511` is the connection queue size, not the PID. The PID is in `users:(("node",pid=...))` |
| Two `node index.js` processes running | Same problem as before: I check with `pgrep -a node` before starting a server, and stop copies with `pkill -f "node index.js"` |

## Questions and answers

A short quiz I did at the end of the project, with the corrected answers.

**1. What does each letter of MERN stand for, and what does each part do?**
MongoDB is a document database (JSON-like documents). Express is the backend framework. React is the frontend library that runs in the browser. Node.js is the runtime that executes JavaScript on the server. Express runs on Node: they are one server, not two.

**2. What is the path of a request when I click "add todo"?**
React sends `POST /api/todos` to port 3000. The Vite proxy forwards it to `localhost:5000`. Express receives it and sends it to the right route in `routes/api.js`. The route uses Mongoose (`Todo.create`), which checks the data with the model and writes it to MongoDB Atlas. The answer goes back to React, which reloads the list with a `GET`.

**3. Why use MongoDB Atlas instead of installing MongoDB on the instance?**
Atlas is a managed service: updates, availability and backups are handled for me. The `t3.micro` instance has little memory. The data is also separated from the server, so deleting the instance does not delete the tasks. The free plan (M0) is a bonus.

**4. Which routes does the API have?**
`GET /api/todos` lists the tasks, `POST /api/todos` creates one, `DELETE /api/todos/:id` deletes the task whose `_id` is in the URL. A REST route is always a method plus a URL.

**5. Why does an empty task return `400` and not `200`?**
`400 Bad Request` means the client sent invalid data. `2xx` is success, `4xx` is a client error, `5xx` is a server error. `200` would wrongly say that everything went well. The route checks `req.body.action` before saving.

**6. What is the role of the Mongoose model?**
It describes the shape of a document (`action`, a string) and its rules (`required`). It also gives the methods (`find`, `create`, `findOneAndDelete`) and the collection name (`todos`).

**7. Why must the `.env` file stay secret?**
It contains the connection string with the database password. Someone who gets it can read, change or delete all the data. It is in `.gitignore`, protected with `chmod 600`, and my notes only show `<my-password>`.

**8. Why a dedicated `todo_app` user and an IP list with only the server?**
Least privilege: `todo_app` has `readWrite` on the `todo` database only. If the application is compromised, the attacker cannot reach other databases or manage the cluster. With `0.0.0.0/0` the whole Internet could try to connect and only the password would protect me: the IP list is a second layer.

**9. What caused the `Server error` although the database was connected?**
An old `node index.js`, started before I fixed the `.env`, still held port 5000 and answered instead of the new one. I found it with `pgrep -a node` (two copies), stopped it with `pkill -f "node index.js"` and restarted.

**10. Why does Vite have a proxy for `/api`?**
Because of CORS. The page comes from port 3000 and the API from port 5000: for the browser these are two different origins, and it blocks such calls. With the proxy the browser only calls port 3000 (same origin), and Vite forwards the request to port 5000 from the server. No `Access-Control-Allow-Origin` header is needed, and the browser never calls port 5000.

### Catch-up questions

**11. What path does a request follow when I click "add todo"?**
The browser sends `POST /api/todos` to the Vite server (port 3000). The proxy forwards it to the backend on `localhost:5000`. Express sends it to the `POST` route. The route calls `Todo.create`: Mongoose checks the data with the model and writes it to Atlas. The route sends the status code back to React, not Mongoose.

**12. What are the 3 routes, written correctly?**
In a REST API the URL names the **resource** (a noun) and the HTTP method is the **action**. The method is never written in the URL.
- `GET /api/todos`: list the tasks
- `POST /api/todos`: create a task
- `DELETE /api/todos/:id`: delete the task with this `_id`

**13. What do the status codes mean, with a case from my project?**

| Code | Meaning | Case in my project |
|---|---|---|
| `200` | everything went well | `GET /api/todos` returns the list |
| `400` | invalid request, the **client** is at fault | `POST` with `{"action":""}` |
| `500` | the **server** failed | the `Server error` caused by the old process |

`4xx` means "you asked wrongly", `5xx` means "I answered wrongly".

**14. Why does the browser block the call to the API, and how does the proxy solve it?**
It is not about the number of servers (here it is the same machine). An **origin** is protocol + address + **port**. `IP:3000` and `IP:5000` have different ports, so they are two origins, and the browser applies the same-origin rule: it blocks the response unless the API allows it (`Access-Control-Allow-Origin`). With the Vite proxy the browser only calls `IP:3000`. Vite forwards the request to port 5000 from the server side, where this browser rule does not apply.

**15. The API answers `{"error":"Server error"}` and Atlas is configured correctly: what do I check, in order?**
1. `pgrep -a node`: is more than one copy running?
2. `cat /tmp/server.log`: the real error is written in the server log, not in the response. An empty log for my request means another process is answering.
3. `pkill -f "node index.js"`, then restart in the background with the log: `(node index.js > /tmp/server.log 2>&1 &)`.

To check the ports: `sudo ss -ltnp | grep -E ':3000|:5000'` (`-E` is needed for the "or").

## AWS cleanup

- [ ] Resources deleted
