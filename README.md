# README

## ２つのボリュームマウントの例
```
[student@workstation podman-demo]$ mkdir data1 data2
[student@workstation podman-demo]$ podman build -t hellodo180 workdir
[student@workstation podman-demo]$ podman run -it -v ~/podman-demo/data1:/data1:Z -v ~/podman-demo/data2:/data2:Z  localhost/hellodo180
[root@2a910dec57a0 /]# echo 'data1' > /data1/data1.txt
[root@2a910dec57a0 /]# echo 'data2' > /data2/data2.txt
[root@2a910dec57a0 /]# exit
exit
[student@workstation podman-demo] $ cat data1/data1.txt 
data1
[student@workstation podman-demo] $ cat data1/data1.txt 
data1
```


