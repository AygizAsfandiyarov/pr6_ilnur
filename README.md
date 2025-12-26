# Отчёт по работе с SOPS и Age на Windows

## Цель работы

Целью данной работы является изучение и практическое освоение инструмента **SOPS (Secrets OPerationS)** для безопасного хранения конфиденциальных данных, а также системы шифрования **Age**

## Ход работы:
### Установка утилит:

Для выполнения работы были установлены утилиты **age** и **sops**:
```bash
sudo apt-get update
sudo apt-get install -y age

curl -LO https://github.com/getsops/sops/releases/download/v3.8.1/sops-v3.8.1.linux.amd64
sudo mv sops-v3.8.1.linux.amd64 /usr/local/bin/sops
chmod +x /usr/local/bin/sops
```

Корректность установки проверялась командами:
```
age --version
sops --version
```
<img width="604" height="86" alt="image" src="https://github.com/user-attachments/assets/1230415f-2f89-4b26-a190-5d33471a63f7" />
### Создание рабочей директории
```bash
$ mkdir -p ~/sops-production-demo
$ cd ~/sops-production-demo

$ mkdir -p public_keys secrets

$ git init
Инициализирован пустой репозиторий Git в /home/user/sops-production-demo/.git/
```

<img width="974" height="431" alt="image" src="https://github.com/user-attachments/assets/d5b8b295-a844-4df4-bbde-8fb03d86275d" />

### Генерация ключей Age:
Для шифрования данных был сгенерирован ключ Age с помощью команды:

```
age-keygen -o keys.txt
```
<img width="974" height="224" alt="image" src="https://github.com/user-attachments/assets/786ca089-d0c8-4504-8753-daed83214d15" />

В результате был создан файл `keys.txt`, содержащий приватный ключ, а также выведен публичный ключ, который используется для шифрования данных.

### Настройка конфигурации SOPS: 

В корне проекта был создан файл конфигурации `.sops.yaml`, содержащий правила шифрования:

```
creation_rules:
  - path_regex: 'secrets\\.*\.yaml$'
    age: 'age1...'
```
<img width="974" height="557" alt="image" src="https://github.com/user-attachments/assets/bdf42b0b-a89f-46b1-a9a1-8f11ddd8608b" />

### Создание .gitignore

<img width="974" height="729" alt="image" src="https://github.com/user-attachments/assets/a54bdd09-7ee7-4994-8339-2a21d478ed05" />

### Создание незашифрованного файла с секретами:

Был создан файл `secrets/database/my_secrets.yaml`.

<img width="974" height="267" alt="image" src="https://github.com/user-attachments/assets/1af7c2e2-67e2-4d3f-81d7-6b8b875b66d7" />

Шифрование выполнялось командой:
```
sops --encrypt secrets/database/my_secrets.yaml > secrets/database/my_secrets.enc.yaml
```
<img width="974" height="310" alt="image" src="https://github.com/user-attachments/assets/f39cb73a-805a-4434-8dd0-01a5d858c3b0" />

В результате был получен зашифрованный файл `my_secrets.enc.yaml`, содержащий зашифрованные значения и служебный блок `sops`.

### Просмотр зашифрованного файла:

Просмотр зашифрованного файла выполнялся командой: 
```bash
$ head -30 secrets/database/postgres.enc.yaml
```

<img width="974" height="622" alt="image" src="https://github.com/user-attachments/assets/f145d787-07fb-426e-bc88-5028f4e1c4d9" />

### Расшифровка данных

Для расшифровки файла была предварительно задана переменная окружения, указывающая путь к приватному ключу Age:

```
$env:SOPS_AGE_KEY_FILE = "$PWD\keys.txt"
```

После этого расшифровка выполнялась командой:

```
sops --decrypt secrets/database/my_secrets.enc.yaml
```
<img width="974" height="149" alt="image" src="https://github.com/user-attachments/assets/924f1365-f8dc-4afc-a356-dbf8c505c5cf" />

Данные были успешно восстановлены в исходном виде.

### Редактирование зашифрованных файлов

<img width="738" height="223" alt="image" src="https://github.com/user-attachments/assets/5d025d21-6d23-4e56-a3da-019e33362567" />

### Вывод

В ходе выполнения работы был успешно освоен инструмент SOPS совместно с Age для безопасного хранения конфиденциальных данных.
