# Task №2
Задание:
Используя команду cat, создать два файла с данными, а затем объединить их. Просмотреть содержимое созданного файла. Переименовать файл, дав ему новое имя.
Создать несколько файлов. Создать директорию, переместить файл туда. Удалить все созданные в этом и предыдущем задании директории и файлы.
Создать файл file1 и наполнить его произвольным содержимым. Скопировать его в file2. Создать символическую ссылку file3 на file1. Создать жесткую ссылку file4 на file1. Посмотреть, какие айноды у файлов. Удалить file1. Что стало с остальными созданными файлами? Попробовать вывести их на экран.
Дать созданным файлам другие, произвольные имена. Создать новую символическую ссылку. Переместить ссылки в другую директорию.

Результат:
Текст команд, которые применялись при выполнении задания. Присылаем в формате текстового документа: задание и команды для решения (без вывода). Формат - PDF (один файл на все задания).

# Solution №2
### Используя команду cat, создать два файла с данными
```linux
cat > data_file_1.txt
This is data file number 1
``` 
*CTRL+D*
```linux
cat > data_file_1.txt
And this onether one data file
```
*CTRL+D*
<br/>
<br/>

### А затем объединить их
```linux
cat data_file_1.txt data_file_2.txt > combined_data_file.txt
```
<br/>
<br/>

### Просмотреть содержимое созданного файла
```linux
cat combined_data_file.txt
```
<br/>
<br/>

### Переименовать файл, дав ему новое имя
```linux
mv combined_data_file.txt new_combined_data_file.txt
```
***
### Создать несколько файлов
```linux
touch file1
touch file2
touch file3
```
<br/>
<br/>

### Создать директорию
```linux
mkdir dir1
```
<br/>
<br/>

### Переместить файл туда
```linux
mv file1 ~/Homeworks/Task_2/dir1
```
<br/>
<br/>

### Удалить все созданные в этом и предыдущем задании директории и файлы
```linux
cd ~
rm -r Homeworks
```
***
### Создать файл file1 и наполнить его произвольным содержимым
```linux
cat > file1
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
```
*CTRL+D*
<br/>
<br/>

### Скопировать его в file2
```linux
cp file1 file2
```
<br/>
<br/>

### Создать символическую ссылку file3 на file1
```linux
ln -s file1 file3
```
<br/>
<br/>

### Создать жесткую ссылку file4 на file1
```linux
ln file1 file4
```
<br/>
<br/>

### Посмотреть, какие айноды у файлов
```linux
ls -i
790281 file1  790672 file2  789741 file3  790281 file4
```
<br/>
<br/>

### Удалить file1
```linux
rm file1
```
### Что стало с остальными созданными файлами?
```linux
ls -i
790672 file2  789741 file3  790281 file4
```
*file3 стал помечен красным, и его содержимое пусто. Остальные файлы не изменились.*
<br/>
<br/>
***

### Дать созданным файлам другие, произвольные имена
```linux
mv file2 new_file_two
mv file3 new_file_three
mv file4 new_file_four
```
<br/>
<br/>

### Создать новую символическую ссылку
```linux
ln -s new_file_four new_softlink_file_four
```
<br/>
<br/>

### Переместить ссылки в другую директорию
```linux
mv new_softlink_file_four ~/Homeworks/new_dir
```