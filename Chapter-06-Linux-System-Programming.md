# Chapter 6: Linux System Programming
## Bridging User Space and Kernel Space

---

## 🎯 **LEARNING OBJECTIVES**

By the end of this chapter, you will master:
- System calls and library functions
- File I/O operations and stream processing
- Process management (fork, exec, wait)
- Inter-process communication mechanisms
- Threading and synchronization primitives

---

## 📚 **THE 5-PILLAR FRAMEWORK**

### **PILLAR 1: PURPOSE — Why System Programming is Essential**

#### **The Motivation: Understanding User-Kernel Interface**

**What is System Programming?**
System programming is the art of writing programs that interact directly with the operating system kernel. Unlike application programming, system programming requires understanding how user space programs communicate with the kernel to access system resources like files, processes, memory, and hardware.

**Why Learn System Programming for Kernel Development?**
- **Bridge Understanding**: System programming is the bridge between user applications and kernel code
- **Kernel Interface**: System calls are the primary interface between user and kernel space
- **Resource Management**: Learn how the kernel manages and provides access to system resources
- **Performance**: Understand how to write efficient programs that work well with the kernel
- **Debugging**: Essential for debugging both user and kernel space issues

**Real-World Applications:**
- **Device Drivers**: System programming concepts are essential for driver development
- **System Utilities**: Tools like `ps`, `top`, `ls` are built using system programming
- **Network Programming**: Socket programming uses system calls extensively
- **Database Systems**: Database engines use system programming for file I/O and memory management
- **Web Servers**: Web servers like Apache and Nginx use system programming for performance

#### **System Call Interface**

**What are System Calls?**
System calls are special functions that allow user programs to request services from the operating system kernel. They are the only way user programs can access kernel functionality.

**How System Calls Work:**
```
User Program → Library Function → System Call → Kernel → Hardware
     ↓              ↓              ↓           ↓         ↓
  Application    glibc/wrapper   syscall()   Kernel    Device
   Code          Function        Instruction  Code      Driver
```

**Example: Reading a File**
```c
// When you do this in a program:
int fd = open("/etc/passwd", O_RDONLY);
char buffer[100];
read(fd, buffer, sizeof(buffer));
close(fd);

// Here's what happens:
// 1. open() is a library function that calls the open() system call
// 2. Library function sets up system call parameters
// 3. syscall() instruction switches to kernel mode
// 4. Kernel validates the request and performs the operation
// 5. Kernel returns result to user space
// 6. Library function returns result to your program
```

#### **Goals of System Programming Mastery**

**Primary Goals:**
1. **System Call Mastery**: Understand and use system calls effectively
2. **File I/O Expertise**: Master file operations and stream processing
3. **Process Management**: Create, manage, and control processes
4. **IPC Mastery**: Implement inter-process communication
5. **Threading Skills**: Use threads and synchronization primitives

**Secondary Goals:**
1. **Performance**: Write efficient system programs
2. **Error Handling**: Implement robust error handling
3. **Security**: Write secure system programs
4. **Portability**: Write portable system programs
5. **Debugging**: Debug system programming issues

---

### **PILLAR 2: FUNCTIONALITY & SCOPE — What System Programming Provides**

#### **System Calls and Library Functions**

**1. File System Calls**
```c
#include <fcntl.h>
#include <unistd.h>
#include <sys/stat.h>

// File operations
int open(const char *pathname, int flags, mode_t mode);
// - pathname: path to the file
// - flags: how to open (O_RDONLY, O_WRONLY, O_RDWR, O_CREAT, etc.)
// - mode: file permissions (only used with O_CREAT)
// Returns: file descriptor (positive integer) or -1 on error

int close(int fd);
// - fd: file descriptor to close
// Returns: 0 on success, -1 on error

ssize_t read(int fd, void *buf, size_t count);
// - fd: file descriptor
// - buf: buffer to read into
// - count: maximum bytes to read
// Returns: number of bytes read, 0 on EOF, -1 on error

ssize_t write(int fd, const void *buf, size_t count);
// - fd: file descriptor
// - buf: buffer to write from
// - count: number of bytes to write
// Returns: number of bytes written, -1 on error

off_t lseek(int fd, off_t offset, int whence);
// - fd: file descriptor
// - offset: byte offset
// - whence: SEEK_SET (beginning), SEEK_CUR (current), SEEK_END (end)
// Returns: new file position, -1 on error
```

