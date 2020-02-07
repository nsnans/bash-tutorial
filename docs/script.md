# Bash 脚本

脚本（script）就是包含一系列命令的一个文件。Shell 读取这个文件，依次执行文件中的所有命令，就好像这些命令直接输入到命令行一样。所有能够在命令行中完成的任务，也能够用脚本来实现。

脚本的好处是可以重复使用，也可以指定在特定场合自动调用，比如系统启动或关闭时。

## Shebang 行

脚本的第一行通常约定是指定解释器，即这个脚本必须通过什么解释器执行。

指定解释器的这一行以`#!`字符开头，这个字符称为 Shebang，所以这一行就叫做 Shebang 行。在`#!`后面就是脚本解释器的位置，Bash 脚本的解释器一般是`/bin/sh`。

```bash
#!/bin/sh
```

如果用户的 Bash 可执行文件不是`/bin/sh`，脚本就无法执行了。为了保险，可以写成下面这样。

```bash
#!/usr/bin/env bash
```

上面命令使用`/usr/bin/env`命令，返回 Bash 可执行文件的位置。`env`命令的详细介绍，请看后文。

每个脚本都应包含一个 Shebang 行。如果缺少该行，就需要手动调用解释器。举例来说，脚本是`script.sh`，有 Shebang 行的时候，可以直接调用执行。

```bash
$ ./script.sh
```

上面例子中，`script.sh`是脚本文件名。脚本通常使用`.sh`后缀名，不过这不是必需的。

如果没有 Shebang 行，就只能手动调用解释器执行。

```bash
$ /bin/sh ./script.sh
# 或者
$ bash ./script.sh
```

## 执行权限和路径

前面说过，通过 Shebang 行指定解释器的脚本，可以直接执行。这有一个前提条件，就是脚本需要有执行权限。可以使用下面的命令，赋予脚本执行权限。

```bash
# 给所有用户执行权限
$ chmod +x script.sh

# 给所有用户读权限和执行权限
$ chmod +rx script.sh
# 或者
$ chmod 555 script.sh

# 只给脚本拥有者读权限和执行权限
$ chmod u+rx script.sh
```

除了执行权限，脚本调用时，一般需要指定脚本的路径。如果将脚本放在环境变量`$PATH`指定的目录中，就不需要指定路径了。因为 Bash 会自动到这些目录中，寻找是否存在同名的可执行文件。

建议在主目录新建一个`~/bin`子目录，专门存放可执行脚本，然后把`~/bin`加入`$PATH`。

```bash
export PATH=$PATH:~/bin
```

上面命令改变环境变量`$PATH`，将`~/bin`添加到`$PATH`的末尾。可以将这一行加到`~/.bashrc`文件里面，然后重新加载一次`.bashrc`，这个配置就可以生效了。

```bash
$ source ~/.bashrc
```

以后不管在什么目录，直接输入脚本文件名，脚本就会执行。

```bash
$ script.sh
```

上面命令没有指定脚本路径，因为`script.sh`在`$PATH`指定的目录中。

## env 命令

`env`命令总是指向`/usr/bin/env`文件。`#!/usr/bin/env NAME`这种语法的意思是，让 Shell 查找`$PATH`环境变量里面第一个匹配的`NAME`。如果你不知道某个命令的路径，这样的写法就很有用。`/usr/bin/env bash`的意思就是，返回`bash`可执行文件的位置，前提是`bash`的路径是在`$PATH`里面。

其他脚本文件也可以使用这个命令。比如 Node.js 脚本的 Shebang 行，可以写成下面这样。

```bash
#!/usr/bin/env node
```

`env`命令的参数如下。

- `-i`, `--ignore-environment`：不带环境变量启动
- `-u`, `--unset=NAME`：从环境变量中删除一个变量
- `--help`：显示帮助
- `--version`：输出版本信息

下面是一个例子，新建一个不带任何环境变量的Shell。

```bash
$ env -i /bin/sh
```

## 注释

Bash 脚本中，`#`表示注释。

```bash
# 本行是注释
echo 'Hello World!'

echo 'Hello World!' # 井号后面的部分也是注释
```

建议在脚本开头，使用注释说明当前脚本的作用，这样有利于日后的维护。

