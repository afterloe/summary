# 树莓派wifi设置
> create by [afterloe](605728727@qq.com)  
> version is 1.0  
> MIT License  

## 未连接过任何wifi
```
$ sudo raspi-config

2 Network Options      Configure network settings 
```

依次输入wifi 的ssid及密码

## 已连接过wifi
```
$ cd /etc/wpa_supplicant
$ sudo vim wpa_supplicant.conf

#添加一项
network={
    ssid="WiFi-A"
	psk="12345678"
	key_mgmt=WPA-PSK
	priority=1
}
```
* **ssid** 网络SSID
* **psk** WPA/WPA2加密方式密码
* **wep_key0** WEP加密方式密码
* **priority** 连接优先级（字数越大优先级越高，不可以是负数）
* **scan_ssid** 连接隐藏wifi的时候需要指定该值
* **key_mgmt** 加密方式（无密码 NONE | WEP NONE | WPA/WPA2 WPA-PSK）

## 连接隐藏wifi
```
network={
    ssid="WiFi-B"
	psk="12345678"
	key_mgmt=WPA-PSK
	priority=2
	scan_ssid=1
}
```
添加 `scan_ssid=1`即可。  

使用 `sudo reboot`重启生效。
