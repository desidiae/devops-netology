# devops-netology

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




