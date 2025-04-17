import os
import subprocess
import sys
from datetime import datetime
from pathlib import Path
from typing import List, Dict
from rich.console import Console
from rich.table import Table
from rich.panel import Panel
from rich.progress import Progress, BarColumn, TextColumn, TimeElapsedColumn

# 🔧 Automatically install required dependencies
def install_dependencies():
    required = ['rich', 'gitpython']
    for package in required:
        try:
            __import__(package)
        except ImportError:
            subprocess.check_call([sys.executable, '-m', 'pip', 'install', package])

install_dependencies()

from git import Repo, InvalidGitRepositoryError

console = Console()


class GitProject:
    def __init__(self, path: Path):
        self.path = path
        self.name = path.name
        self.repo = None
        self.status = {}
        self.progress = 0
        self.readme_score = 0

        try:
            self.repo = Repo(path)
            if self.repo.bare:
                raise InvalidGitRepositoryError
            self.gather_info()
        except InvalidGitRepositoryError:
            pass

    def gather_info(self):
        if not self.repo.head.is_valid():
            self.status['active_branch'] = "-"
            self.status['is_dirty'] = False
            self.status['last_commit'] = None
            self.status['commits_behind'] = 0
            self.status['commits_ahead'] = 0
            return

        self.status['active_branch'] = str(self.repo.active_branch)
        self.status['is_dirty'] = self.repo.is_dirty()
        self.status['last_commit'] = datetime.fromtimestamp(self.repo.head.commit.committed_date)

        try:
            commits_behind = sum(1 for c in self.repo.iter_commits(f"{self.repo.active_branch}..origin/{self.repo.active_branch}"))
            commits_ahead = sum(1 for c in self.repo.iter_commits(f"origin/{self.repo.active_branch}..{self.repo.active_branch}"))
        except:
            commits_behind = commits_ahead = 0

        self.status['commits_behind'] = commits_behind
        self.status['commits_ahead'] = commits_ahead

        self.readme_score = self.analyze_readme()
        self.progress = self.calculate_progress()

    def analyze_readme(self):
        readme_path = self.path / "README.md"
        if not readme_path.exists():
            return 0

        with open(readme_path, 'r', encoding='utf-8') as f:
            content = f.read()

        length = len(content.split())
        return min(100, length // 10)

    def calculate_progress(self):
        days_since_first_commit = 1
        days_since_last_commit = 1

        try:
            commits = list(self.repo.iter_commits())
            if commits:
                first_commit = commits[-1]
                days_since_first_commit = max((datetime.now() - datetime.fromtimestamp(first_commit.committed_date)).days, 1)

                last_commit = commits[0]
                days_since_last_commit = (datetime.now() - datetime.fromtimestamp(last_commit.committed_date)).days
        except:
            pass

        age_factor = min(100, days_since_last_commit * 2)
        return (self.readme_score * 0.5 + age_factor * 0.5)


class GitScope:
    def __init__(self, base_dir: str):
        self.base_dir = Path(base_dir)
        self.projects: List[GitProject] = []

    def scan_projects(self):
        self.projects.clear()  # Clear previous projects before scanning
        for path in self.base_dir.rglob(".git"):
            project_dir = path.parent
            project = GitProject(project_dir)
            if project.repo:
                self.projects.append(project)

    def display_dashboard(self):
        if not self.projects:
            console.print("[bold red]No Git projects found in the specified directory.[/bold red]")
            return

        table = Table(title="[bold cyan]GitScope Dashboard · by H0lwin")
        table.add_column("Project", style="bold green")
        table.add_column("Branch", style="cyan")
        table.add_column("Last Commit", style="white")
        table.add_column("Ahead/Behind", justify="center")
        table.add_column("Dirty", justify="center")
        table.add_column("Progress", justify="right")

        for project in self.projects:
            last_commit = project.status['last_commit'].strftime('%Y-%m-%d') if project.status['last_commit'] else "—"
            table.add_row(
                project.name,
                project.status['active_branch'],
                last_commit,
                f"+{project.status['commits_ahead']} / -{project.status['commits_behind']}",
                "✅" if not project.status['is_dirty'] else "⚠️",
                f"{int(project.progress)}%"
            )

        console.print(table)

    def display_help(self):
        help_text = """
GitScope - A Terminal Dashboard for Monitoring Git Projects

Usage:
  python gitscope_dashboard.py [--path <directory>] [--menu]

Options:
  -p, --path        Specify the base directory to scan (default: current directory)
  -m, --menu        Launch the interactive menu
  --helpme          Show this help message

Features:
  - Automatically detects all Git repositories under a directory
  - Shows current branch, last commit date, commit differences
  - Detects working directory cleanliness (dirty/clean)
  - Estimates project progress based on README and commit age
  - Rich terminal UI — works great on Arch, Debian, or any Unix-based OS
  - Self-installs dependencies (no need to manually install pip packages)

Made with ❤ by H0lwin
        """
        console.print(Panel(help_text.strip(), title="[bold green]Help[/bold green]", border_style="green"))

    def interactive_menu(self):
        while True:
            console.print("\n[bold magenta]GitScope Interactive Menu[/bold magenta]", style="bold")
            console.print("[1] Scan and Display Dashboard")
            console.print("[2] Help")
            console.print("[3] Exit")
            choice = console.input("\nChoose an option [1-3]: ").strip()

            if choice == '1':
                console.print("[yellow]Scanning for Git repositories...[/yellow]")
                self.scan_projects()
                console.print("[green]Done. Displaying dashboard.[/green]\n")
                self.display_dashboard()
            elif choice == '2':
                self.display_help()
            elif choice == '3':
                console.print("[bold red]Exiting GitScope. See you![/bold red]")
                break
            else:
                console.print("[red]Invalid option. Please enter 1, 2, or 3.[/red]")


if __name__ == '__main__':
    import argparse
    parser = argparse.ArgumentParser(description="GitScope - Git Project Status Dashboard")
    parser.add_argument('--path', '-p', default='.', help="Base directory to scan for Git projects")
    parser.add_argument('--menu', '-m', action='store_true', help="Open interactive menu")
    parser.add_argument('--helpme', action='store_true', help="Show help")
    args = parser.parse_args()

    scope = GitScope(args.path)

    if args.helpme:
        scope.display_help()
    elif args.menu:
        scope.interactive_menu()
    else:
        scope.interactive_menu()  # Start with menu if no other argument is provided
