# Lab 1.11 - Working with Variables Practice
##### LPIC-1: Linux Administrator (102-500)

## Lab Overview

This hands-on lab will help you practice creating, modifying, and managing variables in Linux. You'll work through real-world scenarios that reinforce the concepts from lesson 1.11.

**Estimated Time:** 45-60 minutes

## Prerequisites

- Access to a Linux system (virtual machine recommended)
- Basic command line knowledge
- Completion of lesson 1.11

---

## Exercise 1: Basic Variable Operations

### Task 1.1: Create and Display Variables

1. Open a terminal and create the following variables:

```bash
# Create personal information variables
FIRST_NAME="Your_First_Name"
LAST_NAME="Your_Last_Name"
AGE=25
CITY="Your_City"

# Display each variable
echo "First Name: $FIRST_NAME"
echo "Last Name: $LAST_NAME" 
echo "Age: $AGE"
echo "City: $CITY"
```

2. **Verify your work:** All variables should display correctly.

### Task 1.2: Combine Variables

1. Create a full profile using variable combination:

```bash
# Combine first and last name
FULL_NAME="$FIRST_NAME $LAST_NAME"

# Create a complete introduction
INTRODUCTION="Hello, my name is $FULL_NAME. I am $AGE years old and live in $CITY."

# Display the introduction
echo "$INTRODUCTION"
```

2. **Expected output:** A complete sentence with your information.

### Task 1.3: Test Variable Scope

1. Create a variable and test its scope:

```bash
# Create a local variable
LOCAL_VAR="This is local"

# Start a new shell (subshell)
bash

# Try to access the variable (should be empty)
echo "In subshell: $LOCAL_VAR"

# Exit the subshell
exit

# Verify variable still exists in parent shell
echo "In parent shell: $LOCAL_VAR"
```

2. **Question:** Why was the variable empty in the subshell?

---

## Exercise 2: Environment Variables and Export

### Task 2.1: Create and Export Variables

1. Create variables and test exporting:

```bash
# Create a local variable
APP_NAME="MyApplication"

# Check if it appears in environment
env | grep APP_NAME
# Should return nothing

# Export the variable
export APP_NAME

# Check environment again
env | grep APP_NAME
# Should now show: APP_NAME=MyApplication

# Test in a subshell
bash -c 'echo "App name in subshell: $APP_NAME"'
```

2. **Observation:** Exported variables are available in child processes.

### Task 2.2: Database Configuration Scenario

Create a realistic database configuration:

```bash
# Database connection variables
DB_HOST="localhost"
DB_PORT=5432
DB_NAME="inventory_system"
DB_USER="app_user"

# Export all database variables
export DB_HOST DB_PORT DB_NAME DB_USER

# Create a connection string
DB_CONNECTION="postgresql://$DB_USER@$DB_HOST:$DB_PORT/$DB_NAME"
export DB_CONNECTION

# Display configuration
echo "=== Database Configuration ==="
echo "Host: $DB_HOST"
echo "Port: $DB_PORT"
echo "Database: $DB_NAME"
echo "User: $DB_USER"
echo "Connection String: $DB_CONNECTION"
```

---

## Exercise 3: Command Substitution

### Task 3.1: System Information Variables

1. Capture system information in variables:

```bash
# Current user information
CURRENT_USER=$(whoami)
CURRENT_DATE=$(date '+%Y-%m-%d %H:%M:%S')
SYSTEM_UPTIME=$(uptime)
DISK_USAGE=$(df -h / | tail -1 | awk '{print $5}')

# Display system information
echo "=== System Information ==="
echo "Current User: $CURRENT_USER"
echo "Current Date: $CURRENT_DATE"
echo "System Uptime: $SYSTEM_UPTIME"
echo "Root Disk Usage: $DISK_USAGE"
```

### Task 3.2: File and Directory Analysis

1. Analyze current directory:

