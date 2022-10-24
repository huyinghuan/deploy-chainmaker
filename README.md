## "一键"部署长安链

服务器仅支持linux系统

该脚本执行环境必须须安装 `ansible`， 然后执行`ansible-galaxy collection install community.general`

`ansible` 安装见: https://docs.ansible.com/ansible/latest/installation_guide/index.html 【简单点就是 `yum/apt/dnf install ansible`】

本文档命令可在本机也可在服务器执行。

长安链所在服务端系统支持: redhat 系， debian
服务器环境要求，按官方文档配置即可

支持部署的长安链版本:

- v2.3.0
  
  说明: v2.2.1 版本，官方源代码的mod依赖有问题，下载后校验hash不能通过。如果有人有编译通过的方法可以提issue 


不支持 

- go-evm

注意！ 该脚本会禁止 `selinux` 服务

## 单机部署

已测试部署服务器系统:

- centos stream 8
- armbian aarch64 【不支持wxvm,官方没提供相关动态库】
- Rocky Linux 8.6
- Ubuntu 20.04.5 【注意，由于ubuntu特殊性，在hosts在配置服务器ip时，需要加上用户名，如: test@192.168.1.2】

注意！！ 
    
    非`root`用户执行，所有命令均需加 `-K` 参数, 如 `ansible-playbook -i hosts single.playbook.yml -K`
    
    脚本执行时间由服务器的网络和配置决定【需要下载依赖和编译二进制，比较耗费资源】
    
### 本机执行 【即在自己电脑上用ansible远程在服务器运行命令】



#### 准备

```
git clone https://github.com/huyinghuan/deploy-chainmaker.git
cd deploy-chainmaker
```

- 编辑 `hosts` 文件，在其中`single_server`配置服务器ip地址 和服务器登录用户名 
- 远程登录：【默认方式为密码登录】
    - 如果采用密钥登录: 编辑 `ansible.cfg`文件，将`private_key_file`指向服务器的登录私钥, 注释掉`hosts`中所有的`ansible_ssh_pass`
    - 如果采用密码登录编辑 `hosts`中所有的 `ansible_ssh_pass`
- 编辑 `roles/chainmaker/defaults/main.yml` 
    - username 为 https://git.chainmaker.org.cn/ 登录名
    - password 为 相关密码
    - 其他配置请酌情更改

#### 开始部署

```bash
ansible-playbook -i hosts single.playbook.yml # -K # 非root用户 需要加上 -K 参数
```

### 服务器执行，可选 【即在服务器上直接运行命令】

#### 准备
```
git clone https://github.com/huyinghuan/deploy-chainmaker.git
cd deploy-chainmaker
```

- 编辑 `hosts` 文件，在其中`single_server` 下添加 `localhost`, 注释其他

- 编辑 `roles/chainmaker/defaults/main.yml` 
    - username 为 https://git.chainmaker.org.cn/ 登录名
    - password 为 相关密码
    - 其他配置请酌情更改

#### 开始部署

```bash
ansible-playbook single.playbook.yml # -K # 非root用户 需要加上 -K 参数
```

### 其他操作

- 查看是否启动成功【由于初始化启动不一定能及时读取到日志】 `ansible-playbook -i hosts single.playbook.yml --tags start_check`
- 停止进程 `ansible-playbook -i hosts single-opt.playbook.yml --tags stop`
- 重启进程 `ansible-playbook -i hosts single-opt.playbook.yml --tags restart`
- 清除所有数据,包括进程,区块，日志等 `ansible-playbook -i hosts single-reset.playbook.yml`
## 多机房部署

通过测试:

- Rocky Linux 8.6 (即类似centos 8.6的社区版)

### 本机执行

#### 准备

```
git clone https://github.com/huyinghuan/deploy-chainmaker.git
cd deploy-chainmaker
```

- 编辑 `hosts` 文件，在其中`multroom` 配置服务器ip地址
- 编辑 `hosts` 文件，在其中`compiler_server` 配置编译服务器ip地址
    - 注意编译服务器一定要保持和部署服务器一样的系统版本，避免因为gcc等依赖库不一致导致的程序运行错误
- 远程登录：【默认方式为密码登录】
    - 如果采用密钥登录: 编辑 `ansible.cfg`文件，将`private_key_file`指向服务器的登录私钥, 注释掉`hosts`中所有的`ansible_ssh_pass`
    - 如果采用密码登录编辑 `hosts`中所有的 `ansible_ssh_pass`
- 编辑 `roles/chainmaker/defaults/main.yml` 
    - username 为 https://git.chainmaker.org.cn/ 登录名
    - password 为 相关密码
    - 其他配置请酌情更改

#### 执行

1. 编译二进制，申请证书等

```bash
ansible-playbok -i hosts more-compile.playbook.yml
```

该步骤会在编译服务器上的二进制文件和证书文件拷贝到本机上，便于下一步分发到各个服务器上

2. 分发二进制，证书，启动链


```bash
ansible-playbok -i hosts more.playbook.yml
```

### 其他操作

- 查看是否启动成功【由于初始化启动不一定能及时读取到日志】 `ansible-playbook -i hosts more.playbook.yml --tags start_check`
- 停止进程 `ansible-playbook -i hosts more-opt.playbook.yml --tags stop`
- 重启进程 `ansible-playbook -i hosts more-opt.playbook.yml --tags restart`
- 清除所有数据,包括进程,区块，日志等 `ansible-playbook -i hosts more-reset.playbook.yml`

## 其他说明

- 一般配置:  `roles/chainmaker/defaults/main.yml`
- 更多配置: `roles/chainmaker/templates/v2.3.0/*.j2`