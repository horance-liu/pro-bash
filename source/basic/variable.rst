变量
=======

变量定义
-----------

变量定义的行为： ``var=val`` ，其中 ``=`` 两侧不允许有空格。


变量引用
-----------

形式1：$var
^^^^^^^^^^^^^^^^^^^^^

除下划线，字母和数字之外，其他分词符都可以充当合法的分词符。例如，空格，``-`` , ``/`` 等。

.. code-block: bash

    $ name=$hostname-$(git config user.name)

形式2：${var}
^^^^^^^^^^^^^^^^^^^^^

当存在上下文歧义时，需要使用 ``${var}`` 显式引用变量。

.. code-block: bash

    #! /bin/bash

    for file in "$@"; do
        touch "/tmp/rep${file}port.txt"
    done

此处需要显示使用 ``${file}`` ，否则将引用$fnport变量的值。

形式3："${var}"
^^^^^^^^^^^^^^^^^^^^^

当变量扩展后，变量值包含宫格，则需要双引用将其引用。

.. code-block: bash

    #! /bin/bash

    for file in "$@"; do
        touch "${file}"
    done


数组引用
---------

数组引用必须显示使用 ``${arr[id|*|@]}`` 大括号引用。

形式1：${array[id]}
^^^^^^^^^^^^^^^^^^^^^

形式2：${array[*]}
^^^^^^^^^^^^^^^^^^^^^

形式3：${array[@]}
^^^^^^^^^^^^^^^^^^^^^


查看变量
---------------

- env

.. code-block: bash

    $ env | grep "^PATH"

- set

- declare -p

位置参数
-----------

$* 与 $@
^^^^^^^^^^^

- $*: $1 $2 $3 ... ${N}
- $@: $1 $2 $3 ... ${N}
- "$*": "$1c$2c$3c ... ${N}", where c is the first character of $IFS
- "$@": "$1" "$2" "$3" ... "${N}"

.. code-block: bash

    $ $IFS


$#：参数个数
^^^^^^^^^^^^^^^

$?：命令状态
^^^^^^^^^^^^^^^

$?：命令状态
^^^^^^^^^^^^^^^

参数传递
---------

传递包含空格的参数
^^^^^^^^^^^^^^^^^^

一般地，使用位置参数表示传递参数。当参数包含空格时，因为分词导致错误的结果。

.. code-block:: bash

    $ cat ll-oh-my-bash.sh  
    #! /bin/bash
    ls -l ${1}

    $ ./ll-oh-my-bash.sh Oh My Bash 
    ls: Oh: No such file or directory 

因此，需要将 ``Oh My Bash`` 进行双引用。但是，``ls -l ${1}`` 展开后等价于 ``ls -l Oh My Bash`` ，此时传递了三个参数。

.. code-block:: bash

    $ cat ll-oh-my-bash.sh  
    #! /bin/bash
    ls -l ${1}

    $ ./ll-oh-my-bash.sh "Oh My Bash"
    ls: Oh: No such file or directory 
    ls: My: No such file or directory 
    ls: Bash: No such file or directory

因此，在传递给 ``ls`` 之前，也使用 ``"${1}"`` 将其引用作为整体进行传递。

.. code-block:: bash
    
    $ cat ll-oh-my-bash.sh
    #! /bin/bash
    ls -l "${1}"     # note the quotes 
    
    $ ./ll-oh-my-bash.sh "Oh My Bash" 
    -rw-r--r-- 1 smith users 28470 2007-01-11 19:22 Oh My Bash


传递包含空格的参数序列
^^^^^^^^^^^^^^^^^^^^^^^^^


假如当前目录下，包含如下三个 ``mp3`` 文件。

.. code-block: bash
    
    $ ls -1 *.mp3
    vocals.mp3 
    cool music.mp3 
    tophit.mp3

如果使用 ``$*`` 引用传递的参数列表：

.. code-block: bash
    
    $ cat chmod-all.sh
    #!/bin/bash 

    for file in $*; do
        chmod 0750 "$file" 
    done

    $ ./chmod-all.sh *.mp3

则错误地扩展为：

.. code-block: bash
    
    for file in vocals.mp3 cool music.mp3 tophit.mp3

如果将 ``$*`` 实施引用，则错误地扩展为：

.. code-block: bash
    
    for file in "$*"
    
    for file in "vocals.mp3 cool music.mp3 tophit.mp3"

正确的做法，应该将 ``$@`` 引用：
    
.. code-block: bash
    
    $ cat chmod-all.sh
    #!/bin/bash 

    for file in "$@"; do
        chmod 0750 "$file"
    done

    $ ./chmod-all.sh *.mp3

此时扩展为：

.. code-block: bash

    for file in "vocals.mp3" "cool music.mp3" "tophit.mp3"; do
        chmod 0750 "$file" 
    done

需要注意的是， ``"$file"`` 的引用是必须的，包含空格的文件名扩展为：

.. code-block: bash

    chmod 0750 "cool music.mp3"

否则将错误地扩展为：

.. code-block: bash

    chmod 0750 cool music.mp3
