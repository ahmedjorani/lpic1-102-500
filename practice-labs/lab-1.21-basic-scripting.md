# Lab 1.21 - Basic Scripting Practice
##### LPIC-1: Linux Administrator (102-500)

## Lab Overview

This hands-on lab will help you practice creating and executing basic shell scripts. You'll work through progressively complex exercises that cover fundamental scripting concepts, commands, and control structures.

**Estimated Time:** 60-75 minutes

## Prerequisites

- Access to a Linux system (virtual machine recommended)
- Basic command line knowledge
- Completion of lesson 1.21 - Basic Scripting
- Text editor (nano, vim, or gedit)

---

## Exercise 1: Script Fundamentals

### Task 1.1: Create Your First Script

1. Create a basic "Hello World" script:

```bash
# Create a new script file
nano hello.sh

# Add the following content:
#!/bin/bash
# My first script
# Author: [Your Name]
# Date: $(date +%Y-%m-%d)

echo "Hello, World!"
echo "Welcome to Basic Scripting!"
echo "Today is: $(date)"
```

2. Make it executable and run it:

```bash
# Make executable
chmod +x hello.sh

# Run the script
./hello.sh
```

3. **Verify your work:** The script should display the hello message and current date.

### Task 1.2: Script with Variables

1. Create a script that uses variables:

```bash
# Create script: info.sh
nano info.sh

# Add content:
#!/bin/bash
# System information script

SCRIPT_NAME="System Info"
VERSION="1.0"
AUTHOR="[Your Name]"

echo "=== $SCRIPT_NAME v$VERSION ==="
echo "Script by: $AUTHOR"
echo "Hostname: $(hostname)"
echo "Current User: $(whoami)"
echo "Current Directory: $(pwd)"
echo "Date: $(date '+%Y-%m-%d %H:%M:%S')"
```

2. Make executable and test:

```bash
chmod +x info.sh
./info.sh
```

---

## Exercise 2: User Input and Echo Command

### Task 2.1: Interactive Greeting Script

1. Create a script that interacts with the user:

```bash
# Create script: greeting.sh
nano greeting.sh

# Add content:
#!/bin/bash
# Interactive greeting script

echo "=== Personal Greeting Generator ==="
echo

# Get user information
read -p "Enter your first name: " firstname
read -p "Enter your last name: " lastname
read -p "Enter your favorite color: " color

# Create personalized greeting
fullname="$firstname $lastname"
echo
echo "Hello, $fullname!"
echo "I see your favorite color is $color."
echo -e "Here's your name in $color (if your terminal supports colors):"

# Simple color codes (red=31, green=32, blue=34, yellow=33)
case $color in
    "red"|"Red")     echo -e "\033[31m$fullname\033[0m" ;;
    "green"|"Green") echo -e "\033[32m$fullname\033[0m" ;;
    "blue"|"Blue")   echo -e "\033[34m$fullname\033[0m" ;;
    "yellow"|"Yellow") echo -e "\033[33m$fullname\033[0m" ;;
    *)               echo "$fullname" ;;
esac
```

2. Test the script with different inputs.

### Task 2.2: Password Input Script

1. Create a script that handles sensitive input:

```bash
# Create script: secure_input.sh
nano secure_input.sh

# Add content:
#!/bin/bash
# Secure input demonstration

echo "=== Secure Login Simulation ==="

read -p "Username: " username
read -s -p "Password: " password
echo  # Add newline after hidden input

# Simple validation (not for real use!)
if [ "$username" = "admin" ] && [ "$password" = "secret123" ]; then
    echo "Login successful! Welcome, $username."
else
    echo "Login failed. Invalid credentials."
fi

echo "Note: This is just a demonstration - never hardcode passwords!"
```

---

## Exercise 3: Testing and Conditionals

### Task 3.1: File and Directory Checker

1. Create a comprehensive file testing script:

