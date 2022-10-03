### Useful Commands
```bash
// Backend 
docker build --file=backend/Dockerfile.backend -t backend:latest backend 

// Frontend
docker build --file=frontend/Dockerfile.frontend -t frontend:latest frontend
```


## Vue.js + Vite Development:
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