# Lab 1.31 - Advanced Scripting Practice
##### LPIC-1: Linux Administrator (102-500)

## Lab Overview

This hands-on lab will help you master advanced shell scripting techniques including complex loops, conditionals, functions, error handling, and real-world automation scenarios. You'll build progressively sophisticated scripts that could be used in production environments.

**Estimated Time:** 90-120 minutes

## Prerequisites

- Access to a Linux system with sudo privileges (virtual machine recommended)
- Completion of lessons 1.11, 1.21, and 1.31
- Text editor (nano, vim, or gedit)
- Basic understanding of user management concepts

---

## Exercise 1: Advanced Loop Techniques

### Task 1.1: Multiple File Creation Methods

1. Create a script that demonstrates different loop approaches:

```bash
# Create script: loop_comparison.sh
nano loop_comparison.sh

# Add content:
#!/bin/bash
# Comparing different loop approaches for file creation

echo "=== Loop Comparison Demo ==="

# Method 1: For loop with seq
echo "Method 1: For loop with seq"
mkdir -p method1
for i in $(seq 1 5); do
    touch "method1/file_${i}.txt"
    echo "Created method1/file_${i}.txt"
done

# Method 2: While loop countdown
echo -e "\nMethod 2: While loop countdown"
mkdir -p method2
counter=5
while [ $counter -gt 0 ]; do
    touch "method2/file_${counter}.txt"
    echo "Created method2/file_${counter}.txt"
    counter=$((counter - 1))
done

# Method 3: C-style for loop
echo -e "\nMethod 3: C-style for loop"
mkdir -p method3
for ((i=1; i<=5; i++)); do
    touch "method3/file_${i}.txt"
    echo "Created method3/file_${i}.txt"
done

# Method 4: Array iteration
echo -e "\nMethod 4: Array iteration"
mkdir -p method4
files=("alpha" "beta" "gamma" "delta" "epsilon")
for filename in "${files[@]}"; do
    touch "method4/${filename}.txt"
    echo "Created method4/${filename}.txt"
done

# Compare results
echo -e "\n=== Results Comparison ==="
for method in method1 method2 method3 method4; do
    file_count=$(ls "$method" | wc -l)
    echo "$method: $file_count files created"
done
```

2. Make executable and test:

```bash
chmod +x loop_comparison.sh
./loop_comparison.sh
```

### Task 1.2: Nested Loop Matrix Generator

1. Create a script that generates a multiplication table:

```bash
# Create script: matrix_generator.sh
nano matrix_generator.sh

# Add content:
#!/bin/bash
# Matrix and table generator with nested loops

echo "=== Multiplication Table Generator ==="

# Get table size with validation
while true; do
    read -p "Enter table size (1-12): " size
    if [[ "$size" =~ ^[0-9]+$ ]] && [ "$size" -ge 1 ] && [ "$size" -le 12 ]; then
        break
    else
        echo "Please enter a number between 1 and 12"
    fi
done

echo -e "\n=== ${size}x${size} Multiplication Table ==="

# Print header row
printf "%4s" ""
for ((j=1; j<=size; j++)); do
    printf "%4d" $j
done
echo

# Print separator
printf "%4s" ""
for ((j=1; j<=size; j++)); do
    printf "%4s" "----"
done
echo

# Generate table with nested loops
for ((i=1; i<=size; i++)); do
    printf "%2d |" $i  # Row header
    for ((j=1; j<=size; j++)); do
        result=$((i * j))
        printf "%4d" $result
    done
    echo
done

# Advanced: Generate patterns
echo -e "\n=== Pattern Generator ==="
read -p "Generate triangle pattern? (y/n): " pattern

if [ "$pattern" = "y" ]; then
    echo "Triangle pattern:"
    for ((i=1; i<=size; i++)); do
        # Print spaces for alignment
        for ((s=i; s<size; s++)); do
            printf " "
        done
        # Print stars
        for ((j=1; j<=i; j++)); do
            printf "* "
        done
        echo
    done
fi
```

---

## Exercise 2: Advanced Conditional Logic

### Task 2.1: System Resource Monitor

1. Create a comprehensive system monitoring script:

