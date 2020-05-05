# 家里代码备份仓库
妹妹前年买了一个**thinkpad x1c**笔记本，用了几个月就不喜欢了，一直在家里吃灰。我正愁没有代码服务器。

由于家里的**nas**性能也不是很强。而且做其他事情我怕搞坏了。毕竟里面有老婆大人的重要数据。

- 下载`ubuntu server 20LTS`, 用`balenaEtcher`写镜像到**u盘**里面。
- 安装完系统之后，用网络usb转接头进行上线。但是需要配置相关的东西。

## 配置网络
利用**netplan**进行配置。
```bash
sudo netplan generate # 生成示例的配置网络
ip a # 找到usb转接口映射出来的eth口名称
```

配置网络：
```yml
# This is the network config written by 'subiquity'
network:
  #ethernets: {}
  ethernets:
    enx9cebe82d2cc8:
      addresses:
      - 192.168.1.140/24
      dhcp4: false
      gateway4: 192.168.1.1
      nameservers:
        addresses:
        - 61.139.2.69
        search: []
  version: 2
```

重启生效：
```bash
sudo netplan apply
```

## 合盖笔记本不休眠配置
`sudo vim /etc/systemd/logind.conf`配置里面的参数：
```ini
HandleLidSwitch=ignore
```

重启生效：
```bash
sudo service systemd-logind restart
```

顺便说一下其他参数：
- HandlePowerKey: 按下电源键的行为
- HandleSleepKey: 按下挂起键的行为
- HandleHibernateKey: 按下休眠键后的行为 
- HandleLidSwitch: 合上笔记本盖后的行为

变量取值有：
- hibernate: 休眠
- suspend: 睡眠
- poweroff: 关机

## 修改时区
```bash
# 然后选择亚洲Asia，继续选择中国China，最后选择北京Beijing。
sudo tzselect
sudo ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

## 安装go
下载go binary package. 解压到目录：`sudo tar -C /usr/local/ -xvf go1.14.2.linux-amd64.tar.gz`

然后将如下内容放到`~/.bashrc`下面，**source**一下即可。
```bash
export PATH=/usr/local/go/bin:$PATH
export GOPATH=$HOME/go
export GOROOT=/usr/local/go
export PATH=$PATH:$GOPATH/bin
export PATH=$PATH:$GOROOT/bin
export GOBIN=$HOME/go/bin
```
## 安装rust
必须要ss出去才好下载。或者设置2个环境变量再下载[rust换源](https://www.ioio.pw/rust/2018/01/24/change-rust-mirrors.html)
```bash
export RUSTUP_DIST_SERVER=https://mirrors.ustc.edu.cn/rust-static
export RUSTUP_UPDATE_ROOT=https://mirrors.ustc.edu.cn/rust-static/rustup

curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
# 将下面的内容放到.bashrc里面再source一下
export PATH="$HOME/.cargo/bin:$PATH"
```

`vim ~/.cargo/config`写入：
```ini
[registry]
index = "https://mirrors.ustc.edu.cn/crates.io-index/"
[source.crates-io]
registry = "https://github.com/rust-lang/crates.io-index"
replace-with = 'ustc'
[source.ustc]
registry = "https://mirrors.ustc.edu.cn/crates.io-index/"
```
