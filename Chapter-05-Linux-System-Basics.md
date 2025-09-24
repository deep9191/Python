# Chapter 5: Linux System Basics
## Mastering the Linux Environment

---

## 🎯 **LEARNING OBJECTIVES**

By the end of this chapter, you will understand:
- Linux history, philosophy, and design principles
- Linux file system structure and organization
- Essential Linux commands and shell scripting
- Linux system administration fundamentals
- Linux security concepts and best practices

---

## 📚 **THE 5-PILLAR FRAMEWORK**

### **PILLAR 1: PURPOSE — Why Linux System Knowledge is Essential**

#### **The Motivation: Understanding the Linux Foundation**

**What is Linux?**
Linux is a free and open-source operating system kernel that serves as the foundation for many operating systems. It was created by Linus Torvalds in 1991 and has grown to become one of the most widely used operating systems in the world, powering everything from smartphones to supercomputers.

**Why Learn Linux for Kernel Development?**
- **Direct Access**: Linux kernel development gives you direct access to the source code
- **Community**: Large, active community of developers and contributors
- **Flexibility**: Highly customizable and configurable
- **Industry Standard**: Used in servers, embedded systems, mobile devices, and more
- **Learning Platform**: Excellent for understanding operating system concepts

**Real-World Applications:**
- **Servers**: Most web servers run on Linux
- **Cloud Computing**: Major cloud platforms use Linux
- **Mobile Devices**: Android is based on Linux
- **Embedded Systems**: IoT devices, routers, smart TVs
- **Supercomputers**: All top 500 supercomputers run Linux
- **Desktop**: Many users prefer Linux for development

#### **Linux Philosophy: The Unix Way**

**Core Principles:**
1. **"Do One Thing and Do It Well"**: Each program should have a single, well-defined purpose
2. **"Everything is a File"**: All system resources are represented as files
3. **"Small is Beautiful"**: Simple, small programs are better than complex ones
4. **"Make It Work, Make It Right, Make It Fast"**: Focus on functionality first, then optimization
5. **"Composition over Configuration"**: Combine simple tools rather than building complex ones

**Examples of Linux Philosophy:**
```bash
# Instead of one complex program, use simple tools together:
# Find all .txt files, count lines, and sort by count
find . -name "*.txt" -exec wc -l {} \; | sort -n

# This combines:
# - find: searches for files
# - wc -l: counts lines
# - sort -n: sorts numerically
# Each tool does one thing well, and they work together
```

#### **Goals of Linux System Mastery**

**Primary Goals:**
1. **System Navigation**: Efficiently navigate and manage the Linux file system
2. **Command Mastery**: Use Linux commands effectively for system administration
3. **Scripting Skills**: Automate tasks using shell scripting
4. **System Administration**: Manage users, processes, and system resources
5. **Security Awareness**: Understand and implement Linux security practices

**Secondary Goals:**
1. **Troubleshooting**: Diagnose and fix system problems
2. **Performance**: Optimize system performance
3. **Customization**: Configure the system for specific needs
4. **Development**: Set up development environments
5. **Automation**: Automate repetitive tasks

---

### **PILLAR 2: FUNCTIONALITY & SCOPE — What Linux Provides**

#### **Linux File System Structure**

**The Root Directory (/)**
```
/                           # Root directory - top of the file system tree
├── bin/                    # Essential user binaries (commands)
├── boot/                   # Boot loader files and kernel images
├── dev/                    # Device files (hardware representation)
├── etc/                    # System configuration files
├── home/                   # User home directories
├── lib/                    # Essential shared libraries
├── media/                  # Mount points for removable media
├── mnt/                    # Mount points for temporary file systems
├── opt/                    # Optional software packages
├── proc/                   # Virtual file system for process information
├── root/                   # Root user's home directory
├── run/                    # Runtime data (PID files, sockets)
├── sbin/                   # Essential system binaries (admin commands)
├── srv/                    # Service data
├── sys/                    # Virtual file system for system information
├── tmp/                    # Temporary files
├── usr/                    # User programs and data
└── var/                    # Variable data (logs, cache, spool)
```

**Detailed Directory Explanations:**

**1. /bin/ - Essential User Binaries**
```bash
# Contains basic commands that all users need
ls /bin/
# Common commands:
# - ls: list directory contents
# - cp: copy files
# - mv: move/rename files
# - rm: remove files
# - cat: display file contents
# - echo: display text
# - chmod: change file permissions
# - chown: change file ownership
```

