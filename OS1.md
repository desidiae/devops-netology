**1.Какой системный вызов делает команда cd? Вам нужно найти тот единственный который относится именно к cd.**


chdir("/tmp")


**2.Попробуйте использовать команду file на объекты разных типов на файловой системе. Используя strace выясните, где находится база данных file на основании которой она делает свои догадки.**


openat(AT_FDCWD, "/usr/share/misc/magic.mgc", O_RDONLY) = 3  
база данных /usr/share/misc/magic.mgc  


Еще осущесталвется поиск в:  
stat("/home/vagrant/.magic.mgc", 0x7ffe11626090) = -1 ENOENT (No such file or directory)  
stat("/home/vagrant/.magic", 0x7ffe11626090) = -1 ENOENT (No such file or directory)  
openat(AT_FDCWD, "/etc/magic.mgc", O_RDONLY) = -1 ENOENT (No such file or directory)  
stat("/etc/magic", {st_mode=S_IFREG|0644, st_size=111, ...}) = 0  
openat(AT_FDCWD, "/etc/magic", O_RDONLY) = 3  


**3.Предположим, приложение пишет лог в текстовый файл. Этот файл оказался удален (deleted в lsof), однако возможности сигналом сказать приложению переоткрыть файлы или просто перезапустить приложение – нет. Так как приложение продолжает писать в удаленный файл, место на диске постепенно заканчивается. Основываясь на знаниях о перенаправлении потоков предложите способ обнуления открытого удаленного файла (чтобы освободить место на файловой системе).**


надо узнать PID процесса который работает с файлом ps aux, далее echo '' >/proc/PID/fd/4

Пояснение по замечанию:  
Мне надо было выяснить какой дескриптор обнулять, для этого узнаем PID процесса:  
1.при помощи команды ps, в ее выводе искал интересующий процесс и его PID  
2.теперь зная пид, через команду: sudo lsof -p PID, надо найти процесс с отметкой deleted  
3.Смотрим какой в столбце FD номер дескриптора  
4.обнуляем дескриптор командой выше  
  
Нашел , что можно воспользоваться такими командами: sudo lsof +L1, lsof | grep deleted, они выдадут статус deleted  
  
Сейчас пример другой, но вывод с дескриптором 1, w write, в прошлый раз дескриптор был 4  
vagrant@vagrant:~$ sudo lsof +L1  
COMMAND  PID    USER   FD   TYPE DEVICE SIZE/OFF NLINK    NODE NAME  
ping    3072 vagrant    1w   REG  253,0    31040     0 2621459 /home/vagrant/ping_log (deleted)  
  

**4.Занимают ли зомби-процессы какие-то ресурсы в ОС (CPU, RAM, IO)?**


Из вики про зомби-процесс:  дочерний процесс в Unix-системе, завершивший своё выполнение, но ещё присутствующий в списке процессов операционной системы, чтобы дать родительскому процессу считать код завершения.


**5.В iovisor BCC есть утилита opensnoop:**

root@vagrant:~# dpkg -L bpfcc-tools | grep sbin/opensnoop
/usr/sbin/opensnoop-bpfcc

На какие файлы вы увидели вызовы группы open за первую секунду работы утилиты? Воспользуйтесь пакетом bpfcc-tools для Ubuntu 20.04. Дополнительные сведения по установке.

установил: sudo apt-get install bpfcc-tools linux-headers-$(uname -r)  
  
root@vagrant:~# dpkg -L bpfcc-tools | grep sbin/opensnoop  
/usr/sbin/opensnoop-bpfcc  
root@vagrant:/usr/sbin# /usr/sbin/opensnoop-bpfcc  
PID    COMM               FD ERR PATH  

и все, висит в таком состоянии...  

root@vagrant:/usr/sbin# opensnoop-bpfcc  
PID    COMM               FD ERR PATH  

перезагрузил виртуалку, и тоже самое... Не понимаю, что делать.