```bash
# Directory analysis
CURRENT_DIR=$(pwd)
FILE_COUNT=$(ls -1 | wc -l)
HIDDEN_COUNT=$(ls -la | grep '^\.' | wc -l)
LARGEST_FILE=$(ls -lS | head -2 | tail -1 | awk '{print $9 " (" $5 " bytes)"}')

# Display analysis
echo "=== Directory Analysis ==="
echo "Current Directory: $CURRENT_DIR"
echo "Visible Files: $FILE_COUNT"
echo "Hidden Files: $HIDDEN_COUNT"
echo "Largest File: $LARGEST_FILE"
```

---

## Exercise 4: Configuration Files

### Task 4.1: Backup and Modify .bashrc

1. **Safety first:** Backup your .bashrc file:

```bash
# Create backup
cp ~/.bashrc ~/.bashrc.backup.$(date +%Y%m%d)

# Verify backup
ls -la ~/.bashrc*
```

2. Add custom variables to .bashrc:

```bash
# Add custom variables to .bashrc
cat >> ~/.bashrc << 'EOF'

# Custom variables for LPIC-1 lab
export LAB_USER="$USER"
export LAB_DATE="$(date +%Y-%m-%d)"
export LAB_PATH="$HOME/lpic1-labs"

# Custom alias using variables
alias labinfo='echo "Lab User: $LAB_USER | Lab Date: $LAB_DATE | Lab Path: $LAB_PATH"'
EOF
```

3. Reload .bashrc and test:

```bash
# Reload configuration
source ~/.bashrc

# Test the new variables and alias
echo "Lab User: $LAB_USER"
echo "Lab Date: $LAB_DATE"
echo "Lab Path: $LAB_PATH"
labinfo
```

### Task 4.2: Create a Custom Profile Script

1. Create a custom script in profile.d:

```bash
# Create a custom profile script (if you have sudo access)
sudo tee /etc/profile.d/lpic1-lab.sh << 'EOF'
# LPIC-1 Lab Environment Variables
export LPIC1_LAB_VERSION="1.0"
export LPIC1_LAB_AUTHOR="Your Name"
export LPIC1_EXAM_CODE="102-500"

# Welcome message function
lpic1_welcome() {
    echo "=== LPIC-1 Lab Environment ==="
    echo "Version: $LPIC1_LAB_VERSION"
    echo "Author: $LPIC1_LAB_AUTHOR"
    echo "Exam Code: $LPIC1_EXAM_CODE"
    echo "=============================="
}
EOF

# Make it executable
sudo chmod +x /etc/profile.d/lpic1-lab.sh

# Test in a new shell
bash -l -c 'lpic1_welcome'
```

---

## Exercise 5: Advanced Variable Techniques

### Task 5.1: Default Values and Error Handling

1. Practice variable default values:

```bash
# Test default values
echo "Web server port: ${WEB_PORT:-8080}"
echo "Database timeout: ${DB_TIMEOUT:-30}"
echo "Debug mode: ${DEBUG_MODE:-false}"

# Set one variable and test again
WEB_PORT=3000
echo "Web server port: ${WEB_PORT:-8080}"

# Test required variables
CONFIG_FILE=${CONFIG_FILE:?Error: CONFIG_FILE must be set}
# This should show an error
```

### Task 5.2: Application Configuration Scenario

Create a complete application configuration:

```bash
#!/bin/bash
# Web Application Configuration Script

# Set application defaults
APP_NAME=${APP_NAME:-"WebApp"}
APP_VERSION=${APP_VERSION:-"1.0.0"}
APP_ENV=${APP_ENV:-"development"}

# Server configuration
SERVER_HOST=${SERVER_HOST:-"localhost"}
SERVER_PORT=${SERVER_PORT:-8080}
SERVER_WORKERS=${SERVER_WORKERS:-4}

# Database configuration
DB_HOST=${DB_HOST:-"localhost"}
DB_PORT=${DB_PORT:-5432}
DB_NAME=${DB_NAME:-"${APP_NAME,,}_${APP_ENV}"}  # Lowercase app name + env

# Security settings
SECRET_KEY=${SECRET_KEY:?Error: SECRET_KEY must be set for security}
JWT_TIMEOUT=${JWT_TIMEOUT:-3600}

# Create configuration summary
CONFIG_SUMMARY="
=== $APP_NAME Configuration ===
Version: $APP_VERSION
Environment: $APP_ENV

Server Settings:
- Host: $SERVER_HOST
- Port: $SERVER_PORT
- Workers: $SERVER_WORKERS

Database Settings:
- Host: $DB_HOST
- Port: $DB_PORT
- Database: $DB_NAME

Security Settings:
- JWT Timeout: $JWT_TIMEOUT seconds
================================
"

echo "$CONFIG_SUMMARY"
```