**2. /dev/ - Device Files**
```bash
# Contains special files that represent hardware devices
ls /dev/
# Common device files:
# - /dev/sda: first SATA hard drive
# - /dev/sdb: second SATA hard drive
# - /dev/tty: terminal
# - /dev/null: null device (discards data)
# - /dev/zero: zero device (provides zeros)
# - /dev/random: random number generator
# - /dev/urandom: non-blocking random number generator
```

**3. /etc/ - System Configuration**
```bash
# Contains system-wide configuration files
ls /etc/
# Important configuration files:
# - /etc/passwd: user account information
# - /etc/shadow: encrypted passwords
# - /etc/group: group information
# - /etc/hosts: hostname resolution
# - /etc/fstab: file system table
# - /etc/crontab: cron job configuration
# - /etc/ssh/: SSH configuration
```

**4. /proc/ - Process Information**
```bash
# Virtual file system that provides information about processes and system
ls /proc/
# Important files:
# - /proc/cpuinfo: CPU information
# - /proc/meminfo: memory information
# - /proc/loadavg: system load average
# - /proc/uptime: system uptime
# - /proc/version: kernel version
# - /proc/[PID]/: information about specific process
```

**5. /sys/ - System Information**
```bash
# Virtual file system for system and hardware information
ls /sys/
# Important directories:
# - /sys/class/: device classes
# - /sys/devices/: physical devices
# - /sys/fs/: file systems
# - /sys/kernel/: kernel parameters
# - /sys/module/: loaded modules
```

#### **Essential Linux Commands**

**1. File and Directory Operations**
```bash
# Navigation
pwd                        # Print working directory
cd /path/to/directory      # Change directory
cd ..                     # Go to parent directory
cd ~                      # Go to home directory
cd -                      # Go to previous directory

# Listing files
ls                        # List files in current directory
ls -l                     # Long format (permissions, size, date)
ls -a                     # Show hidden files (starting with .)
ls -h                     # Human-readable file sizes
ls -R                     # Recursive listing
ls -t                     # Sort by modification time
ls -S                     # Sort by file size

# File operations
cp source dest            # Copy file
cp -r source dest         # Copy directory recursively
mv source dest            # Move/rename file
rm file                   # Remove file
rm -r directory           # Remove directory recursively
rm -f file                # Force remove (no confirmation)
mkdir directory           # Create directory
rmdir directory           # Remove empty directory
```

**2. File Content Operations**
```bash
# Viewing files
cat filename              # Display entire file
less filename             # Page through file (better for large files)
head filename             # Show first 10 lines
head -n 20 filename       # Show first 20 lines
tail filename             # Show last 10 lines
tail -n 20 filename       # Show last 20 lines
tail -f filename          # Follow file (watch for changes)

# Searching in files
grep pattern filename     # Search for pattern in file
grep -r pattern directory # Search recursively in directory
grep -i pattern filename  # Case-insensitive search
grep -n pattern filename  # Show line numbers
grep -v pattern filename  # Show lines that don't match

# File editing
nano filename             # Simple text editor
vim filename              # Advanced text editor
emacs filename            # Another advanced text editor
```

**3. System Information Commands**
```bash
# System information
uname -a                  # System information
hostname                  # Computer name
whoami                    # Current user
id                        # User and group IDs
date                      # Current date and time
uptime                    # System uptime and load
free -h                   # Memory usage
df -h                     # Disk space usage
du -h directory           # Directory size

# Process information
ps aux                    # All running processes
top                       # Real-time process monitor
htop                      # Enhanced process monitor
pgrep process_name        # Find process by name
kill PID                  # Terminate process
killall process_name      # Terminate all processes with name
```

**4. Network Commands**
```bash
# Network information
ip addr                   # Network interfaces and IP addresses
ip route                  # Routing table
netstat -tuln             # Network connections
ss -tuln                  # Modern replacement for netstat
ping hostname             # Test network connectivity
traceroute hostname       # Trace network path
nslookup hostname         # DNS lookup
dig hostname              # Advanced DNS lookup
```

#### **Shell Scripting Fundamentals**

**1. Basic Shell Script Structure**
```bash
#!/bin/bash
# This is a comment
# Script name: hello.sh

# Display a message
echo "Hello, World!"

# Use variables
NAME="Linux User"
echo "Hello, $NAME"

# Get user input
read -p "Enter your name: " USER_NAME
echo "Hello, $USER_NAME"
```

