<!-- Було реалізовано два варіанти (другий варіант увійшов у реліз)
перший варіант ми створюємо файл pre-commit в .git/hooks

має вигляд:

"#!/bin/sh

# Check if the gitleaks hook is enabled
hook_enabled=$(git config --bool hooks.gitleaks)

if [ "$hook_enabled" != "true" ]; then
    # The hook is not enabled, so just exit
    exit 0
fi

# Check if gitleaks is installed
if ! command -v gitleaks &> /dev/null
then
    echo "gitleaks could not be found, installing..."
    # Install gitleaks using "curl pipe sh", regardless of the OS
    curl -s https://raw.githubusercontent.com/gitleaks/gitleaks/master/install.sh | sh
fi

# Get the changes made
git_diff=$(git diff --cached --name-only)

# Run gitleaks on the changes made
for file in $git_diff
do
    # Check if the file exists before running gitleaks
    if [ -f "$file" ]; then
        gitleaks_result=$(gitleaks --config=gitleaks.toml --path="$PWD/$file" --no-git --quiet)

        if [ "$gitleaks_result" != "" ]; then
            echo "Potential secrets detected in $file!"
            echo "$gitleaks_result"
            exit 1
        fi
    fi
done
"

Спочатку, скрипт перевіряє, чи гук gitleaks увімкнений. якщо ні команда : <git config --local hooks.gitleaks true> Він перевіряє значення параметру hooks.gitleaks у конфігурації Git, щоб визначити, чи гук gitleaks повинен бути ввімкнений. 

Якщо гук gitleaks увімкнений, скрипт перевіряє, чи встановлений gitleaks на вашій системіб якщо  не встановлений, скрипт автоматично завантажує та встановлює gitleaks за допомогою методу "curl pipe sh"

Перевіряється, чи гук gitleaks ввімкнений в конфігурації Git.

Зробіть цей файл виконуваним. Це можна зробити використовуючи команду chmod +x .git/hooks/pre-commit.

Щоб включити або виключити цей хук, ви можете використовувати наступні команди git config:

Для включення: git config --local hooks.gitleaks true
Для виключення: git config --local hooks.gitleaks false


-------------------------------

друга реалізація на python (ходить перевіряє всі файли)

#!/usr/bin/env python3
import subprocess
import sys

def install_gitleaks():
    try:
        subprocess.run(['curl', '-s', 'https://raw.githubusercontent.com/zricethezav/gitleaks/master/install.sh'], check=True, text=True)
        subprocess.run(['chmod', '+x', 'install.sh'], check=True)
        subprocess.run(['./install.sh'], check=True)
    except subprocess.CalledProcessError:
        print("Error: Failed to install gitleaks.")
        sys.exit(1)

def run_gitleaks():
    try:
        subprocess.run(['gitleaks', '--path', '.', '--quiet'], check=True)
    except subprocess.CalledProcessError:
        print("Error: Secrets detected in the code. Commit rejected.")
        sys.exit(1)

def main():
    install_gitleaks()
    run_gitleaks()

if __name__ == '__main__':
    main()

Цей скрипт встановлює gitleaks, якщо він ще не встановлений на системі, використовуючи команди curl і chmod. Потім він запускає gitleaks для перевірки наявності секретів у всьому коді у поточній директорії. Якщо gitleaks виявляє потенційні секрети, виводиться повідомлення про помилку, і коміт відхиляється.

Для використання цього скрипта як pre-commit гука в Git, вам потрібно:

Зберегти цей скрипт у файл з назвою pre-commit (без розширення) в директорії .git/hooks вашого репозиторію.

Встановити виконуваний дозвіл на цей файл, виконавши команду chmod +x .git/hooks/pre-commit.

Виконати команду git config --bool hooks.gitleaks true, щоб увімкнути gitleaks гук.
ЩОб вимкнути git config --bool --unset hooks.gitleaks -->

я ж використав скрипт 

<!-- #!/usr/bin/env python3 -->
<!-- import subprocess
import sys

def run_gitleaks():
    try:
        subprocess.run(['gitleaks', '--path', '.'], check=True)
    except subprocess.CalledProcessError:
        print("Error: Secrets detected in the code. Commit rejected.")
        sys.exit(1)

def main():
    run_gitleaks()

if __name__ == '__main__':
    main() -->


<!-- я реалізував третій варіант

#!/usr/bin/env python3
import subprocess
import sys

def run_gitleaks():
    try:
        subprocess.run(['gitleaks', '--path', '.'], check=True)
    except subprocess.CalledProcessError:
        print("Error: Secrets detected in the code. Commit rejected.")
        sys.exit(1)

def main():
    run_gitleaks()

if __name__ == '__main__':
    main() -->