**2. Process System Calls**
```c
#include <unistd.h>
#include <sys/wait.h>

pid_t fork(void);
// Creates a new process by duplicating the current process
// Returns: 0 in child, child PID in parent, -1 on error

int execve(const char *pathname, char *const argv[], char *const envp[]);
// Replaces current process with a new program
// - pathname: path to executable
// - argv: argument vector (array of strings)
// - envp: environment variables
// Returns: only on error (-1)

pid_t wait(int *wstatus);
// Waits for any child process to change state
// - wstatus: pointer to store exit status
// Returns: child PID, -1 on error

pid_t waitpid(pid_t pid, int *wstatus, int options);
// Waits for specific child process
// - pid: process ID (-1 for any child, 0 for same group, >0 for specific)
// - wstatus: pointer to store exit status
// - options: WNOHANG (don't block), WUNTRACED (include stopped)
// Returns: child PID, 0 if WNOHANG and no child, -1 on error
```

**3. Memory System Calls**
```c
#include <sys/mman.h>

void *mmap(void *addr, size_t length, int prot, int flags, int fd, off_t offset);
// Maps files or devices into memory
// - addr: suggested address (NULL for system choice)
// - length: size of mapping
// - prot: protection (PROT_READ, PROT_WRITE, PROT_EXEC)
// - flags: MAP_SHARED, MAP_PRIVATE, MAP_ANONYMOUS
// - fd: file descriptor (-1 for anonymous mapping)
// - offset: file offset
// Returns: mapped address, MAP_FAILED on error

int munmap(void *addr, size_t length);
// Unmaps memory region
// - addr: mapped address
// - length: size to unmap
// Returns: 0 on success, -1 on error
```

#### **File I/O Operations**

**1. Basic File Operations**
```c
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>
#include <errno.h>

int main() {
    int fd;
    char buffer[100];
    ssize_t bytes_read;
    
    // Open file for reading
    fd = open("/etc/passwd", O_RDONLY);
    if (fd == -1) {
        perror("open failed");
        return 1;
    }
    
    // Read from file
    bytes_read = read(fd, buffer, sizeof(buffer) - 1);
    if (bytes_read == -1) {
        perror("read failed");
        close(fd);
        return 1;
    }
    
    // Null-terminate string
    buffer[bytes_read] = '\0';
    
    // Print content
    printf("Read %zd bytes:\n%s\n", bytes_read, buffer);
    
    // Close file
    close(fd);
    return 0;
}
```

**2. File Creation and Writing**
```c
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>
#include <sys/stat.h>

int main() {
    int fd;
    const char *data = "Hello, World!\n";
    ssize_t bytes_written;
    
    // Create file with permissions
    fd = open("output.txt", O_CREAT | O_WRONLY | O_TRUNC, 0644);
    if (fd == -1) {
        perror("open failed");
        return 1;
    }
    
    // Write data to file
    bytes_written = write(fd, data, strlen(data));
    if (bytes_written == -1) {
        perror("write failed");
        close(fd);
        return 1;
    }
    
    printf("Wrote %zd bytes\n", bytes_written);
    
    // Close file
    close(fd);
    return 0;
}
```

**3. File Positioning**
```c
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>
#include <sys/stat.h>

int main() {
    int fd;
    char buffer[10];
    off_t position;
    
    fd = open("test.txt", O_RDWR | O_CREAT, 0644);
    if (fd == -1) {
        perror("open failed");
        return 1;
    }
    
    // Write some data
    write(fd, "Hello, World!", 13);
    
    // Get current position
    position = lseek(fd, 0, SEEK_CUR);
    printf("Current position: %ld\n", position);
    
    // Seek to beginning
    lseek(fd, 0, SEEK_SET);
    
    // Read first 10 characters
    read(fd, buffer, 10);
    buffer[10] = '\0';
    printf("First 10 chars: %s\n", buffer);
    
    // Seek to end
    position = lseek(fd, 0, SEEK_END);
    printf("File size: %ld bytes\n", position);
    
    close(fd);
    return 0;
}
```

