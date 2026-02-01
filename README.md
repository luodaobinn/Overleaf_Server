# Overleaf Server 使用说明

本文档旨在指导如何使用 Overleaf Server，以及协助管理员进行系统的维护与管理。

## 📖 用户指南 (User Guide)

### 一、 注册

如果您是首次使用，请按照以下步骤完成注册：

1. 访问注册页面： http://192.168.31.76:6086
2. 点击页面 左下角 的 注册 链接。
3. 填写您的邮箱地址。
4. 进入链接设置您的登录密码。

### 二、 登录

请根据您当前的网络环境选择合适的访问地址：

| 网络环境 | 访问地址 (URL) | 备注 |
| --- | --- | --- |
| 实验室局域网 | http://192.168.31.76:6086/login | 速度最快，推荐使用 |
| 公网 (外部访问) | 请联系管理员 | 2026.1.30 更新 |

### 三、 多人协作 (Project Sharing)

支持 邮箱邀请、链接分享 模式进行协作。

- 邮箱邀请 ：在项目中点击 Share，添加协作者邮箱邀请即可。
- 链接分享 ：在项目中点击 Share，打开并复制分享链接发送给协作者。

> ⚠️ 注意事项：
> 1.邮箱邀请时，协作者必须在本服务器注册过，登录自己账号点击接受邀请；
> 2.链接分享时，如果被分享的协作者不在实验室局域网内，请务必手动将链接中的内网 IP (
> 192.168.31.76:6086
> ) 替换为公网 IP，否则对方无法打开。

## 🔧 管理员指南 (Administrator Guide)

### 一、 项目启停 (核心服务)

启动所有服务 (Overleaf 主服务 + 注册后端)：

```bash
toolkit-master/bin/up -d && toolkit-master/bin/run-register start
```

停止所有服务 ：

```bash
toolkit-master/bin/stop && toolkit-master/bin/run-register stop
```

### 二、 注册功能管理

本项目集成了一个额外的 Flask 后端 ( ./app.py ) 以实现用户自助注册功能（原版仅支持管理员后台创建）。该模块通过 toolkit-master/run-register 脚本进行管理。

| 操作 | 命令 |
| --- | --- |
| 启动后端 | ./toolkit-master/run-register start |
| 停止后端 | ./toolkit-master/run-register stop |
| 重启后端 | ./toolkit-master/run-register restart |
| 查看状态 | ./toolkit-master/run-register status |
| 实时日志 | ./toolkit-master/run-register logs |
| 查看帮助 | ./toolkit-master/run-register help |

### 三、 数据备份与回溯

#### 1. 数据备份

执行以下脚本，备份文件将生成在 ./backups 目录下：

```bash
./toolkit-master/backup.sh
```

#### 2. 数据回溯

执行回溯脚本。 注意 ：需要在脚本文件中预先配置备份路径，并根据提示手动确认。

```bash
./toolkit-master/restore.sh
```

> ⚠️ 回溯后权限修复：
> 回溯完成后，必须执行以下命令修复数据目录权限，否则可能导致服务无法读写：

 ```bash
sudo chmod -R 777 ./toolkit-master/data
```

### 四、 系统迁移指南 (Migration)

注：目前尚未实操验证，以下为建议流程。

若需将服务迁移至新服务器，建议按以下步骤操作：

1. 镜像打包 ： 打包 Docker 镜像 sharelatex/sharelatex:6.0.1-ext-v3.4-with-texlive-full （此镜像已包含 Overleaf 社区版扩展批注功能及完整 TeXLive 包）。
2. 数据备份 ： 使用上述“备份功能”对当前数据进行全量备份。
3. 项目迁移 ： 打包并传输整个 Overleaf_Server 项目目录至新服务器。
4. 数据恢复 ： 在新服务器上执行“回溯功能”加载数据。
5. 配置更新 ： 启动项目前，务必检查并修改以下文件中的 IP 和端口配置： ./toolkit-master/lib/docker-compose.base.yml ./app.py
6. 启动服务 ： 按照“项目启动”步骤拉起服务。
