## 配置

启动之后ranger会创建一个目录`~/.config/ranger` 可以使用以下命令复制默认配置文件到这个目录

```text
ranger --copy-config=all
```

- `rc.conf`-选项设置和快捷键
- `commands.py`-能通过`:`执行的命令
- `commands_full.py`-全套命令
- `rifle.conf`-指定不同类型的文件的默认打开程序
- `scope.conf`-负责各种文件预览

注意：如果要使用`~/.config/ranger`目录下的配置生效，需要把`RANGER_LOAD_DEFAULT_RC`变量设置为`false`

```text
bash
echo "export RANGER_LOAD_DEFAULT_RC=false">>~/.bashrc

zsh
echo "export RANGER_LOAD_DEFAULT_RC=false">>~/.zshrc
```

> 可选配置(推荐)

修改配置文件`~/.config/ranger/rc.conf`

- 显示边框`set draw_borders both`
- 显示序号`set line_numbers true`
- 序号从1开始`set one_indexed true`

> 图片预览，需要安装`imgcat`

安装`imgcat`

```text
# 安装
sudo wget -O /usr/local/bin/imgcat https://iterm2.com/utilities/imgcat

#为文件添加权限
sudo chmod 777 /usr/local/bin/imgcat
```

## 快捷键

在浏览时常用的快捷键

```text
S   切换到ranger最后浏览的目录
zh/退回键  显示隐藏文件
H   后退
L   前进
gg  跳到顶端
G   跳到底端
gh  go home
gn  新建标签(tab键切换标签)
f   查找(如果只有一个匹配结果会直接打开该目录或文件)
/   搜索
g   快速进入目录
```

这些快捷键都是与`vim`的操作一样

```text
yy      复制
dd      剪切
pp      粘贴
dD      删除（需要回车键确认）
cw      重命名
A       在当前名称基础上重命名
I       类似A, 但是光标会跳到起始位置
Ctrl-f  向下翻页
Ctrl-b  向上翻页
```

书签

```text
m       新建书签
`       打开书签
um      删除书签
```

标签

```text
gn / Ctrl-n        新建标签
TAB / Shift-TAB     切换标签
gc / Ctrl-w        关闭标签
```

文件排序 :

```text
on/ob   根据文件名进行排序(natural/basename)
oc      根据改变时间进行排序 (Change Time 文件的权限组别和文件自身数据被修改的时间)
os      根据文件大小进行排序(Size)
ot      根据后缀名进行排序 (Type)

oa      根据访问时间进行排序 (Access Time 访问文件自身数据的时间)
om      根据修改进行排序 (Modify time 文件自身内容被修改的时间)
```****