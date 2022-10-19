## "一键"部署长安链

本机必须安装 `ansible`， 安装见: https://docs.ansible.com/ansible/latest/installation_guide/index.html

本文档命令均在客户端执行。

长安链所在服务端，系统支持: centos8 stream 或 redhat 系， debian
服务器环境要求，按官方文档配置即可

支持部署的长安链版本:
- v2.3.0

### 准备

- 编辑 `hosts` 文件，在其中配置 服务器ip地址
- 编辑 `ansible.cfg`文件，将`private_key_file`指向服务器的登录私钥
- 编辑 `roles/chainmaker/defaults/main.yml` 
    - username 为 https://git.chainmaker.org.cn/ 登录名
    - password 为 相关密码
    - 其他配置请酌情更改

## 单机部署

```shell
cd single
ansible-playbook -i hosts single.playbook.yml
```

