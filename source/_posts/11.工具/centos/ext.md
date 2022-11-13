# centos下挂在 extfat格式文件
1.yum install -y http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-5.el7.nux.noarch.rpm
2.yum install exfat-utils fuse-exfat

mount -t extfat /dev/sdc /data