```bash
# Create script: system_monitor.sh
nano system_monitor.sh

# Add content:
#!/bin/bash
# Advanced system resource monitoring with complex conditionals

echo "=== Advanced System Monitor ==="

# Function to get CPU usage
get_cpu_usage() {
    # Get CPU usage percentage (average over 1 second)
    local cpu_usage=$(top -bn2 -d1 | grep "Cpu(s)" | tail -1 | awk '{print $2}' | cut -d'%' -f1)
    echo "${cpu_usage%.*}"  # Remove decimal part
}

# Function to get memory usage percentage
get_memory_usage() {
    local memory_info=$(free | grep Mem)
    local total=$(echo $memory_info | awk '{print $2}')
    local used=$(echo $memory_info | awk '{print $3}')
    local percentage=$((used * 100 / total))
    echo $percentage
}

# Function to get disk usage percentage
get_disk_usage() {
    local disk_usage=$(df / | tail -1 | awk '{print $5}' | cut -d'%' -f1)
    echo $disk_usage
}

# Function to get load average
get_load_average() {
    local load=$(uptime | awk -F'load average:' '{print $2}' | awk '{print $1}' | cut -d',' -f1)
    echo $load
}

# Function to determine system status
check_system_status() {
    local cpu=$1
    local memory=$2
    local disk=$3
    local load=$4
    
    echo "=== System Resource Analysis ==="
    echo "CPU Usage: ${cpu}%"
    echo "Memory Usage: ${memory}%"
    echo "Disk Usage: ${disk}%"
    echo "Load Average: ${load}"
    echo
    
    # Complex conditional logic for system status
    if [ "$cpu" -gt 90 ] && [ "$memory" -gt 90 ]; then
        echo "🔴 CRITICAL: Both CPU and Memory are critically high!"
        echo "   Recommended actions:"
        echo "   - Identify resource-intensive processes"
        echo "   - Consider killing non-essential processes"
        echo "   - Monitor system stability"
        return 3
    elif [ "$cpu" -gt 80 ] || [ "$memory" -gt 85 ] || [ "$disk" -gt 90 ]; then
        echo "🟡 WARNING: High resource usage detected"
        
        # Specific warnings
        [ "$cpu" -gt 80 ] && echo "   - CPU usage is high (${cpu}%)"
        [ "$memory" -gt 85 ] && echo "   - Memory usage is high (${memory}%)"
        [ "$disk" -gt 90 ] && echo "   - Disk usage is critically high (${disk}%)"
        
        echo "   Recommended: Monitor closely and take preventive action"
        return 2
    elif [ "$cpu" -gt 60 ] || [ "$memory" -gt 70 ] || [ "$disk" -gt 80 ]; then
        echo "🟠 CAUTION: Moderate resource usage"
        echo "   System is under moderate load but manageable"
        return 1
    else
        echo "🟢 OK: System resources are normal"
        echo "   All systems operating within normal parameters"
        return 0
    fi
}

# Function to show top processes
show_top_processes() {
    echo "=== Top CPU Processes ==="
    ps aux --sort=-%cpu | head -6 | awk 'NR==1 || NR<=5 {printf "%-8s %-6s %-6s %-s\n", $1, $2, $3, $11}'
    
    echo -e "\n=== Top Memory Processes ==="
    ps aux --sort=-%mem | head -6 | awk 'NR==1 || NR<=5 {printf "%-8s %-6s %-6s %-s\n", $1, $2, $4, $11}'
}

# Main monitoring logic
main() {
    # Get current resource usage
    echo "Collecting system information..."
    cpu=$(get_cpu_usage)
    memory=$(get_memory_usage)
    disk=$(get_disk_usage)
    load=$(get_load_average)
    
    # Analyze system status
    check_system_status "$cpu" "$memory" "$disk" "$load"
    status_code=$?
    
    echo
    
    # Show additional information based on status
    case $status_code in
        3|2)
            echo "=== Detailed Analysis Required ==="
            show_top_processes
            echo
            echo "Additional system information:"
            echo "Uptime: $(uptime)"
            echo "Users logged in: $(who | wc -l)"
            ;;
        1)
            echo "=== Basic Process Information ==="
            echo "Running processes: $(ps aux | wc -l)"
            echo "Load average: $(uptime | cut -d',' -f4-)"
            ;;
        0)
            echo "=== System Summary ==="
            echo "System is running smoothly"
            echo "Uptime: $(uptime -p)"
            ;;
    esac
    
    # Ask for continuous monitoring
    echo
    read -p "Run continuous monitoring? (y/n): " continuous
    
    if [ "$continuous" = "y" ]; then
        echo "Starting continuous monitoring (Ctrl+C to stop)..."
        while true; do
            sleep 10
            clear
            echo "=== Continuous Monitoring - $(date) ==="
            main
            break  # Prevent infinite recursion in this demo
        done
    fi
}

# Run main function
main
```

### Task 2.2: Input Validation Framework

1. Create a comprehensive input validation script:

