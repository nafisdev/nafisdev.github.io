---
layout: post
title: Pipe it up
comments: true
image: redisstream.jpeg
excerpt: Lets Uncover it
keywords: ""
---

To channelise a data stream, or pipe an event from a source to a destination , we have numerous tools and technologies to utilise. We need in complex etl pipeline, or realtime event sourcing designs. Lately, there have been development of services like redisstream, kafka and evenhub to facilaitate such requirements. 
When i was learning bash, i came accross pipe(|) which read from a stdin(default fd) and give result to stdout. So, The pipe symbol enables the establishment of an IPC mechanism using an anonymous pipe for data transfer between the commands involved in the pipeline. In this article, i'll start with basic implementation of IPC and gradually will dwell into similar but complex implentations.



## Lets Start

We can create a pipe to connect two process and execute its respective responsibilities

```rs
#include <unistd.h>
#include <iostream>
#include <sys/wait.h>

int main() {
    int pipefd[2];
    pipe(pipefd); // Creating the anonymous pipe

    pid_t pid = fork(); // Forking a child process

    if (pid == 0) {
        // Child process (command 1)
        close(pipefd[0]); // Close unused read end of the pipe

        // Redirecting stdout to the write end of the pipe
        dup2(pipefd[1], STDOUT_FILENO);

        // Executing command 1 (ls)
        execlp("ls", "ls", nullptr);
    } else if (pid > 0) {
        // Parent process
        pid_t pid2 = fork(); // Forking another child process

        if (pid2 == 0) {
            // Second child process (command 2)
            close(pipefd[1]); // Close unused write end of the pipe

            // Redirecting stdin to the read end of the pipe
            dup2(pipefd[0], STDIN_FILENO);

            // Executing command 2 (grep 'a')
            execlp("grep", "grep", "a", nullptr);
        } else if (pid2 > 0) {
            // Parent process
            close(pipefd[0]); // Close both ends of the pipe in the parent
            close(pipefd[1]);

            // Waiting for the second child process to complete
            wait(nullptr);
        }
    }

    return 0;
}


``` 

As soon , you get a requirement, to wire up source and destination, either we use conventional event handler or use tools like kafka or redis stream or eventhub. 

Lets try using event handler.
```rs

```



Now Lets try redis stream 
```rs
```


## Multiple System:

Issues
<br>
* Sequence of each event
* Achieving Concurrancy

### First Refer Then Invent

#### Kard: Lightweight Data Race Detection with Per-Thread Memory Protection
#### RunTest

#### Delayed Stability and Performance of Distributed Congestion Control[QueueManagement]

#### Alagar-Venkatesan Algorithm

#### FlexPushdownDB
