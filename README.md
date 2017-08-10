# linode-centos7_ChangeKernel
# 安装elrepo提供的最新kernel  

cat > /etc/yum.repos.d/elrepo.repo << EOF  
[elrepo-kernel]  
name=ELRepo.org Community Enterprise Linux Kernel Repository - el7  
baseurl=http://elrepo.org/linux/kernel/el7/\$basearch/  
        http://mirrors.coreix.net/elrepo/kernel/el7/\$basearch/  
        http://jur-linux.org/download/elrepo/kernel/el7/\$basearch/  
        http://repos.lax-noc.com/elrepo/kernel/el7/\$basearch/  
        http://mirror.ventraip.net.au/elrepo/kernel/el7/\$basearch/  
enabled=1  
gpgcheck=0  
EOF  

# 安装GRUB2  
yum -y install kernel-ml grub2  

# 替换内核  
mkdir /boot/grub  
grub2-mkconfig -o /boot/grub/grub.cfg  

# 修改内核启动方式 在Linode后台 - Dashboard - Edit，进行编辑，选择启动方式为GRUB 2  
# 重启  
reboot  

# 开启Google BBR  
cat >>/etc/sysctl.conf << EOF  
net.core.default_qdisc=fq  
net.ipv4.tcp_congestion_control=bbr  
EOF  

# 重载配置文件  
sysctl -p  

# 检测是否生效  
[root@linode1495332 ~]# sysctl net.ipv4.tcp_available_congestion_control  
net.ipv4.tcp_available_congestion_control = bbr cubic reno  
[root@linode1495332 ~]# lsmod | grep bbr  
tcp_bbr                16384  70  
