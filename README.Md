Домашнее задание по лекции "Работа в терминале (лекция 2)"

1. Какого типа команда cd? Попробуйте объяснить, почему она именно такого типа; опишите ход своих мыслей, если считаете что она могла бы быть другого типа.  

vagrant@vagrant:~$ type -a cd  
cd is a shell builtin  

встроенная команда в оболочку, вызывается и выполняется в текущей оболочке. Если сделать ее внешней, то мы же не сможем менять директории в текущей оболочке.   


2. Какая альтернатива без pipe команде grep <some_string> <some_file> | wc -l? man grep поможет в ответе на этот вопрос. Ознакомьтесь с документом о других подобных некорректных вариантах использования pipe.  

grep <some_string> <some_file> -c  


3. Какой процесс с PID 1 является родителем для всех процессов в вашей виртуальной машине Ubuntu 20.04?  

в моем случае systemd(1)  

vagrant@vagrant:~$ pstree -p  
systemd(1)─┬─VBoxService(1083)─┬─{VBoxService}(1084)  
           │                   ├─{VBoxService}(1085)  
           │                   ├─{VBoxService}(1086)  
           │                   ├─{VBoxService}(1087)  
           │                   ├─{VBoxService}(1088)  
           │                   ├─{VBoxService}(1089)  
           │                   └─{VBoxService}(1090)  


4. Как будет выглядеть команда, которая перенаправит вывод stderr ls на другую сессию терминала?  

из /dev/pts/0
vagrant@vagrant:~$ cd qweqweqqq 2>/dev/pts/1  
в /dev/pts/1  
vagrant@vagrant:~$ -bash: cd: qweqweqqq: No such file or directory  

5. Получится ли одновременно передать команде файл на stdin и вывести ее stdout в другой файл? Приведите работающий пример.  


vagrant@vagrant:~$ echo 1 > a.txt  
nano a.txt  
cat < a.txt > b.txt  
vagrant@vagrant:~$ cat a.txt  
ase  
dasda  
dasd  
dasd  
1  
vagrant@vagrant:~$ cat b.txt  
ase  
dasda  
dasd  
dasd  
1  

Работа над ошибками:  
vagrant@vagrant:~$ cat a.txt  
ase  
dasda  
dasd  
dasd  
1  
vagrant@vagrant:~$ cat c_out.txt  
cat: c_out.txt: No such file or directory  
vagrant@vagrant:~$ cat < a.txt > c_out.txt  
vagrant@vagrant:~$ cat c_out.txt  
ase  
dasda  
dasd  
dasd  
1  



6. Получится ли находясь в графическом режиме, вывести данные из PTY в какой-либо из эмуляторов TTY? Сможете ли вы наблюдать выводимые данные?  

Сможем вывести через перенаправление, данные наблюдать не сможем. Данный вопрос полностью у меня не проработан, мы можем использовать комбинацию клавиш alt+ctrl+f1-f6 для эмеляции темрминала, но DE у меня не установлена.  


7. Выполните команду bash 5>&1. К чему она приведет? Что будет, если вы выполните echo netology > /proc/$$/fd/5? Почему так происходит?  

vagrant@vagrant:~$ bash 5>&1  
vagrant@vagrant:~$ echo netology > /proc/$$/fd/5  
netology  

bash 5>&1 - создаем дескриптор с 5 и перенатправит его в stdout  
echo netology > /proc/$$/fd/5 - выводим в дескриптор 5, который был пернеаправлен в stdout  


8. Получится ли в качестве входного потока для pipe использовать только stderr команды, не потеряв при этом отображение stdout на pty? Напоминаем: по умолчанию через pipe передается только stdout команды слева от | на stdin команды справа. Это можно сделать, поменяв стандартные потоки местами через промежуточный новый дескриптор, который вы научились создавать в предыдущем вопросе.  

vagrant@vagrant:~$ ll /root 66>&2 2>&1 1>&66 |grep denied -c  
1  

66>&2 дескриптор перенаправили в stderr  
2>&1 stderr перенаправили stdout   
1>&66 stdout перенаправили в новый дескриптор  


9. Что выведет команда cat /proc/$$/environ? Как еще можно получить аналогичный по содержанию вывод?  

будут выведеные переменные окружения, аналогичный по седержанию вывод можно получить следующим образом:  
printenv  
env  
export  
в последних 3х случаях вывод более визуально приятный  


10. Используя man, опишите что доступно по адресам /proc/<PID>/cmdline, /proc/<PID>/exe.  

/proc/<PID>/cmdline - 'этот файл содержит полную командную строку запуска процесса.  
/proc/<PID>/exe - является символьной ссылкой, содержащей фактическое полное имя выполняемого файла.  


11. Узнайте, какую наиболее старшую версию набора инструкций SSE поддерживает ваш процессор с помощью /proc/cpuinfo.  

