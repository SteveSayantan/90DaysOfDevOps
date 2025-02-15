## Week 3 Challenge 1: User Account Management 

In this challenge, you will create a bash script that provides options for managing user accounts on the system. The script should allow users to perform various user account-related tasks based on command-line arguments.

### Part 1: Account Creation

1. Implement an option `-c` or `--create` that allows the script to create a new user account. The script should prompt the user to enter the new username and password.

2. Ensure that the script checks whether the username is available before creating the account. If the username already exists, display an appropriate message and exit gracefully.

3. After creating the account, display a success message with the newly created username.

### Part 2: Account Deletion

1. Implement an option `-d` or `--delete` that allows the script to delete an existing user account. The script should prompt the user to enter the username of the account to be deleted.

2. Ensure that the script checks whether the username exists before attempting to delete the account. If the username does not exist, display an appropriate message and exit gracefully.

3. After successfully deleting the account, display a confirmation message with the deleted username.

### Part 3: Password Reset

1. Implement an option `-r` or `--reset` that allows the script to reset the password of an existing user account. The script should prompt the user to enter the username and the new password.

2. Ensure that the script checks whether the username exists before attempting to reset the password. If the username does not exist, display an appropriate message and exit gracefully.

3. After resetting the password, display a success message with the username and the updated password.

### Part 4: List User Accounts

1. Implement an option `-l` or `--list` that allows the script to list all user accounts on the system. The script should display the usernames and their corresponding user IDs (UID).

### Part 5: Help and Usage Information

1. Implement an option `-h` or `--help` that displays usage information and the available command-line options for the script.

### My Solution: 
```bash
#! /bin/bash

# Function to display an error message and exit with a specific status code
displayErrMsg(){

    msg="Invalid usage. Try 'usrmanager --help' for more information."
    exStat=1

    if [ -n "$1" ] ;
    then
        msg=$1
    fi

    if [ -n "$2" ];
    then
        exStat=$2
    fi

    echo $msg
    exit $exStat
}

# Function to display the help menu with available options
displayHelp(){
    echo "
Usage: user_manager.sh [OPTIONS]

This script provides options for managing users on the system.

Options:

  -c, --create        Create a new user.
  -d, --delete        Delete an existing user.
  -r, --reset         Reset the password of an existing user.
  -l, --list          List all users and their UIDs.
  -h, --help          Display this help message.

Examples:

  Create a new user:
    user_manager.sh -c

  Delete a user:
    user_manager.sh -d

  Reset a user's password:
    user_manager.sh -r

  List all users:
    user_manager.sh -l
"
}

# Function to get a list of all regular users with their user IDs
getUsers(){

    echo username userid
    echo -------- ------
    awk -F : '$3 >= 1000 && $7 !~ /(nologin|false)$/ {print $1,$3}' /etc/passwd
}

# Function to create a new user with password input
createUser(){
    # input username
    read -p "Enter new username: " username

    # check if username already exists
    if grep -q "^$username" /etc/passwd;
    then
        displayErrMsg "user already exists" 9
    fi

    # Attempt to create the user and handle errors
    if ! useradd -m -s /bin/bash $username &> /dev/null;
    then
        displayErrMsg "some error occurred" 2
    fi
    
    echo success!! $username created
    passwd $username
}

# Function to create a new user with password input : Another approach
createUserV2(){
    # input username
    read -p "Enter new username: " username

    #input password
    read -s -p "Enter password for $username: " password

    echo ''
    
    # check if username already exists
    if grep -q "^$username" /etc/passwd;
    then
        displayErrMsg "user already exists" 9
    fi

    # Attempt to create the user and handle errors
    if ! useradd -m -s /bin/bash $username &> /dev/null;
    then
        displayErrMsg "some error occurred" 2
    fi
    
    # Assign password to the newly created user
    if ! chpasswd <<< "$username:$password";
    then
        displayErrMsg "some error occurred" 2
    fi

    echo $username created successfully

}

# Function to delete an existing user
deleteUser(){
    # input username
    read -p "Enter username: " username

    #check if username exists
    if ! grep -q "^$username" /etc/passwd;
    then
        displayErrMsg "invalid credentials" 1
    fi

    # delete the user
    userdel -r $username
}

# Function to reset the password of an existing user
resetPassword(){
    # input username
    read -p "Enter username: " username

    #check if username exists
    if ! grep -q "^$username" /etc/passwd;
    then
        displayErrMsg "invalid credentials" 1
    fi

    # reset password for given user
    passwd $username
}

# Check if exactly one argument is provided, else show error
if [ $# -ne 1 ]
then
    displayErrMsg
fi


# Handle command-line arguments using a case statement

case ${1} in         
    #first case
    -h | --help) displayHelp ;;     
    #second case
    -c | --create) createUserV2 ;;
    -d | --delete) deleteUser ;;
    -r | --reset) resetPassword ;;
    -l | --list) getUsers ;;
    #default case
    *) displayErrMsg 
esac

```

## Week 3 Challenge 2: Automated Backup & Recovery using Cron


This is another challenge for Day 2 of the Bash Scripting Challenge! In this challenge, you will create a bash script that performs a backup of a specified directory and implements a rotation mechanism to manage backups.

### Challenge Description

Your task is to create a bash script that takes a directory path as a command-line argument and performs a backup of the directory. The script should create timestamped backup folders and copy all the files from the specified directory into the backup folder.

Additionally, the script should implement a rotation mechanism to keep only the last 3 backups. This means that if there are more than 3 backup folders, the oldest backup folders should be removed to ensure only the most recent backups are retained.

> The script will create a timestamped backup folder inside the specified directory and copy all the files into it. It will also check for existing backup folders and remove the oldest backups to keep only the last 3 backups.

### My Solution

```bash
#! /bin/bash

# Check to make sure the user has entered exactly two arguments
if [ $# -ne 2 ]
then
    echo "Usage: backup.sh <source_dir> <target_dir>"
    echo "Please try again"
    exit 1
fi

# Check if both arguments are valid directories
if [ ! -d $1 -o ! -d $2 ];
then
   echo "Invalid location"
   exit 2
fi

# Count the number of existing backup files in the target directory
backup_count=`find $2 -name "backup*" -type f | wc -l`

# If there are 3 or more backups, delete the oldest one
if [ $backup_count -ge 3 ];
then
    # Identify the oldest backup file based on creation time
    temp=$(ls -1 -r --sort=time --time=birth $2 | head -n 1)
    
    # Remove the oldest backup file to maintain only the latest 3 backups
    rm $2/$temp
fi

# Generate a timestamp for the new backup file
current_time=$(date +%F_%H-%M)

# Create a compressed tar archive of the source directory in the target directory
$(which tar) -czf $2/backup_$current_time.tar.gz $1 &> /dev/null

```