```bash
# Create script: input_validator.sh
nano input_validator.sh

# Add content:
#!/bin/bash
# Advanced input validation framework

echo "=== Input Validation Framework ==="

# Function to validate email
validate_email() {
    local email="$1"
    local email_regex="^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$"
    
    if [[ "$email" =~ $email_regex ]]; then
        return 0
    else
        echo "Error: Invalid email format"
        echo "Expected: user@domain.com"
        return 1
    fi
}

# Function to validate phone number
validate_phone() {
    local phone="$1"
    # Remove all non-digit characters for validation
    local digits_only=$(echo "$phone" | tr -d '[:space:]()+.-')
    
    if [[ "$digits_only" =~ ^[0-9]{10,15}$ ]]; then
        return 0
    else
        echo "Error: Invalid phone number"
        echo "Expected: 10-15 digits (formatting optional)"
        return 1
    fi
}

# Function to validate IP address
validate_ip() {
    local ip="$1"
    local ip_regex="^([0-9]{1,3}\.){3}[0-9]{1,3}$"
    
    if [[ "$ip" =~ $ip_regex ]]; then
        # Check each octet is <= 255
        IFS='.' read -ra octets <<< "$ip"
        for octet in "${octets[@]}"; do
            if [ "$octet" -gt 255 ] || [ "$octet" -lt 0 ]; then
                echo "Error: IP octet out of range (0-255): $octet"
                return 1
            fi
        done
        return 0
    else
        echo "Error: Invalid IP address format"
        echo "Expected: XXX.XXX.XXX.XXX"
        return 1
    fi
}

# Function to validate URL
validate_url() {
    local url="$1"
    local url_regex="^https?://[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}(/.*)?$"
    
    if [[ "$url" =~ $url_regex ]]; then
        return 0
    else
        echo "Error: Invalid URL format"
        echo "Expected: http://domain.com or https://domain.com"
        return 1
    fi
}

# Function to validate date
validate_date() {
    local date_input="$1"
    local date_regex="^[0-9]{4}-[0-9]{2}-[0-9]{2}$"
    
    if [[ "$date_input" =~ $date_regex ]]; then
        # Additional validation using date command
        if date -d "$date_input" >/dev/null 2>&1; then
            return 0
        else
            echo "Error: Invalid date"
            return 1
        fi
    else
        echo "Error: Invalid date format"
        echo "Expected: YYYY-MM-DD"
        return 1
    fi
}

# Function to validate password strength
validate_password() {
    local password="$1"
    local errors=()
    
    # Check length
    if [ ${#password} -lt 8 ]; then
        errors+=("Password must be at least 8 characters long")
    fi
    
    # Check for uppercase letter
    if ! [[ "$password" =~ [A-Z] ]]; then
        errors+=("Password must contain at least one uppercase letter")
    fi
    
    # Check for lowercase letter
    if ! [[ "$password" =~ [a-z] ]]; then
        errors+=("Password must contain at least one lowercase letter")
    fi
    
    # Check for number
    if ! [[ "$password" =~ [0-9] ]]; then
        errors+=("Password must contain at least one number")
    fi
    
    # Check for special character
    if ! [[ "$password" =~ [^a-zA-Z0-9] ]]; then
        errors+=("Password must contain at least one special character")
    fi
    
    # Report errors
    if [ ${#errors[@]} -gt 0 ]; then
        echo "Password validation failed:"
        for error in "${errors[@]}"; do
            echo "  - $error"
        done
        return 1
    fi
    
    return 0
}

# Function to get validated input
get_validated_input() {
    local prompt="$1"
    local validator="$2"
    local value
    
    while true; do
        read -p "$prompt: " value
        
        if [ -z "$value" ]; then
            echo "Error: Input cannot be empty"
            continue
        fi
        
        if $validator "$value"; then
            echo "✓ Valid $prompt"
            eval "${prompt//[^a-zA-Z0-9_]/_}='$value'"  # Store in variable
            break
        fi
        echo "Please try again."
    done
}

# Main validation demo
main() {
    echo "This script demonstrates advanced input validation."
    echo "Enter the following information:"
    echo
    
    # Collect and validate various inputs
    get_validated_input "Email" validate_email
    get_validated_input "Phone" validate_phone
    get_validated_input "IP Address" validate_ip
    get_validated_input "Website URL" validate_url
    get_validated_input "Birth Date" validate_date
    
    echo
    echo "Password validation (input will be hidden):"
    while true; do
        read -s -p "Password: " password
        echo
        if validate_password "$password"; then
            echo "✓ Password meets requirements"
            break
        fi
        echo "Please try again."
    done
    
    echo
    echo "=== Validation Complete ==="
    echo "All inputs have been validated successfully!"
    echo "Email: $Email"
    echo "Phone: $Phone"
    echo "IP: ${IP_Address}"
    echo "URL: ${Website_URL}"
    echo "Date: ${Birth_Date}"
    echo "Password: [HIDDEN]"
}

# Run main function
main
```

---

## Exercise 3: User Account Management

### Task 3.1: Enhanced User Creation Script

Building on the lesson example, create a production-ready user management script:

