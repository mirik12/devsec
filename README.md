Git Repository Secret Detection Program

This program allows you to detect secret information and sensitive data in a Git repository using the Gitleaks tool.

Installation

Before using this program, make sure you have Python 3 installed. You should also have Gitleaks installed and properly configured on your system.

Clone the repository to your local machine:

bashCopy code

git clone <repository URL>

Navigate to the program directory:

bashCopy code

cd <directory name>

Install the necessary dependencies:

Copy code

pip install -r requirements.txt

Usage

This program is used as a Git pre-commit hook, which automatically checks for the presence of secret information before committing.

Open the .git/hooks/pre-commit file in your repository.

Copy the contents of the pre-commit file from this repository to your .git/hooks/pre-commit file. Make sure the .git/hooks/pre-commit file has executable permissions (chmod +x .git/hooks/pre-commit).

Make changes to your project.

Commit using the git commit command. The pre-commit hook will trigger the secret detection program.

If the program detects secrets or sensitive data in your code, the commit will be rejected, and a message with the detection results will be displayed.

Configuration

You can configure the program’s parameters in the .git/hooks/pre-commit file:

gitleaks_version: The Gitleaks version you want to use. Set the appropriate version URL in the download_url line in the install_gitleaks function.

gitleaks_enable: Enable or disable Gitleaks checks. Set true or false in the git configuration hooks.gitleaks-enable.

Benefits

This program helps identify secrets and sensitive data in your code before committing, providing increased security for your project. Running Gitleaks before committing helps detect potential security issues and protect your data.

License

This program is distributed under the MIT License.
