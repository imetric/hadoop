# 使用老师分配的腾讯云服务器

## 查找自己的IP地址

IP地址见下面文档。使用老师服务器的同学，可以路过“1.1 购买云服务器”和“1.2 配置服务器安全策略”。

{% file src=".gitbook/assets/学生名单及服务器IP.xlsx" %}

![](<.gitbook/assets/image (1).png>)

如上图所示，王立博同学的服务器公网IP地址是118.195.248.243，内网IP地址是10.206.0.98。记住这两个IP地址。

## 使用Putty软件连接服务器

使用Putty登录服务器，并对远程服务器进行管理和操作。

#### 下载安装Putty软件

{% file src=".gitbook/assets/putty.exe" %}
Putty安装包
{% endfile %}

运行 putty.exe，在程序界面内输入服务器 IP 地址和端口（22 是 SSH 默认端口），选中 SSH 连接类型，设置连接会话名称及点击保存，然后点击 Open 按钮开始连接登录。

<figure><img src=".gitbook/assets/759453120ed0afb413917294283a526a.png" alt=""><figcaption><p>Putty主界面</p></figcaption></figure>

首次连接会提示服务器指纹，选择是或否。“是”将保存指纹，“否”则不保存。保存后登录同一台服务器将不再提示（如果提示，则表示服务器指纹发生了变化，可能是重装系统所致或连接服务器被冒充）。

<figure><img src=".gitbook/assets/0c744299e188e905104154eb23421cbc.png" alt=""><figcaption></figcaption></figure>

之后输入用户名（如果购买的云服务器，用户名为ubuntu）和密码即可登录服务器（输入密码时不会显示输入状态，这是一个安全设计。鼠标右键点击可以粘贴输入）。

> 如果 使用老师购买的服务器，按以下信息登录服务器：
>
> * 用户名：hadoop
> * 密码： Guet@1130182

<figure><img src=".gitbook/assets/d110760f8731eb7b842042f37a51f2ec.png" alt=""><figcaption></figcaption></figure>

## 修改服务器hosts

输入以下命令，进入hosts文件编辑界面。hosts文件存在于/etc/hosts

```
sudo vi /etc/hosts
```

删除以下内容

```
127.0.1.1 localhost.localdomain VM-0-2-ubuntu
```

增加以一上内容，这样就可以直接使用hadoop01来访问这台主机了。注意10.206.0.2为你主机的内网IP地址

```
10.206.0.2 hadoop01
```

![](<.gitbook/assets/image (1).png>)

然后依次输入 **Esc**， **:wq**，**回车**

## 2.2 重新配置无密码登录

```
rm -r ~/.ssh
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 0600 ~/.ssh/authorized_keys
ssh hadoop01
```

## 启动Hadoop

输入以下命令启动

```
/usr/local/hadoop/bin/hdfs namenode -format #格式化HDFS
/usr/local/hadoop/sbin/start-all.sh #启动
```

输入jps命令，查询Hadoop是否启动成功，如果启动成果，输入jps后，会显示如下内容。

![](.gitbook/assets/image.png)

使用浏览器验证Hadoop是否启动成功，在浏览器中访问http://ip:9870，ip改为你的公网IP地址。

![](<.gitbook/assets/image (2).png>)
