# System Calls in C++

This repository contains a practical implementation of system calls related to process management in operating systems, written in C++. The key system calls covered are:

- **`fork()`**: Creates a new process by duplicating the calling process.
- **`exec()`**: Replaces the current process image with a new program.
- **`wait()`**: Makes the parent process wait for the child process to finish execution.

## Table of Contents
1. [Introduction](#introduction)
2. [System Calls Covered](#system-calls-covered)
3. [Code Example](#code-example)
4. [How It Works](#how-it-works)
5. [Running the Code](#running-the-code)

## Introduction

In operating systems, **system calls** provide an interface for applications to interact with the OS kernel. Key system calls related to process management include:

- **`fork()`**: Creates a new process by duplicating the calling process.
- **`exec()`**: Replaces the current process with a new program.
- **`wait()`**: Makes the parent process wait until its child process terminates.

These system calls are fundamental for process creation, management, and inter-process communication in many OS environments.

## System Calls Covered

### 1. **fork()**
The **`fork()`** system call creates a new process by duplicating the calling process. After calling `fork()`, both the parent and child processes will continue executing the code following the `fork()`. It returns a value of `0` in the child process and the child’s PID in the parent process.

### 2. **exec()**
The **`exec()`** family of functions (e.g., **`execlp()`**) replaces the current process image with a new one. This is useful for running external programs or commands.

### 3. **wait()**
The **`wait()`** system call allows the parent process to wait until its child process terminates. This ensures that the parent can handle the child process’s termination status.

## Code Example

Here is an example of a C++ program that demonstrates **`fork()`**, **`exec()`**, and **`wait()`** system calls. The program first creates a child process to run the `ls` command, and after it completes, the parent process creates another child process to run the `pwd` command.

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