vagrant@vagrant:~$ grep sse /proc/cpuinfo  
среди прочих: sse sse2 ssse3 sse4_1 sse4_2, получается наиболее старшая версия sse4_2  

  
12. При открытии нового окна терминала и vagrant ssh создается новая сессия и выделяется pty. Это можно подтвердить командой tty, которая упоминалась в лекции 3.2. Однако:  

vagrant@netology1:~$ ssh localhost 'tty'  
not a tty  

Почитайте, почему так происходит, и как изменить поведение.  

У меня выдает следующее:  

vagrant@vagrant:~$ tty  
/dev/pts/1  
vagrant@vagrant:~$ ssh localhost 'tty'  
The authenticity of host 'localhost (::1)' can't be established.  
ECDSA key fingerprint is SHA256:uY6GIjFdI9qTC4QYb980QRk+WblJF9cd5glr3SmmL+w.  
Are you sure you want to continue connecting (yes/no)? y  
Please type 'yes' or 'no': yes  
Warning: Permanently added 'localhost' (ECDSA) to the list of known hosts.  
vagrant@localhost's password:  

Для варианта с заданием нашел, что при подключении ожидается пользователь, а не другой процесс, и нет локального tty в данный момент. Если добавить -t, команда исполняется c принудительным созданием псевдотерминала  
           
Работа над ошибками:             
vagrant@vagrant:~$ ssh localhost 'tty'  
vagrant@localhost's password:  
not a tty  
vagrant@vagrant:~$ ssh -t localhost 'tty'  
vagrant@localhost's password:  
/dev/pts/1  
Connection to localhost closed.  


13. Бывает, что есть необходимость переместить запущенный процесс из одной сессии в другую. Попробуйте сделать это, воспользовавшись reptyr. Например, так можно перенести в screen процесс, который вы запустили по ошибке в обычной SSH-сессии.  

установил reptyr, следовал инструкции https://github.com/nelhage/reptyr#readme  

на начало эксперимента:  
vagrant@vagrant:~$ pstree  
systemd─┬─VBoxService───7*[{VBoxService}]  
        ├─accounts-daemon───2*[{accounts-daemon}]  
        ├─agetty  
        ├─atd  
        ├─cron  
        ├─dbus-daemon  
        ├─lvmetad  
        ├─lxcfs───2*[{lxcfs}]  
        ├─networkd-dispat───{networkd-dispat}  
        ├─polkitd───2*[{polkitd}]  
        ├─rpcbind  
        ├─rsyslogd───3*[{rsyslogd}]   
        ├─sshd─┬─sshd───sshd───bash  
        │      └─sshd───sshd───bash───pstree  
        ├─systemd───(sd-pam)  
        ├─systemd-journal  
        ├─systemd-logind  
        ├─systemd-network  
        ├─systemd-resolve  
        └─systemd-udevd  

Запустил top, далее CTRL-Z переводит в фон, делаем screen PID top (была ошибка /etc/sysctl.d/10-ptrace.conf, с изменением на kernel.yama.ptrace_scope = 0, ребут виртуалки), и после screen PID top работает  

vagrant@vagrant:~$ who  
vagrant  pts/0        2022-02-28 20:05 (10.0.2.2)  
vagrant  pts/1        2022-02-28 20:09 (10.0.2.2)  

с активным скрином топа, появился процесс reptyr  
vagrant@vagrant:~$ pstree  
systemd─┬─VBoxService───7*[{VBoxService}]  
        ├─accounts-daemon───2*[{accounts-daemon}]  
        ├─agetty  
        ├─atd  
        ├─cron  
        ├─dbus-daemon  
        ├─lvmetad  
        ├─lxcfs───2*[{lxcfs}]  
        ├─networkd-dispat───{networkd-dispat}  
        ├─polkitd───2*[{polkitd}]  
        ├─rpcbind  
        ├─rsyslogd───3*[{rsyslogd}]  
        ├─sshd─┬─sshd───sshd───bash─┬─reptyr  
        │      │                    └─top───top  
        │      └─sshd───sshd───bash───pstree  
        ├─systemd───(sd-pam)  
        ├─systemd-journal  
        ├─systemd-logind  
        ├─systemd-network  
        ├─systemd-resolve  
        └─systemd-udevd  

  
14. sudo echo string > /root/new_file не даст выполнить перенаправление под обычным пользователем, так как перенаправлением занимается процесс shell'а, который запущен без sudo под вашим пользователем. Для решения данной проблемы можно использовать конструкцию echo string | sudo tee /root/new_file. Узнайте что делает команда tee и почему в отличие от sudo echo команда с sudo tee будет работать.  

команда tee делает вывод одновременно и в файл, указаный в качестве параметра, и в stdout, в данном примере команда получает вывод из stdin, перенаправленный через pipe от stdout команды echo и так как команда запущена от sudo, соотвественно имеет права на запись в файл.  