---

## Exercise 6: Troubleshooting Challenge

### Task 6.1: Debug Variable Issues

You've been given this broken script. Find and fix the issues:

```bash
#!/bin/bash
# Broken script - find the errors!

USER_NAME = "admin"                    # Error 1: Space around =
PASSWORD="secret123"
HOST=production-server

# Error 2: Unquoted variable with special characters
echo Connecting to $HOST as $USER_NAME

# Error 3: Variable not exported for child process
mysql -h $HOST -u $USER_NAME -p$PASSWORD -e "SHOW DATABASES;"

# Error 4: Undefined variable used
echo "Backup location: $BACKUP_PATH"

# Error 5: Command substitution with backticks in modern script
DATE=`date`
echo "Script run at: $DATE"
```

**Your task:** Identify the 5 errors and provide the corrected script.

### Task 6.2: Variable Validation Script

Create a script that validates required variables:

```bash
#!/bin/bash
# Variable validation script

check_variable() {
    local var_name="$1"
    local var_value="$2"
    
    if [ -z "$var_value" ]; then
        echo "ERROR: $var_name is not set or empty"
        return 1
    else
        echo "OK: $var_name = $var_value"
        return 0
    fi
}

# Test various variables
echo "=== Variable Validation ==="
check_variable "USER" "$USER"
check_variable "HOME" "$HOME"
check_variable "SHELL" "$SHELL"
check_variable "UNDEFINED_VAR" "$UNDEFINED_VAR"
```

---

## Lab Solutions and Answers

<details>
<summary>Click to reveal answers</summary>

### Exercise 1.3 Answer:
The variable was empty in the subshell because local variables are not inherited by child processes. Only exported (environment) variables are passed to subshells.

### Exercise 6.1 Corrected Script:
```bash
#!/bin/bash
# Corrected script

USER_NAME="admin"                      # Fixed: Removed spaces around =
PASSWORD="secret123"
HOST="production-server"               # Fixed: Added quotes

# Fixed: Quoted variables
echo "Connecting to $HOST as $USER_NAME"

# Fixed: Export variables for child process
export HOST USER_NAME PASSWORD
mysql -h "$HOST" -u "$USER_NAME" -p"$PASSWORD" -e "SHOW DATABASES;"

# Fixed: Define the variable
BACKUP_PATH="/var/backups"
echo "Backup location: $BACKUP_PATH"

# Fixed: Use $() instead of backticks
DATE=$(date)
echo "Script run at: $DATE"
```

</details>

---

## Lab Cleanup

When you're finished with the lab:

```bash
# Restore original .bashrc if needed
cp ~/.bashrc.backup.$(date +%Y%m%d) ~/.bashrc 2>/dev/null

# Remove custom profile script (if created)
sudo rm -f /etc/profile.d/lpic1-lab.sh 2>/dev/null

# Clean up any test variables
unset FIRST_NAME LAST_NAME AGE CITY FULL_NAME
unset APP_NAME DB_HOST DB_PORT DB_NAME DB_USER
```

## Review Questions

1. What's the difference between a local variable and an environment variable?
2. When should you use quotes around variable assignments?
3. How do you make a variable available to child processes?
4. What are three ways to provide default values for variables?
5. Which configuration file would you use for system-wide environment variables?

---

**Congratulations!** You've completed the variables practice lab. These skills are fundamental for shell scripting and system administration.