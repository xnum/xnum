### 2021

職位從VP強迫更名為CTO

機器增長到難以一台一台澆水照顧的狀態。系統也面臨單機Docker無法解決的問題：更新或機器下線的維護時間、數字踩到Docker本身的bug、網路架構受限、跨節點協調和調度困難...。開始往kubernetes的遷入計劃。

買了半櫃的中古機架伺服器和10G網路設備回來做硬體架構實驗，包含虛擬化方案、儲存設備配置、網路配置...。

嘗試架kubernetes：crio and containerd、open-rc and systemd、cgroup fs v1, v2、編譯kernel開啟各種module和support、calico, weave, multus, macvlan、各種pv、各種feature gate和admission。

#### 公司內部服務

不使用kubernetes的服務和機器需求選擇開vm解決。初期研究認為database over iscsi效能不夠好，改採超融合方式搭配SSD raid10 pool運行資料庫。其他節點運行不重要的服務。

vm server使用proxmox ve串成cluster (money aware)

kubernetes叢集使用on-perm + bare-metal建置。master node偷懶丟進vm裡面執行。一般node採用gentoo為OS。

所有節點串在一台10G switch上面走SFP+介面，形成單一L2 Network，kubernetes則使用calico作為in-cluster networking。儲存需求則在SSD raid node上使用hostPath pv。

ingress鏈路使用 pfsense (port forwarding) -> keepalived + haproxy -> NodePort -> ingress nginx -> service

全面回收和檢討IP使用，將所有服務納進統一nginx server再往後端打。

升級公司網路交換器並加大交換頻寬，從1G拉到40G。學習了各種網路知識，重新調整網段和VLAN設計。 (從taobao買了各式各樣的東西回來兜，雖然都不是我下單的)

pfsense開啟NAT reflection，廢棄內部DNS設計，使得所有公司LAN流量都可以順利存取內部服務而不需要內部DNS。

DHCP server搭配DNS forwarder實現自動註冊內部domain name，終於不用再背內部機器IP列表。

#### 公司產品

研究決定了新機房的機器規格，用來汰舊換新兼改架構。

使用kubernetes運行服務，針對底下儲存層和網路層進行強化。

網路層使用兩台Dell switch設定VLT做成active-active式HA，實體節點則用bonding作為網路介面。firewall+vpn使用pfsense HA實現 (拿了一個/28的WAN IP來用)

儲存層同樣採用hostPath pv，但備有兩台獨立節點底下運行nvme ssd raid10 pool，在軟體搭建時可選擇於兩台節點都執行程式，並用軟體內建功能達到HA。

原本預計採用zfs 2.0.x，但效能差距mdadm+LVM過大，最後棄用。

infra層將主要資料庫改用TiDB：3 PD、5 TiKV架構，搭配3 node redis + sentinel、3 node nats streaming

系統開發：

app層將大部分程式翻修為golang version，並符合運行於kubernetes上之需求特徵 (alive/ready probe, prometheus metrics, stateless, logging)

snowflake algorithm微調改採POD IP替代原本的instance ID設計，修改程式為deployment方式運作並搭配程式化控制pod auto scaling。

程式內部使用multus + macvlan CNI組合進行網路連外微加速。

#### 人員訓練

痛定思痛導入gerrit + jenkins，實行per commit review。
