# Building Swift Sandbox Webapp on z
___

## Build for SLES12 on x86
* ### Install Node.js 
    * Follow the instruction on [Nodejs website](https://nodejs.org/en/download/package-manager/#opensuse-and-sle)
* ### Install gulp
    > gulp is a toolkit that helps you automate painful or time-consuming tasks in your development workflow.
    * Follow the instruction on [gulp github page]( https://github.com/gulpjs/gulp/blob/master/docs/getting-started.md)
* ### Install dependency modules
    Output Messages:
    ```
    npm WARN optional Skipping failed optional dependency /chokidar/fsevents:
    npm WARN notsup Not compatible with your operating system or architecture: fsevents@1.0.8
    ```
* ### Run gulp in source directory
    #### 1. `$ gulp`
    Output Message:
    ```
    Starting server in development mode...
    When I hear a change, I will restart the server for you.
    ^C at any time to kill me.

    [13:45:33] Finished 'start-server' after 7.06 ms
    [13:45:33] Starting 'watch'...

    You do not have Redis; Redis is required to run properly.
    $ brew update && brew install redis
    ```
    * #### Install Redis ([download page](http://redis.io/download)) 
        ```sh
        $ http://download.redis.io/releases/redis-3.0.7.tar.gz
        $ tar -xvf redis-3.0.7.tar.gz
        $ cd redis-3.0.7
        $ make
        $ make test
        $ make install 
        ```
    #### 2. `$ gulp`
    Output Message:
    ```
    Starting server in development mode...
    When I hear a change, I will restart the server for you.
    ^C at any time to kill me.

    [14:02:16] Finished 'start-server' after 6.51 ms
    [14:02:16] Starting 'watch'...
    SERVER STDOUT -> The Sandbox server is coming up...Please wait
    SERVER STDOUT -> Creating instance of RedisClient class. Redis URL: redis://127.0.0.1:6379
    SERVER STDOUT -> About to connect to redis server...
    SERVER STDOUT -> Connected to redis server!
    SERVER STDOUT -> Value returned from redis. numberOfExecutions -> null
    SERVER STRERR -> You need to seed numberOfExecutions in Redis!!
    Server initialization failed... The server can not start and will now die. Bye bye :'(
    ```
    * #### Set Redis numberOfExecutions value
    ```sh
    $ screen -S redis-server
    $ redis-server
    ```
    `ctrl` + `a` + `d` (Detach screen) 
    ```sh
    $ redis-cli
    > SET numberOfExecutions 3
    > quit
    ```
    
    #### 3. `$ gulp`
    Output Message
    ```
    Starting server in development mode...
    When I hear a change, I will restart the server for you.
    ^C at any time to kill me.
    
    [14:16:35] Finished 'start-server' after 6.43 ms
    [14:16:35] Starting 'watch'...
    SERVER STDOUT -> The Sandbox server is coming up...Please wait
    SERVER STDOUT -> Creating instance of RedisClient class. Redis URL: redis://127.0.0.1:6379
    SERVER STDOUT -> About to connect to redis server...
    SERVER STDOUT -> Connected to redis server!
    SERVER STDOUT -> Value returned from redis. numberOfExecutions -> 3
    SERVER STDOUT -> Redis client is ready.
    SERVER STDOUT -> Configuration complete. The server is coming up...
    SERVER STDOUT -> Will only service requests with lag less than 100 ms.
    SERVER STDOUT -> Checking for old user Swift files every 1 mins.
    SERVER STDOUT -> Listening on port 3000.
    ```
* ### Open Sandbox in browser 
    The browser webpage shows up correctly. Run `print("hello world")` in Sandbox
    
    Server side output message
    ```  
    SERVER STDOUT -> { version: 'unknown',
                        target: 'unknown',
                         error: { [Error: Command failed: docker run --net=none --ulimit data=134217728:134217728 --ulimit locks=5:5 --ulimit fsize=134217728:134217728 --ulimit rss=134217728:134217728 --ulimit nproc=20:20 --ulimit cpu=6:6 -m 128M --rm -e  SWIFTFILE=code-tmp.swift -v                 /gsa/tlbgsa/home/d/e/deslin/tmp//sandbox/2432-2529f6c0-eadc-11e5-9bff-63ae209bd282/swiftfiles:/swiftfiles swiftlinux /bin/sh: docker: command not found
   ]
     killed: false,
     code: 127,
     signal: null,
     cmd: 'docker run --net=none --ulimit data=134217728:134217728 --ulimit locks=5:5 --ulimit fsize=134217728:134217728 --ulimit rss=134217728:134217728 --ulimit nproc=20:20 --ulimit cpu=6:6 -m 128M --rm -e SWIFTFILE=code-tmp.swift -v /gsa/tlbgsa/home/d/e/deslin/tmp//sandbox/2432-2529f6c0-eadc-11e5-9bff-63ae209bd282/swiftfiles:/swiftfiles swiftlinux' },
  stdout: '',
  stderr: null }

    SERVER STDOUT -> Saving results to cache
    ```
    Client side output message:
    ```
    unknown
    unknown
    Error running code: unknown error code 127.
    ```
* ### Install Docker ([reference](https://docs.docker.com/v1.8/installation/SUSE/))
    The docker repo url has changed from what it is on the docker install reference site.
    ```sh
    $ sudo zypper ar -f http://download.opensuse.org/repositories/Virtualization:/containers/SLE_12/ docker_virtual
    $ sudo zypper in docker
    $ sudo systemctl start docker
    ```
    * #### Run Swift code on Sandbox website
        Server side output message:
        ```
        SERVER STDOUT -> { version: 'unknown',
            target: 'unknown',
             error:
                { [Error]
                 killed: false,
                   code: 127,
                 signal: null,
                    cmd: 'docker run --net=none --ulimit data=134217728:134217728 --ulimit locks=5:5 --ulimit fsize=134217728:134217728 --ulimit rss=134217728:134217728 --ulimit nproc=20:20 --ulimit cpu=6:6 -m 128M --rm -e SWIFTFILE=code-tmp.swift -v /gsa/tlbgsa/home/d/e/deslin/tmp//sandbox/2432-2529f6c0-eadc-11e5-9bff-63ae209bd282/swiftfiles:/swiftfiles swiftlinux' },
            stdout: '',
            stderr: null }
        ```
* ### Build docker images: "swiftlinux:latest"
    ```sh
    $ cd <src_root>/docker/repl
    $ sudo docker build -t swiftlinux:latest .
    ```
    * #### Run docker cmd
    ```sh
    $ sudo docker run --net=none --ulimit data=134217728:134217728 --ulimit locks=5:5 --ulimit fsize=134217728:134217728 --ulimit rss=134217728:134217728 --ulimit nproc=20:20 --ulimit cpu=6:6 -m 128M --rm -e SWIFTFILE=code-tmp.swift -v /gsa/tlbgsa/home/d/e/deslin/tmp//sandbox/2432-2529f6c0-eadc-11e5-9bff-63ae209bd282/swiftfiles:/swiftfiles swiftlinux
    ```
    Output Message:
    ```
    WARNING: Your kernel does not support swap limit capabilities, memory limited without swap.
    docker: Error response from daemon: mkdir /gsa/tlbgsa/home/d/e/deslin/tmp/sandbox: permission denied.
    ```
* ### Run `$ gulp` as root
    ```sh
    $ sudo su
    $ gulp
    ```
    