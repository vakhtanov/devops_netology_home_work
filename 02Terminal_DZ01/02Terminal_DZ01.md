
### Инструкция к заданию

1. Установите средство виртуализации [Oracle VirtualBox](https://www.virtualbox.org/).
![virtula box](https://github.com/vakhtanov/devops_netology_home_work/blob/main/02Terminal_DZ01/virtbox.JPG)

1. Установите средство автоматизации [Hashicorp Vagrant](https://hashicorp-releases.yandexcloud.net/vagrant/).
![vagrant](https://github.com/vakhtanov/devops_netology_home_work/blob/main/02Terminal_DZ01/vagrant.JPG)

1. В вашем основном окружении подготовьте удобный для дальнейшей работы терминал. Можно предложить:

	* iTerm2 в Mac OS X
	* Windows Terminal в Windows
	* выбрать цветовую схему, размер окна, шрифтов и т.д.
	* почитать о кастомизации PS1/применить при желании.

	Несколько популярных проблем:
	* Добавьте Vagrant в правила исключения перехватывающих трафик для анализа антивирусов, таких как Kaspersky, если у вас возникают связанные с SSL/TLS ошибки,
	* MobaXterm может конфликтовать с Vagrant в Windows,
	* Vagrant плохо работает с директориями с кириллицей (может быть вашей домашней директорией), тогда можно либо изменить [VAGRANT_HOME](https://www.vagrantup.com/docs/other/environmental-variables#vagrant_home), либо создать в системе профиль пользователя с английским именем,
	* VirtualBox конфликтует с Windows Hyper-V и его необходимо [отключить](https://www.vagrantup.com/docs/installation#windows-virtualbox-and-hyper-v),
	* [WSL2](https://docs.microsoft.com/ru-ru/windows/wsl/wsl2-faq#does-wsl-2-use-hyper-v-will-it-be-available-on-windows-10-home) использует Hyper-V, поэтому с ним VirtualBox также несовместим,
	* аппаратная виртуализация (Intel VT-x, AMD-V) должна быть активна в BIOS,
	* в Linux при установке [VirtualBox](https://www.virtualbox.org/wiki/Linux_Downloads) может дополнительно потребоваться пакет `linux-headers-generic` (debian-based) / `kernel-devel` (rhel-based).
---
[*Пока буду пользоваться коммандной строкой Windows.* 
*Так как на компе какие-то ошибки или вирусы - не могу установить Win Terminal - не открывается Microsoft Store*]()

[*VAGRANT_HOME - поменял*]()
---

------

## Задание

1. С помощью базового файла конфигурации запустите Ubuntu 20.04 в VirtualBox посредством Vagrant:

	* Создайте директорию, в которой будут храниться конфигурационные файлы Vagrant. В ней выполните `vagrant init`. Замените содержимое Vagrantfile по умолчанию следующим:

		```bash
		Vagrant.configure("2") do |config|
			config.vm.box = "bento/ubuntu-20.04"
		end
		```

	* Выполнение в этой директории `vagrant up` установит провайдер VirtualBox для Vagrant, скачает необходимый образ и запустит виртуальную машину.

	* `vagrant suspend` выключит виртуальную машину с сохранением ее состояния (т.е., при следующем `vagrant up` будут запущены все процессы внутри, которые работали на момент вызова suspend), `vagrant halt` выключит виртуальную машину штатным образом.



	[*установить образ через интерент не удалось из-за за санкционных ограничений с помощью проксисервера скачал с сайта бокс с bento/ubuntu-20.04 и установил с помощью комманд ниже. Работаю из под Windows 10*]()
	
	```bash
	chcp 1251
	vagrant box add e3801813-59e0-4e69-8ace-78362d9f47cf --name bento/ubuntu-20.04
	vagrant init bento/ubuntu20.04
	vagrant up
	```

2. Ознакомьтесь с графическим интерфейсом VirtualBox, посмотрите как выглядит виртуальная машина, которую создал для вас Vagrant, какие аппаратные ресурсы ей выделены. Какие ресурсы выделены по-умолчанию?
 ![virtbox resurs](https://github.com/vakhtanov/devops_netology_home_work/blob/main/02Terminal_DZ01/5virtbox_with_machine.JPG)

3. Ознакомьтесь с возможностями конфигурации VirtualBox через Vagrantfile: [документация](https://www.vagrantup.com/docs/providers/virtualbox/configuration.html). Как добавить оперативной памяти или ресурсов процессора виртуальной машине?

 [*редактировать Vagrantfile файл, добавить строки*]()
 
 ![memory](https://github.com/vakhtanov/devops_netology_home_work/blob/main/02Terminal_DZ01/6memory.JPG)

4. Команда `vagrant ssh` из директории, в которой содержится Vagrantfile, позволит вам оказаться внутри виртуальной машины без каких-либо дополнительных настроек. Попрактикуйтесь в выполнении обсуждаемых команд в терминале Ubuntu.

![ssh connect](https://github.com/vakhtanov/devops_netology_home_work/blob/main/02Terminal_DZ01/7SSH_connect.JPG)

5. Ознакомьтесь с разделами `man bash`, почитайте о настройках самого bash:
    * какой переменной можно задать длину журнала `history`, и на какой строчке manual это описывается?
    
		[*Количество комманд сохраняемых в рамках текущей сессии HISTSIZE - строка 1053, количетво комманд, сохранаяеых в файле истории - HISTFILESIZE - строка 1033*]()

    * что делает директива `ignoreboth` в bash?

		[*Указывает, что при записи истории нужно игнорировать повторяющиеся строки комманд и игнорировать строки, котоыре начинаются с пробела*]()

6. В каких сценариях использования применимы скобки `{}` и на какой строчке `man bash` это описано?
	* строка 309 - создают список значений разделенных разделителем - задает список значений
	* строка 485 - при определении функции - заключают в себя тело функции
	* строка 767, 782 - применятся конструкция ${...} внутри фигурных скобок - вызов функции или элемента массива в цикле
	* строку не нашел, но еще одно применение -  конструкция ${...} указывает, что то, что внутри фигурных скобок - имя переменной, а не текст

7. С учётом ответа на предыдущий вопрос, как создать однократным вызовом `touch` 100000 файлов? Получится ли аналогичным образом создать 300000? Если нет, то почему?

		```bash
		touch a{1..5}
		```
		[*300000 - создать не получится слишком длинный список*]()


8. В man bash поищите по `/\[\[`. Что делает конструкция `[[ -d /tmp ]]`

[*проверяет, что /tmp существует и является директорией*]()


9. Сделайте так, чтобы в выводе команды `type -a bash` первым стояла запись с нестандартным путем, например bash is ... 
Используйте знания о просмотре существующих и создании новых переменных окружения, обратите внимание на переменную окружения PATH 

	```bash
	bash is /tmp/new_path_directory/bash
	bash is /usr/local/bin/bash
	bash is /bin/bash
	```

	(прочие строки могут отличаться содержимым и порядком)
    В качестве ответа приведите команды, которые позволили вам добиться указанного вывода или соответствующие скриншоты.
    
    ```bash
    mkdir /tmp/new_path_directory/
     touch /tmp/new_path_directory/bash
     chmod +x bash
     export PATH=/tmp/new_path_directory:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:
     type -a bash
     ```
     
     ![bash is](https://github.com/vakhtanov/devops_netology_home_work/)
     

10. Чем отличается планирование команд с помощью `batch` и `at`?

[*at - для выполнения разовой задачи в заданное время, batch для выполнения разовой  задачи когда загрузка процессора меньше 0,8*]()

11. Завершите работу виртуальной машины чтобы не расходовать ресурсы компьютера и/или батарею ноутбука.

 ![vagrant halt](https://github.com/vakhtanov/devops_netology_home_work/)

---
