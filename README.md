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


تمامی مراحل رو خط به خط برید جلو بجای IP.iran و IP.kharej ای پی های سرور خودتون رو وارد کنید


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




<p align="center">=============================================================================



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


<p align="center">=============================================================================






