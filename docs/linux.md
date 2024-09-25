# 常见 Linux 指令

## 文件和目录管理
- `ls`
  - 列出目录内容
  - 选项：
    - `ls -l`：详细列表
    - `ls -a`：显示隐藏文件

- `cd <directory>`
  - 切换到指定目录

    ```terminal
    cd /home/user
    ```

- `pwd`
  - 显示当前工作目录

- `mkdir <directory>`
  - 创建新目录

    ```terminal
        mkdir mydir
    ```

- `rmdir <directory>`
  - 删除空目录

    ```terminal
    rmdir mydir
    ```

- `rm <file>`
  - 删除文件或目录
  - 选项：
    - `rm -r`：递归删除目录及其内容
    - `rm -f`：强制删除

    ```terminal
    rm myfile
        rm -rf mydir
    ```

- `cp <source> <destination>`
  - 复制文件或目录
  - 选项：
    - `cp -r`：递归复制目录

    ```terminal
    cp file1 file2
        cp -r dir1 dir2
    ```

- `mv <source> <destination>`
  - 移动或重命名文件或目录

    ```terminal
    mv oldname newname
        mv file1 /home/user
    ```

- `touch <file>`
  - 创建空文件或更新文件的时间戳

    ```terminal
    touch newfile
    ```

## 文件查看和编辑
- `cat <file>`
  - 查看文件内容

    ```terminal
    cat myfile
    ```

- `less <file>`
  - 分页查看文件内容

    ```terminal
    less myfile
    ```

- `head <file>`
  - 查看文件的前几行
  - 选项：
    - `head -n 10`：查看前 10 行

    ```terminal
    head myfile
        head -n 20 myfile
    ```

- `tail <file>`
  - 查看文件的后几行
  - 选项：
    - `tail -n 10`：查看后 10 行
    - `tail -f`：实时查看文件追加内容

    ```terminal
    tail myfile
        tail -n 20 myfile
        tail -f myfile
    ```

- `nano <file>`
  - 使用 nano 编辑器编辑文件

    ```terminal
    nano myfile
    ```

- `vi <file>`
  - 使用 vi 编辑器编辑文件

    ```terminal
    vi myfile
    ```

## 权限管理
- `chmod <permissions> <file>`
  - 更改文件或目录权限

    ```terminal
    chmod 755 myfile
    ```

- `chown <owner>:<group> <file>`
  - 更改文件或目录的所有者和组

    ```terminal
    chown user:group myfile
    ```

## 系统信息
- `uname -a`
  - 显示系统信息

- `top`
  - 实时显示系统进程和资源使用情况

- `df -h`
  - 显示磁盘使用情况

- `du -sh <directory>`
  - 显示目录的大小

    ```terminal
    du -sh /home/user
    ```

- `free -h`
  - 显示内存使用情况

## 网络管理
- `ifconfig`
  - 显示或配置网络接口

- `ping <host>`
  - 测试网络连接

    ```terminal
    ping google.com
    ```

- `ssh <user>@<host>`
  - 通过 SSH 连接到远程主机

    ```terminal
    ssh user@remotehost
    ```

## 压缩和解压
- `tar -czvf <archive>.tar.gz <files>`
  - 创建 tar.gz 压缩包

    ```terminal
    tar -czvf archive.tar.gz mydir
    ```

- `tar -xzvf <archive>.tar.gz`
  - 解压 tar.gz 压缩包

    ```terminal
    tar -xzvf archive.tar.gz
    ```

- `zip <archive>.zip <files>`
  - 创建 zip 压缩包

    ```terminal
    zip archive.zip myfile
    ```

- `unzip <archive>.zip`
  - 解压 zip 压缩包

    ```terminal
    unzip archive.zip
    ```

## 进程管理

- `netstat -tuln`
  - 显示网络端口和连接情况
  - 
- `netstat -tuln|grep 3306`
  - 显示端口是否被占用
  - 
- `losf -i:8080`
  - 查看占用端口的进程pid

- `ps aux`
  - 显示所有进程信息

- `ps aux | grep <process_name>`
  - 显示指定进程的详细信息（进程号）
  
  ```terminal
    ps aux | grep nginx
  ```

- `kill -15 <pid>`
  - 终止指定进程

    ```terminal
    kill 666
    ```

  - `kill -15 666` 或 `kill 666` 发送 SIGTERM（信号 15），是请求进程优雅退出的方式它允许进程进行清理工作
  - `kill -9 666` 发送 SIGKILL（信号 9），强制立即终止进程，不给进程进行任何清理工作的机会