**2. Variables and Data Types**
```bash
#!/bin/bash

# Variable assignment
VARIABLE_NAME="value"
NUMBER=42
PATH="/usr/bin:/bin"

# Using variables
echo $VARIABLE_NAME
echo ${VARIABLE_NAME}

# Environment variables
echo $HOME                 # User's home directory
echo $PATH                 # Command search path
echo $USER                 # Current username
echo $PWD                  # Current directory
echo $SHELL                # Current shell

# Command substitution
CURRENT_DATE=$(date)
echo "Current date: $CURRENT_DATE"

# Arithmetic operations
NUM1=10
NUM2=5
SUM=$((NUM1 + NUM2))
echo "Sum: $SUM"
```

**3. Control Structures**
```bash
#!/bin/bash

# Conditional statements
if [ $1 -gt 10 ]; then
    echo "Number is greater than 10"
elif [ $1 -eq 10 ]; then
    echo "Number equals 10"
else
    echo "Number is less than 10"
fi

# Loops
# For loop
for i in {1..5}; do
    echo "Iteration $i"
done

# While loop
COUNTER=0
while [ $COUNTER -lt 5 ]; do
    echo "Counter: $COUNTER"
    COUNTER=$((COUNTER + 1))
done

# Case statement
case $1 in
    start)
        echo "Starting service"
        ;;
    stop)
        echo "Stopping service"
        ;;
    restart)
        echo "Restarting service"
        ;;
    *)
        echo "Unknown command"
        ;;
esac
```

**4. Functions**
```bash
#!/bin/bash

# Function definition
function_name() {
    echo "This is a function"
    echo "Parameter 1: $1"
    echo "Parameter 2: $2"
}

# Function call
function_name "arg1" "arg2"

# Function with return value
calculate_sum() {
    local num1=$1
    local num2=$2
    local sum=$((num1 + num2))
    echo $sum
}

RESULT=$(calculate_sum 10 20)
echo "Sum: $RESULT"
```

---

### **PILLAR 3: LEVERAGING & MODIFICATION — How to Use Linux Effectively**

#### **System Administration Tasks**

**1. User Management**
```bash
# Add a new user
sudo useradd -m -s /bin/bash username
sudo passwd username

# Add user to group
sudo usermod -a -G groupname username

# Delete user
sudo userdel -r username

# List users
cat /etc/passwd
getent passwd

# List groups
cat /etc/group
getent group

# Change user password
sudo passwd username

# Switch user
su username
sudo -u username command
```

**2. Process Management**
```bash
# View running processes
ps aux                    # All processes
ps -ef                    # Full format
ps -u username            # Processes for specific user

# Real-time monitoring
top                       # Interactive process monitor
htop                      # Enhanced monitor
iotop                     # I/O monitoring

# Process control
kill PID                  # Terminate process
kill -9 PID               # Force terminate
killall process_name      # Kill by name
pkill process_name        # Kill by pattern

# Background processes
command &                 # Run in background
nohup command &           # Run in background, immune to hangups
jobs                      # List background jobs
fg %1                     # Bring job to foreground
bg %1                     # Send job to background
```

**3. Service Management**
```bash
# Systemd service management
sudo systemctl start service_name      # Start service
sudo systemctl stop service_name       # Stop service
sudo systemctl restart service_name    # Restart service
sudo systemctl reload service_name     # Reload configuration
sudo systemctl enable service_name     # Enable auto-start
sudo systemctl disable service_name    # Disable auto-start
sudo systemctl status service_name     # Check service status

# List services
systemctl list-units --type=service    # All services
systemctl list-unit-files --type=service  # Service files
```

**4. Package Management**
```bash
# Ubuntu/Debian (apt)
sudo apt update                        # Update package list
sudo apt upgrade                       # Upgrade packages
sudo apt install package_name          # Install package
sudo apt remove package_name           # Remove package
sudo apt search keyword                # Search packages
sudo apt show package_name             # Show package information

# Red Hat/CentOS (yum/dnf)
sudo yum install package_name          # Install package
sudo yum remove package_name           # Remove package
sudo yum update                        # Update packages
sudo yum search keyword                # Search packages

# Arch Linux (pacman)
sudo pacman -S package_name            # Install package
sudo pacman -R package_name            # Remove package
sudo pacman -Syu                       # Update system
sudo pacman -Ss keyword                # Search packages
```

