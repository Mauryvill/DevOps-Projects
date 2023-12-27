# **SHELL SCRIPTING**

## **Introduction to Shell Scripting and User Input**

In the Git course, we wrote commands on teh terminal and getting corresponding outputs. The commands are instructions to the computer to carryout task.

For example, when we want to clone a git repository, we type the command ```git clone``` and pass in the link to the repository. In less than no time, the repository is downloaded into our local machine.

Let us say you are given a task to clone 1000 repositories. yes you can type the ```git clone``` command 1000 times and the Job gets done. Someone with no great patience may be unable to complete the task and this is where Shell scripting comes in.

Shell scripting helps automate repetitive tasks by writing a script that will clone all 1000 repositories.

Bash scripts are essentially a series of commands and instructions that are executed sequentially in a shell. You can create a shell script by saving a collection of commands ina text file with a **.sh** extension. These scripts can be executed directly from the command line or called from other scripts.

## **Shell Scripting Syntax Elements**
1. **Variables**: It can store data of various types such as numbers, strings and arrays. You can assign values to variables using the ```=``` operator, and access their values using the cariable name preceded by the ```$``` sign.

**Example**: Assigning value to a *variable*:
```console
name-"John"
```

**Example**: Retrieving value from a *variable*:
```console
echo $name
```

2. **Control Flow**: Pash provides control flow statements like ```if-else, for loops, while loops, case statements``` to control the flow of execution in your scripts. These statements allow you to make decisions, iterate over lists, and execute different commands based on conditions.

**Example**: using ```if-else``` command. The below code prompts you to type a number and prints a statement stating the number is positive or negative.

```console
#!/bin/bash

# Example script to check if a number is positive, negative, or zero

read -p "Enter a number: " num

if [ $num -gt 0 ]; then
    echo "The number is positive."
elif [ $num -lt 0 ]; then
    echo "The number is negative."
else
    echo "The number is zero."
fi
```
**Example**: Iterating through a list using a ```for loop```
```console
#!/bin/bash

# Example script to print numbers from 1 to 5 using a for loop

for (( i=1; i<=5; i++ ))
do
    echo $i
done
```
The result is:
![Alt text](<Images/Screenshot 1.png>)

3. **Command Substitution**: Allows you to capture the output of a command and use it as a value within a script. You can use ```backtick``` or the ```$()``` syntax.
**Example**: Using Backtick
```console
current_date=`date +%Y-%m-%d`
```
**Example**: using ```$()```
```console
current_date=$(date +%Y-%m-%d)
```

4. **Input and Output**: Bash provides various ways to handle input and output. You can use read command to accept User input, and output text to the console using ```echo``` command. Additionally, you can redirect using operators like ```>```(output a file) and ```<```(input a file) and ```|```(pipe the output of one command as input to another).

**Example**: Accept User input
```console
echo "Enter your name:"
read name
```

**Example**: Output text to the terminal
```console
echo "Hello World"
```

**Example**: Out the result of a command into a file.
```console
echo "hello world" > index.txt
```

**Example**: Pass the content of a file as input to a command
```console
grep "pattern" < input.txt
```

**Example**:pass the result of command into input of another command.
```console
echo "hello world" | grep "pattern"
```

5. **Functions**: Bash allows to define and use functions to group related commands together. Functions provide a way to modularize codes and make it more usable. It is defined by using function keywords or simply declaring the function names followed by Parenthesis.

```console
#!/bin/bash

# Define a function to greet the user
greet() {
    echo "Hello, $1! Nice to meet you."
}

# Call the greet function and pass the name as an argument
greet "John"
```

## **Let's Write Our First shell Script**

1. On your Terminal, open a folder called ```shell-scripting``` running the command ```mkdir shell-scripting```. This will hold the script we will write in this lession.
2. Create a file called ```user-input.sh``` using ```touch user-input.sh```.

![Alt text](<Images/Screenshot 2.png>)

3. Inside the file paste the below code. This scripts prompts for yoru name and when you type your name, it displays ```hello ! Nice to meet you.``` ```#!/bin/bash``` helps specify the type of Bash interpreter to be used.
```console
#!/bin/bash

# Prompt the user for their name
echo "Enter your name:"
read name

# Display a greeting with the entered name
echo "Hello, $name! Nice to meet you."
```

![Alt text](<Images/Screenshot 3.png>)



4. Save file.
5. Run the command ```chmod +x user-input.sh``` to make the file executable.

![Alt text](<Images/Screenshot 4.png>)


6. Run the script using the command ```./user-input.sh```

![Alt text](<Images/Screenshot 5.png>)

