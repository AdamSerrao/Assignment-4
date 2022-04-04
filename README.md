# Assignment 4
## CP386 Operating Systems Winter 2022
## Authors: Adam Serrao (190197530)

## Contributions
Everything within this project was done by Adam Serrao.

## Purpose
This project is used to practice the concepts of deadlock avoidance and contiguous memory allocation. Question 1 simulates the Banker's Algorithm where the user can request and release resources which the program keeps track of. Question 2 simulates memory allocation using the Best-Fit algorithm which determines the smallest unallocated space available for a new process. 

## How to run
This project includes a makefile to simplify the process of running the C files. The makefile uses the following commands:
<br>make runq1: to run Question 1</br>
<br>make runq2: to run Question 2</br>


## Testing
### Question 1
```
osc@ubuntu:~/A4$ make runq1
gcc -Werror -Wall -g -std=gnu99 -o Question1 Question1.c -lrt -lpthread
./Question1 10 5 7 8
Number of Customers: 5
Currently Available Resources: 10 5 7 8 
Maximum Resources from file: 
6 4 7 3 
4 2 3 2 
2 5 3 3 
6 3 3 2 
5 5 7 5 
Enter a Command: RQ 0 1 0 0 1
State is safe, and request is satisfied
Enter a Command: RQ 1 1 1 1 1
State is safe, and request is satisfied
Enter a Command: RQ 2 2 2 2 2
State is safe, and request is satisfied
Enter a Command: RQ 3 1 1 1 1
State is safe, and request is satisfied
Enter a Command: RQ 4 1 0 0 0
State is safe, and request is satisfied
Enter a Command: Status
Available Resources:
4 1 3 3 
Maximum Resources:
6 4 7 3 
4 2 3 2 
2 5 3 3 
6 3 3 2 
5 5 7 5 
Allocated Resources:
1 0 0 1 
1 1 1 1 
2 2 2 2 
1 1 1 1 
1 0 0 0 
Need Resources:
5 4 7 2 
3 1 2 1 
0 3 1 1 
5 2 2 1 
4 5 7 5 
Enter a Command: Run
Safe Sequence is: 1 3 2 4 0 
--> Customer/Thread 1
    Allocated Resources: 1 1 1 1 
    Needed: 3 1 2 1 
    Available: 4 1 3 3 
    Thread has started
    Thread has finished
    Thread is releasing resources
    New Available: 5 2 4 4 
--> Customer/Thread 3
    Allocated Resources: 1 1 1 1 
    Needed: 5 2 2 1 
    Available: 5 2 4 4 
    Thread has started
    Thread has finished
    Thread is releasing resources
    New Available: 6 3 5 5 
--> Customer/Thread 2
    Allocated Resources: 2 2 2 2 
    Needed: 0 3 1 1 
    Available: 6 3 5 5 
    Thread has started
    Thread has finished
    Thread is releasing resources
    New Available: 8 5 7 7 
--> Customer/Thread 4
    Allocated Resources: 1 0 0 0 
    Needed: 4 5 7 5 
    Available: 8 5 7 7 
    Thread has started
    Thread has finished
    Thread is releasing resources
    New Available: 9 5 7 7 
--> Customer/Thread 0
    Allocated Resources: 1 0 0 1 
    Needed: 5 4 7 2 
    Available: 9 5 7 7 
    Thread has started
    Thread has finished
    Thread is releasing resources
    New Available: 10 5 7 8 
Enter a Command: Exit
```
### Question 2
```
osc@ubuntu:~/A4$ make runq2
./Question2 1000000
Allocated 1000000 bytes of memory
command>RQ P0 200000 B
Successfully allocated 200000 to process P0
command>RQ P1 350000 B
Successfully allocated 350000 to process P1
command>RQ P2 300000 B
Successfully allocated 300000 to process P2
command>RL P0
Successfully released memory for process P0
command>Status
Partitions [Allocated Memory = 650000]
Address [200000 : 549999] Process P1
Address [550000 : 849999] Process P2


Holes [Free Memory = 350000]
Address [0 : 199999] len 200000
Address [850000 : 999999] len 150000
command>RQ P3 120000 B
Successfully allocated 120000 to process P3
command>Status
Partitions [Allocated Memory = 770000]
Address [200000 : 549999] Process P1
Address [550000 : 849999] Process P2
Address [850000 : 969999] Process P3


Holes [Free Memory = 230000]
Address [0 : 199999] len 200000
Address [970000 : 999999] len 30000
command>RQ P4 150000 B
Successfully allocated 150000 to process P4
command>RQ P5 80000 B
No hole of sufficient size
command>Status
Partitions [Allocated Memory = 920000]
Address [0 : 149999] Process P4
Address [200000 : 549999] Process P1
Address [550000 : 849999] Process P2
Address [850000 : 969999] Process P3


Holes [Free Memory = 80000]
Address [150000 : 199999] len 50000
Address [970000 : 999999] len 30000
command>Exit
```
## Test Cases
This program is meant for general use when using the input values given in the makefile.

## About Me
I am a third year computer science major at Wilfrid Laurier University in Ontario, Canada.

## License