#### **Process Management**

**1. Process Creation with fork()**
```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    pid_t pid;
    int status;
    
    printf("Parent process: PID = %d\n", getpid());
    
    // Create child process
    pid = fork();
    
    if (pid == 0) {
        // Child process
        printf("Child process: PID = %d, Parent PID = %d\n", 
               getpid(), getppid());
        
        // Child does some work
        sleep(2);
        printf("Child process finished\n");
        exit(42);  // Exit with status 42
        
    } else if (pid > 0) {
        // Parent process
        printf("Parent process: Created child with PID = %d\n", pid);
        
        // Wait for child to finish
        pid_t child_pid = wait(&status);
        printf("Parent process: Child %d finished with status = %d\n", 
               child_pid, WEXITSTATUS(status));
        
    } else {
        // Error creating child
        perror("fork failed");
        return 1;
    }
    
    return 0;
}
```

**2. Process Replacement with exec()**
```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    pid_t pid;
    int status;
    
    pid = fork();
    
    if (pid == 0) {
        // Child process - replace with new program
        char *args[] = {"ls", "-la", "/home", NULL};
        char *env[] = {"PATH=/bin:/usr/bin", NULL};
        
        execve("/bin/ls", args, env);
        
        // This line only executes if execve fails
        perror("execve failed");
        exit(1);
        
    } else if (pid > 0) {
        // Parent process
        printf("Parent: Created child process %d\n", pid);
        
        // Wait for child to finish
        wait(&status);
        printf("Parent: Child finished with status %d\n", WEXITSTATUS(status));
        
    } else {
        perror("fork failed");
        return 1;
    }
    
    return 0;
}
```

**3. Process Groups and Sessions**
```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    pid_t pid;
    
    printf("Process: PID=%d, PGID=%d, SID=%d\n", 
           getpid(), getpgid(0), getsid(0));
    
    pid = fork();
    
    if (pid == 0) {
        // Child process
        printf("Child: PID=%d, PGID=%d, SID=%d\n", 
               getpid(), getpgid(0), getsid(0));
        
        // Create new process group
        setpgid(0, 0);
        printf("After setpgid: PID=%d, PGID=%d, SID=%d\n", 
               getpid(), getpgid(0), getsid(0));
        
        sleep(2);
        exit(0);
        
    } else {
        // Parent process
        wait(NULL);
        printf("Parent: Child finished\n");
    }
    
    return 0;
}
```

---

### **PILLAR 3: LEVERAGING & MODIFICATION — Advanced System Programming**

#### **Inter-Process Communication (IPC)**

**1. Pipes**
```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>
#include <string.h>

int main() {
    int pipefd[2];
    pid_t pid;
    char buffer[100];
    
    // Create pipe
    if (pipe(pipefd) == -1) {
        perror("pipe failed");
        return 1;
    }
    
    pid = fork();
    
    if (pid == 0) {
        // Child process - writer
        close(pipefd[0]);  // Close read end
        
        const char *message = "Hello from child!";
        write(pipefd[1], message, strlen(message));
        close(pipefd[1]);
        
    } else {
        // Parent process - reader
        close(pipefd[1]);  // Close write end
        
        ssize_t bytes_read = read(pipefd[0], buffer, sizeof(buffer) - 1);
        if (bytes_read > 0) {
            buffer[bytes_read] = '\0';
            printf("Parent received: %s\n", buffer);
        }
        
        close(pipefd[0]);
        wait(NULL);
    }
    
    return 0;
}
```

**2. Named Pipes (FIFOs)**
```c
#include <stdio.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>

// Writer process
int writer() {
    int fd;
    const char *fifo_name = "/tmp/myfifo";
    const char *message = "Hello from named pipe!";
    
    // Create FIFO
    mkfifo(fifo_name, 0666);
    
    // Open for writing
    fd = open(fifo_name, O_WRONLY);
    if (fd == -1) {
        perror("open failed");
        return 1;
    }
    
    // Write message
    write(fd, message, strlen(message));
    close(fd);
    
    return 0;
}

// Reader process
int reader() {
    int fd;
    char buffer[100];
    const char *fifo_name = "/tmp/myfifo";
    
    // Open for reading
    fd = open(fifo_name, O_RDONLY);
    if (fd == -1) {
        perror("open failed");
        return 1;
    }
    
    // Read message
    ssize_t bytes_read = read(fd, buffer, sizeof(buffer) - 1);
    if (bytes_read > 0) {
        buffer[bytes_read] = '\0';
        printf("Received: %s\n", buffer);
    }
    
    close(fd);
    unlink(fifo_name);  // Remove FIFO
    
    return 0;
}
```

