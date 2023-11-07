# **Linux and Basic Commands**

Linux is a family of Open-source Unix operating systems based on the Linux Kernel. They include Ubuntu, RedHat, OpenSUSE, Debian and Fedora.
When Operating Linux, you need to use a shell which is a program that gives you access to the operating system services. 
Although most Linux distributions use a graphical user interface(GUI) which makes them beginner-friendly, we recommend utilizing the command-line interface (CLI) as it is quicker and offers more control.

## **Linux commands**

A Linux command is a program or utility that runs on the CLI which interacts with the system via texts and processes similar to command prompt application on Windows.
Linux commands are executed on the terminal by pressing the Enter key on your keyboard at the end of the line/ command.
See below a sample of how a Linux command syntax looks like:

CommandName [option(s)] [parameter(s)]

A command may contain an option or parameter and can still run with them.
**CommandName** is the rule  you want to perform, **Option** or **Flag** modifies a command operation using hyphens(-) or double hyphens (--) and a **Parameter** or **Argument specifies any neccessary  information for the command.

**Note**: All Linux commands are case-sensitive.




## **File Manipulation**

1. ### **sudo**: 
Is a short form for SuperUser do. It is one of the most basic commands to perform tasks that require administrative or root permissions. You will be promoted to autheticate with a password.

```console 
sudo apt upgrade
```
![Alt text](<Images/Screenshot 2023-11-07 131142.png>)



2. ### **pwd**:
is a short form for Present working directory. it is used to show the current path/directory.

```console
pwd
```
![Alt text](<Images/Screenshot 2.png>)


3. ### **cd**:
Is used to navigate through Linux files and directories. Adding the below options gives the below output.

```console
cd ~[username] (goes to another user's home directory)

cd .. (moves one directory up)

cd - (moves to your previous directory)
```

![Alt text](<Images/Screenshot 3.png>)


4. ### **ls**:
Is used to list files and directories within the system. Adding the below options gives the below output.

```console
ls -R                    //list all the files in teh subdirectory//
ls -a                    //show hidden files//
ls -lh                //shows the files sizes in a readable format such as MB,GB.TB//
```
![Alt text](<Images/Screenshot 4.png>)


5. ### **cat**:
Is a short form for concatenate which is also one of the most commonly used to list, combine and write files content to a standard output. 

```console
cat [file name and its extension]
Example: cat devops_folder

cat [filename1.txt] [filename2.txt] > [filename3.txt]    //merges the output of filename 1 and 2 in 3//

tac [filename.txt]    //displaces content of file in a reverse order//
```

![Alt text](<Images/Screenshot 5.png>)


6. ### **cp and mv**:
Cp is used to copy files or directories and their content to another directory or file. Adding the flag -R copies the entire directory.

Mv is used to move and rename files and directories. Additionally, it does not produce an output upon execution. Type mv followed by the file name and destination directory or file name with new filename incase or renaming.

```console
cp devops_folder /home/vboxuser/DevOps_folder             // to copy file to a directory//
cp text1.txt text2.txt /home/vboxuser/DevOps_folder      //to copy files into a directory//
cp text3.txt text1.txt                    //to copy content of file into another file//
cp -R /home/vboxuser/DevOps_folder /home/vboxuser/echo      //copy directory to directory//


mv devops_folder /home/vboxuser/echo                  //move file to a directory//
mv text2.txt text22.txt                               //rename a file//
```

![Alt text](<Images/Screenshot 6.png>)


7. ### **mkdir, rmdir, rm**:
Mkdir command is used to create one or multiple directories and set permissions for each of them. mkdir [option] directory_name is a syntax for this. 

Rmdir command is used to remove or permanently delete an empty directory. User must have sudo priviledges.

Rm command is used to delete files within a directory. User performing this action must have a write permission.


```console
mkdir Musics                            //create a directory called Music//
mkdir Musics/songs                      // create a subdirectory inside Music//

rmdir -p Musics/songs                    //deletes an empty subdirectory//

rm text1.txt                            // to delete single file//
rm text22.txt text3.txt                 // to delete multiple files// 
```

![Alt text](<Images/Screenshot 7.png>)


8. ### **touch, locate, find, grep**:
Touch command is used to create an empty file or generate and modify a timestamp in the Linux command line.

Locate command is used to find a file in the database system.