#### **System Monitoring and Maintenance**

**1. System Monitoring**
```bash
# System information
uname -a                                # System information
hostnamectl                             # Hostname information
lscpu                                   # CPU information
lsmem                                   # Memory information
lsblk                                   # Block device information
lspci                                   # PCI device information
lsusb                                   # USB device information

# Resource monitoring
free -h                                 # Memory usage
df -h                                   # Disk usage
du -h directory                          # Directory size
iostat                                  # I/O statistics
vmstat                                  # Virtual memory statistics
sar                                     # System activity reporter

# Network monitoring
ss -tuln                                # Network connections
netstat -i                              # Network interface statistics
iftop                                   # Network traffic monitor
nethogs                                 # Per-process network usage
```

**2. Log Management**
```bash
# View logs
journalctl                              # System journal
journalctl -f                           # Follow journal
journalctl -u service_name              # Service logs
tail -f /var/log/syslog                 # System log
tail -f /var/log/auth.log               # Authentication log
tail -f /var/log/kern.log               # Kernel log

# Log rotation
sudo logrotate -f /etc/logrotate.conf  # Force log rotation
ls /var/log/                            # Check log files
```

**3. System Maintenance**
```bash
# Update system
sudo apt update && sudo apt upgrade     # Update packages
sudo apt autoremove                     # Remove unused packages
sudo apt autoclean                      # Clean package cache

# Clean temporary files
sudo rm -rf /tmp/*                      # Clean /tmp
sudo rm -rf /var/tmp/*                  # Clean /var/tmp
sudo apt clean                          # Clean package cache

# Check disk health
sudo smartctl -a /dev/sda               # Check disk health
sudo badblocks /dev/sda                 # Check for bad blocks
sudo fsck /dev/sda1                     # Check file system

# Monitor system health
sudo dmesg | tail -20                   # Recent kernel messages
sudo journalctl -p err                  # Error messages
```

#### **Security Fundamentals**

**1. File Permissions**
```bash
# Understanding permissions
ls -l file                              # Show permissions
# Output: -rwxr-xr-x 1 user group size date file
#         ^^^ ^^^ ^^^
#         ||| ||| |||
#         ||| ||| ||+-- Others: read, no write, execute
#         ||| ||+------ Group: read, no write, execute
#         ||| |+------- Owner: read, write, execute
#         ||+---------- File type: - = regular file, d = directory
#         |+----------- Special permissions
#         +------------ File type

# Change permissions
chmod 755 file                          # rwxr-xr-x
chmod u+x file                          # Add execute for owner
chmod g-w file                          # Remove write for group
chmod o-r file                          # Remove read for others

# Change ownership
chown user:group file                   # Change owner and group
chown user file                         # Change owner only
chgrp group file                        # Change group only
```

**2. User Security**
```bash
# Check user information
id username                             # User ID and groups
groups username                         # User groups
sudo -l                                # Sudo privileges

# Password policies
sudo chage -l username                 # Password aging information
sudo chage -M 90 username              # Set maximum password age
sudo chage -m 7 username               # Set minimum password age

# Account locking
sudo usermod -L username                # Lock account
sudo usermod -U username                # Unlock account
sudo passwd -l username                 # Lock password
sudo passwd -u username                 # Unlock password
```

**3. Network Security**
```bash
# Firewall management (ufw)
sudo ufw status                         # Check firewall status
sudo ufw enable                         # Enable firewall
sudo ufw disable                        # Disable firewall
sudo ufw allow 22                       # Allow SSH
sudo ufw deny 80                        # Deny HTTP
sudo ufw allow from 192.168.1.0/24     # Allow from subnet

# Network security
sudo netstat -tuln                      # Check listening ports
sudo ss -tuln                           # Modern netstat
sudo nmap localhost                     # Scan local ports
```

---

### **PILLAR 4: DEBUGGING — How to Find and Fix Linux Issues**

#### **Common Linux Problems and Solutions**

**1. System Boot Issues**

**Problem: System Won't Boot**
```bash
# Symptoms:
# - Black screen
# - Error messages during boot
# - System hangs during startup

# Debugging steps:
# 1. Check boot messages
dmesg | grep -i error
journalctl -b                           # Boot messages

# 2. Check GRUB configuration
sudo nano /etc/default/grub
sudo update-grub

# 3. Boot from recovery mode
# - Hold Shift during boot
# - Select "Advanced options"
# - Choose "Recovery mode"

# 4. Check file system
sudo fsck /dev/sda1
sudo fsck -y /dev/sda1                  # Auto-fix errors

# 5. Check disk health
sudo smartctl -a /dev/sda
sudo badblocks /dev/sda
```