**3. Shared Memory**
```c
#include <stdio.h>
#include <sys/ipc.h>
#include <sys/shm.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    key_t key = 1234;
    int shmid;
    char *shared_memory;
    pid_t pid;
    
    // Create shared memory segment
    shmid = shmget(key, 1024, IPC_CREAT | 0666);
    if (shmid == -1) {
        perror("shmget failed");
        return 1;
    }
    
    // Attach to shared memory
    shared_memory = shmat(shmid, NULL, 0);
    if (shared_memory == (char *)-1) {
        perror("shmat failed");
        return 1;
    }
    
    pid = fork();
    
    if (pid == 0) {
        // Child process - writer
        strcpy(shared_memory, "Hello from shared memory!");
        printf("Child wrote to shared memory\n");
        
    } else {
        // Parent process - reader
        wait(NULL);  // Wait for child
        
        printf("Parent read from shared memory: %s\n", shared_memory);
        
        // Detach from shared memory
        shmdt(shared_memory);
        
        // Remove shared memory segment
        shmctl(shmid, IPC_RMID, NULL);
    }
    
    return 0;
}
```

#### **Threading and Synchronization**

**1. Basic Threading**
```c
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

void *thread_function(void *arg) {
    int thread_num = *(int *)arg;
    
    for (int i = 0; i < 5; i++) {
        printf("Thread %d: iteration %d\n", thread_num, i);
        sleep(1);
    }
    
    return NULL;
}

int main() {
    pthread_t thread1, thread2;
    int arg1 = 1, arg2 = 2;
    
    // Create threads
    pthread_create(&thread1, NULL, thread_function, &arg1);
    pthread_create(&thread2, NULL, thread_function, &arg2);
    
    // Wait for threads to complete
    pthread_join(thread1, NULL);
    pthread_join(thread2, NULL);
    
    printf("All threads completed\n");
    return 0;
}
```

**2. Mutex Synchronization**
```c
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

int counter = 0;
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;

void *increment_function(void *arg) {
    int thread_num = *(int *)arg;
    
    for (int i = 0; i < 1000; i++) {
        // Lock mutex before accessing shared variable
        pthread_mutex_lock(&mutex);
        
        counter++;
        printf("Thread %d: counter = %d\n", thread_num, counter);
        
        // Unlock mutex after accessing shared variable
        pthread_mutex_unlock(&mutex);
        
        usleep(1000);  // Small delay
    }
    
    return NULL;
}

int main() {
    pthread_t thread1, thread2;
    int arg1 = 1, arg2 = 2;
    
    // Create threads
    pthread_create(&thread1, NULL, increment_function, &arg1);
    pthread_create(&thread2, NULL, increment_function, &arg2);
    
    // Wait for threads to complete
    pthread_join(thread1, NULL);
    pthread_join(thread2, NULL);
    
    printf("Final counter value: %d\n", counter);
    
    // Destroy mutex
    pthread_mutex_destroy(&mutex);
    
    return 0;
}
```