## 脚本的调试

脚本执行过程中，有时为了方便调试，需要显示当前执行的命令。这时可以使用`bash`命令的`-x`参数，该参数在执行每一条命令时，都会将该命令先打印出来。

下面是一个脚本`script.sh`。

```bash
# script.sh
echo hello world
```

加上`-x`参数，执行每条命令之前，都会显示该命令。

```bash
$ bash -x script.sh
+ echo hello world
hello world
```

上面例子中，行首为`+`的行，显示该行是所要执行的命令，下一行才是该命令的执行结果。

## 脚本参数

调用脚本的时候，脚本文件名后面可以带有参数。

```bash
$ script.sh word1 word2 word3
```

上面例子中，`script.sh`是一个脚本文件，`word1`、`word2`和`word3`是三个参数。

脚本文件内部，可以使用特殊变量，引用这些参数。

- `$0`：脚本文件名，即`script.sh`
- `$1`：第一个参数，即`word1`
- `$2`：第二个参数，即`word2`
- `$3`：第三个参数，即`word3`
- `$4`～`$9`：第四个到第九个参数，本例没有
- `$#`：参数的总数
- `$@`：全部的参数

下面是一个脚本内部读取命令行参数的例子。

```bash
#!/bin/bash

echo "全部参数：" $@
echo "命令行参数数量：" $#
echo '$0 = ' $0
echo '$1 = ' $1
echo '$2 = ' $2
echo '$3 = ' $3
```

执行结果如下。

```bash
$ script.sh a b c
全部参数：a b c
命令行参数数量：3
$0 =  script.sh
$1 =  a
$2 =  b
$3 =  c
```

用户可以输入任意数量的参数，利用`for`循环，可以读取每一个参数。

```bash
#!/bin/bash

for i in "$@"; do
  echo $i
done
```

上面例子中，`$@`返回一个全部参数的列表，然后使用`for`循环遍历。

`while`循环结合`shift`命令，也可以读取每一个参数。

```bash
#!/bin/bash

echo "一共输入了 $# 个参数"

while [ "$1" != "" ]; do
    echo "剩下 $# 个参数"
    echo "参数：$1"
    shift
done
```

上面例子中，`shift`命令的作用是移除当前第一个参数，即`$2`变成`$1`、`$3`变成`$2`、`$4`变成`$3`，以此类推。

## 别名

`alias`命令用来为一个命令指定别名。

```bash
alias NAME=DEFINITION
```

上面命令中，`Name`是别名的名称，`DEFINITION`是别名对应的原始命令。

下面的例子是指定`ls -ltr`命令的别名为`lt`。

```bash
$ alias lt='ls -ltr'
```

指定别名以后，就可以像使用其他命令一样使用别名。一般来说，都会把常用的别名写在`~/.bashrc`的末尾。

下面是通过别名定义`today`命令的写法。

```bash
$ alias today='date +"%A, %B %-d, %Y"'
$ today
星期一, 一月 6, 2020
```

有时为了防止误删除文件，可以指定`rm`命令的别名。

```bash
$ alias rm='rm -i'
```

上面命令指定`rm`命令是`rm -i`，每次删除文件之前，都会让用户确认。

直接调用`alias`命令，可以显示所有别名。

```bash
$ alias
```

`unalias`命令可以解除别名。

```bash
$ unalias lt
```

## 函数

别名只适合封装简单的单个命令，如果要封装复杂的多行命令，就需要函数。

Bash 允许自定义函数，便于代码的复用。函数定义的语法如下。

```bash
fn() {
  # codes
}
```

上面命令中，`fn`是自定义的函数名，函数代码就写在大括号之中。

下面是一个简单函数的例子。

```bash
hello() { echo "Hello $1"; }
```

上面代码中，函数体里面的`$1`表示命令行的第一个参数。

调用方法如下。

```bash
$ hello world
hello world
```

下面是显示当前日期时间的函数。

```bash
today() {
  echo -n "Today's date is: "
  date +"%A, %B %-d, %Y"
}
```

这个函数可以写在脚本文件里面，调用的时候直接写函数名即可。

```bash
$ today
```

函数里面可以用`local`命令声明局部变量。