**Problem: Slow Boot**
```bash
# Debugging steps:
# 1. Check boot time
systemd-analyze                          # Boot time analysis
systemd-analyze blame                    # Services taking longest
systemd-analyze critical-chain           # Critical path

# 2. Disable unnecessary services
sudo systemctl disable service_name
sudo systemctl mask service_name        # Prevent enabling

# 3. Check for errors
journalctl -p err -b                    # Boot errors
dmesg | grep -i error                   # Kernel errors
```

**2. Performance Issues**

**Problem: System Running Slow**
```bash
# Debugging steps:
# 1. Check system load
uptime                                   # Load average
top                                      # Process monitor
htop                                     # Enhanced monitor

# 2. Check memory usage
free -h                                 # Memory usage
cat /proc/meminfo                        # Detailed memory info
ps aux --sort=-%mem | head -10           # Top memory users

# 3. Check disk usage
df -h                                    # Disk space
du -h / | sort -hr | head -10            # Largest directories
sudo find / -type f -size +100M 2>/dev/null  # Large files

# 4. Check I/O usage
iostat -x 1                              # I/O statistics
iotop                                    # I/O by process

# 5. Check network usage
iftop                                    # Network traffic
nethogs                                  # Network by process
```

**Problem: High CPU Usage**
```bash
# Debugging steps:
# 1. Find CPU-intensive processes
top                                      # Interactive process monitor
ps aux --sort=-%cpu | head -10           # Top CPU users
htop                                     # Enhanced monitor

# 2. Check for runaway processes
ps aux | grep -v grep | awk '{print $3}' | sort -n | tail -5

# 3. Check system load
uptime                                   # Load average
cat /proc/loadavg                        # Load average details

# 4. Check for kernel issues
dmesg | grep -i error                   # Kernel errors
journalctl -p err                        # System errors
```

**3. Network Issues**

**Problem: Network Connectivity**
```bash
# Debugging steps:
# 1. Check network interfaces
ip addr                                  # Network interfaces
ip route                                # Routing table
ip link show                            # Link status

# 2. Test connectivity
ping google.com                          # Test connectivity
ping -c 4 8.8.8.8                       # Test with count
traceroute google.com                    # Trace network path

# 3. Check DNS resolution
nslookup google.com                      # DNS lookup
dig google.com                           # Advanced DNS lookup
cat /etc/resolv.conf                     # DNS configuration

# 4. Check network services
sudo netstat -tuln                       # Listening ports
sudo ss -tuln                            # Modern netstat
sudo systemctl status networking        # Network service status
```

**Problem: Permission Denied**
```bash
# Debugging steps:
# 1. Check file permissions
ls -l file                               # File permissions
stat file                                # Detailed file info

# 2. Check directory permissions
ls -ld directory                         # Directory permissions
ls -la directory/                        # Directory contents

# 3. Check user permissions
id                                       # Current user info
groups                                   # User groups
sudo -l                                  # Sudo privileges

# 4. Check SELinux status (if enabled)
getenforce                               # SELinux status
sestatus                                 # SELinux status details
ls -Z file                               # SELinux context
```

#### **System Debugging Tools**

**1. Log Analysis**
```bash
# System logs
journalctl                               # System journal
journalctl -f                            # Follow journal
journalctl -u service_name               # Service logs
journalctl -p err                        # Error messages
journalctl --since "1 hour ago"          # Recent logs

# Log files
tail -f /var/log/syslog                  # System log
tail -f /var/log/auth.log                # Authentication log
tail -f /var/log/kern.log                # Kernel log
tail -f /var/log/messages                # General messages

# Log analysis
grep -i error /var/log/syslog            # Search for errors
grep -i "failed\|error\|warning" /var/log/syslog  # Multiple patterns
awk '/error/ {print $0}' /var/log/syslog # AWK pattern matching
```

**2. System Monitoring**
```bash
# Real-time monitoring
top                                      # Process monitor
htop                                     # Enhanced monitor
iotop                                    # I/O monitor
iftop                                    # Network monitor
nethogs                                  # Network by process

# System information
uname -a                                 # System info
lscpu                                    # CPU info
lsmem                                    # Memory info
lsblk                                    # Block devices
lspci                                    # PCI devices
lsusb                                    # USB devices

# Resource usage
free -h                                  # Memory usage
df -h                                    # Disk usage
du -h directory                          # Directory size
iostat                                   # I/O statistics
vmstat                                   # Virtual memory stats
sar                                      # System activity
```

