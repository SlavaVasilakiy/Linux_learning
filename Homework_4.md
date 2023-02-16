# Task №4
Задание:
1 Подключить дополнительный репозиторий на выбор: Docker, Nginx, Oracle MySQL.<br>
Установить любой пакет из этого репозитория.

https://dev.mysql.com/doc/mysql-apt-repo-quick-guide/en/#apt-repo-setup
http://nginx.org/ru/linux_packages.html
https://www.virtualbox.org/wiki/Linux_Downloads
https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository

2 Установить и удалить deb-пакет с помощью dpkg.<br>
3 Установить и удалить snap-пакет.<br>
4 Добавить задачу для выполнения каждые 3 минуты (создание директории, запись в файл).<br>
5 * Подключить PPA-репозиторий на выбор. Установить из него пакет. Удалить PPA из системы.<br>
6 * Создать задачу резервного копирования (tar) домашнего каталога пользователя.<br>
Реализовать с использованием пользовательских crontab-файлов.<br>

Результат:
Текст команд, которые применялись при выполнении задания. При наличии: часть конфигурационных файлов, <br>
которые решают задачу. Присылаем в формате текстового документа:<br>
задание и команды для решения (без вывода). Формат - PDF (один файл на все задания).

# Solution №4

### Подключить дополнительный репозиторий на выбор.

Рассмотрим данную операцию на примере VirtualBox:<br>
1) Переходим по ссылке https://www.virtualbox.org/wiki/Linux_Downloads
2) Для подключения репозитория нам нужно скопировать соответствующую строку:<br>
   ```
   deb [arch=amd64 signed-by=/usr/share/keyrings/oracle-virtualbox-2016.gpg] https://download.virtualbox.
   org/virtualbox/debian <mydist> contrib
   ```
   Где вместо ```<mydist>``` нужно указать версию ОС ```jammy``` в случае с Ubuntu 22.04.
3) Далее нам нужно открыть файл /etc/apt/sources.list. Для этого вводим в консоли:
    ```linux
    sudo nano /etc/apt/sources.list
    ```
4) В данном файле вставляем строку из пункта 2.<br>
Затем сохраняем изменения ```Ctrl+O``` 
и выходим из редактора ```Ctrl+X```.
5) В консоли вводим команду:
    ```linux
    wget -O- https://www.virtualbox.org/download/oracle_vbox_2016
    .asc | sudo gpg --dearmor --yes --output /usr/share/keyrings/
    oracle-virtualbox-2016.gpg
    ```
    Для подключения к репозиторию.

Готово, репозиторий добавлен в список и подключен.

### Установить любой пакет из этого репозитория.
1) Обновляем список пакетов:
   ```linux
   sudo apt update
   ```
2) Устанавливаем пакет:
   ```linux
   sudo apt install virtualbox-6.1
   ```

### Установить и удалить deb-пакет с помощью dpkg.
Рассмотрим операцию на примере Google Chrome.<br>

Для установки:
1) Переходим на страницу загрузки https://www.google.com/chrome/?platform=linux
2) Скачиваем DEB пакет.
3) Переходим в папку Загрузки:
   ```linux
   cd Загрузки/
   ```
4) Запускаем установку пакета:
   ```linux
   sudo dpkg -i google-chrome-stable_current_amd64.deb
   ```
5) Устанавливаем зависимости:
   ```linux
   sudo apt -f install
   ```

   Для удаления вводим -r и название пакета (не имя установочного файла):
   ```linux
   sudo dpkg -r google-chrome-stable
   ```

### Установить и удалить snap-пакет.
Установка:
```linux
sudo snap install gimp
```

Удаление:
```linux
sudo snap remove gimp
```

### Добавить задачу для выполнения каждые 3 минуты (создание директории, запись в файл).
1) Входим в режим редактирования
   ```linux
   crontab -e
   ```
2) Выбираем ```1``` пункт меню для редактирования в nano редакторе.
3) И в файле прописываем 
   ```
   */3 * * * * mkdir helloworld; echo "Hello world!" >> /home/{user}/helloworld/helloworld.txt
   ```
4) Сохраняем ```Ctrl+O``` и выходим из редактора ```Ctrl+X```.

### * Подключить PPA-репозиторий на выбор. Установить из него пакет. Удалить PPA из системы.

```linux

```

### * Создать задачу резервного копирования (tar) домашнего каталога пользователя.

```linux

```

### * Реализовать с использованием пользовательских crontab-файлов.

```linux

```