```shell
#输出到文件里是为了看下下载的脚本内容
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh > rpm.sh
chmod +x rpm.sh
./rpm.sh

#使用yum安装gitlab
yum install -y gitlab-ee

#修改配置
vim /etc/gitlab/gitlab.rb
#重启生效
gitlab-ctl reconfigure #执行很长时间
gitlab-restart

#查看gitlab启动状态
gitlb-ctl status
#停止gitlab
gitlab-ctl stop
#卸载gitlab
rpm -e gitlab-ee	#或者是gitlab-ce
#关闭进程
ps -ef|grep gitlab
kill -9 pid
#删除gitlab文件
find / -name *gitlab*|xargs rm -rf
find / -name gitlab|xargs rm -rf




```