**3. Network Debugging**
```bash
# Network information
ip addr                                  # Network interfaces
ip route                                 # Routing table
ip link show                            # Link status
ss -tuln                                 # Network connections
netstat -tuln                            # Legacy netstat

# Network testing
ping hostname                            # Test connectivity
traceroute hostname                      # Trace path
nslookup hostname                        # DNS lookup
dig hostname                             # Advanced DNS lookup
telnet hostname port                     # Test port connectivity
nc -zv hostname port                     # Test port with netcat
```

---

### **PILLAR 5: INTERNAL MECHANISM — What Happens Behind the Scenes**

#### **Linux Boot Process**

**1. BIOS/UEFI Phase**
```
Power On → BIOS/UEFI → Hardware Check → Boot Device Selection
```

**What happens:**
- Computer powers on
- BIOS/UEFI performs hardware checks (POST - Power-On Self-Test)
- BIOS/UEFI looks for bootable devices
- BIOS/UEFI loads and executes the boot loader

**2. Boot Loader Phase**
```
GRUB → Kernel Loading → Initial RAM Disk (initrd) → Kernel Execution
```

**What happens:**
- GRUB (Grand Unified Bootloader) loads
- GRUB displays boot menu (if multiple OS installed)
- GRUB loads the Linux kernel into memory
- GRUB loads initial RAM disk (initrd/initramfs)
- GRUB transfers control to the kernel

**3. Kernel Initialization**
```c
// Kernel startup process
start_kernel() {
    // 1. Initialize basic kernel subsystems
    setup_arch();           // Architecture-specific setup
    mm_init();              // Memory management initialization
    sched_init();           // Scheduler initialization
    
    // 2. Initialize device drivers
    init_IRQ();             // Interrupt handling
    time_init();            // Time subsystem
    console_init();         // Console initialization
    
    // 3. Mount root file system
    vfs_caches_init();      // Virtual file system
    rest_init();            // Start init process
}
```

**4. Init Process**
```bash
# Init process (PID 1) starts
# Modern systems use systemd
systemd → Service Management → User Login → Desktop Environment
```

#### **Linux Process Management**

**1. Process Creation (fork/exec)**
```c
// When you run a command like: ls -la
// Here's what happens internally:

// 1. Shell calls fork() to create new process
pid_t pid = fork();

if (pid == 0) {
    // Child process
    // 2. Child calls exec() to replace itself with 'ls'
    execve("/bin/ls", ["ls", "-la"], environment);
} else {
    // Parent process
    // 3. Parent waits for child to complete
    waitpid(pid, &status, 0);
}
```

**2. Process Scheduling**
```c
// Linux uses Completely Fair Scheduler (CFS)
// Here's how it works:

struct sched_entity {
    struct load_weight load;    // Process weight
    struct rb_node run_node;   // Red-black tree node
    unsigned int on_rq;        // On run queue
    
    u64 exec_start;            // Execution start time
    u64 sum_exec_runtime;      // Total execution time
    u64 vruntime;             // Virtual runtime
};

// Scheduler selects process with smallest vruntime
// vruntime = actual_runtime / process_weight
```

**3. Memory Management**
```c
// Virtual memory management
struct mm_struct {
    pgd_t *pgd;                    // Page global directory
    struct vm_area_struct *mmap;   // Memory areas
    unsigned long total_vm;      // Total virtual memory
    unsigned long locked_vm;        // Locked memory
};

// Page table translation
Virtual Address → Page Table → Physical Address
     ↓              ↓            ↓
  0x400000    Page Table Entry  0x800000
  (User)      (Kernel Managed)  (RAM)
```

#### **Linux File System**

**1. Virtual File System (VFS)**
```c
// VFS provides unified interface to different file systems
struct inode {
    umode_t i_mode;                    // File type and permissions
    const struct inode_operations *i_op;  // Inode operations
    const struct file_operations *i_fop;  // File operations
    struct super_block *i_sb;          // Superblock
};

// File operations
struct file_operations {
    ssize_t (*read)(struct file *, char __user *, size_t, loff_t *);
    ssize_t (*write)(struct file *, const char __user *, size_t, loff_t *);
    int (*open)(struct inode *, struct file *);
    int (*release)(struct inode *, struct file *);
};
```