```bash
# Create script: file_checker.sh
nano file_checker.sh

# Add content:
#!/bin/bash
# File and directory testing script

echo "=== File/Directory Checker ==="

read -p "Enter a file or directory path: " path

if [ -z "$path" ]; then
    echo "Error: No path provided!"
    exit 1
fi

echo "Checking: $path"
echo

# Test what type of file/directory it is
if [ -e "$path" ]; then
    echo "✓ Path exists"
    
    if [ -f "$path" ]; then
        echo "  Type: Regular file"
        echo "  Size: $(stat -c%s "$path") bytes"
        echo "  Last modified: $(stat -c%y "$path")"
    elif [ -d "$path" ]; then
        echo "  Type: Directory"
        echo "  Contents: $(ls -1 "$path" 2>/dev/null | wc -l) items"
    elif [ -L "$path" ]; then
        echo "  Type: Symbolic link"
        echo "  Points to: $(readlink "$path")"
    else
        echo "  Type: Special file"
    fi
    
    # Check permissions
    echo "  Permissions:"
    [ -r "$path" ] && echo "    ✓ Readable" || echo "    ✗ Not readable"
    [ -w "$path" ] && echo "    ✓ Writable" || echo "    ✗ Not writable"
    [ -x "$path" ] && echo "    ✓ Executable" || echo "    ✗ Not executable"
    
else
    echo "✗ Path does not exist"
    
    # Ask if user wants to create it
    read -p "Create this path? (f)ile/(d)irectory/(n)o: " choice
    case $choice in
        f|F) touch "$path" && echo "File created: $path" ;;
        d|D) mkdir -p "$path" && echo "Directory created: $path" ;;
        *) echo "No action taken" ;;
    esac
fi
```

### Task 3.2: Number Comparison Script

1. Create a script that compares numbers:

```bash
# Create script: number_compare.sh
nano number_compare.sh

# Add content:
#!/bin/bash
# Number comparison script

echo "=== Number Comparison Tool ==="

# Get two numbers with validation
while true; do
    read -p "Enter first number: " num1
    if [[ "$num1" =~ ^-?[0-9]+$ ]]; then
        break
    else
        echo "Please enter a valid integer"
    fi
done

while true; do
    read -p "Enter second number: " num2
    if [[ "$num2" =~ ^-?[0-9]+$ ]]; then
        break
    else
        echo "Please enter a valid integer"
    fi
done

echo
echo "Comparison Results:"
echo "Number 1: $num1"
echo "Number 2: $num2"
echo

# Perform all comparisons
if [ "$num1" -eq "$num2" ]; then
    echo "✓ Numbers are equal"
elif [ "$num1" -lt "$num2" ]; then
    echo "✓ $num1 is less than $num2"
    echo "  Difference: $((num2 - num1))"
else
    echo "✓ $num1 is greater than $num2"
    echo "  Difference: $((num1 - num2))"
fi

# Additional analysis
echo
echo "Additional Information:"
echo "Sum: $((num1 + num2))"
echo "Product: $((num1 * num2))"

if [ "$num2" -ne 0 ]; then
    echo "Division: $((num1 / num2)) (integer division)"
else
    echo "Division: Cannot divide by zero"
fi
```

---

## Exercise 4: Loops and Iteration

### Task 4.1: Menu System with For Loop

1. Create a system information menu:

```bash
# Create script: system_menu.sh
nano system_menu.sh

# Add content:
#!/bin/bash
# System information menu with loops

show_menu() {
    echo
    echo "=== System Information Menu ==="
    echo "1. System Date and Time"
    echo "2. System Uptime"
    echo "3. Disk Usage"
    echo "4. Memory Usage"
    echo "5. Network Interfaces"
    echo "6. Logged in Users"
    echo "7. Process Count"
    echo "8. Exit"
    echo
}

# Main menu loop
while true; do
    show_menu
    read -p "Select an option (1-8): " choice
    
    case $choice in
        1)
            echo "Current Date and Time:"
            echo "  $(date)"
            echo "  Timezone: $(date +%Z)"
            ;;
        2)
            echo "System Uptime:"
            echo "  $(uptime)"
            ;;
        3)
            echo "Disk Usage:"
            df -h | head -10
            ;;
        4)
            echo "Memory Usage:"
            free -h
            ;;
        5)
            echo "Network Interfaces:"
            ip addr show | grep -E "^[0-9]+:|inet " | head -10
            ;;
        6)
            echo "Logged in Users:"
            who
            ;;
        7)
            echo "Process Count:"
            echo "  Total processes: $(ps aux | wc -l)"
            echo "  Running processes: $(ps aux | grep -v ' Z ' | wc -l)"
            ;;
        8)
            echo "Goodbye!"
            exit 0
            ;;
        *)
            echo "Invalid option. Please try again."
            ;;
    esac
    
    read -p "Press Enter to continue..."
done
```

