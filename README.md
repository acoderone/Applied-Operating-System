# Applied Operating System

This repository is a collection of practical implementations and examples related to core topics in operating systems, with a focus on systems programming and process management. It includes various topics that demonstrate key concepts such as system calls, process management, file systems, memory management, concurrency, and more.

## Table of Contents

1. [System Calls](#system-calls)
2. [Process Management](#process-management)
3. [Memory Management](#memory-management)
4. [Concurrency](#concurrency)
5. [File Systems](#file-systems)
6. [Synchronization](#synchronization)
7. [Future Topics](#future-topics)

## System Calls

In operating systems, **system calls** provide an interface between user programs and the operating system. Key system calls related to process management include:

- **`fork()`**: Creates a new process by duplicating the calling process.
- **`exec()`**: Replaces the current process image with a new one.
- **`wait()`**: Makes the parent process wait for the child process to finish execution.

This section provides an implementation of the **`fork()`**, **`exec()`**, and **`wait()`** system calls to demonstrate their usage.

### Code Example

The following C++ code demonstrates how to create child processes, run external commands, and replace processes using `fork()`, `execlp()`, and `wait()`.

```cpp
#include<iostream>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <cstdlib>
#include<bits/stdc++.h>
using namespace std;

void execute_file(){
    pid_t pid;
    
    // Creating the first child process
    pid = fork();
    
    if(pid == 0){  // Child process
        cout << "Child process: Running 'ls' command..." << endl;
        execlp("/bin/ls", "ls", nullptr);
        cerr << "Exec for 'ls' failed" << endl;
        exit(1);
    }
    else if(pid < 0){  // Error in fork
        cerr << "Fork failed!" << endl;
        exit(1);
    }
    else{
        // Parent process waits for the first child process to finish
        int status;
        wait(&status);
        
        if(WIFEXITED(status)){
            cout << "Child process (ls) completed. Replacing it with 'pwd' command..." << endl;
            
            // Creating a second child process to run 'pwd'
            pid = fork();
            
            if(pid == 0){  // Child process for 'pwd'
                execlp("/bin/pwd", "pwd", nullptr);
                cerr << "Exec for 'pwd' failed!" << endl;
                exit(1);
            }
            else if(pid < 0){  // Error in fork
                cerr << "Fork failed!" << endl;
                exit(1);
            }
            else{
                // Parent process waits for the second child process to finish
                wait(&status);
                
                if(WIFEXITED(status)){
                    cout << "Child process (pwd) completed successfully." << endl;
                }
                else{
                    cout << "Child process (pwd) failed." << endl;
                }
            }
        }
        else{
            cout << "Initial child process did not terminate normally." << endl;
        }
    }
}

int main(){
    execute_file();
    return 0;
}
