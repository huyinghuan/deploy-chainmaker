## "一键"部署长安链

服务器仅支持linux系统

该脚本执行环境必须须安装 `ansible` 和 `ansible-galaxy collection install community.general`，

`ansible` 安装见: https://docs.ansible.com/ansible/latest/installation_guide/index.html

本文档命令可在本机也可在服务器执行。

长安链所在服务端系统支持: redhat 系， debian
服务器环境要求，按官方文档配置即可

支持部署的长安链版本:

- v2.3.0
  
  说明: v2.2.1 版本，官方源代码的mod依赖有问题，下载后校验hash不能通过。如果有人有编译通过的方法可以提issue 

已测试部署服务器系统:

- centos stream 8
- armbian aarch64 【不支持wxvm,官方没提供相关动态库】


## 单机部署

### 本机执行 【即在自己电脑上用ansible远程在服务器运行命令】

#### 准备

```
git clone https://github.com/huyinghuan/deploy-chainmake.git
cd deploy-chainmake/single
```

- 编辑 `hosts` 文件，在其中`single_server`配置服务器ip地址 和服务器登录用户名 
- 编辑 `ansible.cfg`文件，将`private_key_file`指向服务器的登录私钥
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
git clone https://github.com/huyinghuan/deploy-chainmake.git
cd deploy-chainmake/single
```

- 编辑 `single.playbook.yml` 文件，在其中`hosts: single_server` 改为: `hosts: localhost`

- 编辑 `roles/chainmaker/defaults/main.yml` 
    - username 为 https://git.chainmaker.org.cn/ 登录名
    - password 为 相关密码
    - 其他配置请酌情更改

#### 开始部署

```bash
ansible-playbook single.playbook.yml # -K # 非root用户 需要加上 -K 参数
```

## 多机房部署

### 本机执行

```bash
```

### 【不可用】服务器执行

