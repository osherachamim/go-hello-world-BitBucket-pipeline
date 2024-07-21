# Go Hello World WebApp with Bitbucket Pipeline and GitHub Integration

## Project Overview
This project demonstrates how to set up a simple Go web application, integrate Bitbucket Pipelines for CI/CD, and push changes to a GitHub repository. It serves as a learning project to showcase the process of continuous integration and deployment using Bitbucket and GitHub.

## Prerequisites
- Bitbucket account
- GitHub account
- Basic knowledge of Git and Go

## Steps

### 1. Create a Bitbucket Project and Repository

1. **Create a Bitbucket Project:**
   - Log in to your Bitbucket account.
   - Click on **Projects** in the sidebar.
   - Click **Create project** and provide a name and key for the project.

2. **Create a Bitbucket Repository:**
   - Within the newly created project, click **Create repository**.
   - Provide a repository name (e.g., `go-hello-world-pipeline`).
   - Choose the project created in the previous step.
   - Click **Create repository**.

3. **Create a `main` Branch:**
   - Clone the repository to your local machine.
   - Create a `main` branch if it does not exist:
     ```sh
     git checkout -b main
     git push -u origin main
     ```

### 2. Create a GitHub Repository

1. **Create a GitHub Repository:**
   - Log in to your GitHub account.
   - Click on the **+** icon in the top-right corner and select **New repository**.
   - Provide a repository name (e.g., `go-hello-world-pipeline`).
   - Set the repository to public or private as needed.
   - Click **Create repository**.

### 3. Setup Go Hello World WebApp

1. **Create a Go WebApp:**
   - Create a file named `hello-world.go` in your local repository with the following content:
     ```go
     package main

     import (
         "fmt"
         "net/http"
     )

     func helloWorld(w http.ResponseWriter, r *http.Request) {
         fmt.Fprintf(w, "Hello, World!")
     }

     func main() {
         http.HandleFunc("/", helloWorld)
         http.ListenAndServe(":8080", nil)
     }
     ```

### 4. Configure Bitbucket Pipeline

1. **Create a Bitbucket Pipeline Configuration File:**
   - In your local repository, create a file named `bitbucket-pipelines.yml` with the following content:
     ```yaml
     image: golang:1.15

     pipelines:
       default:
         - parallel:
             - step:
                 name: &build-app
                 script:
                   # Build compiles the packages
                   - go build hello-world.go
             - step:
                 name: Lint code
                 image: golangci/golangci-lint:v1.31.0
                 script:
                   - golangci-lint run -v
     branches:
       main:
         # Use * for alias
         - step:
             name: Deploy to Server
             script:
               - go build hello-world.go
               - apt-get update && apt-get install -y git
               - git config --global user.email "osher@bitbucket.com"
               - git config --global user.name "Osher"
               - git init
               - if git remote | grep -q 'origin'; then git remote set-url origin https://${USERNAME}:${GITHUB_TOKEN}@github.com/${USERNAME}/${REPO}.git; else git remote add origin https://${USERNAME}:${GITHUB_TOKEN}@github.com/${USERNAME}/${REPO}.git; fi
               - git fetch origin
               - git merge -X theirs origin/main
               - git add .
               - git commit -m "Automated commit from Bitbucket pipeline"
               - git push -u origin main
     ```

### 5. Store Variables Securely in Bitbucket

1. **Add Repository Variables:**
   - In Bitbucket, navigate to **Repository settings** > **Repository variables**.
   - Add the following variables:
     - `USERNAME`: Your GitHub username (e.g., `osherachamim`).
     - `REPO`: The name of your GitHub repository (e.g., `go-hello-world-pipeline`).
     - `GITHUB_TOKEN`: Your GitHub personal access token. 

### 6. Commit and Push Changes

1. **Commit and Push Changes:**
   - Commit your changes and push to the `main` branch in Bitbucket:
     ```sh
     git add .
     git commit -m "Initial commit"
     git push origin main
     ```

### 7. Verify the Pipeline

1. **Run the Pipeline:**
   - The pipeline will automatically run on the `main` branch.
   - Verify the build, lint, and deployment steps complete successfully.

### Best Practices

1. **Security:**
   - Use Bitbucket repository variables to store sensitive information like the GitHub personal access token.
   - Ensure that the personal access token has the minimum necessary permissions.

2. **Code Quality:**
   - Include steps for linting and testing in your pipeline to maintain code quality.

## Conclusion

This project demonstrates setting up a Go web application with a CI/CD pipeline using Bitbucket and pushing changes to GitHub. It showcases best practices for secure variable storage and continuous integration workflows.

Feel free to explore and modify the project to suit your learning needs!

## License
This project is licensed under the MIT License.

---

Happy coding!