### Task 4.2: File Processing with For Loop

1. Create a script that processes multiple files:

```bash
# Create script: file_processor.sh
nano file_processor.sh

# Add content:
#!/bin/bash
# File processing script

echo "=== File Processor ==="

# Create some test files first
echo "Creating test files..."
for i in $(seq 1 5); do
    echo "This is test file number $i" > "testfile_$i.txt"
    echo "Created on: $(date)" >> "testfile_$i.txt"
    echo "File size will vary based on content" >> "testfile_$i.txt"
done

echo "Test files created!"
echo

# Process the files
echo "Processing files matching pattern: testfile_*.txt"
echo

file_count=0
total_size=0

for file in testfile_*.txt; do
    if [ -f "$file" ]; then
        file_count=$((file_count + 1))
        size=$(stat -c%s "$file")
        total_size=$((total_size + size))
        
        echo "File #$file_count: $file"
        echo "  Size: $size bytes"
        echo "  Lines: $(wc -l < "$file")"
        echo "  Words: $(wc -w < "$file")"
        echo "  Last modified: $(stat -c%y "$file")"
        echo
    fi
done

echo "=== Summary ==="
echo "Total files processed: $file_count"
echo "Total size: $total_size bytes"

if [ $file_count -gt 0 ]; then
    average_size=$((total_size / file_count))
    echo "Average file size: $average_size bytes"
fi

# Cleanup
read -p "Delete test files? (y/N): " cleanup
if [ "$cleanup" = "y" ] || [ "$cleanup" = "Y" ]; then
    rm -f testfile_*.txt
    echo "Test files deleted"
else
    echo "Test files preserved"
fi
```

---

## Exercise 5: Advanced User Interaction

### Task 5.1: User Account Information Script

1. Create a comprehensive user information script:

```bash
# Create script: user_info.sh
nano user_info.sh

# Add content:
#!/bin/bash
# User account information script

echo "=== User Account Information Tool ==="

# Function to check if user exists
user_exists() {
    id "$1" &>/dev/null
}

# Function to get user information
show_user_info() {
    local username="$1"
    
    echo "Information for user: $username"
    echo "================================"
    
    # Basic user info
    user_info=$(getent passwd "$username")
    if [ -n "$user_info" ]; then
        IFS=':' read -r user_name password uid gid gecos home_dir shell <<< "$user_info"
        
        echo "Username: $user_name"
        echo "User ID (UID): $uid"
        echo "Group ID (GID): $gid"
        echo "Full Name: ${gecos:-"Not specified"}"
        echo "Home Directory: $home_dir"
        echo "Default Shell: $shell"
        echo
        
        # Check home directory
        if [ -d "$home_dir" ]; then
            echo "Home Directory Status: ✓ Exists"
            echo "  Location: $home_dir"
            echo "  Owner: $(stat -c%U "$home_dir")"
            echo "  Permissions: $(stat -c%A "$home_dir")"
            echo "  Size: $(du -sh "$home_dir" 2>/dev/null | cut -f1)"
        else
            echo "Home Directory Status: ✗ Missing"
        fi
        echo
        
        # Show groups
        echo "Group Memberships:"
        groups "$username" | sed "s/$username : /  /"
        echo
        
        # Check if user is currently logged in
        if who | grep -q "^$username "; then
            echo "Login Status: ✓ Currently logged in"
            who | grep "^$username " | sed 's/^/  /'
        else
            echo "Login Status: Not currently logged in"
        fi
        
    else
        echo "Error: Could not retrieve user information"
    fi
}

# Main script
while true; do
    echo
    read -p "Enter username to check (or 'quit' to exit): " username
    
    if [ "$username" = "quit" ] || [ "$username" = "exit" ]; then
        echo "Goodbye!"
        break
    fi
    
    if [ -z "$username" ]; then
        echo "Please enter a username"
        continue
    fi
    
    if user_exists "$username"; then
        echo
        show_user_info "$username"
    else
        echo "Error: User '$username' does not exist"
        
        # Suggest similar usernames
        echo "Similar usernames found:"
        getent passwd | cut -d: -f1 | grep -i "$username" | head -5 | sed 's/^/  /'
    fi
done
```