**2. File System Types**
```bash
# Common Linux file systems:
# - ext4: Default for most Linux distributions
# - xfs: High-performance file system
# - btrfs: Copy-on-write file system
# - zfs: Advanced file system with features
# - tmpfs: In-memory file system
# - proc: Virtual file system for process information
# - sysfs: Virtual file system for system information
```

**3. File System Operations**
```c
// When you do: cat /etc/passwd
// Here's what happens:

// 1. Open file
int fd = open("/etc/passwd", O_RDONLY);
// - VFS looks up inode
// - Checks permissions
// - Returns file descriptor

// 2. Read file
ssize_t bytes = read(fd, buffer, size);
// - VFS calls file system's read function
// - File system reads from disk
// - Data copied to user space

// 3. Close file
close(fd);
// - File descriptor freed
// - File system cleanup
```

#### **Linux Security Model**

**1. User and Group Management**
```c
// User information stored in /etc/passwd
struct passwd {
    char *pw_name;      // Username
    uid_t pw_uid;       // User ID
    gid_t pw_gid;       // Group ID
    char *pw_dir;       // Home directory
    char *pw_shell;     // Shell
};

// Group information stored in /etc/group
struct group {
    char *gr_name;      // Group name
    gid_t gr_gid;       // Group ID
    char **gr_mem;      // Group members
};
```

**2. File Permissions**
```c
// File permissions stored in inode
struct inode {
    umode_t i_mode;     // File mode (permissions)
    kuid_t i_uid;       // User ID
    kgid_t i_gid;       // Group ID
};

// Permission checking
int inode_permission(struct inode *inode, int mask) {
    // Check user permissions
    if (current_fsuid() == inode->i_uid) {
        if (mask & MAY_READ && !(inode->i_mode & S_IRUSR))
            return -EACCES;
        if (mask & MAY_WRITE && !(inode->i_mode & S_IWUSR))
            return -EACCES;
        if (mask & MAY_EXEC && !(inode->i_mode & S_IXUSR))
            return -EACCES;
    }
    
    // Check group permissions
    if (in_group_p(inode->i_gid)) {
        if (mask & MAY_READ && !(inode->i_mode & S_IRGRP))
            return -EACCES;
        // ... similar for write and execute
    }
    
    // Check other permissions
    if (mask & MAY_READ && !(inode->i_mode & S_IROTH))
        return -EACCES;
    // ... similar for write and execute
    
    return 0;
}
```

**3. Capabilities**
```c
// Linux capabilities provide fine-grained permissions
// Instead of root or not-root, capabilities allow specific permissions

// Common capabilities:
// - CAP_SYS_ADMIN: System administration
// - CAP_NET_ADMIN: Network administration
// - CAP_SYS_TIME: System time modification
// - CAP_SYS_MODULE: Kernel module loading
// - CAP_DAC_OVERRIDE: Bypass file permissions

// Capability checking
bool capable(int cap) {
    return security_capable(current_cred(), &init_user_ns, cap, CAP_OPT_NONE) == 0;
}
```

---

## 🛠️ **PRACTICAL EXERCISES**

### **Exercise 1: File System Navigation**
```bash
#!/bin/bash
# Exercise: Navigate and explore the Linux file system

echo "=== Linux File System Exploration ==="

# TODO: Complete these tasks:
# 1. Navigate to your home directory
echo "1. Current directory: $(pwd)"
cd ~
echo "   After cd ~: $(pwd)"

# 2. List all files including hidden ones
echo "2. All files in home directory:"
ls -la

# 3. Create a test directory
echo "3. Creating test directory:"
mkdir -p ~/linux_exercise
cd ~/linux_exercise

# 4. Create some test files
echo "4. Creating test files:"
echo "Hello, Linux!" > file1.txt
echo "This is a test file" > file2.txt
mkdir subdirectory
echo "File in subdirectory" > subdirectory/file3.txt

# 5. Display file contents
echo "5. File contents:"
cat file1.txt
cat file2.txt
cat subdirectory/file3.txt

# 6. Show file permissions
echo "6. File permissions:"
ls -l

# 7. Change file permissions
echo "7. Changing permissions:"
chmod 755 file1.txt
chmod 644 file2.txt
ls -l

# 8. Clean up
echo "8. Cleaning up:"
cd ~
rm -rf ~/linux_exercise
echo "Exercise completed!"
```

