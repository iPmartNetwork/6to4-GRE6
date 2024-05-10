<p align="center">
<picture>
<img width="160" height="160"  alt="XPanel" src="https://github.com/iPmartNetwork/iPmart-SSH/blob/main/images/logo.png">
</picture>
  </p> 
<p align="center">
<h1 align="center"/>6to4-GRE6</h1>
<h6 align="center">تانل برپایه ایپی ورژن 6 و ایپی لوکال ورژن 4 
<h6>
</p>


<p align="center">===========================================================================


تمامی مراحل رو خط به خط برید جلو بجای IP.iran و IP.kharej ای پی های سرور خودتون رو وارد کنید

ابتدا در سرور ایران باید این دستورات رو وارد کنیم تا تانل 6to4 در سرور ایران ، برقرار شود .



## Iran


```bash
ip tunnel add 6to4_To_KH mode sit remote IP.KHARJ local ip.IRAN
ip -6 addr add fc00::1/64 dev 6to4_To_KH
ip link set 6to4_To_KH mtu 1480
ip link set 6to4_To_KH up
```


## Kharej

```bash
ip tunnel add 6to4_To_IR mode sit remote ip.IRAN local IP.KHARJ
ip -6 addr add fc00::2/64 dev 6to4_To_IR
ip link set 6to4_To_IR mtu 1480
ip link set 6to4_To_IR up
```



<p align="center">===========================================================================





تا اینجای کار اگر دستورات رو درست وارد کرده باشید تانل 6to4 بین سرور خارج و ایران برقرار شده .
- آیپی v6 سرور ایران : fc00::1
- آیپی v6 سرور خارج : fc00::2

برای اطمینان از اینکه تانل برقرار شده باشه وارد هر دو سرور بشید و از آیپی سرور مقابل پینگ بگیرید.
- به عنوان مثل وارد سرور ایران میشیم و این دستور رو برای پینگ گرفتن IPv6 استفاده میکنیم : 
```shell
ping6 fc00::2 
```
- روی سرور خارچ نیز همین کار رو تکرار میکنیم :
```shell
ping6 fc00::1
```





<p align="center">===========================================================================



## Iran


```bash
ip -6 tunnel add GRE6Tun_To_KH mode ip6gre remote fc00::2 local fc00::1
ip addr add 192.168.13.1/30 dev GRE6Tun_To_KH
ip link set GRE6Tun_To_KH mtu 1436
ip link set GRE6Tun_To_KH up
```



## Kharej

```bash
ip -6 tunnel add GRE6Tun_To_IR mode ip6gre remote fc00::1 local fc00::2
ip addr add 192.168.13.2/30 dev GRE6Tun_To_IR
ip link set GRE6Tun_To_IR mtu 1436
ip link set GRE6Tun_To_IR up
```


<p align="center">===========================================================================




تا اینجای کار اگر دستورات رو درست وارد کرده باشید تانل GRE6 بین سرور خارج و ایران برقرار شده .
- آیپی v4 لوکال سرور ایران : 192.168.13.1
- آیپی v4 لوکال سرور خارج : 192.168.13.2

برای اطمینان از اینکه تانل برقرار شده باشه وارد هر دو سرور بشید و از آیپی سرور مقابل پینگ بگیرید.
- به عنوان مثل وارد سرور ایران میشیم و این دستور رو برای پینگ گرفتن آیپی v4 لوکال استفاده میکنیم : 
```shell
ping 192.168.13.2
```
- روی سرور خارچ نیز همین کار رو تکرار میکنیم :
```shell
ping 192.168.13.1
```
**تا اینجای کار تانل 6to4 برقرار شده و با استفاده از IP forward ترافیک رو به سمت تانلی که ساختیم میفرستیم :**





<p align="center">===========================================================================


## فوروارد کردن ترافیک با IP forward : (در سرور ایران)



## Iran


```bash
sysctl net.ipv4.ip_forward=1
iptables -t nat -A PREROUTING -p tcp --dport 22 -j DNAT --to-destination 192.168.13.1
iptables -t nat -A PREROUTING -j DNAT --to-destination 192.168.13.2
iptables -t nat -A POSTROUTING -j MASQUERADE 
```

<p align="center">===========================================================================


## توجه : 

بعد از ریبوت شدن سرور دستورات پاک میشوند ، در صورت نیاز مینوانید یک فایل ایجاد کنید و دستورات رو در داخل آن قرار دهید :


## Iran


```bash
sudo nano /etc/rc.local

```


```bash
#! /bin/bash
ip tunnel add 6to4_To_KH mode sit remote IP.KHARJ local IP.IRAN
ip -6 addr add fc00::1/64 dev 6to4_To_KH
ip link set 6to4_To_KH mtu 1480
ip link set 6to4_To_KH up

ip -6 tunnel add ipip6Tun_To_KH mode ipip6 remote fc00::2 local fc00::1
ip addr add 192.168.13.1/30 dev ipip6Tun_To_KH
ip link set ipip6Tun_To_KH mtu 1440
ip link set ipip6Tun_To_KH up

sysctl net.ipv4.ip_forward=1
iptables -t nat -A PREROUTING -p tcp --dport 22 -j DNAT --to-destination 192.168.13.1
iptables -t nat -A PREROUTING -j DNAT --to-destination 192.168.13.2
iptables -t nat -A POSTROUTING -j MASQUERADE 

exit 0

```

```bash
sudo chmod +x /etc/rc.local

```



## Kharej


```bash
sudo nano /etc/rc.local

```


```bash
#! /bin/bash
ip tunnel add 6to4_To_IR mode sit remote IP.IRAN local IP.KHARJ
ip -6 addr add fc00::2/64 dev 6to4_To_IR
ip link set 6to4_To_IR mtu 1480
ip link set 6to4_To_IR up

ip -6 tunnel add ipip6Tun_To_IR mode ipip6 remote fc00::1 local fc00::2
ip addr add 192.168.13.2/30 dev ipip6Tun_To_IR
ip link set ipip6Tun_To_IR mtu 1440
ip link set ipip6Tun_To_IR up

exit 0

```

```bash
sudo chmod +x /etc/rc.local

```



 
# تلگرام

[@ipmart_network](https://t.me/ipmart_network)

[@iPmart Group](https://t.me/ipmartnetwork_gp)




 # حمایت از ما :hearts:
حمایت های شما برای ما دلگرمی بزرگی است<br> 
<p align="left">
<a href="https://plisio.net/donate/kB7QU7f7" target="_blank"><img src="https://plisio.net/img/donate/donate_light_icons_mono.png" alt="Donate Crypto on Plisio" width="240" height="80" /></a><br>
	
|                    TRX                   |                       BNB                         |                    Litecoin                       |
| ---------------------------------------- |:-------------------------------------------------:| -------------------------------------------------:|
| ```TJbTYV1fFo2485sYMyajxGPLFzxmNmPrNA``` |  ```0x4af3de9b303a8d43105e284823d95b4c600961a3``` | ```MPrkzFiNtw4Rg67bbZB6gCxa9LV87orABM``` |	

</p>	




<p align="center">
<picture>
<img width="160" height="160"  alt="XPanel" src="https://github.com/iPmartNetwork/iPmart-SSH/blob/main/images/logo.png">
</picture>
  </p> 



