<h1 align="center">
    <span valign="middle">
        # Piston
    </span>
</h1>

<h3 align="center">A high performance general purpose code execution engine.</h3>

<h4 align="center">
  <a href="https://piston.readthedocs.io">Documentation</a>
</h4>

---

<br>

# About

<h4>
    Piston is a high performance general purpose code execution engine. It excels at running untrusted and
    possibly malicious code without fear from any harmful effects.
</h4>

<br>

It's used in numerous places including:

-   [EMKC Challenges](https://emkc.org/challenges)
-   [EMKC Weekly Contests](https://emkc.org/contests)
-   [Engineer Man Discord Server](https://discord.gg/engineerman)
-   Web IDEs
-   200+ direct integrations

<br>

### Official Extensions

The following are approved and endorsed extensions/utilities to the core Piston offering.

-   [I Run Code](https://github.com/engineer-man/piston-bot), a Discord bot used in 4100+ servers to handle arbitrary code evaluation in Discord. To get this bot in your own server, go here: https://emkc.org/run.
-   [Piston CLI](https://github.com/Shivansh-007/piston-cli), a universal shell supporting code highlighting, files, and interpretation without the need to download a language.
-   [Node Piston Client](https://github.com/dthree/node-piston), a Node.js wrapper for accessing the Piston API.
-   [Piston4J](https://github.com/the-codeboy/Piston4J), a Java wrapper for accessing the Piston API.
-   [Pyston](https://github.com/ffaanngg/pyston), a Python wrapper for accessing the Piston API.
-   [Go-Piston](https://github.com/milindmadhukar/go-piston), a Golang wrapper for accessing the Piston API.
-   [piston_rs](https://github.com/Jonxslays/piston_rs), a Rust wrapper for accessing the Piston API.
-   [piston_rspy](https://github.com/Jonxslays/piston_rspy), Python bindings for accessing the Piston API via `piston_rs`.

<br>

# Public API

-   Requires no installation and you can use it immediately.
-   Reference the Runtimes/Execute sections below to learn about the request and response formats.

<br>

When using the public Piston API, use the following two URLs:

```
GET  https://emkc.org/api/v2/piston/runtimes
POST https://emkc.org/api/v2/piston/execute
```

> Important Note: The Piston API is rate limited to 5 requests per second. If you have a need for more requests than that
> and it's for a good cause, please reach out to me (EngineerMan#0001) on [Discord](https://discord.gg/engineerman)
> so we can discuss potentially getting you an unlimited key. What is and isn't a good cause is up to me, but, in general
> if your project is a) open source, b) helping people at no cost to them, and c) not likely to use tons of resources
> thereby impairing another's ability to enjoy Piston, you'll likely be granted a key.

<br>

# Getting Started

## All In One

### Host System Package Dependencies

-   Docker
-   Docker Compose
-   Node JS (>= 13, preferably >= 15)

### After system dependencies are installed, clone this repository:

```sh
# clone and enter repo
git clone https://github.com/engineer-man/piston
```

### Installation with docker in general

```sh
# Start the API container
docker-compose up -d api

# Install all the dependencies for the cli
cd cli && npm i && cd -
```

The API will now be online with no language runtimes installed. To install runtimes, [use the CLI](#cli).

## Just Piston (no CLI)

### Host System Package Dependencies

-   Docker

### Installation

```sh
docker run \
    -v $PWD:'/piston' \
    --tmpfs /piston/jobs \
    -dit \
    -p 2000:2000 \
    --name piston_api \
    ghcr.io/engineer-man/piston
```

## Piston for testing packages locally

### Host System Package Dependencies

-   Same as [All In One](#All-In-One)

### Installation

```sh
# Build the Docker containers
./piston start

# For more help
./piston help
```
<br>

### Installation local docker image
```sh
# Navigate to docker-compose.yaml

services:
    api:
        build: 
            context: .
            dockerfile: ./api/Dockerfile
        container_name: piston_api
        restart: always
        ports:
            - 2000:2000
        volumes:
            - `navigate\to\your\piston\directory`\piston\api:/piston_api/api
        tmpfs:
            - /piston/jobs:exec,uid=1000,gid=1000,mode=711
            - /tmp:exec

```
<br>

```sh
# Navigate to api/Dockerfile

FROM node:15.10.0-buster-slim

ENV DEBIAN_FRONTEND=noninteractive

RUN dpkg-reconfigure -p critical dash
RUN for i in $(seq 1001 1500); do \
    groupadd -g $i runner$i && \
    useradd -M runner$i -g $i -u $i ; \
    done
RUN apt-get update && \
    apt-get install -y libxml2 gnupg tar coreutils util-linux libc6-dev \
    binutils build-essential locales libpcre3-dev libevent-dev libgmp3-dev \
    libncurses6 libncurses5 libedit-dev libseccomp-dev rename procps python3 \
    libreadline-dev libblas-dev liblapack-dev libpcre3-dev libarpack2-dev \
    libfftw3-dev libglpk-dev libqhull-dev libqrupdate-dev libsuitesparse-dev \
    libsundials-dev libpcre2-dev && \
    rm -rf /var/lib/apt/lists/*

RUN sed -i '/en_US.UTF-8/s/^# //g' /etc/locale.gen && locale-gen

WORKDIR /piston_api
COPY ["./api", "./"]
RUN npm install


RUN make -C ./src/nosocket/ all && make -C ./src/nosocket/ install

CMD [ "node", "src"]
EXPOSE 2000/tcp

```
<br>

### CORS error solving

```sh
 #Navigate to to api/package.json
 #add cors and make sure about the version
"dependencies": {
        "cors": "^2.8.5",
}

```


```sh
 #Navigate to to api/src/index.js
 #import cors

const cors = require('cors');

 # Enable CORS middleware
const allowedOrigins = ['http://example.example'];
app.use(cors({
  origin: function (origin, callback) {
    if (allowedOrigins.includes(origin) || !origin) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  }
}));

```

```sh
docker-compose build api

docker-compose up -d

```
<br>

### When we want to run the piston with the backend project we need to do some steps:

### 1. turn off the docker compose
```sh
docker-compose down

```
<br>

### 2. delete the docker image with name  ``piston_api`` form docker desktop or by command line
```sh
docker rmi piston-api

```

<br>

### 3. run build
```sh
docker-compose build api

```

<br>

### 4. check the docker networks 
```sh 
docker network ls

```

<br>

### 5. check what is in the network
```sh
docker network inspect [network-name]

```

<br>

### 6. run the docker image into project network
```sh
docker run --name piston-api --network [project-network-name] -p 2000:2000 -v /path/to/piston_data:/piston piston-api

```

<br>
 
# Usage

### CLI

The CLI is the main tool used for installing packages within piston, but also supports running code.

You can execute the cli with `cli/index.js`.

```sh
# List all available packages
cli/index.js ppman list

# Install latest python
cli/index.js ppman install python

# Install specific version of python
cli/index.js ppman install python=3.9.4

# Run a python script using the latest version
echo 'print("Hello world!")' > test.py
cli/index.js run python test.py

# Run a python script using a specific version
echo 'print("Hello world!")' > test.py
cli/index.js run python test.py -l 3.9.4
cli/index.js run python test.py -l 3.x
cli/index.js run python test.py -l 3
```

If you are operating on a remote machine, add the `-u` flag like so:

```sh
cli/index.js -u http://piston.server:2000 ppman list
```

### API

The container exposes an API on port 2000 by default.
This is used by the CLI to carry out running jobs and package management.

#### Runtimes Endpoint

`GET /api/v2/runtimes`
This endpoint will return the supported languages along with the current version and aliases. To execute
code for a particular language using the `/api/v2/execute` endpoint, either the name or one of the aliases must
be provided, along with the version.
Multiple versions of the same language may be present at the same time, and may be selected when running a job.

```json
HTTP/1.1 200 OK
Content-Type: application/json

[
    {
        "language": "bash",
        "version": "5.1.0",
        "aliases": [
            "sh"
        ]
    },
    {
        "language": "brainfuck",
        "version": "2.7.3",
        "aliases": [
            "bf"
        ]
    },
    ...
]
```

#### Execute Endpoint

`POST /api/v2/execute`
This endpoint requests execution of some arbitrary code.

-   `language` (**required**) The language to use for execution, must be a string and must be installed.
-   `version` (**required**) The version of the language to use for execution, must be a string containing a SemVer selector for the version or the specific version number to use.
-   `files` (**required**) An array of files containing code or other data that should be used for execution. The first file in this array is considered the main file.
-   `files[].name` (_optional_) The name of the file to upload, must be a string containing no path or left out.
-   `files[].content` (**required**) The content of the files to upload, must be a string containing text to write.
-   `files[].encoding` (_optional_) The encoding scheme used for the file content. One of `base64`, `hex` or `utf8`. Defaults to `utf8`.
-   `stdin` (_optional_) The text to pass as stdin to the program. Must be a string or left out. Defaults to blank string.
-   `args` (_optional_) The arguments to pass to the program. Must be an array or left out. Defaults to `[]`.
-   `compile_timeout` (_optional_) The maximum time allowed for the compile stage to finish before bailing out in milliseconds. Must be a number or left out. Defaults to `10000` (10 seconds).
-   `run_timeout` (_optional_) The maximum time allowed for the run stage to finish before bailing out in milliseconds. Must be a number or left out. Defaults to `3000` (3 seconds).
-   `compile_memory_limit` (_optional_) The maximum amount of memory the compile stage is allowed to use in bytes. Must be a number or left out. Defaults to `-1` (no limit)
-   `run_memory_limit` (_optional_) The maximum amount of memory the run stage is allowed to use in bytes. Must be a number or left out. Defaults to `-1` (no limit)

```json
{
    "language": "js",
    "version": "15.10.0",
    "files": [
        {
            "name": "my_cool_code.js",
            "content": "console.log(process.argv)"
        }
    ],
    "stdin": "",
    "args": ["1", "2", "3"],
    "compile_timeout": 10000,
    "run_timeout": 3000,
    "compile_memory_limit": -1,
    "run_memory_limit": -1
}
```

A typical response upon successful execution will contain 1 or 2 keys `run` and `compile`.
`compile` will only be present if the language requested requires a compile stage.

Each of these keys has an identical structure, containing both a `stdout` and `stderr` key, which is a string containing the text outputted during the stage into each buffer.
It also contains the `code` and `signal` which was returned from each process.

```json
HTTP/1.1 200 OK
Content-Type: application/json

{
    "language": "js",
    "version": "15.10.0",
    "run": {
        "stdout": "[\n  '/piston/packages/node/15.10.0/bin/node',\n  '/piston/jobs/9501b09d-0105-496b-b61a-e5148cf66384/my_cool_code.js',\n  '1',\n  '2',\n  '3'\n]\n",
        "stderr": "",
        "output": "[\n  '/piston/packages/node/15.10.0/bin/node',\n  '/piston/jobs/9501b09d-0105-496b-b61a-e5148cf66384/my_cool_code.js',\n  '1',\n  '2',\n  '3'\n]\n",
        "code": 0,
        "signal": null
    }
}
```

If a problem exists with the request, a `400` status code is returned and the reason in the `message` key.

```json
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
    "message": "html-5.0.0 runtime is unknown"
}
```

<br>

# Supported Languages

`awk`,
`bash`,
`befunge93`,
`brachylog`,
`brainfuck`,
`bqn`,
`c`,
`c++`,
`cjam`,
`clojure`,
`cobol`,
`coffeescript`,
`cow`,
`crystal`,
`csharp`,
`csharp.net`,
`d`,
`dart`,
`dash`,
`dragon`,
`elixir`,
`emacs`,
`emojicode`,
`erlang`,
`file`,
`forte`,
`forth`,
`fortran`,
`freebasic`,
`fsharp.net`,
`fsi`,
`go`,
`golfscript`,
`groovy`,
`haskell`,
`husk`,
`iverilog`,
`japt`,
`java`,
`javascript`,
`jelly`,
`julia`,
`kotlin`,
`lisp`,
`llvm_ir`,
`lolcode`,
`lua`,
`matl`,
`nasm`,
`nasm64`,
`nim`,
`ocaml`,
`octave`,
`osabie`,
`paradoc`,
`pascal`,
`perl`,
`php`,
`ponylang`,
`powershell`,
`prolog`,
`pure`,
`pyth`,
`python`,
`python2`,
`racket`,
`raku`,
`retina`,
`rockstar`,
`rscript`,
`ruby`,
`rust`,
`samarium`,
`scala`,
`smalltalk`,
`sqlite3`,
`swift`,
`typescript`,
`basic`,
`basic.net`,
`vlang`,
`vyxal`,
`yeethon`,
`zig`,

<br>

# Principle of Operation

Piston uses Docker as the primary mechanism for sandboxing. There is an API within the container written in Node
which takes in execution requests and executees them within the container safely.
High level, the API writes any source code to a temporary directory in `/piston/jobs`.
The source file is either ran or compiled and ran (in the case of languages like c, c++, c#, go, etc.).

<br>

# Security

Docker provides a great deal of security out of the box in that it's separate from the system.
Piston takes additional steps to make it resistant to
various privilege escalation, denial-of-service, and resource saturation threats. These steps include:

-   Disabling outgoing network interaction
-   Capping max processes at 256 by default (resists `:(){ :|: &}:;`, `while True: os.fork()`, etc.)
-   Capping max files at 2048 (resists various file based attacks)
-   Cleaning up all temp space after each execution (resists out of drive space attacks)
-   Running as a variety of unprivileged users
-   Capping runtime execution at 3 seconds
-   Capping stdout to 65536 characters (resists yes/no bombs and runaway output)
-   SIGKILLing misbehaving code

<br>

# License

Piston is licensed under the MIT license.
