扩展
======

路径名扩展
----------

列出大写开头的文件列表，包括目录名。

.. code-block:: bash

    $ ls -d [[:upper:]]*
    Desktop Documents Downloads Pictures

路径名扩展不会包含隐藏文件。

.. code-block:: bash

    $ ls -d *
    Desktop Documents Downloads Pictures bin code doc icon

如果要显式所有隐藏文件(包括目录)，需要显式过滤掉 ``.`` 和 ``..`` ，并且 ``.`` 后面的文件名至少包含一个字符。

.. code-block:: bash

    $ ls -d .[!.]?*
    .vim .vimrc .bash_history .bashrc

其中， ``ls -d`` 显示目录名，而不进入目录进行罗列。

波浪线扩展
-----------

- ``~``: ``$HOME`` 主目录
- ``~horance``: ``horance`` 用户的主目录
- ``~+`` : 等价于 ``$PWD`` 当前目录
- ``~-`` : 等价于 ``$OLDPWD`` 上一个当前目录；如果 ``$OLDPWD`` 为空，则显式~-

.. code-block:: bash
    
    $ echo ~horance
    $ echo ~
    $ echo $HOME
    $ echo ~+   
    $ echo ~-

算术扩展
---------

支持整数的算术运算: ``$((exp))``

.. code-block:: bash
    
    $ MAX=2
    $ echo $(( MAX*3 ))   # 无需使用$引用变量


花括号扩展
-----------

批量创建子目录:

.. code-block:: bash
    
    $ mkdir -p cub/{include,src,test}
    $ mkdir -p date/{2000..2020}-{01..09}

备份文件，后缀名追加 ``.bak``

.. code-block:: bash

    $ cp /etc/apt/sources.list{,bak}

在大括号中, 不允许有空白, 除非这个空白被引用或转义。

.. code-block:: bash

    $ echo file{1,2}{\ A," B",' C'}

参数扩展
----------

打印PATH条目，每个目录一行：

.. code-block:: bash

    $ echo $PATH | tr ':' '\n'


追加PATH搜索路径：

.. code-block:: bash

    $ export PATH="$HOME/.cargo/bin:$PATH"


当存在上下文歧义时，需要使用 ``${var}`` 引用变量的值。

.. code-block:: bash

    #! /bin/bash

    for fn in 1 2 3 4 5; do
        touch /tmp/rep${fn}port.txt    # 否则，将引用$fnport变量的值
    done

    rm -r /tmp/rep{1..5}port.txt

引用数组元素时，必须显式指定 ``${arr[*|id]}`` 。

.. code-block:: bash

    #! /bin/bash
    
    nums=(1,2,3,4,5)
    
    for fn in ${nums[*]}; do
        touch /tmp/rep${fn}port.txt    # 否则，将引用$fnport变量的值
    done

    rm -r /tmp/rep{1..5}port.txt

命令替换
----------

- ``$(cmd)``
- ```cmd```

``$(cmd)`` 将在子Shell中执行命令，并捕获其输出。例如：

.. code-block:: bash

    $ ls -d /usr/bin/* | grep zip
    /usr/bin/bzip2
    /usr/bin/gzip
    /usr/bin/unzip
    /usr/bin/zip

然后，使用 ``file`` 获取文件类型。

.. code-block:: bash

    $ file $(ls -d /usr/bin/* | grep zip)
    /usr/bin/bzip2:          Mach-O 64-bit executable x86_64
    /usr/bin/gzip:           Mach-O 64-bit executable x86_64
    /usr/bin/unzip:          Mach-O 64-bit executable x86_64
    /usr/bin/zip:            Mach-O 64-bit executable x86_64

历史扩展
----------

.. code-block:: bash

    $ history | grep "find"
    798  2020-10-01 11:59:34 find . -name "*.log" -print0

.. code-block:: bash

    $ !978   # 等价于执行 find . -name "*.log" -print0

其中， ``!!`` 引用上次执行的命令：

.. code-block:: bash

    $ !!

历史扩展在脚本中被禁止的，仅用于交互式环境。
