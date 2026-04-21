# Модуль Linux

В этой папке хранится практика по Linux
для проекта DevOps Production Platform.

## Темы модуля Linux

- Пользователи
- Группы
- Права доступа
- sudo
- Процессы
- Сервисы
- Логи

---

## Практика: Работа с пользователями Linux

В этом разделе я учусь создавать пользователей
и управлять их правами.

---

### Создание пользователя devops

Команда:

sudo adduser devops

Что делает:

Создаёт нового пользователя с именем devops.

Автоматически создаётся:

- домашняя папка:
  
  /home/devops

- группа devops

---

### Добавление пользователя devops в группу sudo

Команда:

sudo usermod -aG sudo devops

Что делает:

Добавляет пользователя devops в группу sudo.

Это даёт ему права администратора.

Теперь он может выполнять системные команды:

sudo apt update  
sudo apt install nginx  

---

### Проверка групп пользователя

Команда:

groups devops

Что делает:

Показывает, в каких группах состоит пользователь.

Важно увидеть:

sudo

Это значит, что пользователь имеет права администратора.

---

### Переключение на пользователя devops

Команда:

su - devops

Что делает:

Переключает текущего пользователя на devops.

После этого команды выполняются от имени devops.

---

### Проверка текущего пользователя

Команда:

whoami

Что делает:

Показывает имя текущего пользователя.

Например:

devops  
или  
redman  

---

### Проверка прав администратора

Команда:

sudo whoami

Что делает:

Проверяет, работает ли sudo.

Если всё правильно, вывод будет:

root

Это означает, что права администратора работают.

---

### Создание пользователей appuser и testuser

Команды:

sudo adduser appuser
sudo adduser testuser

Если домашние папки не создались автоматически:

sudo mkdir /home/appuser
sudo mkdir /home/testuser

Назначение владельцев папок:

sudo chown appuser:appuser /home/appuser
sudo chown testuser:testuser /home/testuser

Что делает:

Создаёт пользователей appuser и testuser.

Создаются домашние папки:

/home/appuser
/home/testuser

Назначается владелец папки —
сам пользователь.

## Работа с группами и общей директорией (shared)

### Создание группы developers

Создаём группу для пользователей, которые будут работать вместе:

sudo groupadd developers

Проверка:

cat /etc/group | grep developers


---

### Добавление пользователей в группу developers

Добавляем пользователей appuser и testuser в группу developers:

sudo usermod -aG developers appuser
sudo usermod -aG developers testuser

Проверка:

groups appuser
groups testuser


---

### Создание общей директории

Создаём папку shared:

sudo mkdir /home/shared

Назначаем владельцем группу developers:

sudo chown :developers /home/shared

Выдаём права:

sudo chmod 770 /home/shared

Что означает 770:

Владелец → полный доступ  
Группа → полный доступ  
Остальные → нет доступа


---

### Проверка доступа пользователей

Переключаемся на пользователя:

su - appuser

Переходим в директорию:

cd /home/shared

Создаём файл:

touch app_file.txt

Проверяем:

ls -l


---

### Что делает setgid

По умолчанию новые файлы получают группу пользователя.

Например:

appuser создаёт файл → группа файла = appuser

Это неудобно для общей работы.

Чтобы новые файлы автоматически получали группу директории,
используется setgid.


---

### Включение setgid

Команда:

sudo chmod g+s /home/shared

Проверка:

ls -ld /home/shared

Результат:

drwxrws---

Буква "s" означает, что включён setgid.


---

### Проверка работы setgid

Создаём новый файл:

su - appuser

cd /home/shared

touch after_setgid.txt

Проверяем:

ls -l

Теперь файл должен иметь группу:

developers

Пример:

-rw-r--r-- 1 appuser developers after_setgid.txt


---

### Итог

После настройки:

Все пользователи группы developers могут:

- заходить в /home/shared
- создавать файлы
- видеть файлы друг друга
- работать с общими файлами

Все новые файлы автоматически получают группу developers.