### Task 5.2: System Backup Simulation Script

1. Create a backup simulation script:

```bash
# Create script: backup_sim.sh
nano backup_sim.sh

# Add content:
#!/bin/bash
# Backup simulation script (educational purposes)

echo "=== System Backup Simulation ==="

# Configuration
BACKUP_BASE="/tmp/backups"
DATE=$(date +%Y%m%d_%H%M%S)

# Create backup directory
mkdir -p "$BACKUP_BASE"

# Function to simulate backup
simulate_backup() {
    local source="$1"
    local dest="$2"
    local items=$(find "$source" -type f 2>/dev/null | wc -l)
    
    echo "Backing up: $source"
    echo "Destination: $dest"
    echo "Files found: $items"
    
    # Simulate progress
    for i in $(seq 1 10); do
        echo -n "."
        sleep 0.2
    done
    echo " Done!"
    
    # Create a dummy backup file
    echo "Backup completed on $(date)" > "$dest/backup_${DATE}.log"
    echo "Source: $source" >> "$dest/backup_${DATE}.log"
    echo "Files: $items" >> "$dest/backup_${DATE}.log"
    
    echo "Backup log created: $dest/backup_${DATE}.log"
}

# Main backup menu
while true; do
    echo
    echo "=== Backup Options ==="
    echo "1. Backup home directory"
    echo "2. Backup system configuration (/etc)"
    echo "3. Custom backup location"
    echo "4. View previous backups"
    echo "5. Exit"
    echo
    read -p "Select backup option (1-5): " choice
    
    case $choice in
        1)
            backup_dir="$BACKUP_BASE/home_backup"
            mkdir -p "$backup_dir"
            simulate_backup "$HOME" "$backup_dir"
            ;;
        2)
            backup_dir="$BACKUP_BASE/etc_backup"
            mkdir -p "$backup_dir"
            simulate_backup "/etc" "$backup_dir"
            ;;
        3)
            read -p "Enter directory to backup: " custom_dir
            if [ -d "$custom_dir" ]; then
                backup_name=$(basename "$custom_dir")
                backup_dir="$BACKUP_BASE/${backup_name}_backup"
                mkdir -p "$backup_dir"
                simulate_backup "$custom_dir" "$backup_dir"
            else
                echo "Error: Directory '$custom_dir' does not exist"
            fi
            ;;
        4)
            echo "Previous backup logs:"
            if ls "$BACKUP_BASE"/*/backup_*.log &>/dev/null; then
                ls -la "$BACKUP_BASE"/*/backup_*.log
            else
                echo "No backup logs found"
            fi
            ;;
        5)
            echo "Backup simulation completed!"
            echo "Note: This was a simulation - no real backups were created"
            break
            ;;
        *)
            echo "Invalid option"
            ;;
    esac
done
```

---

## Exercise 6: Troubleshooting Challenge

### Task 6.1: Debug the Broken Script

You've been given this script with multiple errors. Find and fix all the issues:

