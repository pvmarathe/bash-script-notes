# bash-script-notes
These are my notes for bash script

Here is a comprehensive set of Markdown-formatted notes based on the tutorial. You can copy and paste this directly into a `.md` file for your GitHub repository.

***

# Comprehensive Shell Scripting Guide

## 1. Introduction to Shell Scripting
Shell scripting is the process of automating day-to-day manual, tedious, or repetitive activities on a Linux computer or virtual machine (VM). For example, if you are asked to write numbers from 1 to 1,000 or create 1,000 files, doing it manually is tedious; a shell script automates this effort.

## 2. Basic Linux Shell Commands
These are the fundamental commands used to navigate and manage a Linux system:

*   **`pwd` (Print Working Directory):** Shows the current directory folder you are in.
*   **`mkdir` (Make Directory):** Creates a new folder. Usage: `mkdir my_first_folder`.
*   **`cd` (Change Directory):** Navigates into different folders. Usage: `cd my_first_folder` or `cd ..` to go back.
*   **`ls` (List):** Lists files and folders in the current directory. 
    *   `ls -ltr`: Lists files with their timestamps, showing which was created first/last, along with permissions and ownership details.
*   **`touch`:** Creates an empty file without opening it. This is highly preferred in automation scripts where opening thousands of files would crash the system.
*   **`cat`:** Prints the contents of a file directly to the terminal without opening a text editor.
*   **`rm -rf`:** Deletes files or folders recursively and forcefully.
*   **`history`:** Displays a list of all the commands you have previously executed.
*   **`man` (Manual):** Acts as the manual for any command. Suffix a command with `man` (e.g., `man ls` or `man touch`) to read its documentation and available options.
*   **`find`:** Searches the entire file system for specific files or folders. Usage: `find / -name filename` (adding `sudo` is recommended to prevent permission denied errors).

## 3. Using the Vi/Vim Editor
Unlike `touch`, `vi` or `vim` creates and opens a file for editing. `vi` is available by default on almost all Linux platforms.
*   **Insert Mode:** When you open a file, press the `Escape` key and then `i` to enter Insert mode. You will see "INSERT" at the bottom left, meaning you can type.
*   **Save and Quit:** Press `Escape`, type `:wq!` (write and quit forcefully), and press `Enter`.
*   **Quit without Saving:** Press `Escape`, type `:q!` and press `Enter`. The file will close without saving any typed content.
*   **Read-Only Mode:** To open a file without accidentally modifying it, use the read-only flag: `vim -R filename`.

## 4. File Permissions (`chmod`)
Linux is highly secure; creating a file does not guarantee you can execute it. You must grant permissions using the `chmod` (change mode) command. 

Permissions are divided into three categories:
1.  **User (Owner):** The person who created the file.
2.  **Group:** The user group the file belongs to.
3.  **Everyone:** Any other user logging into the machine.

Permissions follow a 4-2-1 formula:
*   **4** = Read
*   **2** = Write
*   **1** = Execute

**Examples:**
*   `chmod 777 filename`: Grants read (4) + write (2) + execute (1) = 7 access to the User, Group, and Everyone.
*   `chmod 444 filename`: Grants read-only access to everyone.
*   `chmod 770 filename`: Grants full access to the User and Group, but denies all access to Everyone.

To execute a file once it has permissions, prefix the filename with `./` (e.g., `./script.sh`) or use `sh script.sh`.

## 5. Shell Scripting Best Practices
A professional shell script should always include specific structural elements.

### The Shebang (`#!/bin/bash`)
The first line of any script should be the shebang. It informs the Linux kernel which executable interpreter to use. 
*   **Best Practice:** Always use `#!/bin/bash`. 
*   **Warning:** Avoid `#!/bin/sh`. Historically, `sh` was a soft link to `bash`. However, modern operating systems (like Ubuntu) often link `sh` to `dash` by default, which has a different syntax and can cause your bash scripts to fail unexpectedly.

### Metadata/Comments
Comments are ignored by the compiler and are written using the `#` symbol. Always include metadata at the top of your script containing the Author, Date, Purpose, and Version of the script.

### Set Options for Error Handling and Debugging
Instead of cluttering a script with `echo` statements to track execution, configure the script's behavior using `set` commands:
*   `set -x`: Puts the script in debug mode, printing each command and its output to the terminal as it runs.
*   `set -e`: Automatically exits the script the moment any command throws an error. This prevents subsequent commands from executing based on a failed prerequisite.
*   `set -o pipefail`: `set -e` has a flaw—it only checks the last command in a piped sequence. Adding `pipefail` ensures the script catches and exits if *any* command within a pipe sequence fails.
*   *Note: While you can combine these as `set -exo pipefail`, it is better practice to write them on separate lines so they can be easily toggled on/off with comments*.