Find command is used to seaech for files within a specific directory and perform subsequent operations. Here is a general syntax     find [option] [path] [expression]

Grep command is short form for global expression print used to find a word by searching through all texts in a file.


```console
touch location                       //to create a file//
locate -i school                     //add i to remove case sentivity//
find /home -name text1.txt           //locate a file named test.txt in directory//
grep values New_folder_1            // to search for New_folder_1 in notepad//
```

![Alt text](<Images/Screenshot 8.png>)


9. ### **df, du, head, tail, diff, tar**:
Df is used to report disk usage.

Du is used to check mow much space a file or directory is taking up.

Head is used to view first ten lines of a text or file.

Tail is used to see last ten lines of text or file.

Diff is used to look for difference.

Tar is used to archive files into a TAR dile(similar to ZIP).


```console
df -h                              // see current directory system disk usage//
du /home/vboxuser/DevOps_folder    //check file and directory space usage//
head text2.txt                     // check first ten lines of text2.txt file//
tail -n text1.txt                  // check first ten lines of text2.txt file//
diff text1.txt text2.txt           //check difference between text2.txt and text3.txt//
tar -cvf newarchive.tar /home/vboxuser   //create a new TAR name in /home/vboxuser//
```
![Alt text](<Images/Screenshot 9.png>)




## **File Permission and Ownership**

1. ### **chmod, chown, uname, echo**:
Chmod is used to change the permissions of a file or directory. In linux, we have three classes Owner, group member and User.

Chown is used to change owner of file or directory 

Uname is used to print information about a linux system and hardware.

Echo is used to display a line of text.

```console
chmod 777 text1.txt         // allow everyone to read/write and execute on text1.txt//
chown vboxuser text2.txt    // making vboxuser the owner of text2.txt//
uname -a                    //prints all system information//
echo "Hello World"          //display text//
```
![Alt text](<Images/Screenshot 10.png>)


2. ### **jobs, kill, ping, wget,ps**:
Jobs is used to display all running process along with their statuses. This command is only available in bash, csh, ksh and tcsh shells.

Kill is used to terminate an unresponsive program manually. You need to know the PID of the process.

Ping is used to check if a network is reachable.

wget is used to download files from internet.

Ps produces the snapshot of all running processes.


```console
jobs -l                                 //list jobs with thier Job Ids//
ps ux                                   //to get the process ID(PID)//
kill SIGKILL pid          // to kill the process, SIGKILL forces the program to stop//
ping google.com                         //ping google.com site//
wget https://wordpress.org/latest.zip   // download latest version of WordPress//
```

![Alt text](<Images/Screenshot 11.png>)
![Alt text](<Images/Screenshot 11_1.png>)



3. ### **top, history, man, zip/unzip**:
Top is used to display all running processes.

History is used to list up to 500 previously executed commands allowing you to reuse them.

Man is used to provide user manual of any command ran on the terminal including name, description and options.

Zip command is used to compress a file into a ZIP file and unzip is used to extract the ZIPPED file.


```console
top
history
man ls
man 2 ls                        //if you want to see session 2 of the ls command//
zip archive.zip text2.txt
unzip archive.zip
```
![Alt text](<Images/Screenshot 12.png>)
![Alt text](<Images/Screenshot 12_2.png>)


4. ### **hostname, useradd, userdelete**:
Hostname is used to know the system's host name.

Useradd is used to add a new user/account, then use passwd command to add a password.

Userdel is used to delete a user.


```console
hostname -i                            //displays the ip//
sudo useradd Williams
sudo passwd the_password_combination
userdel John
```
![Alt text](<Images/Screenshot 13.png>)


5. ### **apt-get, nano, vi**:
Apt-get is used to retrieve information from authenticated sources to manage, update, remove and install software and its dependencies.

Nano/vi/ jed is used to edit and manage files via text editor.


```console
sudo apt-get upgrade
nano text2.txt
```
![Alt text](<Images/Screenshot 14.png>)
![Alt text](<Images/Screenshot 14_2.png>)


6. ### **su, htop**: 
Su is used to switch User

Htop is used to monitor system resources and processes in real time



```console
su Maurvill
htop -d
```

![Alt text](<Images/Screenshot 15.png>)
![Alt text](<Images/Screenshot 15_2.png>)


Thank you.