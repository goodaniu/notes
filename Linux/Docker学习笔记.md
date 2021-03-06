# Docker使用笔记

## Docker Image和Container的区别

### 使用Dockerfile创建新镜像

`Docker` 可以通过 `Dockerfile` 脚本来自动构建镜像。 `Dockerfile` 是一个包含创建镜像所有命令的文本文件，通过 `docker build` 命令可以根据 `Dockerfile` 的内容构建镜像。

### Dockerfile语法

Dockerfile的命令语法比较简单，有以下几个指令：

- FROM
- MAINTAINER
- RUN
- CMD
- EXPOSE
- ENV
- ADD
- COPY
- ENTRYPOINT
- VOLUME
- USER
- WORKDIR
- ONBUILD

#### FROM

用法：

```
FROM <image>
```

- FROM 指定构建镜像的基础源镜像，如果本地没有指定的镜像，则会自动从 Docker 的公共库 pull 镜像下来。
- FROM 必须是 Dockerfile 中非注释行的第一个指令，即一个 Dockerfile 从 FROM 语句开始。
- FROM 可以在一个 Dockerfile 中出现多次，如果有需求在一个 Dockerfile 中创建多个镜像。
- 如果 FROM 语句没有指定镜像标签，则默认使用 latest 标签。

#### MAINTAINER

用指定维护者信息，用法： 

```
MAINTAINER <name>
```

#### RUN

用法：

```
RUN <command>
```

或者：

```
RUN ["executable", "param1", "param2"]
```

前者将在 `shell` 终端中运行命令，即 `/bin/sh -c` ；后者则使用 `exec` 执行。指定使用其它终端可以通过第二种方式实现，例如 `RUN ["/bin/bash", "-c", "echo hello"]` 。当命令较长时可以使用 `\` 来换行。

每条 `RUN` 指令将在当前镜像基础上执行指定命令，并提交为新的镜像，后续的 `RUN` 都在之前 `RUN` 提交后的镜像为基础，镜像是分层的，可以通过一个镜像的任何一个历史提交点来创建，类似源码的版本控制。

`RUN` 产生的缓存在下一次构建的时候是不会失效的，会被重用，可以使用 `--no-cache` 选项，即 `docker build --no-cache` ，如此便不会缓存。

#### CMD

`CMD` 有三种使用方式：

- `CMD [“executable”,”param1”,”param2”] (exec form, this is the preferred form, 优先选择)`
- `CMD [“param1”,”param2”] (as default parameters to ENTRYPOINT)`
- `CMD command param1 param2 (shell form)`

`CMD` 指定在 `Dockerfile` 中只能使用一次，如果有多个，则只有最后一个会生效。

`CMD` 的目的是为了在启动容器时提供一个默认的命令执行选项。如果用户启动容器时指定了运行的命令，则会覆盖掉 `CMD` 指定的命令。

```
*CMD* 会在启动容器的时候执行， *build* 时不执行，而 *RUN* 只是在构建镜像的时候执行，后续镜像构建完成之后，启动容器就与 *RUN* 无关了，这个初学者容易弄混这个概念，这里简单注解一下。
```

#### EXPOSE

用法：

```
XPOSE <port> [<port>...]
```

告诉 Docker 服务端容器对外映射的本地端口，需要在 docker run 的时候使用 -p 或者 -P 选项生效。

#### ENV

用法：

```
ENV <key> <value>       # 只能设置一个变量
ENV <key>=<value> ...   # 允许一次设置多个变量
```

指定一个环节变量，会被后续 RUN 指令使用，并在容器运行时保留。

例子：

```
ENV myName="John Doe" myDog=Rex\ The\ Dog \
    myCat=fluffyf
```

相当于：

```
ENV myName John Doe
ENV myDog Rex The Dog
ENV myCat fluffy
```

#### ADD

用法：

```
ADD <src>... <dest>
```

`ADD` 复制本地主机文件、目录或者远程文件 *URLS* 从 `<src>` 并且添加到容器指定路径中 `<dest>` 。
`<src>` 支持通过 `GO` 的正则模糊匹配，具体规则可参见[Go filepath.Match](https://golang.org/pkg/path/filepath/#Match)。

```
ADD hom* /mydir/        # adds all files starting with "hom"
ADD hom?.txt /mydir/    # ? is replaced with any single character
```

- <dest> 路径必须是绝对路径，如果 <dest> 不存在，会自动创建对应目录
- <src> 路径必须是 Dockerfile 所在路径的相对路径
- <src> 如果是一个目录，只会复制目录下的内容，而目录本身则不会被复制

#### COPY

用法：

```
COPY <src>... <dest>
```

`COPY` 复制新文件或者目录从 `<src>` 添加到容器指定路径中 `<dest>` 。用法同 `ADD` ，唯一的不同是不能指定远程文件 `URLS` 。

#### ENTRYPOINT

用法：

```
ENTRYPOINT ["executable", "param1", "param2"]
ENTRYPOINT command param1 param2（shell中执行）。
```

配置容器启动后执行的命令，并且不可被 `docker run` 提供的参数覆盖，如果需要覆盖，则可以使用 docker run --entrypoint 选项。
每个 `Dockerfile` 中只能有一个 `ENTRYPOINT` ，当指定多个时，只有最后一个起效。
通过 `ENTRYPOINT` 使用 `exec form` 方式设置稳定的默认命令和选项，而使用 `CMD` 添加默认之外经常被改动的选项。

#### VOLUME

用法：

```
VOLUME ["/data"]
```

创建一个可以从本地主机或其他容器挂载的挂载点，一般用来存放数据库和需要保持的数据等。

#### USER

用法：

```
USER daemon
```

指定运行容器时的用户名或 UID，后续的 RUN、CMD、ENTRYPOINT 也会使用指定用户。

#### WORKDIR

用法：

```
WORKDIR /path/to/workdir。
为后续的 RUN、CMD、ENTRYPOINT 指令配置工作目录。
```

可以使用多个 `WORKDIR` 指令，后续命令如果参数是相对路径，则会基于之前命令指定的路径。例如

```
WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd
```

则最终路径为 /a/b/c。

## Docker Machine

`Docker Machine` 是目前在 `Windows` 和 `OS X` 上运行 `Docker` 的官方解决方案。
