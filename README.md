# README

# イメージのビルドとレイヤーの表示
* ubiを親に持つカスタムコンテナーイメージをビルドする。
* 生成されたイメージのヒストリーを確認することで、ubiの上の新たなレイヤーが構築されていることを確認する。
```
[student@workstation podman-demo]$ cat workdir/Containerfile
FROM ubi9/ubi
RUN mkdir /data1 /data2
CMD /bin/bash

[student@workstation podman-demo]$ podman build -t hellodo180 workdir
STEP 1/3: FROM ubi9/ubi
Resolved "ubi9/ubi" as an alias (/etc/containers/registries.conf.d/000-shortnames.conf)
Trying to pull registry.access.redhat.com/ubi9/ubi:latest...
<略>

[student@workstation podman-demo]$ podman images
REPOSITORY                           TAG         IMAGE ID      CREATED        SIZE
localhost/hellodo180                 latest      568ade886b23  6 seconds ago  217 MB
registry.access.redhat.com/ubi9/ubi  latest      781f92ea81ad  2 weeks ago    217 MB

[student@workstation podman-demo]$ podman history localhost/hellodo180:latest 
ID            CREATED         CREATED BY                                     SIZE        COMMENT
20c73644b365  58 seconds ago  /bin/sh -c #(nop) CMD /bin/bash                0B          FROM 20c73644b365
<missing>     58 seconds ago  /bin/sh -c mkdir /data1 /data2                 4.1kB       FROM registry.access.redhat.com/ubi9/ubi:latest
781f92ea81ad  2 weeks ago     /bin/sh -c mv -fZ /tmp/ubi.repo /etc/yum.r...  217MB       
<missing>     2 weeks ago     /bin/sh -c rm -f /tmp/tls-ca-bundle.pem        0B          
<missing>     2 weeks ago     /bin/sh -c rm -f '/etc/yum.repos.d/odcs-22...  0B          
<missing>     2 weeks ago     /bin/sh -c #(nop) LABEL "release"="722.169...  0B          
<missing>     2 weeks ago     /bin/sh -c #(nop) ADD file:e4818bdd48eca78...  0B          
<missing>     2 weeks ago     /bin/sh -c #(nop) ADD file:fde3c80d4331562...  0B          
<missing>     2 weeks ago     /bin/sh -c mkdir -p /var/log/rhsm              0B          
<missing>     2 weeks ago     /bin/sh -c rm -rf /var/log/*                   0B          
<missing>     2 weeks ago     /bin/sh -c #(nop) CMD ["/bin/bash"]            0B          
<missing>     2 weeks ago     /bin/sh -c #(nop) ENV PATH /usr/local/sbin...  0B          
<missing>     2 weeks ago     /bin/sh -c #(nop) ENV container oci            0B          
<missing>     2 weeks ago     /bin/sh -c #(nop) LABEL io.openshift.tags=...  0B          
<missing>     2 weeks ago     /bin/sh -c #(nop) LABEL io.openshift.expos...  0B          
<missing>     2 weeks ago     /bin/sh -c #(nop) LABEL io.k8s.display-nam...  0B          
<missing>     2 weeks ago     /bin/sh -c #(nop) LABEL description="The U...  0B          
<missing>     2 weeks ago     /bin/sh -c #(nop) LABEL summary="Provides ...  0B          
<missing>     2 weeks ago     /bin/sh -c #(nop) LABEL com.redhat.license...  0B          
<missing>     2 weeks ago     /bin/sh -c #(nop) LABEL com.redhat.compone...  0B          
<missing>     2 weeks ago     /bin/sh -c #(nop) LABEL maintainer="Red Ha...  0B          
<missing>     2 weeks ago     /bin/sh -c #(nop) ADD multi:b0ab630b23847a...  0B          
<missing>     2 weeks ago     /bin/sh -c #(nop) ADD file:214c1de395c24e4...  0B          
<missing>     2 weeks ago     /bin/sh -c mv -f /etc/yum.repos.d/ubi.repo...  0B          
<missing>     2 weeks ago     /bin/sh -c #(nop) ADD file:c3c0082ae3828a5...  0B   
```

## ２つのボリュームマウントの例
* 外部ディレクトリをマウントして、コンテナー内部でマウントポイントにファイルを格納する。
* コンテナーのプロセスが終了した後も外部ディレクトリにファイルが格納されていることを確認する。
```
[student@workstation podman-demo]$ mkdir data1 data2
[student@workstation podman-demo]$ podman run -it -v ~/podman-demo/data1:/data1:Z -v ~/podman-demo/data2:/data2:Z  localhost/hellodo180
[root@2a910dec57a0 /]# ls -ld data* 
drwxr-xr-x. 1 root root 0 Sep  7 14:44 data1
drwxr-xr-x. 1 root root 0 Sep  7 14:44 data2
[root@2a910dec57a0 /]# echo 'data1' > /data1/data1.txt
[root@2a910dec57a0 /]# echo 'data2' > /data2/data2.txt
[root@2a910dec57a0 /]# exit
exit
[student@workstation podman-demo] $ cat data1/data1.txt 
data1
[student@workstation podman-demo] $ cat data1/data1.txt 
data1
```


