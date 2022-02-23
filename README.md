1.Установите средство виртуализации Oracle VirtualBox.  
установлено sudo apt-get install VirtualBox

2.Установите средство автоматизации Hashicorp Vagrant.  
установлено sudo apt-get install VirtualBox

3.В вашем основном окружении подготовьте удобный для дальнейшей работы терминал.  
установлено szh и oh my zsh

4.С помощью базового файла конфигурации запустите Ubuntu 20.04 в VirtualBox посредством Vagrant:  
запустил

5.Ознакомьтесь с графическим интерфейсом VirtualBox, посмотрите как выглядит виртуальная машина, которую создал для вас Vagrant, какие аппаратные ресурсы ей выделены. Какие ресурсы выделены по-умолчанию?

CPU:2  
RAM:1024mb  
HDD:64Gb  
Video:4mb

6.Ознакомьтесь с возможностями конфигурации VirtualBox через Vagrantfile: документация. Как добавить оперативной памяти или ресурсов процессора виртуальной машине?

добавлением комманд в VagrantFile:  
  v.memory = 1024  
  v.cpus = 2  
или командами ВМ:  
   config.vm.provider "virtualbox" do |vb|  
     vb.memory = "1024"  
     vb.cpu = "2"  
   end  

7.Команда vagrant ssh из директории, в которой содержится Vagrantfile, позволит вам оказаться внутри виртуальной машины без каких-либо дополнительных настроек. Попрактикуйтесь в выполнении обсуждаемых команд в терминале Ubuntu.  
сделано

8.Ознакомиться с разделами man bash, почитать о настройках самого bash:  

-Какой переменной можно задать длину журнала history, и на какой строчке manual это описывается?   
Длину журнала можно задать переменной HISTFILESIZE, 760 строка  

-что делает директива ignoreboth в bash?  
Ignoreboth включает оба значения переменных Ignoredups и Ignorespace.
Ignoredups — Не писать строку после команды дублирования строки.
Ignorespace — не писать строки, начинающиеся с одного или нескольких пробелов в истории.

9.В каких сценариях использования применимы скобки {} и на какой строчке man bash это описано?  

 { list; }
 list  is  simply  executed in the current shell environment.  list must be terminated with a newline or 
 semicolon.  This is known as a group command.  The return status is the exit status of list.  Note that
 unlike  the  metacharacters ( and ), { and } are reserved words and must occur where a reserved word is
 permitted to be recognized.  Since they do not cause a word break, they must be separated from list  by
 whitespace or another shell metacharacter.  
                            
строка 191, Список выполняется в среде текущего командного интерпретатора. Список должен завершаться переводом строки или точкой с запятой. Эту команду называют командой группировки. Статусом возврата является статус выхода списка.

10.С учётом ответа на предыдущий вопрос, как создать однократным вызовом touch 100000 файлов? Получится ли аналогичным образом создать 300000? Если нет, то почему?  

можно создать следующей командой: touch {000001..100000}.txt  

300000 не получается создать:
vagrant@vagrant:~/test_touch$ touch {000001..300000}.txt
-bash: /usr/bin/touch: Argument list too long  


11.В man bash поищите по /\[\[. Что делает конструкция [[ -d /tmp ]]  

Проверяет наличие директории tmp.

12.Основываясь на знаниях о просмотре текущих (например, PATH) и установке новых переменных; командах, которые мы рассматривали, добейтесь в выводе type -a bash в виртуальной машине наличия первым пунктом в списке:

vagrant@vagrant:~$ mkdir /tmp/new_path_dir/  
vagrant@vagrant:~$ cp /bin/bash /tmp/new_path_dir/  
vagrant@vagrant:~$ type -a bash  
bash is /usr/bin/bash  
bash is /bin/bash  
  
vagrant@vagrant:~$ PATH=/tmp/new_path_dir/:$PATH  
vagrant@vagrant:~$ type -a bash  
bash is /tmp/new_path_dir/bash  
bash is /tmp/new_path_directory/bash  
bash is /bin/bash  
  

13.Чем отличается планирование команд с помощью batch и at?  

at      выполняет команды в указанное время
batch   выполняет команды когда определенная загрузка системы, например ниже 1.5


14.Завершите работу виртуальной машины чтобы не расходовать ресурсы компьютера и/или батарею ноутбука.

сделано  
  
    
.      
 .       
  .        
   .       
devops-netology, пока пусть будет

1 stroka
2 line
3 line
4 line
5 line

fefewfewfew
dasdasdas
fdsfdsf

игнорируются пустые стоки и строки со знаком хэша;

**/.terraform/*
игнорируется в подкаталоге файлы с расширением терраформ

*.tfstate
*.tfstate.*
игнорируются файлы с расширением .tfstate; следующую строку не понял, расширение файла между **

crash.log игнорируется файл логов crash.log в данном каталоге и в подкаталогах, и других директориях

override.tf override.tf.json игнорируются файлы с именами выше в данном каталоге и в подкаталогах и других директориях

*_override.tf
*_override.tf.json игнорируются файлы в данном каталоге и других, у которых в названии есть _override.tf _
override.tf.json, к примеру name1_override.tf, name2_override.tf.json

в примерах шаблонов не нашел информации по *.example.*
