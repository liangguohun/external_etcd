# [来源](https://blog.csdn.net/lswzw/article/details/109027255)

-- ca-config.json 中 \
server auth表示client可以用该ca对server提供的证书进行验证 \
client auth表示server可以用该ca对client提供的证书进行验证

# 步骤一: 
etcd.json  \ 
etcd.service  \ 
每个节点都要修改

# 步骤二：
第一个节点要执行 init_etcd \

第二个节点起执行 addNode \
及拷贝第一个节点生成的证书 \
scp -r /data/etcd 192.168.3.5:/data/etcd \
scp -r /data/etcd 192.168.3.5(当前节点):/data/etcd  \

后执行： \
systemctl daemon-reload \
systemctl enable etcd \
systemctl start etcd \
systemctl status etcd

# 步骤三：
验证 \
cd /data/etcd/ssl 
## 查看状态
../bin/etcdctl --ca-file=ca.pem --cert-file=server.pem --key-file=server-key.pem --endpoints="https://192.168.3.4:2379" cluster-health 
## 查看集群主机
../bin/etcdctl --ca-file=ca.pem --cert-file=server.pem --key-file=server-key.pem --endpoints="https://192.168.3.4:2379" member list 

# 备份恢复
备份脚本 etcd_backup 需要适当修改 通过cronb 定时备份 \
etcdctl snapshot restore snapshot.db.2021-08-28-10-02 -data-dir /data/etcd \

restore： 指定用于恢复的快照文件 \
-data-dir：恢复到哪个目录