```bash
# Create script: user_manager.sh
nano user_manager.sh

# Add content:
#!/bin/bash
# Production-ready user account management system

# Script configuration
readonly SCRIPT_NAME="User Account Manager"
readonly VERSION="2.0"
readonly LOG_FILE="/tmp/user_manager_$(date +%Y%m%d).log"

# Default settings
readonly MIN_UID=1000
readonly MAX_UID=60000
readonly DEFAULT_SHELL="/bin/bash"
readonly DEFAULT_HOME_BASE="/home"

# Color codes
readonly RED='\033[0;31m'
readonly GREEN='\033[0;32m'
readonly YELLOW='\033[1;33m'
readonly BLUE='\033[0;34m'
readonly NC='\033[0m'

# Logging function
log_message() {
    local level="$1"
    local message="$2"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    echo "[$timestamp] $level: $message" >> "$LOG_FILE"
    
    case $level in
        "ERROR") echo -e "${RED}$message${NC}" >&2 ;;
        "WARN")  echo -e "${YELLOW}$message${NC}" ;;
        "INFO")  echo -e "${BLUE}$message${NC}" ;;
        "SUCCESS") echo -e "${GREEN}$message${NC}" ;;
        *) echo "$message" ;;
    esac
}

# Check if running with appropriate privileges
check_privileges() {
    if [ "$EUID" -ne 0 ]; then
        log_message "ERROR" "This script must be run as root or with sudo"
        echo "Usage: sudo $0"
        exit 1
    fi
}

# Validate username
validate_username() {
    local username="$1"
    
    # Check length (3-32 characters)
    if [ ${#username} -lt 3 ] || [ ${#username} -gt 32 ]; then
        log_message "ERROR" "Username must be 3-32 characters long"
        return 1
    fi
    
    # Check format (start with letter, alphanumeric + underscore/dash)
    if ! [[ "$username" =~ ^[a-z][a-z0-9_-]*$ ]]; then
        log_message "ERROR" "Username must start with lowercase letter and contain only lowercase letters, numbers, underscore, or dash"
        return 1
    fi
    
    # Check if user already exists
    if id "$username" &>/dev/null; then
        log_message "ERROR" "User '$username' already exists"
        return 1
    fi
    
    # Check against reserved usernames
    local reserved=("root" "daemon" "bin" "sys" "sync" "games" "man" "lp" "mail" "news" "uucp" "proxy" "www-data" "backup" "list" "irc" "gnats" "nobody" "systemd" "messagebus" "avahi" "sshd")
    for reserved_user in "${reserved[@]}"; do
        if [ "$username" = "$reserved_user" ]; then
            log_message "ERROR" "Username '$username' is reserved"
            return 1
        fi
    done
    
    return 0
}

# Validate full name
validate_fullname() {
    local fullname="$1"
    
    if [ -z "$fullname" ]; then
        log_message "ERROR" "Full name cannot be empty"
        return 1
    fi
    
    if [ ${#fullname} -gt 100 ]; then
        log_message "ERROR" "Full name too long (max 100 characters)"
        return 1
    fi
    
    # Allow letters, spaces, periods, apostrophes, hyphens
    if ! [[ "$fullname" =~ ^[a-zA-Z[:space:].\''-]+$ ]]; then
        log_message "ERROR" "Full name contains invalid characters"
        return 1
    fi
    
    return 0
}

# Advanced password validation
validate_password() {
    local password="$1"
    local username="$2"
    local errors=()
    
    # Length check
    [ ${#password} -lt 8 ] && errors+=("At least 8 characters required")
    [ ${#password} -gt 128 ] && errors+=("Maximum 128 characters allowed")
    
    # Character type checks
    [[ "$password" =~ [A-Z] ]] || errors+=("At least one uppercase letter required")
    [[ "$password" =~ [a-z] ]] || errors+=("At least one lowercase letter required")
    [[ "$password" =~ [0-9] ]] || errors+=("At least one number required")
    [[ "$password" =~ [^a-zA-Z0-9] ]] || errors+=("At least one special character required")
    
    # Security checks
    if [ -n "$username" ]; then
        # Password shouldn't contain username
        if [[ "${password,,}" == *"${username,,}"* ]]; then
            errors+=("Password cannot contain username")
        fi
    fi
    
    # Common password check
    local common_passwords=("password" "123456" "123456789" "qwerty" "abc123" "password123")
    for common in "${common_passwords[@]}"; do
        if [ "${password,,}" = "$common" ]; then
            errors+=("Password is too common")
            break
        fi
    done
    
    # Report errors
    if [ ${#errors[@]} -gt 0 ]; then
        log_message "ERROR" "Password validation failed:"
        for error in "${errors[@]}"; do
            log_message "ERROR" "  - $error"
        done
        return 1
    fi
    
    return 0
}

# Get next available UID
get_next_uid() {
    local next_uid=$MIN_UID
    
    while getent passwd "$next_uid" >/dev/null 2>&1; do
        next_uid=$((next_uid + 1))
        if [ "$next_uid" -gt "$MAX_UID" ]; then
            log_message "ERROR" "No available UIDs in range $MIN_UID-$MAX_UID"
            return 1
        fi
    done
    
    echo "$next_uid"
}

# Create user account
create_user() {
    local username="$1"
    local fullname="$2"
    local password="$3"
    local shell="${4:-$DEFAULT_SHELL}"
    local home_dir="${5:-$DEFAULT_HOME_BASE/$username}"
    
    log_message "INFO" "Creating user account: $username"
    
    # Get next available UID
    local uid=$(get_next_uid)
    if [ $? -ne 0 ]; then
        return 1
    fi
    
    # Create user account
    if useradd -m -u "$uid" -c "$fullname" -s "$shell" -d "$home_dir" "$username"; then
        log_message "SUCCESS" "User account created successfully"
    else
        log_message "ERROR" "Failed to create user account"
        return 1
    fi
    
    # Set password
    if echo "$username:$password" | chpasswd; then
        log_message "SUCCESS" "Password set successfully"
    else
        log_message "ERROR" "Failed to set password"
        userdel -r "$username" 2>/dev/null  # Cleanup on failure
        return 1
    fi
    
    # Set password policy (expire on first login)
    if chage -d 0 "$username" 2>/dev/null; then
        log_message "INFO" "Password expiry policy set (user must change on first login)"
    fi
    
    # Set home directory permissions
    if [ -d "$home_dir" ]; then
        chmod 750 "$home_dir"
        chown "$username:$username" "$home_dir"
        log_message "INFO" "Home directory permissions set"
    fi
    
    # Verify account creation
    if id "$username" >/dev/null 2>&1 && [ -d "$home_dir" ]; then
        log_message "SUCCESS" "Account verification successful"
        display_user_summary "$username"
        return 0
    else
        log_message "ERROR" "Account verification failed"
        return 1
    fi
}

# Display user account summary
display_user_summary() {
    local username="$1"
    
    echo
    echo "=== Account Creation Summary ==="
    echo "Username: $username"
    echo "UID: $(id -u "$username")"
    echo "GID: $(id -g "$username")"
    echo "Full Name: $(getent passwd "$username" | cut -d: -f5)"
    echo "Home Directory: $(getent passwd "$username" | cut -d: -f6)"
    echo "Shell: $(getent passwd "$username" | cut -d: -f7)"
    echo "Groups: $(groups "$username" | cut -d: -f2)"
    echo "Created: $(date)"
    echo
}

# Interactive user creation
interactive_create() {
    echo "=== Interactive User Creation ==="
    
    # Get username
    while true; do
        read -p "Username: " username
        if validate_username "$username"; then
            log_message "SUCCESS" "Username '$username' is valid"
            break
        fi
    done
    
    # Get full name
    while true; do
        read -p "Full Name: " fullname
        if validate_fullname "$fullname"; then
            log_message "SUCCESS" "Full name is valid"
            break
        fi
    done
    
    # Get password
    while true; do
        read -s -p "Password: " password
        echo
        if validate_password "$password" "$username"; then
            read -s -p "Confirm Password: " password_confirm
            echo
            if [ "$password" = "$password_confirm" ]; then
                log_message "SUCCESS" "Password confirmed"
                break
            else
                log_message "ERROR" "Passwords do not match"
            fi
        fi
    done
    
    # Optional settings
    read -p "Custom shell [$DEFAULT_SHELL]: " custom_shell
    shell=${custom_shell:-$DEFAULT_SHELL}
    
    read -p "Custom home directory [$DEFAULT_HOME_BASE/$username]: " custom_home
    home_dir=${custom_home:-$DEFAULT_HOME_BASE/$username}
    
    # Confirmation
    echo
    echo "=== Account Creation Preview ==="
    echo "Username: $username"
    echo "Full Name: $fullname"
    echo "Shell: $shell"
    echo "Home Directory: $home_dir"
    echo
    
    read -p "Create this user account? (y/N): " confirm
    
    if [ "$confirm" = "y" ] || [ "$confirm" = "Y" ]; then
        create_user "$username" "$fullname" "$password" "$shell" "$home_dir"
    else
        log_message "INFO" "User creation cancelled"
    fi
}

# Show usage
show_usage() {
    cat << EOF
$SCRIPT_NAME v$VERSION

USAGE:
    $0 [OPTIONS]

OPTIONS:
    -h, --help      Show this help message
    -i, --interactive   Interactive user creation
    -v, --version   Show version information

EXAMPLES:
    sudo $0 --interactive
    sudo $0 -i

LOGS:
    Script activity is logged to: $LOG_FILE

EOF
}

# Main function
main() {
    case "${1:-}" in
        -h|--help)
            show_usage
            exit 0
            ;;
        -v|--version)
            echo "$SCRIPT_NAME v$VERSION"
            exit 0
            ;;
        -i|--interactive|"")
            check_privileges
            log_message "INFO" "Starting $SCRIPT_NAME v$VERSION"
            interactive_create
            ;;
        *)
            echo "Unknown option: $1"
            show_usage
            exit 1
            ;;
    esac
}

# Run main function
main "$@"
```

