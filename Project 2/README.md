# **Initializing a Repository and Making Commits**

## **What is Git**
Git is a distributed version control system. It solves the problem of sharing source code efficiently and keeping track of changes made to the code.
Before Git, there was SVN which had a central repository and chnages are made one at a time. This gives Git an edge as it allows developers to make their own copy of central repository making it a distributed version control system.

## **Initializing Git repository**
Before initializing, you must first installthe Git termibal on your computer via this link https://git-scm.com/downloads
Follow the below steps to initialize git Repository;
1. Create a working directory/folder example **DevOps_folder** using the ```mkdir command```
2. Change or move the directory using the ```cd DevOps_folder``` command
3. Whilst inside the folder, run ```git init``` to initialize.

![Alt text](<Images/Screenshot 1.png>)


## **Making your First Commit**
Commit is more or less saving the changes made to your files. It can be adding, modifying or delete files or text. When a commit is made, Git takes a snapshot of the current state of your repository and saves a copy in teh git folder in your working directory.

Follow the below steps to make commits;

1. Create a file **index.txt** using ```touch index.txt``` 
2. Write sentences in the text file and save changes.
3. Add your changes to git staging using ```git add .```
4. Commit changes to git using ```git commit -m "initial commit"```

![Alt text](<Images/Screenshot 2.png>)


## **Working with Branches**
Git branch helps you create a different copy of your source code. On the new branch, you can make  changes as you please as the change is independent of what is available on the main branch. It is also an improtant tool for collaboration and after changes are made, you can merge back to the main branch.

Follow the below steps to create branch and further merge the branches;

1. Make a new branch by running the command ```git checkout -b My_new_Branch```
2. list the branches worked on using ```git branch```
3. Change into an old branch running ```git checkout <branch name>```
4. Merge branch running ```git merge <branch>```
5. Delete branch running ```git branch -d <branch name>```

![Alt text](<Images/Screenshot 3.png>)


## **Collaboration and Remote Repositories**
GitHub is a web based platform where git repositories are hosted. This way, it becomes available in the public internet(it is possible to create private repository and limit who has access to the repo). Remote Teams can now view, update and make changes in same repository.

Follow the below steps to create your repository;
1. First create a gitHub account via this link https://github.com/
2. Enter your email, password, username.
3. Click on Verify button to verify your identity
4. Click on create and enter the activation code sent to your email on the textboxes.
5. Select number of Users and github plans and click on continue for free.
6. Click on the plus sign at the top right corner of your github account to create a new Repository, then fill out the forms and add the README.md (tick the box) and click on create.
7. Add a remote repository to your local repository running ```git remote add origin <link to github repo>```
8. After making changes, you can commit back to the Github repository running ```git push .```
9. You can clone remote Git repo running ```git clone <link to remote repo>```

![Alt text](<Images/Screenshot 4.png>)
![Alt text](<Images/Screenshot 5.png>)
![Alt text](<Images/Screenshot 6_1.png>)
![Alt text](<Images/Screenshot 6.png>)
![Alt text](<Images/Screenshot 7.png>)
![Alt text](<Images/Screenshot 9.png>)
![Alt text](<Images/Screenshot 10.png>)

## **Branch Management and Tagging**

### **Introduction to Markdown Syntax**
Markdown syntax is used for formatting plain text. It allows you to add formatting elements to your texts without using complex HTML or orther formatting languages. It is commonly used for creating documents, README.md files etc.
Examples:
1. **Headings**: to create heading, the number of hash symbol determines the level of heading.
```console
# Heading 1 
## Heading 2 
### Heading 3
```
Output:
# Heading 1
## Heading 2
### Heading 3


2. **Emphasis**
```console
*italic* or _italic_
**bold** or __bold__
```
Output: 

*italic* or _italic_
**bold** or __bold__

3. Lists: support ordered and unordered lists.
```console
- Item 1
- Item 2
- Item 3
1. First item
2. Second item
3. Third item


```
Output:
- Item 1
- Item 2
- Item 3
1. First item
2. Second item
3. Third item



4. Links: to create hyperlinks, use square brackets.
```console
[visit darey.io](https://www.darey.io)
```
Output:
[visit darey.io](https://www.darey.io)


5. Images: use exclamation mark followed by brackets for alt text and parentheses containing the image URL.
```console
![Alt Text](https://example.com/image.jpg)
```

Output:
![Alt Text](https://example.com/image.jpg)

6. Code: use backticks
```console
`console.log('Welcome to darey.io')`
```
Output:
`console.log('Welcome to darey.io')`


Here is a link for more insight on https://learn.microsoft.com/en-us/contribute/content/markdown-reference 

Thank you.

