Пояснение по замечанию:  
К сожалению ваше предложение не помогает, попробовал разные варианты:  
vagrant@vagrant:~$ sudo opensnoop-bpfcc  
PID    COMM               FD ERR PATH  
^Cvagrant@vagrant:~$ sudo su  
root@vagrant:/home/vagrant# opensnoop-bpfcc  
PID    COMM               FD ERR PATH  
^Croot@vagrant:/home/vagrant# /usr/sbin/opensnoop-bpfcc  
PID    COMM               FD ERR PATH  



**6.Какой системный вызов использует uname -a? Приведите цитату из man по этому системному вызову, где описывается альтернативное местоположение в /proc, где можно узнать версию ядра и релиз ОС.**

uname -a системный вызов
в виртуалке через man uname и info uname про альтернативное расположение нет информации. На сайте opennet.ru нашел: Часть информации из структуры utsname может быть получена также через sysctl и через /proc/sys/kernel/{ostype, hostname, osrelease, version, domainname}.

По английски должно быть: Part of the utsname information is also accessible via /proc/sys/kernel/{ostype, hostname, osrelease, version, domainname}
________________________________________________________________________________________________________________________

info uname

Next: hostname invocation,  Prev: nproc invocation,  Up: System context

'uname': Print system information
======================================

‘uname’ prints information about the machine and operating system it is
run on.  If no options are given, ‘uname’ acts as if the ‘-s’ option
were given.  Synopsis:

     uname [OPTION]...

   If multiple options or ‘-a’ are given, the selected information is
printed in this order:

     KERNEL-NAME NODENAME KERNEL-RELEASE KERNEL-VERSION
     MACHINE PROCESSOR HARDWARE-PLATFORM OPERATING-SYSTEM

   The information may contain internal spaces, so such output cannot be
parsed reliably.  In the following example, RELEASE is
‘2.2.18ss.e820-bda652a #4 SMP Tue Jun 5 11:24:08 PDT 2001’:

     uname -a
     ⇒ Linux dumdum 2.2.18 #4 SMP Tue Jun 5 11:24:08 PDT 2001 i686 unknown unknown GNU/Linux

   The program accepts the following options.  Also see *note Common
options::.

‘-a’
‘--all’
     Print all of the below information, except omit the processor type
     and the hardware platform name if they are unknown.

‘-i’
‘--hardware-platform’
     Print the hardware platform name (sometimes called the hardware
     implementation).  Print ‘unknown’ if this information is not
     available.  Note this is non-portable (even across GNU/Linux
     distributions).

‘-m’
‘--machine’
     Print the machine hardware name (sometimes called the hardware
     class or hardware type).

‘-n’
‘--nodename’
     Print the network node hostname.

‘-p’
‘--processor’
     Print the processor type (sometimes called the instruction set
     architecture or ISA). Print ‘unknown’ if this information is not
     available.  Note this is non-portable (even across GNU/Linux
     distributions).

‘-o’
‘--operating-system’
     Print the name of the operating system.

‘-r’
‘--kernel-release’
     Print the kernel release.

‘-s’
‘--kernel-name’
     Print the kernel name.  POSIX 1003.1-2001 (*note Standards
     conformance::) calls this “the implementation of the operating
     system”, because the POSIX specification itself has no notion of
     “kernel”.  The kernel name might be the same as the operating
     system name printed by the ‘-o’ or ‘--operating-system’ option, but
     it might differ.  Some operating systems (e.g., FreeBSD, HP-UX)
     have the same name as their underlying kernels; others (e.g.,
     GNU/Linux, Solaris) do not.

‘-v’
‘--kernel-version’
     Print the kernel version.

   An exit status of zero indicates success, and a nonzero value
indicates failure.
_______________________________________________________________________________________________________________________


_______________________________________________________________________________________________________________________

man uname


UNAME(1)                                                                         User Commands                                                                         UNAME(1)

NAME
       uname - print system information

SYNOPSIS
       uname [OPTION]...

