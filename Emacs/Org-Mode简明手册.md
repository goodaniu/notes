# Org-Mode简明手册

笔记最近打算由 `markdown` 迁移到 `org-mode` 上来了，所以特意去学习下 `org-mode` 的语法和命令。

## Org Mode文件结构

Org是基于Outline模式的，中文的意思就是大纲。大纲模式可以让我们用层次结构来组织文档，这种结构可以折叠隐藏文档的一部分而只显示文档的大概结构或者只显示我们正在处理的部分，比较适合笔记的浏览。Org专门提供了个命令： `org-cycle` ，显示或隐藏大纲，如果是使用 *spacemacs* 的话，这个命令绑定到 `TAB` 键上。

### 视图循环

当Emacs打开org文件的时候，全局查看的状态是 `OVERVIEW` 的，也就是说只有顶层的标题栏可见，不过可见性设置可以通过 `org-startup-folded` 变量来设置。或者在org文件声明：

```
#+STARTUP: content
```

### 标题移动

如果是使用 `spacemacs` 的话下面的命令可以跳转到缓冲区其它的标题处：

| 快捷键 | 描述                          |
|--------+-------------------------------|
| gj     | 下个同级Title                 |
| gk     | 上个同级Title                 |
| gh     | 上一个Title                   |
| gl     | 下一个Title                   |
| t      | 创建一个Todo Title            |
| T      | 将目前所在Title改为Todo Title |
| H      | 所在可视范围第一行            |
| L      | 所在可视范围最后一行          |

### 标题升降级

| 快捷键    | 描述            |
|-----------+-----------------|
| SPC m S l | 把Title降一级   |
| SPC m S h | 把Title升一级   |
| SPC m S k | 往上移动Title树 |
| SPC m S j | 往下移动Title树 |

## 基本语法

### 标题

标题定义了大纲树的结构， 它以处于一行左边缘的一个或多个星号开头：

```
* Top level headline
** Second level
*** 3rd level
    some text
*** 3rd level
    more text

* Another top level headline
```

### 列表

Org支持有序列表、无序列表和描述列表：

- 无序列表项以 -、 + 或者 * 开头。
- 有序列表项以 1. 、 1) 开通。
- 描述列表用 :: 将项和描述分开。

同一列表中的项的第一行必须缩进相同程度。当下一行的缩进与列表项的的开头的符号或者数字相同或者更小时，这一项就结束了。当所有的项都关上时，或者后面有两个空行时，列表就结束了。例如： 

```
** Lord of the Rings
   My favorite scenes are (in this order)
   1. The attack of the Rohirrim
   2. Eowyn's fight with the witch king
       + this was already my favorite scene in the book
       + I really like Miranda Otto.
   Important actors in this film are:
   - Elijah Wood :: He plays Frodo
   - Sean Austin :: He plays Sam, Frodo's friend.
```

### 脚注

脚注就是以脚注定义符号开头的一段话，脚注定义符号是将脚注名称放在一个方括号里形成的，要求放在第0列，不能有缩进。而引用就是在正文中将脚注名称用方括号括起来。例如：

```
The Org homepage[fn:1] now looks a lot better than it used to.

[fn:1] The link is: http://orgmode.org
```

### 表格

Org也提供了表格功能，这个和原来的Markdown表格扩展的语法差不多。

### 超链接

### 文档结构元素

#### 文档标题 

导出文件的标题在特定行给出： 

```
#+TITLE: This is the title of the document
```

#### 标题和章节

第二章描述的大纲结构确定了导出文档的结构基础。然而由于大纲结构也用于（比如说）列表和任务，因此只有前三个级别用作标题。更深的级别会被看作项目列表。你可以通过变量 org-export-headline-levels 在全局设置这个开关，或者只是在单个文件中设置：

```
#+OPTIONS: H:4
```

#### 目录表

目录表通常会直接插入在文档第一个标题之前。 

```
#+OPTIONS: toc:2 (目录中只显示二级标题)
#+OPTIONS: toc:nil (无目录)
```

#### 源代码

Org可以很简洁的显示笔记中的代码块，虽然不如Markdown =_+。

```
#+begin_src
#+end_src
```

像上面在 `begin` 和 `end` 之间包裹代码既可。
同时，Org支持代码高亮，只需在`#+begin_src`后既可。

#### 引用

Org的引用文字也是使用结构标识符来声明的：

```
#+begin_quote
#+end_quote
```

## Org-Capture

Org-Mode可以开启一个临时Buffer来记录突发的灵感，这个功能就是 `Org-Capture` ，我们可以这样设置 `Org-Capture` 的模板：

```
(setq org-capture-templates
        `(("n" "随笔" entry (file+headline "~/Documents/notes/org/notes.org" "Kesco的随笔")
           "* %?\n\n记录于%U\n %i\n %a" :prepend t :empty-lines 1)
          ("t" "待办事项" entry (file+headline "~/Documents/notes/org/todo.org" "待办事项")
           "* TODO %?\n%U  %i" :prepend t)
          ("j" "Journal" entry (file+datetree "~/Documents/notes/org/finished.org" "Journals")
           "* %?\n %T  %i")
          ))
```

在扩展模板时，可以用%转义符进行动态地插入内容。

| 转移符 | 描述                                     |
|--------+------------------------------------------|
| %a     | 注解，通常是由 org-store-link 创建的链接 |
| %i     | 初始化内容，当记忆时区域被C-u调用        |
| %t     | 时间戳，只是日期                         |
| %T     | 带有日期和时间的时间戳                   |
| %u，%U | 同上，但是时间戳不激活                   |
| %?     | 载入模板后，光标的位置                   |
