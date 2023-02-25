# Домашнее задание к занятию «Операционные системы. Лекция 1»

### Цель задания

В результате выполнения задания вы:

* познакомитесь с инструментом strace, который помогает отслеживать системные вызовы процессов и является необходимым для отладки и расследований при возникновении ошибок в работе программ;
* рассмотрите различные режимы работы скриптов, настраиваемые командой set. Один и тот же код в скриптах в разных режимах работы ведёт себя по-разному.

### Чеклист готовности к домашнему заданию

1. Убедитесь, что у вас установлен инструмент `strace`, выполнив команду `strace -V` для проверки версии. В Ubuntu 20.04 strace установлен, но в других дистрибутивах его может не быть в коплекте «из коробки». Обратитесь к документации дистрибутива, чтобы понять, как установить инструмент strace.

![strace](https://github.com/vakhtanov/devops_netology_home_work/blob/main/03-sysadmin-03-os/strace.JPG)

2. Убедитесь, что у вас установлен пакет `bpfcc-tools`, информация по установке [по ссылке](https://github.com/iovisor/bcc/blob/master/INSTALL.md).

![bpfcc](https://github.com/vakhtanov/devops_netology_home_work/blob/main/03-sysadmin-03-os/bpfcc.JPG)

### Дополнительные материалы для выполнения задания

1. Изучите документацию lsof — `man lsof`. Та же информация есть [в сети](https://linux.die.net/man/8/lsof).
2. Документация по режимам работы bash находится в `help set` или [в сети](https://www.gnu.org/software/bash/manual/html_node/The-Set-Builtin.html).

------

## Задание

1. Какой системный вызов делает команда `cd`? 

    В прошлом ДЗ вы выяснили, что `cd` не является самостоятельной  программой. Это `shell builtin`, поэтому запустить `strace` непосредственно на `cd` не получится. Вы можете запустить `strace` на `/bin/bash -c 'cd /tmp'`. В этом случае увидите полный список системных вызовов, которые делает сам `bash` при старте. 

    Вам нужно найти тот единственный, который относится именно к `cd`. Обратите внимание, что `strace` выдаёт результат своей работы в поток stderr, а не в stdout.
    
   ```bash
   strace -f bash -c 'cd /tmp'
   
   ```
   
   ...  
   *chdir("/tmp")*  
   ...
   

2. Попробуйте использовать команду `file` на объекты разных типов в файловой системе. Например:

    ```bash
    vagrant@netology1:~$ file /dev/tty
    /dev/tty: character special (5/0)
    vagrant@netology1:~$ file /dev/sda
    /dev/sda: block special (8/0)
    vagrant@netology1:~$ file /bin/bash
    /bin/bash: ELF 64-bit LSB shared object, x86-64
    ```
    
    Используя `strace`, выясните, где находится база данных `file`, на основании которой она делает свои догадки.
    
    ```bash
    vagrant@vagrant:~$ strace  -e openat -y -s 65000 file .bashrc
    openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3</etc/ld.so.cache>
    openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libmagic.so.1", O_RDONLY|O_CLOEXEC) = 3</usr/lib/x86_64-linux-gnu/libmagic.so.1.0.0>
    openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3</usr/lib/x86_64-linux-gnu/libc-2.31.so>
    openat(AT_FDCWD, "/lib/x86_64-linux-gnu/liblzma.so.5", O_RDONLY|O_CLOEXEC) = 3</usr/lib/x86_64-linux-gnu/liblzma.so.5.2.4>
    openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libbz2.so.1.0", O_RDONLY|O_CLOEXEC) = 3</usr/lib/x86_64-linux-gnu/libbz2.so.1.0.4>
    openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libz.so.1", O_RDONLY|O_CLOEXEC) = 3</usr/lib/x86_64-linux-gnu/libz.so.1.2.11>
    openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libpthread.so.0", O_RDONLY|O_CLOEXEC) = 3</usr/lib/x86_64-linux-gnu/libpthread-2.31.so>
    openat(AT_FDCWD, "/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3</usr/lib/locale/locale-archive>
    openat(AT_FDCWD, "/etc/magic.mgc", O_RDONLY) = -1 ENOENT (No such file or directory)
    openat(AT_FDCWD, "/etc/magic", O_RDONLY) = 3</etc/magic>
    openat(AT_FDCWD, "/usr/share/misc/magic.mgc", O_RDONLY) = 3</usr/lib/file/magic.mgc>
    openat(AT_FDCWD, "/usr/lib/x86_64-linux-gnu/gconv/gconv-modules.cache", O_RDONLY) = 3</usr/lib/x86_64-linux-gnu/gconv/gconv-modules.cache>
    openat(AT_FDCWD, ".bashrc", O_RDONLY|O_NONBLOCK) = 3</home/vagrant/.bashrc>
    .bashrc: ASCII text
    +++ exited with 0 +++

    ```  
    *видимо это файл `/usr/lib/file/magic.mgc`*  
    
    ```bash  
    
    vagrant@vagrant:~$ file /usr/lib/file/magic.mgc
    /usr/lib/file/magic.mgc: magic binary file for file(1) cmd (version 14) (little endian)
    
    ```
    
    
    

3. Предположим, приложение пишет лог в текстовый файл. Этот файл оказался удалён (deleted в lsof), но сказать сигналом приложению переоткрыть файлы или просто перезапустить приложение возможности нет. Так как приложение продолжает писать в удалённый файл, место на диске постепенно заканчивается. Основываясь на знаниях о перенаправлении потоков, предложите способ обнуления открытого удалённого файла, чтобы освободить место на файловой системе.
```bash
    lsof - опеределяем FD и PID для этого файла
    далее
    cat /dev/null > /proc/PID/fd/FD
```

```bash
    в параллельной сесии открыл на редактирвоание файл /home/vagrant/test_touch/some в редакторе vi
    
    в текущей сессии
    lsof -c vi
    
    COMMAND  PID    USER   FD   TYPE DEVICE SIZE/OFF    NODE NAME
    ......
    vi      7315 vagrant    4u   REG  253,0    12288 1310809 /home/vagrant/test_touch/.some.swp
    
    cat /dev/null > /proc/7315/fd/4
    
    lsof -c vi
    
    vi      7315 vagrant    4u   REG  253,0        0 1310809 /home/vagrant/test_touch/.some.swp
    
    ```
    

1. Занимают ли зомби-процессы ресурсы в ОС (CPU, RAM, IO)?
1. В IO Visor BCC есть утилита `opensnoop`:

    ```bash
    root@vagrant:~# dpkg -L bpfcc-tools | grep sbin/opensnoop
    /usr/sbin/opensnoop-bpfcc
    ```
    
    На какие файлы вы увидели вызовы группы `open` за первую секунду работы утилиты? Воспользуйтесь пакетом `bpfcc-tools` для Ubuntu 20.04. Дополнительные сведения по установке [по ссылке](https://github.com/iovisor/bcc/blob/master/INSTALL.md).

1. Какой системный вызов использует `uname -a`? Приведите цитату из man по этому системному вызову, где описывается альтернативное местоположение в `/proc` и где можно узнать версию ядра и релиз ОС.

1. Чем отличается последовательность команд через `;` и через `&&` в bash? Например:

    ```bash
    root@netology1:~# test -d /tmp/some_dir; echo Hi
    Hi
    root@netology1:~# test -d /tmp/some_dir && echo Hi
    root@netology1:~#
    ```
    
    Есть ли смысл использовать в bash `&&`, если применить `set -e`?

1. Из каких опций состоит режим bash `set -euxo pipefail`, и почему его хорошо было бы использовать в сценариях?

1. Используя `-o stat` для `ps`, определите, какой наиболее часто встречающийся статус у процессов в системе. В `man ps` изучите (`/PROCESS STATE CODES`), что значат дополнительные к основной заглавной букве статуса процессов. Его можно не учитывать при расчёте (считать S, Ss или Ssl равнозначными).

----

