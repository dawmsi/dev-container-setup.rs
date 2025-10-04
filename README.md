# 🛡️ Zero Trust Dev Environment для Rust на macOS (Apple Silicon)

Ця інструкція включає всі виправлення та обхідні шляхи, знайдені під час налаштування.

---

## 1. Встановлення Передумов (Apple Silicon Ready) 🐳

1. **Homebrew:** Переконайтеся, що Homebrew встановлено.
    
2. **Docker Desktop (Для Apple Silicon):** Встановіть, **запустіть** і **увійдіть** в додаток. **! ПЕРЕВІРКА:** Переконайтеся, що **Docker Desktop запущено** перед використанням `devpod up`.
    
    Bash
    
    ```
    brew install --cask docker
    ```
    
3. **VS Codium:** Встановіть ваш IDE.
    
    Bash
    
    ```
    brew install --cask vscodium
    ```
    
4. **DevPod CLI:** Встановіть основний інструмент керування середовищами.
    
    Bash
    
    ```
    brew install devpod
    ```
    

---

## 2. Налаштування DevPod та VSCodium

2.1. **Конфігурація Провайдера:**

Bash

```
devpod provider add docker
```

2.2. **Встановлення Розширення VSCodium:** У VSCodium встановіть розширення **`DevPod Containers`** від **3timeslazy** (з Open VSX). 2.3. **Вирішення Проблеми "No view is registered" (Критичний Крок!):**

- Відкрийте **Command Palette** (⇧⌘P), виберіть **`Preferences: Configure Runtime Arguments`**.
    
- Додайте ідентифікатор розширення до масиву `"enable-proposed-api"`:
    
    JSON
    
    ```
    "enable-proposed-api": ["3timeslazy.vscodium-devpodcontainers"]
    ```
    
- **Повністю завершіть роботу VSCodium (⌘Q)** і запустіть його знову.
    

---

## 3. Налаштування Zero Trust Конфігурації

### 3.1. Створення Папки та Конфігурації

1. Створіть кореневу папку проєкту (наприклад, `rust-zero-trust-app`).
    
2. Створіть підпапку: `mkdir .devcontainer`
    
3. Створіть файл **`.devcontainer/devcontainer.json`** з цим вмістом:
    

JSON

```
{
    "name": "Rust Zero Trust DevPod Minimal",
    "image": "mcr.microsoft.com/devcontainers/rust:latest",
    
    // ПРИНЦИП НАЙМЕНШИХ ПРИВІЛЕЇВ
    "remoteUser": "vscode", 
    
    // ВИМКНЕНО: Щоб уникнути помилок
    "features": {}, 
    "postCreateCommand": "", 
    
    "customizations": {
        "devpod": {
            "ide": "vscodium" 
        },
        "vscode": {
            "extensions": [
                "rust-lang.rust-analyzer",
                "tamasfe.even-better-toml",
                "serayuzgur.crates"
            ]
        }
    }
}
```

---

## 4. Запуск та Підключення 🚀

4.1. **Запуск Робочого Простору:** Перебуваючи в **кореневій директорії проєкту** (`rust-zero-trust-app`), виконайте:

Bash

```
devpod up . --recreate 
```

4.2. **Обхід Проблем із Підключенням:** Якщо браузер відкривається, підключіться вручну через **Command Palette** (⇧⌘P) → **`DevPod: Show Containers`** → Виберіть ваш простір.

---

## 5. Фіналізація та Ініціалізація Проєкту (Всередині Контейнера) 🎯

Після успішного підключення до контейнера через VSCodium, відкрийте **термінал всередині контейнера** і виконайте ці команди:

1. ### ІНІЦІАЛІЗАЦІЯ ПРОЄКТУ (Створення виконуваного проєкту)
    
    _Оскільки папка вже існує, використовуйте `cargo init` для створення `src/main.rs`._
    
    Bash
    
    ```
    cargo init .
    ```
    
2. ### ВСТАНОВЛЕННЯ ІНСТРУМЕНТІВ БЕЗПЕКИ ТА СИСТЕМНИХ ПАКЕТІВ
    
    Bash
    
    ```
    cargo install cargo-audit
    sudo apt-get update && sudo apt-get install -y build-essential
    ```
    
3. ### ВИПРАВЛЕННЯ АКТИВАЦІЇ ПЛАГІНІВ (Якщо не працює підсвічування)
    
    _Якщо ви не бачите підсвічування чи кнопок "Run", виконайте цей обхідний шлях:_
    
    - Перейдіть на вкладку **Extensions** (Розширення).
        
    - Знайдіть **`rust-lang.rust-analyzer`**.
        
    - Натисніть **Uninstall** (Видалити в Remote Space).
        
    - Негайно натисніть **Install in DevPod Space** (Встановити в DevPod Space).
        
    - Виконайте **`Developer: Reload Window`** (Перезавантажити вікно) через Command Palette.
        

---

## 6. Завершення Роботи (Економія Ресурсів)

**VSCodium НЕ зупиняє контейнер!** Видаліть контейнер та звільніть ресурси:

Bash

```
devpod delete <назва_простору>
# Наприклад: devpod delete rust-zero-trust-app
```