### **Exercise 2: System Information Gathering**
```bash
#!/bin/bash
# Exercise: Gather system information

echo "=== System Information Gathering ==="

# TODO: Complete these tasks:
# 1. System information
echo "1. System Information:"
uname -a
hostname
whoami
id

# 2. Hardware information
echo "2. Hardware Information:"
lscpu | head -10
free -h
lsblk
lspci | head -5
lsusb | head -5

# 3. Process information
echo "3. Process Information:"
ps aux | head -10
echo "Top 5 CPU users:"
ps aux --sort=-%cpu | head -6

# 4. Network information
echo "4. Network Information:"
ip addr show
ip route show
ss -tuln | head -10

# 5. Disk usage
echo "5. Disk Usage:"
df -h
echo "Largest directories in /home:"
du -h /home 2>/dev/null | sort -hr | head -5

# 6. System load
echo "6. System Load:"
uptime
cat /proc/loadavg
```

### **Exercise 3: Shell Scripting Practice**
```bash
#!/bin/bash
# Exercise: Create a system monitoring script

echo "=== System Monitoring Script ==="

# TODO: Create a script that:
# 1. Monitors system resources
# 2. Logs information to a file
# 3. Sends alerts if thresholds are exceeded
# 4. Runs continuously

LOG_FILE="/tmp/system_monitor.log"
ALERT_THRESHOLD_CPU=80
ALERT_THRESHOLD_MEMORY=80
ALERT_THRESHOLD_DISK=90

# Function to log with timestamp
log_message() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') - $1" >> "$LOG_FILE"
}

# Function to check CPU usage
check_cpu() {
    CPU_USAGE=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1)
    if (( $(echo "$CPU_USAGE > $ALERT_THRESHOLD_CPU" | bc -l) )); then
        log_message "ALERT: High CPU usage: ${CPU_USAGE}%"
    else
        log_message "CPU usage: ${CPU_USAGE}%"
    fi
}

# Function to check memory usage
check_memory() {
    MEMORY_USAGE=$(free | grep Mem | awk '{printf "%.2f", $3/$2 * 100.0}')
    if (( $(echo "$MEMORY_USAGE > $ALERT_THRESHOLD_MEMORY" | bc -l) )); then
        log_message "ALERT: High memory usage: ${MEMORY_USAGE}%"
    else
        log_message "Memory usage: ${MEMORY_USAGE}%"
    fi
}

# Function to check disk usage
check_disk() {
    DISK_USAGE=$(df / | tail -1 | awk '{print $5}' | cut -d'%' -f1)
    if [ "$DISK_USAGE" -gt "$ALERT_THRESHOLD_DISK" ]; then
        log_message "ALERT: High disk usage: ${DISK_USAGE}%"
    else
        log_message "Disk usage: ${DISK_USAGE}%"
    fi
}

# Main monitoring loop
echo "Starting system monitoring..."
log_message "System monitoring started"

while true; do
    check_cpu
    check_memory
    check_disk
    sleep 60  # Check every minute
done
```

---

## 📚 **SUMMARY AND NEXT STEPS**

### **Key Takeaways**

1. **Linux Philosophy** emphasizes simplicity, modularity, and the Unix way of doing things
2. **File System Structure** provides organized access to all system resources
3. **Command Line Mastery** enables efficient system administration and automation
4. **Shell Scripting** allows automation of complex tasks and system management
5. **System Administration** requires understanding of users, processes, and security

### **What You've Learned**

✅ **Purpose**: Why Linux is essential for kernel development and system administration  
✅ **Functionality**: Core Linux features and how they work  
✅ **Leveraging**: How to use Linux effectively for system management  
✅ **Debugging**: How to diagnose and fix Linux system issues  
✅ **Internal Mechanism**: How Linux systems work behind the scenes  

### **Next Steps**

In **Chapter 6: Linux System Programming**, you'll learn:
- System calls and library functions
- File I/O operations
- Process management (fork, exec, wait)
- Inter-process communication
- Threading and synchronization

### **Recommended Practice**

1. **Practice Linux commands** daily to build muscle memory
2. **Write shell scripts** to automate common tasks
3. **Monitor system resources** using the tools learned
4. **Experiment with file permissions** and user management
5. **Study system logs** to understand system behavior

---

**Ready to dive into Linux system programming? Let's continue with Chapter 6! 🚀**