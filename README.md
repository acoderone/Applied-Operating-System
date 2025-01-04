# Applied Operating System

This repository serves as a comprehensive guide to understanding and implementing core concepts of operating systems through practical examples. The main focus is on **systems programming** and **process management**. Each topic includes explanations, example code, and step-by-step guidance to help developers deepen their understanding of operating system internals.

---

## Table of Contents

1. [Introduction](#introduction)
2. [System Calls](#system-calls)
   - [Overview](#overview)
   - [Code Example](#code-example)
   - [Explanation](#explanation)
3. [Process Management](#process-management)
   - [Key Concepts](#key-concepts)
   - [Lifecycle of a Process](#lifecycle-of-a-process)
   - [Process Scheduling](#process-scheduling)
   - [Code Example](#process-scheduling-example)
4. [Memory Management](#memory-management)
5. [Concurrency](#concurrency)
6. [File Systems](#file-systems)
7. [Synchronization](#synchronization)
8. [How to Run the Code](#how-to-run-the-code)
9. [Contributing](#contributing)


---

## Introduction

An operating system (OS) acts as an intermediary between users and computer hardware. Core functionalities of an OS include:

- Managing processes and resources
- Providing security and access control
- Offering an interface for user applications through system calls

This repository focuses on hands-on demonstrations to understand these core functionalities better.

---

## System Calls

### Overview

System calls are the interface between user programs and the operating system kernel. They allow user programs to request services from the OS, such as process creation, file handling, and memory management.

### Common System Calls

1. **`fork()`**: Used to create a new process by duplicating the calling process.
2. **`exec()`**: Replaces the current process image with a new program.
3. **`wait()`**: Makes the parent process wait for the termination of the child process.

### Code Example

```cpp
#include <iostream>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <cstdlib>

using namespace std;

void execute_file() {
    pid_t pid = fork();

    if (pid == 0) {
        // Child process
        cout << "Child process: Running 'ls' command..." << endl;
        execlp("/bin/ls", "ls", nullptr);
        cerr << "Exec for 'ls' failed!" << endl;
        exit(1);
    } else if (pid > 0) {
        // Parent process
        int status;
        wait(&status);
        if (WIFEXITED(status)) {
            cout << "Child process completed successfully." << endl;
        }
    } else {
        cerr << "Fork failed!" << endl;
    }
}

int main() {
    execute_file();
    return 0;
}
```

### Explanation

- **`fork()`**: Creates a child process that is an exact copy of the parent.
- **`execlp()`**: Replaces the current process with the `ls` command.
- **`wait()`**: Ensures the parent process waits for the child to finish execution before continuing.

---

## Process Management

### Key Concepts

1. **Process Lifecycle**: Understanding the stages of a process—New, Ready, Running, Waiting, and Terminated.
2. **Process Control Block (PCB)**: Data structure used by the OS to store information about a process (e.g., PID, program counter, and state).
3. **Scheduling Algorithms**: Determines how processes share CPU time efficiently.
4. **Inter-Process Communication (IPC)**: Mechanisms for processes to communicate and synchronize.

### Lifecycle of a Process

1. **New**: Process is being created.
2. **Ready**: Process is ready to run but waiting for CPU time.
3. **Running**: Process is currently being executed by the CPU.
4. **Waiting**: Process is waiting for some I/O or event to complete.
5. **Terminated**: Process has finished execution.

### Process Scheduling

Efficient process scheduling ensures maximum CPU utilization and fair process execution. Common algorithms include:

- **First-Come, First-Served (FCFS)**
- **Shortest Job Next (SJN)**
- **Round Robin (RR)**
- **Priority Scheduling**

### Process Scheduling Example

The following code demonstrates **Round Robin Scheduling**:

```cpp
#include <iostream>
#include <queue>
#include <vector>

using namespace std;

struct Process {
    int pid;
    int burstTime;
    int remainingTime;
};

void roundRobinScheduling(vector<Process> &processes, int timeQuantum) {
    queue<Process> readyQueue;
    for (auto &process : processes) {
        readyQueue.push(process);
    }

    cout << "Round Robin Scheduling:" << endl;

    while (!readyQueue.empty()) {
        Process current = readyQueue.front();
        readyQueue.pop();

        if (current.remainingTime <= timeQuantum) {
            cout << "Process " << current.pid << " completed. "
                 << "Time taken: " << current.burstTime << " units." << endl;
        } else {
            current.remainingTime -= timeQuantum;
            cout << "Process " << current.pid << " executed for "
                 << timeQuantum << " units. Remaining time: "
                 << current.remainingTime << " units." << endl;
            readyQueue.push(current);
        }
    }
}

int main() {
    vector<Process> processes = {
        {1, 10, 10},
        {2, 5, 5},
        {3, 8, 8},
    };

    int timeQuantum = 3;
    roundRobinScheduling(processes, timeQuantum);
    return 0;
}
```

---

## Memory Management

**Coming Soon**: Detailed examples of memory allocation techniques such as paging, segmentation, and dynamic memory allocation.

---

## Concurrency

**Coming Soon**: Implementation of threads, synchronization primitives, and solutions to common concurrency issues.

---

## File Systems

**Coming Soon**: Simulations of file handling, directory structures, and file system management.

---

## Synchronization

**Coming Soon**: Demonstrations of semaphores, monitors, and other synchronization tools to resolve critical section problems.

---

## How to Run the Code

1. Clone the repository:
   ```bash
   git clone https://github.com/your-username/applied-operating-system.git
   cd applied-operating-system
   ```

2. Compile the C++ files:
   ```bash
   g++ -o process_management process_management.cpp
   g++ -o round_robin round_robin.cpp
   ```

3. Run the executables:
   ```bash
   ./process_management
   ./round_robin
   ```

---

## Contributing

Contributions are welcome! Feel free to open an issue or submit a pull request.

---


