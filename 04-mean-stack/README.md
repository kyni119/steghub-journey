# 04. MEAN Stack Deployment to Ubuntu in AWS

- [ ] 101
- [ ] 102
- [ ] 103
- [ ] 104
- [ ] 105  (assignment)

## Notes

- Self study (OSI model, load balancing, HTML + CSS + JavaScript form, MEAN vs MERN): [101-self-study.md](101-self-study.md)
- EC2 instance `mean-server`, dedicated security group and SSH connection: [102-ec2-mean.md](102-ec2-mean.md)
- Node.js, MongoDB installed on the server, user with least privilege, authentication: [103-nodejs-mongodb.md](103-nodejs-mongodb.md)
- Express backend with Mongoose, `.env`, tests with curl: [104-express-backend.md](104-express-backend.md)
- Angular frontend, swap, build served by Express: [105-angular-frontend.md](105-angular-frontend.md)

## What is different from the course guide

| Course guide | What I did | Why |
|---|---|---|
| Node.js 12 | Node.js 22 | Node 12 is no longer maintained |
| MongoDB 7.0 with the `jammy` repository | MongoDB 8.0 with the `noble` repository | my server is Ubuntu 24.04 |
| MongoDB without authentication | user `books_app` (`readWrite` on `books` only), authentication enabled | least privilege |
| Ports 22, 80, 443, 5000 open to everybody | ports 22 and 3300 from my IP only; 27017 never opened | smaller attack surface |
| `sudo npm install`, `body-parser` | `npm install` as my user, `express.json()` | no files owned by `root`, one package less |
| Address of the database written in the code | `.env` file (`chmod 600`, in the `.gitignore`) | no secret in the code or on GitHub |
| Routes `/book`, delete by ISBN | `/api/books`, delete by `_id`, validation and status codes | REST: the URL names the resource, the method is the action |
| AngularJS 1.6 (end of life) | Angular 22 (TypeScript, signals), build served by Express | maintained framework, one server for the page and the API |

## Errors and fixes

| Error | Fix |
|---|---|
| `mongod` failed to start: `Unrecognized option: processManagement.authorization` | In `/etc/mongod.conf` the line `#security:` was still commented, so `authorization: enabled` belonged to `processManagement`. I removed the `#` (`sudo sed -i 's/^#security:/security:/' /etc/mongod.conf`) and restarted |
| `Command listCollections requires authentication` | Expected: the authentication is on. I connect with `mongosh -u books_app --authenticationDatabase books books` |
| `Cannot GET /` in the browser | Normal before the Angular build is in the `public` folder: the API is under `/api/books` |
| The wizard proposed the name `mern-sg` for the new security group | I changed it to `mean-sg`: one group per project, so it can be deleted alone |
| Angular asked to share usage data and the default answer was *Yes* | I turn it off with `npx ng analytics disable` |
| `ls src/apps: No such file or directory` | A typo (`apps` instead of `app`). In Angular 22 the files are in `src/app` and are called `app.ts` and `app.html` |

## AWS cleanup

- [ ] Resources deleted