---

## Exercise 4: Functions and Modular Programming

### Task 4.1: System Administration Toolkit

1. Create a modular script with reusable functions:

```bash
# Create script: admin_toolkit.sh
nano admin_toolkit.sh

# Add content:
#!/bin/bash
# Modular system administration toolkit

# Global configuration
readonly TOOLKIT_VERSION="1.0"
readonly LOG_DIR="/tmp/admin_toolkit"
readonly BACKUP_DIR="/tmp/backups"

# Create required directories
mkdir -p "$LOG_DIR" "$BACKUP_DIR"

# Logging functions
setup_logging() {
    local log_file="$LOG_DIR/toolkit_$(date +%Y%m%d_%H%M%S).log"
    exec 1> >(tee -a "$log_file")
    exec 2> >(tee -a "$log_file" >&2)
    echo "Logging started: $(date)"
}

log_action() {
    echo "[$(date '+%H:%M:%S')] $*"
}

# System information functions
get_system_info() {
    log_action "Collecting system information..."
    
    echo "=== System Information ==="
    echo "Hostname: $(hostname)"
    echo "Kernel: $(uname -r)"
    echo "OS: $(lsb_release -d 2>/dev/null | cut -d: -f2 | xargs || echo "Unknown")"
    echo "Architecture: $(uname -m)"
    echo "CPU cores: $(nproc)"
    echo "Total memory: $(free -h | grep Mem | awk '{print $2}')"
    echo "Disk space: $(df -h / | tail -1 | awk '{print $2 " total, " $4 " available"}')"
    echo "Uptime: $(uptime -p)"
    echo "Load average: $(uptime | awk -F'load average:' '{print $2}')"
}

get_network_info() {
    log_action "Collecting network information..."
    
    echo "=== Network Information ==="
    echo "IP addresses:"
    ip addr show | grep -E "inet [0-9]" | awk '{print "  " $2 " on " $NF}'
    echo "Default gateway: $(ip route | grep default | awk '{print $3}')"
    echo "DNS servers:"
    grep nameserver /etc/resolv.conf | awk '{print "  " $2}'
    echo "Active connections: $(ss -tuln | wc -l)"
}

get_disk_info() {
    log_action "Collecting disk information..."
    
    echo "=== Disk Information ==="
    echo "Disk usage by filesystem:"
    df -h | grep -E "^/dev|^tmpfs" | awk '{printf "  %-20s %5s %5s %5s %s\n", $1, $2, $3, $4, $5}'
    
    echo -e "\nLargest directories in /var:"
    du -sh /var/* 2>/dev/null | sort -hr | head -5 | sed 's/^/  /'
    
    echo -e "\nLargest directories in /tmp:"
    du -sh /tmp/* 2>/dev/null | sort -hr | head -5 | sed 's/^/  /' || echo "  /tmp is empty or inaccessible"
}

# User management functions
list_users() {
    log_action "Listing user accounts..."
    
    echo "=== User Accounts ==="
    echo "Regular users (UID >= 1000):"
    getent passwd | awk -F: '$3 >= 1000 && $3 != 65534 {printf "  %-15s (UID: %s) %s\n", $1, $3, $5}'
    
    echo -e "\nSystem users (UID < 1000):"
    getent passwd | awk -F: '$3 < 1000 {printf "  %-15s (UID: %s)\n", $1, $3}' | head -10
    
    echo -e "\nCurrently logged in:"
    who | awk '{printf "  %-15s %s %s\n", $1, $3, $4}'
}

check_user_activity() {
    log_action "Checking user activity..."
    
    echo "=== User Activity ==="
    echo "Last 10 logins:"
    last -n 10 | head -10 | sed 's/^/  /'
    
    echo -e "\nFailed login attempts:"
    if [ -f /var/log/auth.log ]; then
        grep "Failed password" /var/log/auth.log 2>/dev/null | tail -5 | sed 's/^/  /' || echo "  No recent failed attempts"
    else
        echo "  Auth log not accessible"
    fi
}

# Process management functions
show_processes() {
    log_action "Analyzing running processes..."
    
    echo "=== Process Information ==="
    echo "Total processes: $(ps aux | wc -l)"
    echo -e "\nTop CPU consumers:"
    ps aux --sort=-%cpu | head -6 | awk 'NR==1 || NR<=5 {printf "  %-8s %6s %6s %s\n", $1, $2, $3, $11}'
    
    echo -e "\nTop memory consumers:"
    ps aux --sort=-%mem | head -6 | awk 'NR==1 || NR<=5 {printf "  %-8s %6s %6s %s\n", $1, $2, $4, $11}'
    
    echo -e "\nProcesses by user:"
    ps aux | awk 'NR>1 {user[$1]++} END {for (u in user) printf "  %-15s %d processes\n", u, user[u]}' | sort -k2 -nr | head -5
}

# Service management functions
check_services() {
    log_action "Checking system services..."
    
    echo "=== System Services ==="
    
    if command -v systemctl >/dev/null; then
        echo "Failed services:"
        systemctl --failed --no-pager | grep -E "●|failed" | sed 's/^/  /' || echo "  No failed services"
        
        echo -e "\nActive services (sample):"
        systemctl list-units --type=service --state=active | head -10 | tail -n +2 | sed 's/^/  /'
    else
        echo "Systemd not available, checking traditional services:"
        service --status-all 2>/dev/null | head -10 | sed 's/^/  /' || echo "  Service status not available"
    fi
}

# Security functions
security_check() {
    log_action "Performing basic security checks..."
    
    echo "=== Security Check ==="
    
    # Check for users with empty passwords
    echo "Users with empty passwords:"
    awk -F: '$2 == "" {print "  " $1}' /etc/shadow 2>/dev/null || echo "  Cannot access shadow file"
    
    # Check for users with UID 0
    echo -e "\nUsers with UID 0 (root privileges):"
    awk -F: '$3 == 0 {print "  " $1}' /etc/passwd
    
    # Check SSH configuration
    echo -e "\nSSH configuration:"
    if [ -f /etc/ssh/sshd_config ]; then
        echo "  Root login: $(grep -i "PermitRootLogin" /etc/ssh/sshd_config | head -1 | awk '{print $2}' || echo "default")"
        echo "  Password auth: $(grep -i "PasswordAuthentication" /etc/ssh/sshd_config | head -1 | awk '{print $2}' || echo "default")"
    else
        echo "  SSH config not found"
    fi
    
    # Check open ports
    echo -e "\nOpen network ports:"
    ss -tuln | grep LISTEN | head -5 | sed 's/^/  /'
}

# Backup functions
create_config_backup() {
    log_action "Creating configuration backup..."
    
    local backup_file="$BACKUP_DIR/config_backup_$(date +%Y%m%d_%H%M%S).tar.gz"
    local config_dirs=("/etc" "/var/lib/dpkg" "/var/lib/rpm")
    local existing_dirs=()
    
    # Check which directories exist
    for dir in "${config_dirs[@]}"; do
        [ -d "$dir" ] && existing_dirs+=("$dir")
    done
    
    if [ ${#existing_dirs[@]} -gt 0 ]; then
        echo "Creating backup of system configuration..."
        if tar -czf "$backup_file" "${existing_dirs[@]}" 2>/dev/null; then
            echo "✓ Backup created: $backup_file"
            echo "  Size: $(du -h "$backup_file" | cut -f1)"
        else
            echo "✗ Backup failed"
        fi
    else
        echo "No configuration directories found to backup"
    fi
}

# Main menu system
show_menu() {
    cat << EOF

=== System Administration Toolkit v$TOOLKIT_VERSION ===
1.  System Information
2.  Network Information  
3.  Disk Information
4.  User Accounts
5.  User Activity
6.  Process Information
7.  Service Status
8.  Security Check
9.  Create Config Backup
10. Full System Report
11. Exit

EOF
}

# Generate full report
generate_full_report() {
    log_action "Generating full system report..."
    
    echo "========================================"
    echo "FULL SYSTEM REPORT - $(date)"
    echo "========================================"
    
    get_system_info
    echo
    get_network_info
    echo
    get_disk_info
    echo
    list_users
    echo
    check_user_activity
    echo
    show_processes
    echo
    check_services
    echo
    security_check
    
    echo
    echo "========================================"
    echo "REPORT COMPLETED - $(date)"
    echo "========================================"
}

# Main program loop
main() {
    setup_logging
    log_action "System Administration Toolkit started"
    
    while true; do
        show_menu
        read -p "Select option (1-11): " choice
        
        case $choice in
            1)  get_system_info ;;
            2)  get_network_info ;;
            3)  get_disk_info ;;
            4)  list_users ;;
            5)  check_user_activity ;;
            6)  show_processes ;;
            7)  check_services ;;
            8)  security_check ;;
            9)  create_config_backup ;;
            10) generate_full_report ;;
            11) 
                log_action "Toolkit session ended"
                echo "Goodbye!"
                exit 0
                ;;
            *)  echo "Invalid option. Please try again." ;;
        esac
        
        echo
        read -p "Press Enter to continue..."
    done
}

# Run main function
main "$@"
```

