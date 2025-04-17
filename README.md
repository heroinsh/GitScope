# GitScope - Git Project Status Dashboard 🚀

**GitScope** is a powerful terminal dashboard that helps you monitor and manage your Git projects with style! 💻🎉 Whether you're a solo developer or part of a team, GitScope provides a real-time overview of your repositories' status, commit progress, and much more in a clean and vibrant UI. 🌈

## Features 🌟
- **Automatic Git Repository Detection**: Scans your directory for all Git repositories and pulls their data automatically.
- **Detailed Project Information**: Shows the current branch, last commit, commits ahead/behind, and whether the repository is clean or dirty. 🔄
- **Progress Tracking**: Estimates the project's progress based on the README file length and the last commit age. 📈
- **Rich Terminal UI**: Beautifully designed with **[rich]** for an engaging terminal experience (works well on Linux-based systems like Arch and Debian). 🎨
- **Self-Installing Dependencies**: GitScope will install the required dependencies automatically — no manual setup needed! 📦

## Installation 🔧

Simply clone the repository and run the script. GitScope will handle installing any missing dependencies for you. 🤖

### Clone the Repo:
```bash
git clone https://github.com/yourusername/GitScope.git
cd GitScope
python gitscope.py
```
Install Dependencies:
GitScope automatically installs any missing dependencies. Just make sure you have pip installed, and it will handle the rest! 🎯

Usage 🎮
You can run GitScope in multiple ways:

Scan and display the dashboard:

```
python gitscope.py
```
Specify a directory to scan:


```python gitscope.py --path <your-directory>```
Interactive Menu: Start the interactive menu for easy navigation:


```python gitscope.py --menu```
Help: Show detailed instructions and options:


```python gitscope.py --helpme```
Interactive Menu 🧑‍💻
GitScope features an interactive terminal menu to easily scan for Git projects and display the status of each repository.

Scan and Display Dashboard: Scans the directory for Git projects and shows the status of each project in a neat table.

Help: Displays a guide on how to use the tool.

Exit: Exits the application.

Contributing 🤝
Feel free to fork, improve, or create pull requests! I'm open to suggestions and contributions. 🛠️

License 📜
This project is licensed under the MIT License - see the LICENSE file for details. 📝

Made with ❤ by H0lwin 💫
GitScope - Stay on top of your Git projects with style! 🚀