DESCRIPTION
       Print certain system information.  With no OPTION, same as -s.

       -a, --all
              print all information, in the following order, except omit -p and -i if unknown:

       -s, --kernel-name
              print the kernel name

       -n, --nodename
              print the network node hostname

       -r, --kernel-release
              print the kernel release

       -v, --kernel-version
              print the kernel version

       -m, --machine
              print the machine hardware name

       -p, --processor
              print the processor type (non-portable)

       -i, --hardware-platform
              print the hardware platform (non-portable)

       -o, --operating-system
              print the operating system

       --help display this help and exit

       --version
              output version information and exit

AUTHOR
       Written by David MacKenzie.

REPORTING BUGS
       GNU coreutils online help: <http://www.gnu.org/software/coreutils/>
       Report uname translation bugs to <http://translationproject.org/team/>

COPYRIGHT
       Copyright © 2017 Free Software Foundation, Inc.  License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>.
       This is free software: you are free to change and redistribute it.  There is NO WARRANTY, to the extent permitted by law.

SEE ALSO
       arch(1), uname(2)

       Full documentation at: <http://www.gnu.org/software/coreutils/uname>
       or available locally via: info '(coreutils) uname invocation'

GNU coreutils 8.28                                                                January 2018                                                                         UNAME(1)
 Manual page uname(1) line 1/61 (END) (press h for help or q to quit)
________________________________________________________________________________________________________________________

  

**7.Чем отличается последовательность команд через ; и через && в bash? Например:**

root@netology1:~# test -d /tmp/some_dir; echo Hi  
Hi  
root@netology1:~# test -d /tmp/some_dir && echo Hi  
root@netology1:~#  

; разделитель команд, выполнит последовательно команды  
&& логический оператор, если какая-либо команда завершится ошибкой, остановится  

 Есть ли смысл использовать в bash &&, если применить set -e?  

вероятно использовать с && не имеет смысла, если при ошибке выполнение команд прекратится  


**8.Из каких опций состоит режим bash set -euxo pipefail и почему его хорошо было бы использовать в сценариях?**


`-e` Немедленный выход, если команда завершается с ненулевым статусом. 
`-u` При подстановке обрабатывать неустановленные переменные как ошибку.
`-x` Печатать команды и их аргументы по мере их выполнения. 
`-o option-pipefail` возвращаемое значение конвейера - это статус последней команды для выхода с ненулевым статусом или ноль, если ни одна команда не завершилась с ненулевым статусом.

Для сценария увеличивает детальность лога. Завершит сценарий при наличии ошибок, кроме последней завершающей команды


**9.Используя -o stat для ps, определите, какой наиболее часто встречающийся статус у процессов в системе. В man ps ознакомьтесь (/PROCESS STATE CODES) что значат дополнительные к основной заглавной буквы статуса процессов. Его можно не учитывать при расчете (считать S, Ss или Ssl равнозначными).**


vagrant@vagrant:~$ ps -o stat
STAT
Ss   - спящий процес, который можно прервать, ожидает завершения некоего события
R+   - выполяемый процес или который можно запустить, в группе приоритетных

PROCESS STATE CODES
       Here are the different values that the s, stat and state output specifiers (header "STAT" or "S") will display to describe the state of a process:

               D    uninterruptible sleep (usually IO)
               R    running or runnable (on run queue)
               S    interruptible sleep (waiting for an event to complete)
               T    stopped by job control signal
               t    stopped by debugger during the tracing
               W    paging (not valid since the 2.6.xx kernel)
               X    dead (should never be seen)
               Z    defunct ("zombie") process, terminated but not reaped by its parent

       For BSD formats and when the stat keyword is used, additional characters may be displayed:

               <    high-priority (not nice to other users)
               N    low-priority (nice to other users)
               L    has pages locked into memory (for real-time and custom IO)
               s    is a session leader
               l    is multi-threaded (using CLONE_THREAD, like NPTL pthreads do)
               +    is in the foreground process group