If you like text based learning material, this guide will help [Learn-Shell-Scripting](https://www.freecodecamp.org/news/bash-scripting-tutorial-linux-shell-script-and-command-line-for-beginners/)            . 

Visit this [link](https://www.learnshell.org/) to learn shell scripting in an interactive environment for free.



# **Directory Manipulation and Navigation** 
The below script will display the current directory, create a new directory called "my_directory", change to that directory, create two files inside, list the files, move back one level up, remove the "my_directory" and its contents, and finally list the files in the current directory again.

Proceed by following the steps below:
- Step 1: open a file named *navigating-linux-filesystem.sh*

![Alt text](<Images/Screenshot 6.png>)


- Step 2: paste the code block below into the file.

```console
#!/bin/bash

# Display current directory
echo "Current directory: $PWD"

# Create a new directory
echo "Creating a new directory..."
mkdir my_directory
echo "New directory created."

# Change to the new directory
echo "Changing to the new directory..."
cd my_directory
echo "Current directory: $PWD"

# Create some files
echo "Creating files..."
touch file1.txt
touch file2.txt
echo "Files created."

# List the files in the current directory
echo "Files in the current directory:"
ls

# Move one level up
echo "Moving one level up..."
cd ..
echo "Current directory: $PWD"

# Remove the new directory and its contents
echo "Removing the new directory..."
rm -rf my_directory
echo "Directory removed."

# List the files in the current directory again
echo "Files in the current directory:"
ls
```
![Alt text](<Images/Screenshot 7.png>)


- Step 3: Run the command ```chmod +x navigating-linux-filesystem.sh``` to set execute permission on the file.

![Alt text](<Images/Screenshot 8.png>)

- Step 4: Run your script using this command ```./navigating-linux-filesystem.sh```


![Alt text](<Images/Screenshot 9.png>)


# **File Operations and Sorting**

This script creates three files (file1.txt, file2.txt, and file3.txt), display the files in their current order, sorts them alphabetically, saves the sorted files in sorted_files.txt, displays the sorted files, removes the original files, renames the sorted file to sorted_files_sorted_alphabetically.txt, and finally displays the contents of the final sorted file.

- Step1: On the existing directory (my_directory), create a file called sorting.sh using the command ```touch sorting.sh```

![Alt text](<Images/Screenshot 10.png>)

- Step2: Copy and paste the code block below into the file

```console
#!/bin/bash

# Create three files
echo "Creating files..."
echo "This is file3." > file3.txt
echo "This is file1." > file1.txt
echo "This is file2." > file2.txt
echo "Files created."

# Display the files in their current order
echo "Files in their current order:"
ls

# Sort the files alphabetically
echo "Sorting files alphabetically..."
ls | sort > sorted_files.txt
echo "Files sorted."

# Display the sorted files
echo "Sorted files:"
cat sorted_files.txt

# Remove the original files
echo "Removing original files..."
rm file1.txt file2.txt file3.txt
echo "Original files removed."

# Rename the sorted file to a more descriptive name
echo "Renaming sorted file..."
mv sorted_files.txt sorted_files_sorted_alphabetically.txt
echo "File renamed."

# Display the final sorted file
echo "Final sorted file:"
cat sorted_files_sorted_alphabetically.txt
```

![Alt text](<Images/Screenshot 11.png>)

- Step3: Set executive permission on *sorting.sh* using this command ```chmod +x sorting.sh```
![Alt text](<Images/Screenshot 12.png>)


- Step4: Run your script using the command ```./sorting.sh```

![Alt text](<Images/Screenshot 13.png>)


# **Working with Numbers and Calculations**

This script defines two variables num1 and num2 with numeric values, performs basic arithmetic operations (addition, substraction, multiplication, division, and modulus), and displays the results. It also performs more complex calculations such as raising num1 to the power of 2 and calculating the square root of num2, and displays those results as well.

Lets proceedby following below steps below:
- Step1: On the existing directory (my_directory), create a file named *calculations.sh* using the command ```touch calculations.sh```

![Alt text](<Images/Screenshot 14.png>)

- Step2: Copy and paste the below command into the file:

```console
#!/bin/bash

# Define two variables with numeric values
num1=10
num2=5

# Perform basic arithmetic operations
sum=$((num1 + num2))
difference=$((num1 - num2))
product=$((num1 * num2))
quotient=$((num1 / num2))
remainder=$((num1 % num2))

# Display the results
echo "Number 1: $num1"
echo "Number 2: $num2"
echo "Sum: $sum"
echo "Difference: $difference"
echo "Product: $product"
echo "Quotient: $quotient"
echo "Remainder: $remainder"

# Perform some more complex calculations
power_of_2=$((num1 ** 2))
square_root=$(echo "sqrt($num2)" | bc)

# Display the results
echo "Number 1 raised to the power of 2: $power_of_2"
echo "Square root of number 2: $square_root"
```

![Alt text](<Images/Screenshot 15.png>)


- Step3: Set execute permission on the *calculations.sh* file using the command ```chmod _x calculations.sh```
![Alt text](<Images/Screenshot 16.png>)

- Step4: Run the script using the command ```./calculations.sh```

![Alt text](<Images/Screenshot 17.png>)

# **File Backup and Timestamping**

This script defines the source directory and backup directory paths. It then creates a timestampusing the current date and time, and creates a backup directory with timestamp appended to its name.The script then copies all files from the source directory to the backup directory using the cp command with the -r option for recursive copying. Finally, it displays a message indicating the completion of the backup process and shows the path of the backup directory with timestamp.

Lets proceed using the steps below:

- Step1: On the existing directory(my_directory), create a file called *backup.sh* using the command ```touch backup.sh```
![Alt text](<Images/Screenshot 18.png>)

- Step2: Copy and paste the code block below into the file.

```console
#!/bin/bash

# Define the source directory and backup directory
source_dir="/path/to/source_directory"
backup_dir="/path/to/backup_directory"

# Create a timestamp with the current date and time
timestamp=$(date +"%Y%m%d%H%M%S")

# Create a backup directory with the timestamp
backup_dir_with_timestamp="$backup_dir/backup_$timestamp"

# Create the backup directory
mkdir -p "$backup_dir_with_timestamp"

# Copy all files from the source directory to the backup directory
cp -r "$source_dir"/* "$backup_dir_with_timestamp"

# Display a message indicating the backup process is complete
echo "Backup completed. Files copied to: $backup_dir_with_timestamp"
```
![Alt text](<Images/Screenshot 19.png>)


- Step3: Set teh excute permission on backup.sh file using the command ```chmod +X backup.sh```

![Alt text](<Images/Screenshot 20.png>)

- Step4: Run the script using ```./backup.sh```

![Alt text](<Images/Screenshot 21.png>)

Thank you.