## 6. Advanced Commands and Tools

### Process Monitoring
*   **`nproc`:** Prints the number of CPUs.
*   **`free -g`:** Identifies free, total, and used memory (RAM).
*   **`df -h`:** Prints available disk space.
*   **`top`:** Real-time resource monitor showing which processes are consuming CPU and memory.
*   **`ps -ef`:** Prints full details of all processes currently running on the machine (background, zombie, system processes, etc.).

### Searching, Filtering, and Piping
*   **Piping (`|`):** Sends the standard output (stdout) of the first command as the standard input (stdin) to the second command.
    *   *Interview Question Trap:* `date | echo "Today is"` will NOT print the date. The `date` command natively sends its output to `stdin`, so the pipe cannot redirect it.
*   **`grep`:** Filters command output or file text for specific strings or rows.
    *   *Example:* `ps -ef | grep amazon` lists only the processes with the word "amazon".
*   **`awk`:** A powerful pattern scanning language used to retrieve specific columns from an output.
    *   *Example:* `ps -ef | grep amazon | awk -F" " '{print $2}'` splits the line by spaces (`-F" "`) and prints only the 2nd column (the Process ID).

### Fetching Remote Files (`curl` vs `wget`)
Both tools retrieve data from remote URLs (like APIs or cloud-hosted logs), but they behave differently:
*   **`curl`:** Prints the fetched data directly to the terminal stdout. Great for piping directly into another command (e.g., `curl <url> | grep ERROR`).
*   **`wget`:** Downloads the data and saves it to a file on your local disk. Requires two steps to filter: download it, then `cat` and `grep` the new file.

### Administrative Switching
*   **`sudo su -`**: `sudo` (substitute user do) provides root privileges. `su` (switch user) changes the active user profile. The `-` specifies switching fully to the root user.

## 7. Scripting Logic and Flow Control

### If/Else Statements
Shell script `if` statements require specific bracket spacing and close with `fi` (if spelled backwards).
```bash
a=4
b=10

if [ "$a" -gt "$b" ]; then
    echo "a is greater than b"
else
    echo "b is greater than a"
fi
```
*(Source:)*

### For Loops
Loops iterate over a sequence to perform repetitive actions.
```bash
for i in {1..100}; do
    echo "$i"
done
```
*(Source:)*

### Loop Control: Break vs. Continue
*   **`break`:** Completely stops and exits the loop immediately (e.g., stopping a search loop once a specific student's name is found).
*   **`continue`:** Skips the current iteration and jumps to the next item in the loop.

## 8. Specific Scripting Use Cases (Interview Exercises)

**Scenario 1: Print numbers divisible by 3 and 5, but NOT 15.**
```bash
for i in {1..100}; do
    if ([ `expr $i % 3` == 0 ] || [ `expr $i % 5` == 0 ]) && [ `expr $i % 15` != 0 ]; then
        echo $i
    fi
done
```
*(Source:)*

**Scenario 2: Find the occurrence count of the letter "s" in "mississippi".**
```bash
x="mississippi"
grep -o "s" <<< "$x" | wc -l
```
*(Explanation: `grep -o` gets only the matched character. `<<<` feeds the variable into grep as a standard input. `wc -l` counts the number of lines returned.)*

## 9. Core Concepts & Terminology

*   **Signals and Trapping:** A signal is an instruction sent to the OS (e.g., pressing `Ctrl+C` sends the `SIGINT` interrupt signal to stop a script). The `trap` command can intercept these signals. For example, `trap "echo 'don't use ctrl-c'; rm -rf *" SIGINT` will intercept a Ctrl+C, warn the user, and delete partially written database entries.
*   **Soft Link vs. Hard Link:** 
    *   *Hard Link:* Creates a literal mirror backup of a file. If the original is deleted, the hard link data remains intact.
    *   *Soft Link:* Creates a shortcut alias (e.g., `python` pointing to `python3`). If the original is deleted, the soft link breaks and becomes useless.
*   **Crontab:** A scheduling utility used by Linux Admins to automatically execute scripts at designated times (e.g., 6 PM daily) without manual intervention.
*   **Dynamic vs Static Typing:** Shell scripting (like Python) is dynamically typed, meaning you don't have to declare a variable's data type (string, int) strictly. Languages like Go are statically typed and will fail if types mismatch.
*   **Network Troubleshooting:** Use `traceroute` or `tracepath` to see the IP hops and connection times between your machine, your ISP, and a destination server.
*   **Logrotate:** An application used to automatically zip, compress, and delete huge application log files on a rolling schedule (e.g., deleting logs older than 30 days) to prevent disks from running out of space.
