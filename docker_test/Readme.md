## Commands to Run

```bash
docker build -t {dockerhub-username}/{name-of-the-docker-image}:{version-num} .

docker run -p 5000:8080 {dockerhub-username}/{name-of-the-docker-image}:{version-num} .
```

- The Docker container will still be running even after we closed the terminal.

- There can be situations where we want to share data across multiple containers.

- The prefer way to do that is with volumes (To persist files).

  

  ### To Create a volume:

  ```bash
  PS E:\VS Code Projects\FullStack Projects\docker_test> docker volume create shared-stuff
  shared-stuff
  
  docker run --mount source=shared-stuff,target=/stuff
  ```

  - We can mount it somewhere in our container when we run it.
  - Multiple volumes can be mount this volume simultaneously and access the same set of files.

  

## Docker Compose

- A tool for running multiple docker containers at the same time.

```bash
PS E:\VS Code Projects\FullStack Projects\docker_test> docker-compose down
[+] Running 4/4
 ✔ Container demoapp            Removed       0.9s 
 ✔ Container docker_test-db-1   Removed       1.7s 
 ✔ Network docker_test_webnet   Removed       0.9s 
 ✔ Network docker_test_default  Removed       0.5s 
PS E:\VS Code Projects\FullStack Projects\docker_test> docker volume rm docker_test_db-data
docker_test_db-data
PS E:\VS Code Projects\FullStack Projects\docker_test> docker-compose up -d
[+] Running 5/5
 ✔ Network docker_test_default   Created      0.1s 
 ✔ Network docker_test_webnet    Created      0.1s 
 ✔ Volume "docker_test_db-data"  Created      0.0s 
 ✔ Container demoapp             Started      0.5s 
 ✔ Container docker_test-db-1    Started      0.5s 
PS E:\VS Code Projects\FullStack Projects\docker_test> docker-compose ps   
NAME               IMAGE                        COMMAND                  SERVICE   CREATED         STATUS         PORTS
demoapp            chanmyaeaung29/demoapp:1.0   "docker-entrypoint.s…"   web       4 seconds ago   Up 4 seconds   0.0.0.0:8080->8080/tcp, [::]:8080->8080/tcp
docker_test-db-1   mysql                        "docker-entrypoint.s…"   db        4 seconds ago   Up 4 seconds   3306/tcp, 33060/tcp
PS E:\VS Code Projects\FullStack Projects\docker_test> curl http://localhost:8080
{"message":"Docker is easy"}
```

