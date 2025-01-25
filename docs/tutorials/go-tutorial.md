# Setting up a dev container for Go

* Primary author: [Maxim Chadaev](https://github.com/maximdolphin)  
* Reviewer: [Daniel Henderson](https://github.com/HendersonDaniel)

## Initial Setup

1. To create a project, run ```mkdir <name>``` and ```cd <project-name>``` for your specific project
2. Then, you need to intiialize a git repo with ```git init```
3. Add a readme file to make your first commit with ```bash
echo "# My Rust Project" > README.md
git add README.md
git commit -m "Initial commit with README"```

4. Then, to create a Remote Repository on Github, login to Github and go to the 'Create a New Repository Page`
5. Choose your settings and details, and choose the same name as your project and create the repository
6. Then, add the Github repository as a remote with ```git remote add origin https://github.com/<your-username>/<project-name>```. Make sure you replace `<your-username>` and `<project-name>` with your GitHub username and project name respectively.
7. Check the default branch with ```git branch``` and if it is not main, rename it with ```git branch -M main```.
8. Then push it with ```git push --set-upstream origin main```

## Set up Devcontainer

1. Create a directory `.devcontainer`.
2. Create a dev container configuration `.devcontainer/devcontainer.json`.
3. Make sure you have Docker installed. If you use Windows, make sure your project is on a partition shared with Docker.

### Dev Container configuration

```json
{
    "name": "Your project Dev",
    "image": "mcr.microsoft.com/devcontainers/go:latest",
    "extensions": [
        "golang.go",
    ],
    "settings": {
        "go.useLanguageServer": true
    },
    "postCreateCommand": "go mod download",
}
```

It also runs go mod download after the container is set up to download your Go dependencies when the container is ready.

## Using Go Commands

1. Verify the Go Installation ```go version```
2. Initialize a Go module ```go mod init my_project_name```
3. Make a main.go file in your folder and paste this code:
```go
package main

import "fmt"

func main() {
    fmt.Println("Hello COMP423!")
}
```

4. Run your Go code ```go run main.go``` or build a binary ```go build -o myapp main.go```
Unlike go run, go build generates a reusable binary that can be executed multiple times or shared with others without needing recompilation, similar to how gcc compiles source code into an executable binary.

### Launching it

Launch it by opening the VS Code command palette and selecting ```Remote-Containers: Open folder in container...``` and choose the current folder.

This will build the Docker image, install the VS Code dependencies in there, and bind mount everything for you!

Finally, you have a terminal running a beautiful zsh inside VS Code (open a terminal if you don’t see it).

### Pushing to GitHub
Now you can commit and push these changes to github. Stage your changes and commit them, with an appropriate message, before pushing to your remote repository.
```
git add .
git commit -m "Created Go project"
git push origin main
```
Now, you are done!

# References
[Citation 1 -> Medium Article](https://medium.com/@quentin.mcgaw/ultimate-go-dev-container-for-visual-studio-code-448f5e031911)
[Citation 2 -> Kris Jordan's MkDocs Tutorial](https://comp423-25s.github.io/resources/MkDocs/tutorial/)
[Citation 3 -> Daniel Henderson's Rust Tutorial](https://hendersondaniel.github.io/comp423-course-notes/tutorials/rust-setup/)
