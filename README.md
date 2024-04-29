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


تمامی براحا رو خط به خط برید جلو بجای IP.iran و IP.kharej ای پی های سرور خودتون رو وارد کنید


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
