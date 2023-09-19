# 如何使用 frp 安全访问实验室深度学习工作站

# 简介

之前使用的公共节点 Zerotier 在学校之外的打洞成功率很差，因此换用 [frp](https://github.com/fatedier/frp) 。两者工作原理类似——依赖具有公网 IP 的中心节点中转。frp 的中心节点只能自建但相应的稳定性更优。

frp 基于认证的 `stcp(secret tcp)` 模式可以将内网服务安全地暴露到公网。frp 的可执行文件包括服务端（frps）与客户端（frpc），前者运行在具有公网 IP 的节点，后者运行在公网无法直接访问的、提供/使用服务的节点。`.ini` 后缀的文件记录了 frp节点间（不单是 frpc 与 frps，还包括 frpc 之间）的连接信息。

![frp 的原理图示](https://image.assets.xuchunqiu.com/img/2023/09/ADIOPI.png)

我们在阿里云上租用的 VPS 运行着 frps（连接不同 frpc 的桥梁），当前工作站上运行着 frpc（提供服务），而各位同学要在自己的电脑上同样运行 frpc（使用服务）。

# 快速上手

为了跑起来一个使用服务的 frpc，各位同学只需下载对应自己操作系统的 frpc，并使用正确的 `*.ini` 文件。

1. 从 [发布页面](https://github.com/fatedier/frp/releases) 下载  frpc。建议与 VPS 上运行的 frps 保持一致的版本（0.37.1）以减少出现问题的可能性。
   
   ![发布页截图及下载说明](https://image.assets.xuchunqiu.com/img/2023/09/RjW7i1.png)
   
2. 解压压缩包并精简。frp 没有安装的概念，但下载到的压缩包是个大杂烩，可以按照下图删掉浅蓝色标注的文件/文件夹。
   
   ![压缩包内容介绍](https://image.assets.xuchunqiu.com/img/2023/09/Ur6w5t.png)
   
3. 下载基本的 `*.ini` 文件。在解压目录中创建以下文件（覆盖原来的 `frpc.ini`）。这两个文件的内容未来修改的可能性非常低。

    {{< admonition type=note title="为什么需要多个配置文件？" open=true >}}
   为了便于理解和维护，frp 支持对配置文件进行拆分。
   
   实现方式是在主 `.ini` 文件中使用 `includes` 项记录需要的 [子配置文件]^(仅语义上的定义) 的相对/绝对路径，并以半身逗号分隔。
   
   特别要指出的是，即使指定的子配置文件不存在也不会影响 frp 运行。因此在 快速上手 部分即使我们没有 `frpc.ini` 中 `includes` 的 `frpc_personal.ini` 也能让 frpc 跑起来。
    {{< /admonition >}}
   
    {{< admonition type=info title="frpc.ini" open=false >}}
   ```ini
    [common]
    # VPS 上 frps 的连接信息
    server_addr = IP 地址
   
    server_port = 端口号
   
    authentication_method = token
   
    authenticate_new_work_conns = true
   
    token = 认证信息
   
    includes = frpc_workstation_public.ini, frpc_personal.ini   
   ```
    {{< /admonition >}}

    {{< admonition type=info title="frpc_workstation_public.ini" open=false >}}
   ```ini
    [AIMAX_Web]
    # 连接协议
    type = stcp
   
    # 本机 frpc 的角色是服务使用者
    role = visitor
   
    # 与工作站上 frpc 完全一致的校验信息
    server_name = workstation_AIMAX_Web
   
    sk = 另一组认证信息
   
    # 本机监听地址
    bind_addr = 127.0.0.1
   
    # 本机监听端口。指定访问 AIMAX 网页的端口号
    bind_port = 8081  
   ```
    {{< /admonition >}}
   
4. 运行 frpc。打开本机上的终端，进入 frp 的解压目录，或者使用 `Shift` + `鼠标右键` 的方式快速进入。
   
   ![快速在指定文件夹下启动终端的方法](https://image.assets.xuchunqiu.com/img/2023/09/yhOJIk.png)
   
   执行以下命令即可启动 frpc。
   {{< admonition type=example title="启动 frpc（Windows）" open=true >}}
   ```bash
   ./frpc.exe -c ./frpc.ini
   ```

   `-c` 参数指定 config 文件，由于 frpc 默认会使用当前工作区下的 `frpc.ini` 文件。因此可以简化为：
   ```bash
   ./frpc
   ```
    {{< /admonition >}}

   接下来打开本机浏览器访问 127.0.0.1:8081 即可访问到 AIMAX 的网页了：

   ![成功访问远端页面](https://image.assets.xuchunqiu.com/img/2023/09/NxFza6.png)

   {{< admonition type=tip title="如何停止 frpc？" open=true >}}
   如需结束 frpc，在启动 frpc 的终端窗口内使用 `Ctrl` + `c`。
    {{< /admonition >}}

# 配置详解

在步骤二成功访问到 AIMAX 网页版后，需要开始编写 `frpc_personal.ini` 配置文件。

## 原因

因为 AIMAX 本身的一些问题，导致远程访问下所有交互式开发环境都无法进入，但像创建交互式开发、上传下载私有/共享数据等操作是没问题的。

![不能直接进入交互式开发环境](https://image.assets.xuchunqiu.com/img/2023/09/A7QFnX.png)

暂时的解决方案是对交互式开发里需要用到的每一个端口都生成一段 frpc 的配置（约定该文件的名称为 `frpc_personal.ini` ），因此需要同学们对 `*.ini` 文件的结构有一定概念。

## `*.ini` 文件结构说明

最基本的 *.ini 文件如下：

```ini
[section]
name=value
```

- 一个 section 对应一个服务，section 的名字完全不重要，写得自己能理解就好（可用中文）；
- 一个 `*.ini` 文件中可以有多个 section，它们之间以方括号区隔而非换行；
- 一个 section 下可以有多个 name,value 的对，它们之间没有先后顺序要求；
- name,value 对内部可以随意使用空格，name,value 对间可以随意使用换行；
- name,value 对内部大小写敏感；
- 行间注释以 `;` 或 `#` 开头，没有行内注释。

## `frpc_personal.ini` 的编写

模板如下，有几个端口需要远程访问就写几个 section。section 下只有 [`service_name`]^(服务名)、[`sk`]^(密码) 强制修改，且严格与服务端配置完全对应。写好后保存为 `frpc_personal.ini` 放入解压缩文件夹中，再次启动 `frpc`。

{{< admonition type=tip title="需要远程访问的端口号填到哪里？" open=true >}}
section 对应服务的远端端口号只在 frps 的配置文件中体现。
{{< /admonition >}}

{{< admonition type=info title="frpc_personal.ini" open=true >}}
```ini
[service1]
# 固定不变
type = stcp
# 固定不变
role = visitor
# service_name, sk 必须严格与服务端配置完全对应
service_name = user_projector
sk = 一个密码
# 本机监听地址。可改为 0.0.0.0 以允许本机和所在局域网内其它设备访问
bind_addr = 127.0.0.1
# 本机监听端口。可自定，要求不同服务的本机监听端口不能重复
bind_port = 8083

[service2]
type = stcp
role = visitor
server_name = user_ssh
sk = 另一个密码
bind_addr = 127.0.0.1
bind_port = 8084
```
{{< /admonition >}}

![成功地访问了两个远程服务](https://image.assets.xuchunqiu.com/img/2023/09/KP6j49.png)

# 根据 frpc 日志故障排除

注意必须先在终端中启动 frpc，才能通过浏览器（或其它软件）访问到 frpc 提供的远程页面。启动 frpc 的终端会输出日志。下图展示了如何根据日志信息分析可能的问题。其中3、4是要访问对应服务才能看出的错误。

![根据日志信息判断可能的问题](https://image.assets.xuchunqiu.com/img/2023/09/FRJJwy.png)