```bash
# Create script: broken_script.sh
nano broken_script.sh

# Add this intentionally broken content:
#!/bin/bash
# Broken script - find and fix the errors!

echo "=== User Management Script ==="

# Error 1: Missing quotes around variable assignment with spaces
USER_NAME = "system admin"

# Error 2: Incorrect test syntax
if [ $USER_NAME = "admin" ]; then
    echo "Welcome, administrator!"
fi

# Error 3: Using undefined variable
echo "Processing user: $USERNAME"

# Error 4: Incorrect loop syntax
for i in 1 2 3 4 5
    echo "Processing item $i"
done

# Error 5: Missing 'do' keyword
while [ $counter -lt 5 ]
    echo "Counter: $counter"
    counter=$((counter + 1))
done

# Error 6: Incorrect conditional structure
read -p "Continue? (y/n): " answer
if [ $answer = "y" ]
    echo "Continuing..."
elif [ $answer = "n" ]
    echo "Stopping..."
else
    echo "Invalid input"

# Error 7: Command substitution issue
current_date=`date
echo "Today is: $current_date"

echo "Script completed"
```

**Your Challenge:**
1. Identify all 7 errors in the script
2. Create a corrected version called `fixed_script.sh`
3. Test the corrected script to ensure it works properly

### Task 6.2: Create the Corrected Version

Create your fixed version and test it:

```bash
# Create the corrected script
nano fixed_script.sh

# Test your corrected script
chmod +x fixed_script.sh
./fixed_script.sh
```

---

## Lab Solutions and Review

<details>
<summary>Click to reveal solutions</summary>

### Task 6.1 - Errors Found:

1. **Error 1**: `USER_NAME = "system admin"` - No spaces around = in assignments
   **Fix**: `USER_NAME="system admin"`

2. **Error 2**: `if [ $USER_NAME = "admin" ]` - Variable not quoted
   **Fix**: `if [ "$USER_NAME" = "admin" ]`

3. **Error 3**: `$USERNAME` - Wrong variable name (should be $USER_NAME)
   **Fix**: `echo "Processing user: $USER_NAME"`

4. **Error 4**: Missing `do` in for loop
   **Fix**: Add `do` after the list

5. **Error 5**: Missing `do` in while loop
   **Fix**: Add `do` after condition

6. **Error 6**: Missing `then` keywords in if statement
   **Fix**: Add `then` after each condition

7. **Error 7**: Incomplete command substitution
   **Fix**: `current_date=$(date)` or proper backtick closure

### Corrected Script:
```bash
#!/bin/bash
# Fixed script

echo "=== User Management Script ==="

# Fixed: Removed spaces around =
USER_NAME="system admin"

# Fixed: Added quotes around variable
if [ "$USER_NAME" = "admin" ]; then
    echo "Welcome, administrator!"
fi

# Fixed: Correct variable name
echo "Processing user: $USER_NAME"

# Fixed: Added 'do'
for i in 1 2 3 4 5; do
    echo "Processing item $i"
done

# Initialize counter and fix loop
counter=1
while [ $counter -lt 5 ]; do
    echo "Counter: $counter"
    counter=$((counter + 1))
done

# Fixed: Added 'then' keywords
read -p "Continue? (y/n): " answer
if [ "$answer" = "y" ]; then
    echo "Continuing..."
elif [ "$answer" = "n" ]; then
    echo "Stopping..."
else
    echo "Invalid input"
fi

# Fixed: Proper command substitution
current_date=$(date)
echo "Today is: $current_date"

echo "Script completed"
```

</details>

---

## Lab Cleanup

Clean up any test files created during the lab:

```bash
# Remove test scripts (keep the ones you want to save)
rm -f testfile_*.txt
rm -rf /tmp/backups

# List your created scripts
ls -la *.sh
```

## Review Questions

1. What's the purpose of the shebang (`#!/bin/bash`) line?
2. How do you make a script executable?
3. What's the difference between `echo` and `echo -e`?
4. How do you read user input into a variable?
5. What's the syntax difference between `test -f file` and `[ -f file ]`?
6. When should you quote variables in test conditions?
7. What's the difference between a `for` loop and a `while` loop?

---

**Congratulations!** You've completed the basic scripting practice lab. You now have hands-on experience with:
- Creating and executing scripts
- Using echo, read, and test commands
- Implementing control structures
- Handling user input and validation
- Debugging script errors

These skills form the foundation for more advanced scripting techniques!