---

## Exercise 5: Error Handling and Debugging

### Task 5.1: Robust Script with Error Handling

1. Create a script demonstrating comprehensive error handling:

```bash
# Create script: error_handling_demo.sh
nano error_handling_demo.sh

# Add content:
#!/bin/bash
# Comprehensive error handling demonstration

# Enable strict error handling
set -euo pipefail

# Global variables
readonly SCRIPT_NAME="Error Handling Demo"
readonly LOG_FILE="/tmp/error_demo_$(date +%Y%m%d_%H%M%S).log"
declare -g ERROR_COUNT=0
declare -g WARNING_COUNT=0

# Color codes
readonly RED='\033[0;31m'
readonly YELLOW='\033[1;33m'
readonly GREEN='\033[0;32m'
readonly BLUE='\033[0;34m'
readonly NC='\033[0m'

# Error handler function
error_handler() {
    local line_number=$1
    local command="$2"
    local error_code=$3
    
    ERROR_COUNT=$((ERROR_COUNT + 1))
    
    echo -e "${RED}ERROR:${NC} Command failed at line $line_number" >&2
    echo -e "${RED}Command:${NC} $command" >&2
    echo -e "${RED}Exit Code:${NC} $error_code" >&2
    echo "$(date): ERROR: Line $line_number: Command '$command' failed with exit code $error_code" >> "$LOG_FILE"
    
    # Show stack trace in debug mode
    if [ "${DEBUG:-false}" = "true" ]; then
        echo -e "${BLUE}Call Stack:${NC}" >&2
        local frame=0
        while caller $frame 2>/dev/null; do
            ((frame++))
        done | sed 's/^/  /' >&2
    fi
}

# Set error trap
trap 'error_handler ${LINENO} "$BASH_COMMAND" $?' ERR

# Cleanup function
cleanup() {
    local exit_code=$?
    
    echo
    echo "=== Script Summary ==="
    echo "Errors encountered: $ERROR_COUNT"
    echo "Warnings issued: $WARNING_COUNT"
    echo "Log file: $LOG_FILE"
    
    # Clean up temporary files
    if [ -n "${TEMP_DIR:-}" ] && [ -d "$TEMP_DIR" ]; then
        rm -rf "$TEMP_DIR"
        echo "Temporary directory cleaned up"
    fi
    
    exit $exit_code
}

# Set cleanup trap
trap cleanup EXIT

# Logging functions
log_info() {
    echo -e "${BLUE}INFO:${NC} $*"
    echo "$(date): INFO: $*" >> "$LOG_FILE"
}

log_warning() {
    WARNING_COUNT=$((WARNING_COUNT + 1))
    echo -e "${YELLOW}WARNING:${NC} $*" >&2
    echo "$(date): WARNING: $*" >> "$LOG_FILE"
}

log_success() {
    echo -e "${GREEN}SUCCESS:${NC} $*"
    echo "$(date): SUCCESS: $*" >> "$LOG_FILE"
}

log_error() {
    ERROR_COUNT=$((ERROR_COUNT + 1))
    echo -e "${RED}ERROR:${NC} $*" >&2
    echo "$(date): ERROR: $*" >> "$LOG_FILE"
}

# Function with error handling
safe_execute() {
    local command="$1"
    local description="$2"
    local allow_failure="${3:-false}"
    
    log_info "Executing: $description"
    
    if eval "$command" 2>>"$LOG_FILE"; then
        log_success "$description completed"
        return 0
    else
        local exit_code=$?
        if [ "$allow_failure" = "true" ]; then
            log_warning "$description failed but continuing (exit code: $exit_code)"
            return 0
        else
            log_error "$description failed (exit code: $exit_code)"
            return $exit_code
        fi
    fi
}

# Function with retry logic
execute_with_retry() {
    local max_attempts=3
    local delay=1
    local command="$1"
    local description="$2"
    
    for ((attempt=1; attempt<=max_attempts; attempt++)); do
        log_info "Attempt $attempt/$max_attempts: $description"
        
        if eval "$command" 2>>"$LOG_FILE"; then
            log_success "Succeeded on attempt $attempt"
            return 0
        else
            local exit_code=$?
            log_warning "Attempt $attempt failed (exit code: $exit_code)"
            
            if [ $attempt -lt $max_attempts ]; then
                log_info "Retrying in $delay seconds..."
                sleep $delay
                delay=$((delay * 2))  # Exponential backoff
            fi
        fi
    done
    
    log_error "All $max_attempts attempts failed for: $description"
    return 1
}

# Input validation with error handling
validate_and_process_input() {
    local input="$1"
    local type="$2"
    
    case $type in
        "number")
            if [[ "$input" =~ ^[0-9]+$ ]]; then
                log_success "Valid number: $input"
                return 0
            else
                log_error "Invalid number format: $input"
                return 1
            fi
            ;;
        "email")
            if [[ "$input" =~ ^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$ ]]; then
                log_success "Valid email: $input"
                return 0
            else
                log_error "Invalid email format: $input"
                return 1
            fi
            ;;
        "file")
            if [ -f "$input" ]; then
                log_success "File exists: $input"
                return 0
            else
                log_error "File not found: $input"
                return 1
            fi
            ;;
        *)
            log_error "Unknown validation type: $type"
            return 1
            ;;
    esac
}

# File operations with error handling
safe_file_operations() {
    log_info "Starting file operations demo"
    
    # Create temporary directory
    TEMP_DIR=$(mktemp -d) || {
        log_error "Failed to create temporary directory"
        return 1
    }
    log_success "Created temporary directory: $TEMP_DIR"
    
    # Create test files
    for i in {1..5}; do
        local test_file="$TEMP_DIR/test_file_$i.txt"
        safe_execute "echo 'Test content $i' > '$test_file'" "Creating test file $i"
    done
    
    # List created files
    safe_execute "ls -la '$TEMP_DIR'" "Listing created files"
    
    # Simulate file processing
    for file in "$TEMP_DIR"/*.txt; do
        if [ -f "$file" ]; then
            safe_execute "wc -l '$file'" "Processing $(basename "$file")" true
        fi
    done
    
    # Simulate a command that might fail
    safe_execute "grep 'nonexistent' '$TEMP_DIR'/*.txt" "Searching for nonexistent pattern" true
    
    log_success "File operations demo completed"
}

# Network operations with retry
network_operations_demo() {
    log_info "Starting network operations demo"
    
    # Test connectivity with retry
    execute_with_retry "ping -c 1 8.8.8.8 >/dev/null" "Testing connectivity to 8.8.8.8"
    
    # Try to resolve a hostname
    execute_with_retry "nslookup google.com >/dev/null" "DNS resolution test"
    
    # Test a local service (this might fail)
    safe_execute "nc -z localhost 22" "Testing SSH port" true
    
    log_success "Network operations demo completed"
}

# Main demonstration
main() {
    log_info "Starting $SCRIPT_NAME"
    log_info "Log file: $LOG_FILE"
    
    echo "=== Error Handling Demonstration ==="
    echo "This script demonstrates comprehensive error handling techniques."
    echo
    
    # Input validation demo
    echo "1. Input Validation Demo"
    read -p "Enter a number: " user_number
    validate_and_process_input "$user_number" "number" || log_warning "Number validation failed, continuing..."
    
    read -p "Enter an email: " user_email  
    validate_and_process_input "$user_email" "email" || log_warning "Email validation failed, continuing..."
    
    read -p "Enter a filename to check: " user_file
    validate_and_process_input "$user_file" "file" || log_warning "File validation failed, continuing..."
    
    echo
    echo "2. File Operations Demo"
    safe_file_operations
    
    echo
    echo "3. Network Operations Demo (with retry)"
    network_operations_demo
    
    echo
    echo "4. Intentional Error Demo"
    read -p "Trigger an intentional error? (y/n): " trigger_error
    if [ "$trigger_error" = "y" ]; then
        log_info "Triggering intentional error..."
        # This will fail and be caught by error handler
        false || log_error "Intentional error triggered and handled"
    fi
    
    log_success "$SCRIPT_NAME completed successfully"
}

# Initialize logging
echo "$(date): Script started" > "$LOG_FILE"

# Run main function
main "$@"
```

