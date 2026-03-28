# 常用指令
- chmod + 权限 + 文件 (修改文件权限)
	- 权限是所有者，当前组，其他用户
	- chmod 7 7 7 first.sh
	- 111->r(**r**ead)w(**w**rite)x(e**x**ecute)
- ls(列出当前文件夹下所有文件名称)
- ll(列出当前文件夹下所有文件信息)
- echo(重复echo后面的内容）
- cat
- rm/rmdir(remove/remove directory)
- mkdir(make directory)
- locate（寻找文件）
- .. : 上一级文件夹
- pwd: 显示当前文件夹路径

- if的`[ condition ]`必须有空格
- expr的运算符，也必须有空格`expr 1 + 2 + $a + $b`
- 字符比较是`[[ name == "123" ]]`
- 数字比较看下表

| 符号  | Shell 表达              |
| --- | --------------------- |
| >   | -gt(greater then)     |
| <   | -lt(less then)        |
| >=  | -ge(greater or equal) |
| <=  | -le(less or equal)    |
| ==  | -eq(equal)            |
| !=  | -ne                   |
- 运算时要使用\`expr\`


- readarray : 按行读取文件
- for word in (readarray): 按字符读取当前行内的单词（按空格分开）
- hh hh hh hh hh(hh)