- `killall <process>`
  - 终止所有指定名称的进程

    ```terminal
    killall nginx
    ```

- `pkill <pattern>`
  - 根据名称模式终止进程

    ```terminal
    pkill -f myprocess
    ```

- `sudo netstat -tulnp | grep <process_id>`
  - 显示指定进程的端口和连接信息

    ```terminal
    sudo netstat -tulnp | grep 1234
    ```

## 软件包管理（基于 Debian）
- `apt update`
  - 更新软件包列表

- `apt upgrade`
  - 升级所有已安装的软件包

- `apt install <package>`
  - 安装新软件包

    ```terminal
    apt install vim
    ```

- `apt remove <package>`
  - 删除软件包

    ```terminal
    apt remove vim
    ```

- `apt search <package>`
  - 搜索软件包
    ```terminal
    apt search vim
    ```

## CORN 定时任务

- `crontab -e`
  - 打开 crontab 编辑器

- `crontab -l`
  - 列出 crontab 中的所有任务
 
- `0 2 * * * /path/to/my_script.sh`
  - 在 corntab 中添加一个在 2 点（每天）执行脚本的任务

## 其他命令
- `systemctl enable <process_name>`
  - 开机自启动
  ```terminal
    systemctl enable docker
  ```

- `history`
  - 显示命令历史记录

- `alias <name>='<command>'`
  - 创建命令别名

    ```terminal
    alias ll='ls -l'
    ```

- `wget <url>`
  - 下载文件

    ```terminal
    wget http://example.com/file.zip
    ```

- `curl <url>`
  - 下载或发送数据

    ```terminal
    curl http://example.com
    ```

- `echo <text>`
  - 输出文本

    ```terminal
    echo "Hello, World!"
    ```

- `date`
  - 显示当前日期和时间

- `cal`
  - 显示当前月份的日历

## 常见 YUM 命令

### 基本命令
- `yum update`
  - 更新所有已安装的软件包到最新版本

    ```terminal
    yum update
    ```

- `yum check-update`
  - 检查可更新的软件包，但不实际更新

    ```terminal
    yum check-update
    ```

- `yum install <package>`
  - 安装指定的软件包

    ```terminal
    yum install vim
    ```

- `yum remove <package>`
  - 删除指定的软件包

    ```terminal
    yum remove vim
    ```

- `yum list`
  - 列出所有可用和已安装的软件包

    ```terminal
    yum list
    ```

- `yum list installed`
  - 列出所有已安装的软件包

    ```terminal
    yum list installed
    ```

- `yum list available`
  - 列出所有可用的软件包

    ```terminal
    yum list available
    ```

- `yum search <keyword>`
  - 搜索与关键字匹配的软件包

    ```terminal
    yum search nginx
    ```

- `yum info <package>`
  - 显示指定软件包的信息

    ```terminal
    yum info vim
    ```

### 软件包组管理
- `yum group list`
  - 列出所有可用的软件包组

    ```terminal
    yum group list
    ```

- `yum group install "<group_name>"`
  - 安装指定的软件包组

    ```terminal
    yum group install "Development Tools"
    ```

- `yum group remove "<group_name>"`
  - 删除指定的软件包组

    ```terminal
    yum group remove "Development Tools"
    ```

### 清理缓存
- `yum clean all`
  - 清理所有缓存

    ```terminal
    yum clean all
    ```

- `yum clean packages`
  - 清理缓存的包文件

    ```terminal
    yum clean packages
    ```

- `yum clean metadata`
  - 清理缓存的元数据

    ```terminal
    yum clean metadata
    ```

### 其他命令
- `yum provides <file>`
  - 查找包含指定文件的软件包

    ```terminal
    yum provides /etc/yum.conf
    ```

- `yum history`
  - 显示 yum 命令的历史记录

    ```terminal
    yum history
    ```

- `yum history info <transaction_id>`
  - 显示指定交易 ID 的详细历史信息

    ```terminal
    yum history info 10
    ```

- `yum history undo <transaction_id>`
  - 撤销指定交易 ID 的操作

    ```terminal
    yum history undo 10
    ```

- `yum repolist`
  - 列出所有启用的仓库

    ```terminal
    yum repolist
    ```

- `yum-config-manager --add-repo <repo_url>`
  - 添加一个新的 yum 仓库

    ```terminal
    yum-config-manager --add-repo http://example.com/repo
    ```

- `yum-config-manager --disable <repo>`
  - 禁用指定的仓库

    ```terminal
    yum-config-manager --disable epel
    ```

- `yum-config-manager --enable <repo>`
  - 启用指定的仓库

    ```terminal
    yum-config-manager --enable epel
    ```

