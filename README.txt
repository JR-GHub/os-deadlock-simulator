CS4760 Project 5 - Resource Management with Deadlock Detection and Recovery
Jerome Ramey

Table of Contents
1. Overview
2. How to Compile
3. How to Run
4. Parameters
5. File Descriptions
6. Notes
7. Cleanup

Overview
This project simulates an operating system (oss) that manages multiple processes and allocates shared system resources. It detects and recovers from deadlocks by killing the process holding the most resources among those deadlocked. It uses a shared memory clock and a message queue for interprocess communication between the master and worker processes.

How to Compile
Run the following commands:

make clean
make

This compiles two executables: oss (master) and user_proc (worker processes).

How to Run
Run the simulation using:

./oss -n 20 -i 250 -f oss.log

Parameters:

-n        Number of total user processes to launch (default max is 20)
-i        Interval in milliseconds to attempt launching new child processes (default 250ms)
-f        Path to the log file (e.g., oss.log)
-h        Show help and usage message

File Descriptions

oss.c
Main controller. Initializes shared resources, forks user processes, tracks clock time, handles messages (requests/releases), queues blocked processes, and runs deadlock detection every second. Logs system status every 0.5s.

user_proc.c
Simulated user process. Periodically decides whether to request, release, or terminate based on clock time and random bounds. Sends messages to oss and blocks until a grant is received.

config.c / config.h
Contains constants, max limits, and utility configuration used across the system.

shared_clock.c / shared_clock.h
Manages simulated system time in shared memory (seconds and nanoseconds).

process_table.c / process_table.h
Implements the process control block (PCB) table. Tracks PID, active status, and resource allocations per process.

resource.c / resource.h
Defines resource descriptor structures and logic for allocating, releasing, and tracking available instances.

msgqueue.c / msgqueue.h
Sets up the System V message queue. Provides helper functions to send and receive structured messages for process communication.

token.c / token.h
Defines the Message structure and message types used by the processes to communicate over the message queue.

logger.c / logger.h
Handles all logging to both the terminal and the output file with optional verbosity controls and periodic summaries.

deadlock.c / deadlock.h
Contains deadlock detection logic (banker-style algorithm). Identifies cycles, picks a victim (process holding most resources), terminates it, and releases its allocations.

Makefile
Compiles all object files and links them into the two final executables: oss and user_proc. Also includes a make clean target to remove binaries and object files.

oss.log
Generated during execution. Contains logged system output, resource allocations, grants, releases, deadlocks, terminations, and summaries every 20 grants.

Notes
• Maximum 18 concurrent children at once (due to system constraints).
• Resources: 5 types, 10 instances each.
• IPC uses message queues only; shared memory is used only for the clock.
• Deadlock detection is triggered every simulated second.
• A maximum of 10000 log lines are written; logging stops after that point unless the run finishes.
• If a process terminates (after 1s of simulated time), it releases all its resources.
• In case of deadlock, oss kills processes one at a time (starting with the one holding the most resources) until the cycle is broken.

Cleanup
Make sure no shared memory segments or message queues are left. These are handled automatically in oss, but in case of abnormal termination:

ipcs
ipcrm -m [shmid]
ipcrm -q [msgid]