```bash
funct_1 () {
    local foo  # variable foo local to funct_1
    foo=1
    echo "funct_1: foo = $foo"
}
```

## exit 命令

`exit`命令用于终止当前脚本的执行，并向 Shell 返回一个退出值。

```bash
$ exit
```

上面命令中止当前脚本，将最后一条命令的退出状态，作为整个脚本的退出状态。

`exit`命令后面可以跟参数，该参数就是退出状态。

```bash
# 退出值为0（成功）
$ exit 0

# 退出值为1（失败）
$ exit 1
```

退出时，脚本会返回一个退出值。脚本的退出值，`0`表示正常，`1`表示发生错误，`2`表示用法不对，`126`表示不是可执行脚本，`127`表示命令没有发现。如果脚本被信号`N`终止，则退出值为`128 + N`。简单来说，只要退出值非0，就认为执行出错。

下面是一个例子。

```bash
if [ $(id -u) != "0" ]; then
  echo "根用户才能执行当前脚本"
  exit 1
fi
```

上面的例子中，`id -u`命令返回用户的 ID，一旦用户的 ID 不等于`0`（根用户的 ID），脚本就会退出，并且退出码为`1`，表示运行失败。

上一条命令的退出值，可以用系统变量`$?`查询。使用这个命令，可以知道上一条命令是否执行成功。

`exit`与`return`命令的差别是，`return`命令是函数的退出，并返回一个值给调用者，脚本依然执行。`exit`是整个脚本的退出，如果在函数之中调用`exit`，则退出函数，并终止脚本执行。

## read 命令

有时，脚本需要用户输入参数，这时可以使用`read`命令。它将用户的输入存入一个参数变量，方便后面的代码使用。

下面是一个例子`demo.sh`。

```bash
#!/bin/bash

echo -n "输入一些文本 > "
read text
echo "你的输入：$text"
```

上面例子中，先显示一行提示文本，然后会等待用户输入文本。用户输入的文本，存入变量`text`，在下一行显示出来。

```bash
$ bash demo.sh
输入一些文本 > 你好，世界
你的输入：你好，世界
```

`read`命令的参数，就是保存用户输入内容的变量名。如果省略了`read`命令的参数，用户输入的内容会保存在环境变量`REPLY`。

`read`可以接受用户输入的多个值。

```bash
#!/bin/bash
echo Please, enter your firstname and lastname
read FN LN
echo "Hi! $LN, $FN !"
```

上面例子中，`read`根据用户的输入，同时为两个变量赋值。

`read`命令的`-t`参数，设置了超时的秒数。如果超过了指定时间，用户仍然没有输入，脚本将放弃等待，继续向下执行。

```bash
#!/bin/bash

echo -n "输入一些文本 > "
if read -t 3 response; then
  echo "用户已经输入了"
else
  echo "用户没有输入"
fi
```

上面例子中，输入命令会等待3秒，如果用户超过这个时间没有输入，这个命令就会执行失败。`if`根据这个返回码，转入`else`代码块，继续往下执行。

`-s`参数使得用户的输入不显示在屏幕上，这常常用于输入密码或保密信息。

## 命令执行结果

命令执行结束后，会有一个返回值。`0`表示执行成功，非`0`（通常是`1`）表示执行失败。

利用这一点，可以在脚本中对命令执行结果进行判断。

```bash
cd $some_directory
if [ "$?" = "0" ]; then
  rm *
else
  echo "无法切换目录！" 1>&2
  exit 1
fi
```

上面例子中，`cd $some_directory`这个命令如果执行成功（返回值等于`0`），就删除该目录里面的文件，否则退出脚本，整个脚本的返回值变为`1`，表示执行失败。

由于`if`可以判断命令的执行结果，执行相应的操作，上面的脚本可以用`if`命令改写成下面的样子。

```bash
if cd $some_directory; then
  rm *
else
  echo "Could not change directory! Aborting." 1>&2
  exit 1
fi
```

更简洁的写法是利用两个逻辑运算符`&&`（且）和`||`（或）。

```bash
# 第一步执行成功，才会执行第二步
cd $some_directory && rm *

# 第一步执行失败，才会执行第二步
cd $some_directory || exit 1
```