**3. Condition Variables**
```c
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>

int data_ready = 0;
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t condition = PTHREAD_COND_INITIALIZER;

void *producer(void *arg) {
    printf("Producer: Starting\n");
    
    sleep(2);  // Simulate work
    
    // Lock mutex and signal condition
    pthread_mutex_lock(&mutex);
    data_ready = 1;
    printf("Producer: Data ready, signaling consumer\n");
    pthread_cond_signal(&condition);
    pthread_mutex_unlock(&mutex);
    
    return NULL;
}

void *consumer(void *arg) {
    printf("Consumer: Starting\n");
    
    // Lock mutex and wait for condition
    pthread_mutex_lock(&mutex);
    while (!data_ready) {
        printf("Consumer: Waiting for data\n");
        pthread_cond_wait(&condition, &mutex);
    }
    printf("Consumer: Data received\n");
    pthread_mutex_unlock(&mutex);
    
    return NULL;
}

int main() {
    pthread_t producer_thread, consumer_thread;
    
    // Create threads
    pthread_create(&producer_thread, NULL, producer, NULL);
    pthread_create(&consumer_thread, NULL, consumer, NULL);
    
    // Wait for threads to complete
    pthread_join(producer_thread, NULL);
    pthread_join(consumer_thread, NULL);
    
    // Cleanup
    pthread_mutex_destroy(&mutex);
    pthread_cond_destroy(&condition);
    
    return 0;
}
```

---

### **PILLAR 4: DEBUGGING — System Programming Debugging**

#### **Common System Programming Issues**

**1. File I/O Debugging**
```c
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>
#include <errno.h>
#include <string.h>

int safe_file_operations(const char *filename) {
    int fd;
    char buffer[100];
    ssize_t bytes_read;
    
    // Open file with error checking
    fd = open(filename, O_RDONLY);
    if (fd == -1) {
        fprintf(stderr, "Error opening %s: %s (errno: %d)\n", 
                filename, strerror(errno), errno);
        return -1;
    }
    
    // Read with error checking
    bytes_read = read(fd, buffer, sizeof(buffer) - 1);
    if (bytes_read == -1) {
        fprintf(stderr, "Error reading from %s: %s (errno: %d)\n", 
                filename, strerror(errno), errno);
        close(fd);
        return -1;
    }
    
    // Null-terminate string
    buffer[bytes_read] = '\0';
    printf("Read %zd bytes: %s\n", bytes_read, buffer);
    
    // Close file
    if (close(fd) == -1) {
        fprintf(stderr, "Error closing file: %s\n", strerror(errno));
        return -1;
    }
    
    return 0;
}
```

**2. Process Debugging**
```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>
#include <errno.h>

int debug_process_creation() {
    pid_t pid;
    int status;
    
    printf("Parent process: PID = %d\n", getpid());
    
    // Create child process
    pid = fork();
    
    if (pid == -1) {
        fprintf(stderr, "fork failed: %s (errno: %d)\n", 
                strerror(errno), errno);
        return -1;
    }
    
    if (pid == 0) {
        // Child process
        printf("Child process: PID = %d, Parent PID = %d\n", 
               getpid(), getppid());
        
        // Simulate some work
        sleep(1);
        
        // Exit with specific status
        exit(42);
        
    } else {
        // Parent process
        printf("Parent: Created child with PID = %d\n", pid);
        
        // Wait for child with error checking
        pid_t child_pid = wait(&status);
        if (child_pid == -1) {
            fprintf(stderr, "wait failed: %s (errno: %d)\n", 
                    strerror(errno), errno);
            return -1;
        }
        
        if (WIFEXITED(status)) {
            printf("Child %d exited normally with status %d\n", 
                   child_pid, WEXITSTATUS(status));
        } else if (WIFSIGNALED(status)) {
            printf("Child %d killed by signal %d\n", 
                   child_pid, WTERMSIG(status));
        }
    }
    
    return 0;
}
```

**3. Thread Debugging**
```c
#include <stdio.h>
#include <pthread.h>
#include <unistd.h>
#include <errno.h>

void *debug_thread_function(void *arg) {
    int thread_num = *(int *)arg;
    
    printf("Thread %d: Starting (TID: %lu)\n", thread_num, pthread_self());
    
    for (int i = 0; i < 3; i++) {
        printf("Thread %d: iteration %d\n", thread_num, i);
        sleep(1);
    }
    
    printf("Thread %d: Finished\n", thread_num);
    return NULL;
}

int debug_threading() {
    pthread_t thread1, thread2;
    int arg1 = 1, arg2 = 2;
    int result;
    
    printf("Main thread: PID = %d, TID = %lu\n", getpid(), pthread_self());
    
    // Create first thread
    result = pthread_create(&thread1, NULL, debug_thread_function, &arg1);
    if (result != 0) {
        fprintf(stderr, "pthread_create failed: %s\n", strerror(result));
        return -1;
    }
    
    // Create second thread
    result = pthread_create(&thread2, NULL, debug_thread_function, &arg2);
    if (result != 0) {
        fprintf(stderr, "pthread_create failed: %s\n", strerror(result));
        return -1;
    }
    
    // Wait for threads
    result = pthread_join(thread1, NULL);
    if (result != 0) {
        fprintf(stderr, "pthread_join failed: %s\n", strerror(result));
        return -1;
    }
    
    result = pthread_join(thread2, NULL);
    if (result != 0) {
        fprintf(stderr, "pthread_join failed: %s\n", strerror(result));
        return -1;
    }
    
    printf("All threads completed\n");
    return 0;
}
```

