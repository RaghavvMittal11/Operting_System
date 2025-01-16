SIMPLE LOADER:

An ELF Loader in C

How to run : Type command "make" in Linux shell to run the code

Global Variables:

● Elf32_Ehdr *ehdr : Pointer to the ELF header ( initialized to NULL ). ● Elf32_Phdr *phdr : Pointer to the program header ( initialized to NULL ). ● int fd: File descriptor for the ELF binary (initialized to -1 ).. ● typedef int (*StartFuncType)(void): Function pointer to the entry point of the ELF binary

Cleanup:

● loader_cleanup(): This function is responsible for cleaning up resources: ○ This function frees the allocated heap memory for elf header and program header and initializes the reference variable to NULL. It also closes the file if it is open.

Main Loading and Execution Logic:

● **load_and_run_elf(char argv): This function does the loading and execution of the ELF binary. It performs the following steps: ○ It opens a read only file fd ○ Allocates memory for elf header and program header using malloc function . ○ It seeks the fd to the program header offset using lseek function ○ It iterates through every program header one by one and checks whether it is PT_LOAD . If it is then allocates virtual memory using mmap function ( we changed the NULL part to (void *)(uintptr_t)phdr[i].p_vaddr ) ○ It then type casts the entry point address extracted from elf header to _start using (StartFuncType) defined globally ○ Result is printed

Main Function:

● **main(int argc, char argv): This function is the entry point of the program. It:

Calls load_and_run_elf() to load and execute the ELF binary.
Calls loader_cleanup() to release resources after execution.
System Calls:

● open(): Used to open the ELF file in read-only mode. ● read(): Used to read data from the file descriptor. ● lseek(): Used to change file offset. ● mmap(): Used to map the segments of the ELF file into memory. ● close(): Used to close the opened ELF file descriptor. ● malloc(): Used to allocate heap memory for the ELF header and program header. ● free(): Used to free the allocated heap memory. ● exit(): Used to terminate the program in case of errors.

Error Checks

● It provides error checks wherever necessary

SHELL:

This is implementation of custom shell in C . Here user can also run pipe commands along with feature of & foreground processes .

Key Components:

Data Structure:
○ Data: It contain command in form of char** command where each element of array is command line argument , process ID, start time, and end time. ○ Node: Node of a double linked list that contains Data.

Node Management:
○ createNode(): Takes Data as input gives a node with previous and next pointing to null ○ addNode(): Adds a new node to the end of the linked list.

Command Execution:
○ command(): it is used for command without piping . ○ Executes a command using execvp(). ○ It creates a child process that does the execvp(). ○ If process is background(&) then parent does not wait() for child to finish execution else it uses wait(null) ○ In both cases commands data in added to the linked list as history.

Manual Command Parsing:
○ func(): The function takes command string as argument and splits it on basis of spaces and stores each string in char** args.

Pipes Handling:
○ Pipe(): It handles commands that have piping in them. ○ It separates commands by | symbol and pass them to function then creates child process for each one ○ Parent process set up the pipes . ○ Child process executes each commands using execvp(). The input and output of child processes are directed by using dup2 commands ○ History of each child process is saved in Linked list.

History:
○ printHis(): Displays the history in form of commands pid , start time , end time. ○ The shell supports history with help of double linked list which stores process command ,pid ,start time,, end time in from of Data Struct .

Main Function:
○ It takes input of command in an infinite while loop and checks if it is pipe or not ans uses command() and pipe() functions accordingly. ○ The loop terminates when the user enters exit, displaying the history before exiting.

SIMPLE SCHEDULER:

How to run:

type "make" in Linux Shell

type "./scheduler NCPU TSLICE"

MULTITHREADING:

How to Run:

type "make" in linux shell

type "./vector" and "./matrix"

Using Multithreading with Ease

The first thing the library needs to do is include the headers it needs like pthread. # 1. #include // h for thread management # 2. #include // functional for passing callable function # 3. #include // vector for thread handles and args # 4. #include // chrono for execution time measurement # 5. #include // iostream for logging These headers form the basis of the library’s powerful and reusable multithreading features.

For example, at a lower layer of the library are two basic structures of it called ThreadArgs1D and ThreadArgs2D. These structs simply wrap the arguments that threads need to work on 1D and 2D ranges. Dynamically assigned thread id (threadId), number of threads (numThreads), 1D thread index range (low, high) or 2D thread index range (low1, high1, low2, high2) to each thread. Additionally, a callable task There is also a callable task, in the form of a lambda function ( lambda ), which defines the computation logic for the assigned indices.

The execution starts when the user calls either one of those two parallel_for functions. 1D ParallelThen generates a range of indices to set a granular_1D_parallel_for implementation. In the same way, 2D parallel_for breaks the outer loop range into a set of outer indices to execute on threads while iterating the inner loop in full range for every assigned outer index. These partitions guarantee that the work is evenly spread across the available threads.

Task Distribution

The chunks will be distributed for processing by the threads, and the library will divide the range of the indices into equal-sized chunks. For 1D iterations, indices are divided linearly, and for 2D iterations, the outer loop is split across threads, while the inner loop is computed fully for every outer index. Should the range not divide evenly, the last thread will process the remainder, so everything always runs.

Creating and Managing Threads

Threads are created with the pthread_create function and each thread runs the designated function (thread_function_1d or thread_function_2d). These are the implementations of your threads, which assume the thread arguments, calculate the minimums and maximums of their threads, then call the user lambda function with the start and end index.

Synchronization

The last step is that pthread_join is called in the main thread, waiting for all threads to finish executing. This makes sure no thread exits early, and the main thread can continue safely, as long as all parallel computations have ended.

Parallel Execution

The library achieves a great speed-up in compute-heavy operations by distributing the load over several threads. The independence of threads allows for true parallelism and more efficient CPU usage, with each thread doing its work in its own range.

