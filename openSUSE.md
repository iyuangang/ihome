openSUSE 42.3 raspberrypi Wifi enable

```
sudo vim /etc/dracut.conf.d/raspberrypi_modules.conf
```

删除文件第一行的 sdhci_iproc ，再取消最后一行的注释。运行以下命令保存修改：

```
mkinitrd -f
```