#### **Debugging Tools and Techniques**

**1. Using strace for System Call Tracing**
```bash
# Trace system calls of a program
strace ./program

# Trace specific system calls
strace -e trace=open,read,write ./program

# Trace system calls of running process
strace -p PID

# Save trace to file
strace -o trace.log ./program
```

**2. Using gdb for Debugging**
```bash
# Compile with debug information
gcc -g -o program program.c

# Start gdb
gdb ./program

# Set breakpoints
(gdb) break main
(gdb) break function_name

# Run program
(gdb) run

# Step through code
(gdb) step
(gdb) next

# Examine variables
(gdb) print variable_name
(gdb) print *pointer

# Examine memory
(gdb) x/10x address
(gdb) x/10s string_address

# Backtrace
(gdb) bt
```

**3. Using valgrind for Memory Debugging**
```bash
# Check for memory leaks
valgrind --tool=memcheck --leak-check=full ./program

# Check for memory errors
valgrind --tool=memcheck --track-origins=yes ./program

# Check for thread errors
valgrind --tool=helgrind ./program
```

---

### **PILLAR 5: INTERNAL MECHANISM — System Programming Internals**

#### **System Call Implementation**

**1. System Call Interface**
```c
// System call numbers are defined in /usr/include/asm/unistd.h
#define __NR_read 0
#define __NR_write 1
#define __NR_open 2
#define __NR_close 3
#define __NR_fork 57
#define __NR_execve 59

// System call wrapper functions
static inline long syscall(long number, ...) {
    long ret;
    va_list args;
    va_start(args, number);
    
    // Move arguments to registers
    register long r10 asm("r10");
    register long r8 asm("r8");
    register long r9 asm("r9");
    
    // Perform system call
    asm volatile (
        "syscall"
        : "=a" (ret)
        : "a" (number), "D" (va_arg(args, long)),
          "S" (va_arg(args, long)), "d" (va_arg(args, long)),
          "r" (r10), "r" (r8), "r" (r9)
        : "rcx", "r11", "memory"
    );
    
    va_end(args);
    return ret;
}
```

**2. Kernel System Call Handler**
```c
// Kernel system call handler
SYSCALL_DEFINE3(read, unsigned int, fd, char __user *, buf, size_t, count) {
    struct fd f;
    ssize_t ret = -EBADF;
    
    // Validate file descriptor
    f = fdget(fd);
    if (!f.file)
        goto out;
    
    // Check if file supports reading
    if (!(f.file->f_mode & FMODE_READ))
        goto out_fput;
    
    // Perform the actual read
    ret = vfs_read(f.file, buf, count, &f.file->f_pos);
    
out_fput:
    fdput(f);
out:
    return ret;
}
```

#### **Process Creation Internals**

**1. fork() Implementation**
```c
// Kernel fork implementation
SYSCALL_DEFINE0(fork) {
    return _do_fork(SIGCHLD, 0, 0, NULL, NULL, 0);
}

long _do_fork(unsigned long clone_flags, unsigned long stack_start,
              unsigned long stack_size, int __user *parent_tidptr,
              int __user *child_tidptr, unsigned long tls) {
    struct task_struct *p;
    int trace = 0;
    long nr;
    
    // Create new task structure
    p = copy_process(clone_flags, stack_start, stack_size,
                     parent_tidptr, child_tidptr, tls, trace);
    
    if (!IS_ERR(p)) {
        // Wake up new process
        wake_up_new_task(p);
        
        // Return child PID to parent
        nr = task_pid_vnr(p);
    } else {
        nr = PTR_ERR(p);
    }
    
    return nr;
}
```

