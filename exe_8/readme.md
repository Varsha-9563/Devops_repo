# Exercise 8: Jenkins Freestyle Project with GitHub

## Objective

Create a simple shell script, push it to a public GitHub repository, and configure a Jenkins Freestyle project to fetch the repository and execute the script.

## Tools Used

- Git and GitHub
- Jenkins
- Docker
- Bash shell scripting
- Windows PowerShell

## 1. Create the Shell Script

Created a file named `hello-world.sh` with the following content:

```bash
#!/bin/bash
echo "Hello, Jenkins!"
```

## 2. Push the Code to GitHub

Created a public GitHub repository named **devops-sample-code**.

Repository: https://github.com/Varsha-9563/devops-sample-code

Initialized the local Git repository and pushed the script to the `main` branch.

```bash
git init
git branch -M main
git add hello-world.sh
git commit -m "Add Hello World script"
git remote add origin https://github.com/Varsha-9563/devops-sample-code.git
git push -u origin main
```

## 3. Configure the Jenkins Freestyle Project

Created a Freestyle project named `HelloWorld` in Jenkins.

### Source Code Management

- Selected **Git**.
- Configured the repository URL:

  `https://github.com/Varsha-9563/devops-sample-code.git`

- Set the branch specifier to `*/main`.
- Left credentials unset because the repository is public.

### Build Step

Selected **Execute shell** under Build Steps and added:

```bash
sh hello-world.sh
```

Saved the configuration.

## 4. Execute the Jenkins Build

Triggered the job using **Build Now** and inspected the build's Console Output.

Expected output:

```text
Hello, Jenkins!
Finished: SUCCESS
```

The build completed successfully, confirming that Jenkins could clone the GitHub repository, check out the `main` branch, and execute the shell script.

## 5. Result

Successfully integrated a public GitHub repository with a Jenkins Freestyle project and executed a shell script through a Jenkins build.

## Concepts Learned

- Git repository initialization and remote configuration
- Committing and pushing code to GitHub
- Jenkins Freestyle project configuration
- Source Code Management (SCM) using Git
- Executing shell scripts in Jenkins
- Monitoring builds through Console Output
