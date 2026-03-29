# Linux命令
- `ls` : 列出**当前目录**下所有文件
- `ll` : 列出**当前目录**下所有文件以及*文件权限信息，创建时间，所有者*等
- `rm/rmdir + 文件/文件夹名称`: 删除文件/删除文件夹（空的）
	- 删除**有东西**的文件夹使用 `rm -r + 文件夹名称`
- `mkdir` : 创建文件夹
- `who` : 显示当前登录用户名称
- `pwd` : 显示当前所在目录
	- `/root/bxy/I/Love/You`这样
- `echo + 输出内容` : 输出输出内容
	- 可以这样书写`echo`: `echo | pwd` 即打印 `|`后面命令执行(execute)完成的结果到控制台
	- 还可以使用`>`重定向(redirect)到文件: `echo 文本 > 文件名称`（会覆盖(overwrite)文件）
		- 使用`>>`追加(append)到文件最后一行（不覆盖文件）

# Shell语法
## 变量
### 特殊变量
- `$#`: 控制台(console)传入参数（positional parameter）数量
```bash
gq2gbb@szamrend:~/test$ bash.sh hello <-控制台传入参数
```
- `$0`: 文件名称
- `$1`: 第一个传入参数(parameter)
- `$2....`: 第N个传入参数（不列举了）

### 普通变量
#### 赋值
```bash
var=0
```
`var`和`=`和`0`之间**必须没有空格**

#### 取值
```
$var
```
#### 比较数值运算
| 符号  | Shell 表达              |
| --- | --------------------- |
| >   | -gt(greater then)     |
| <   | -lt(less then)        |
| >=  | -ge(greater or equal) |
| <=  | -le(less or equal)    |
| ==  | -eq(equal)            |
| !=  | -ne                   |
比较字符串还是使用传统的 `==`

### 数组
```shell
arr=()      #（创建数组）
arr[$index] #访问数组（从0开始）
```

### 循环
#### for
```shell
for ele in arr
do
	#操作
done
```

for循环会读取列表/数组中每个元素到ele中，也会把只有空格分隔的字符串中的单词读入ele中

```shell
arr=(1,2,3,4,5)
for ele in arr
do
	echo ele
done
#输出1
#输出2
#输出3
#输出4
#输出5

str="你 好 漂 亮 啊"
for word in str
do
	echo ele
done
#输出  你
#输出  好
#输出  漂
#输出  亮
#输出  啊
```

#### while
```shell
while (条件)
do
done
```

### 分支
```shell
if [ 条件 ]
then

elif [[ 比较字符串 ]]
then

else

fi
```
- 只有带if的`if/elif`需要在下面写begin
- 在最后结尾的时候要写`fi`结束分支(branch)
- 条件必须和`[]`之间有空格，且变量和比较运算符之间也必须有空格
```shell
if [ $var -eq 0 ]
```


### expr
- `expr`中的`*`需要写成`\*`，因为`*`在操作系统里表示当前情况下所有文件的意思，所以需要一个斜杠(slash)区分一下，**注意斜杠不要写在`*`后面**

# 写完代码
- 第一件事`chmod 777 文件名称`
	- 第一个对应当前用户
	- 第二个对应当前组内用户
	- 第三个对应其他用户
		- 二进制上的三个位`000 -> 111(7十进制)`
			- 第一个0是读
			- 第二个0是写
			- 第三个0是运行(execute)