**2. exec() Implementation**
```c
// Kernel exec implementation
SYSCALL_DEFINE3(execve, const char __user *, filename,
                const char __user *const __user *, argv,
                const char __user *const __user *, envp) {
    return do_execve(getname(filename), argv, envp);
}

int do_execve(struct filename *name, const char __user *const __user *argv,
              const char __user *const __user *envp) {
    struct linux_binprm *bprm;
    int retval;
    
    // Allocate binary parameters structure
    bprm = alloc_bprm();
    if (!bprm)
        return -ENOMEM;
    
    // Prepare binary for execution
    retval = prepare_binprm(bprm);
    if (retval < 0)
        goto out_free;
    
    // Load binary
    retval = search_binary_handler(bprm);
    if (retval < 0)
        goto out_free;
    
    // Execute binary
    retval = exec_binprm(bprm);
    
out_free:
    free_bprm(bprm);
    return retval;
}
```

#### **Memory Management Internals**

**1. mmap() Implementation**
```c
// Kernel mmap implementation
SYSCALL_DEFINE6(mmap, unsigned long, addr, unsigned long, len,
                unsigned long, prot, unsigned long, flags,
                unsigned long, fd, unsigned long, off) {
    return ksys_mmap_pgoff(addr, len, prot, flags, fd, off >> PAGE_SHIFT);
}

unsigned long ksys_mmap_pgoff(unsigned long addr, unsigned long len,
                              unsigned long prot, unsigned long flags,
                              unsigned long fd, unsigned long pgoff) {
    struct file *file = NULL;
    unsigned long retval;
    
    // Handle anonymous mapping
    if (!(flags & MAP_ANONYMOUS)) {
        file = fget(fd);
        if (!file)
            return -EBADF;
    }
    
    // Create memory mapping
    retval = vm_mmap_pgoff(file, addr, len, prot, flags, pgoff);
    
    if (file)
        fput(file);
    
    return retval;
}
```

**2. Memory Protection**
```c
// Memory protection implementation
int do_mprotect_pkey(unsigned long start, size_t len, unsigned long prot,
                     int pkey) {
    struct mm_struct *mm = current->mm;
    unsigned long nstart;
    unsigned long tmp, reqprot;
    
    // Validate parameters
    if (prot & ~(PROT_READ | PROT_WRITE | PROT_EXEC | PROT_SEM | PROT_SAO))
        return -EINVAL;
    
    // Align start address
    nstart = start & PAGE_MASK;
    
    // Change memory protection
    return mprotect_fixup(mm, &nstart, len, prot, pkey);
}
```

---

## 🛠️ **PRACTICAL EXERCISES**

### **Exercise 1: File I/O System Programming**
```c
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>
#include <sys/stat.h>
#include <errno.h>
#include <string.h>

// TODO: Implement a file copy program using system calls
int copy_file_syscalls(const char *src, const char *dst) {
    int src_fd, dst_fd;
    char buffer[4096];
    ssize_t bytes_read, bytes_written;
    struct stat src_stat;
    
    // TODO: Complete the implementation
    // 1. Open source file for reading
    // 2. Get source file information (size, permissions)
    // 3. Open destination file for writing
    // 4. Copy data in chunks
    // 5. Set destination file permissions
    // 6. Close both files
    // 7. Handle all errors appropriately
    
    return 0;
}

int main() {
    const char *src = "source.txt";
    const char *dst = "destination.txt";
    
    // Create test source file
    int fd = open(src, O_CREAT | O_WRONLY | O_TRUNC, 0644);
    if (fd == -1) {
        perror("Failed to create source file");
        return 1;
    }
    
    const char *data = "This is test data for file copying.\n";
    write(fd, data, strlen(data));
    close(fd);
    
    // Copy file using system calls
    if (copy_file_syscalls(src, dst) == 0) {
        printf("File copied successfully\n");
        
        // Verify copy
        printf("Source file contents:\n");
        system("cat source.txt");
        printf("\nDestination file contents:\n");
        system("cat destination.txt");
    } else {
        printf("File copy failed\n");
    }
    
    // Cleanup
    unlink(src);
    unlink(dst);
    
    return 0;
}
```