---

## Lab Challenges

### Challenge 1: Advanced User Batch Creator

Create a script that can create multiple users from a CSV file with full validation and error handling.

### Challenge 2: System Health Monitor

Build a comprehensive system monitoring script that checks resources, generates alerts, and can send notifications.

### Challenge 3: Backup and Restore System

Develop a complete backup solution with compression, encryption, and restoration capabilities.

---

## Lab Cleanup

```bash
# Clean up created files and directories
rm -rf method1 method2 method3 method4
rm -f *.sh
rm -rf /tmp/admin_toolkit /tmp/backups
rm -f /tmp/error_demo_*.log /tmp/user_manager_*.log

# List remaining files
ls -la
```

## Review Questions

1. What's the difference between a for loop and a while loop?
2. How do you implement error handling in bash scripts?
3. What are the benefits of using functions in scripts?
4. How do you validate user input securely?
5. What security considerations should you keep in mind when creating user accounts?
6. How do you implement retry logic for unreliable operations?

---

**Congratulations!** You've completed the advanced scripting practice lab. You now have experience with:

- Complex loop structures and patterns
- Advanced conditional logic and validation
- Professional user account management
- Modular programming with functions
- Comprehensive error handling
- Real-world system administration scripts

These skills prepare you for creating production-ready scripts in professional environments!