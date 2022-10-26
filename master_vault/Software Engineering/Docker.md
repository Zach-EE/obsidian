# Docker Compose:

## Postgresql:
### - Compose
```bash
// docker-compose.yml
version: '3.8'
services:
 db:
   container_name: postgres_container
   image: postgres
   restart: always
   environment:
     POSTGRES_DB: postgres_db
     POSTGRES_USER: admin
     POSTGRES_PASSWORD: secret
     PGDATA: /var/lib/postgresql/data
   ports:
     - "5432:5432"
   volumes:
     - db-data:/var/lib/postgresql/data

 pgadmin:
   container_name: pgadmin4_container
   image: dpage/pgadmin4:5.5
   restart: always
   environment:
     PGADMIN_DEFAULT_EMAIL: admin@admin.com
     PGADMIN_DEFAULT_PASSWORD: secret
     PGADMIN_LISTEN_PORT: 80
   ports:
     - "8080:80"
   volumes:
     - pgadmin-data:/var/lib/pgadmin
volumes:
 db-data:
 pgadmin-data:
```
### - Test DB Command:
```bash
docker run -p 5432:5432 -d -e POSTGRES_PASSWORD=1234 -e POSTGRES_USER=postgres -e POSTGRES_DB=test postgres
```

## MySQL:
```bash
// 
docker run --name some-mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=my-sec-pw -d mysql:tag)


```
1.  Pull image: `docker pull mysql/mysql-server:latest` 
2. Deploy:
	- `docker run --name=[container_name] -d [image_tag_name]`
	- `docker run --name=mysql_container -d mysql/mysql-server:latest`
3. See logs:
	- `docker logs [container_name]`
4. Access Container Shell & Update Password
	- `docker exec -it [container_name] bash`
	- `mysql> ALTER USER 'root'@'localhost' IDENTIFIED BT '[new_Password]'` 
5. `docker run -p 13306:3306 --name mysql-docker-local -e MYSQL_ROOT_PASSWORD=[password]-d mysql:latest`
# Useful Commands
```bash
// Backend 
docker build --file=backend/Dockerfile.backend -t backend:latest backend 

// Frontend
docker build --file=frontend/Dockerfile.frontend -t frontend:latest frontend

// Kill all running Docker Containers
docker kill $(docker container ls -q)

docker start [container_name]

docker restart [container_name]

docker stop [container_name]

docker rm [container_name]
```


# Vue.js + Vite Development:
### Getting Started
1.  Setup Docker Compose file: 
```
//docker-compose.yml
version: '3.9'

services:

  my_frontend:

    container_name: my_frontend

    image: node:lts

    working_dir: /var/www/html/app/

    entrypoint: /bin/bash

    ports:

      - "8000:8000"

    volumes:

      - ./frontend/:/var/www/html/app

    tty: true
```
2.  Spin up container
	- `docker compose up -d --build`
	- `docker exec -it my_frontend /bin/bash`
3. Create new Vue Project
	- `npm create vite@latest my-vue-app -- --template vue`
4. Update your Vue project to run in a docker container
- Update vite.config.js file
```js
//vite.config.js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

// https://vitejs.dev/config/
export default defineConfig({
  server: {    // <-- this object is added
    port: 8000
  },
  plugins: [vue()]
})
```
- Update package.json file
```json
// package.json
...
"scripts": {
    "dev": "vite --host",
...
```
5. Inside Docker Shell
	- `cd my_frontend && npm install`
	- `npm run dev` 
	- ... and you are of too the races