### **Exercise 2: Process Management**
```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>
#include <errno.h>
#include <string.h>

// TODO: Implement a process manager
int process_manager() {
    pid_t pids[3];
    int status;
    
    // TODO: Create 3 child processes
    // Each child should:
    // 1. Print its PID and parent PID
    // 2. Sleep for a different amount of time
    // 3. Exit with a different status code
    
    for (int i = 0; i < 3; i++) {
        pids[i] = fork();
        
        if (pids[i] == 0) {
            // Child process
            printf("Child %d: PID=%d, Parent PID=%d\n", 
                   i+1, getpid(), getppid());
            
            // Sleep for different amounts
            sleep(i + 1);
            
            printf("Child %d: Finished\n", i+1);
            exit(i + 1);  // Exit with different status
            
        } else if (pids[i] == -1) {
            perror("fork failed");
            return -1;
        } else {
            // Parent process
            printf("Parent: Created child %d with PID %d\n", i+1, pids[i]);
        }
    }
    
    // TODO: Wait for all children and collect their exit status
    // Print the exit status of each child
    
    for (int i = 0; i < 3; i++) {
        pid_t child_pid = wait(&status);
        if (child_pid == -1) {
            perror("wait failed");
            return -1;
        }
        
        if (WIFEXITED(status)) {
            printf("Child %d exited with status %d\n", 
                   child_pid, WEXITSTATUS(status));
        }
    }
    
    return 0;
}

int main() {
    printf("Process Manager Exercise\n");
    printf("Parent process: PID = %d\n", getpid());
    
    if (process_manager() == 0) {
        printf("All processes completed successfully\n");
    } else {
        printf("Process management failed\n");
    }
    
    return 0;
}
```

### **Exercise 3: Inter-Process Communication**
```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>
#include <sys/ipc.h>
#include <sys/shm.h>
#include <string.h>
#include <errno.h>

// TODO: Implement a shared memory communication system
int shared_memory_communication() {
    key_t key = 1234;
    int shmid;
    char *shared_memory;
    pid_t pid;
    
    // TODO: Create shared memory segment
    // 1. Create shared memory with shmget()
    // 2. Attach to shared memory with shmat()
    // 3. Create child process
    // 4. Child writes message to shared memory
    // 5. Parent reads message from shared memory
    // 6. Detach and remove shared memory
    // 7. Handle all errors appropriately
    
    return 0;
}

int main() {
    printf("Shared Memory Communication Exercise\n");
    
    if (shared_memory_communication() == 0) {
        printf("Shared memory communication successful\n");
    } else {
        printf("Shared memory communication failed\n");
    }
    
    return 0;
}
```

---

## 📚 **SUMMARY AND NEXT STEPS**

### **Key Takeaways**

1. **System Programming** is the bridge between user applications and kernel functionality
2. **System Calls** are the primary interface for accessing kernel services
3. **File I/O** requires understanding of file descriptors, permissions, and error handling
4. **Process Management** involves creating, controlling, and communicating between processes
5. **Inter-Process Communication** enables processes to share data and coordinate

### **What You've Learned**

✅ **Purpose**: Why system programming is essential for kernel development  
✅ **Functionality**: Core system programming concepts and APIs  
✅ **Leveraging**: How to use system programming effectively  
✅ **Debugging**: How to debug system programming issues  
✅ **Internal Mechanism**: How system calls work behind the scenes  

### **Next Steps**

In **Chapter 7: Development Environment Setup**, you'll learn:
- Hardware requirements and setup
- Operating system installation and configuration
- Development tools installation
- Kernel source code setup
- Build system configuration

### **Recommended Practice**

1. **Practice system calls** with the exercises above
2. **Experiment with process management** using fork() and exec()
3. **Implement IPC mechanisms** for process communication
4. **Use debugging tools** like strace and gdb
5. **Study kernel source code** to understand system call implementation

---

**Ready to set up your development environment? Let's continue with Chapter 7! 🚀**