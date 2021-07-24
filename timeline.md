## 這份文件的用意

我並不怎麼喜歡所謂的美式履歷，其中的資訊太過理性、量化，在大量審閱的時候很有幫助，但並不能更深入的理解這個人的貢獻或經歷。但自傳式的內容又太過隱惡揚善，著重於博得一份工作機會。

我想用較為長篇鬆散的形式表達分享過去經歷了哪些事情，並不是作為求職用，而是在這之上的背後故事。

## ???

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

### 2018-06 ~ 2019-05 第二份工作

### 2017-06 ~ 2018-06 第一份工作

## 研究所 

研究所以前的記錄僅是事後追憶，記載一些人生重大的事件或趣事，其來源多半為零散的信件、披兔隱藏舊文，或有主觀或個人情感色彩。


我是第一屆的戊組推甄進去的，那時候交大的畢業學分是24分，由於大部分偏理論的課我完全不行，所以當初選的全部是實作型課程 (programming結尾的)

要求一門英文授課，有投影片的情況下只聽一半懂還是可以順利pass，考券可以寫中文所以英文爛也沒太大困擾，畢業也沒有英文考試要求

所以儘管研究所畢業，我的英文聽說還是爛的可以，寫的也不怎麼樣


戊組除了上課以外另一項義務就是當PT勞力，當時每學期都可以兼到兩三個計畫名額，每個月一兩萬塊剛好可以應付學費+吃飯+租房

常態性兼職的是程式檢定助教，大概就是系上程式考試的時候幫忙巡場講解題目或給予適當幫助

另外就是幫忙學校裡面的單位開發一些網站，第一個兼差的是師陪中心的一個網站，這也是第一次學MVC開始寫網站，用Laravel

以前都是寫前後端黏在一起的噁心PHP程式碼，這算是第一次用上了現代的東西。

後來幫忙作了一個自動讓教職員學生可以申請office和gmail教育版的網站，就串學校的SSO打個api開帳號，

為了做這個網站拿了許多管理員權限，不過我是個正義的人，是不會拿來做壞事的！


也因為這個戊組的緣故，除了研究生會在實驗室有個位置之外，我在校計中的三樓房間裡也有一個位置，我大部分的時間都是待在校計中

只有實驗室meeting的時候會大家一起在二樓的會議室開會。比起大部分人比較懷念實驗室，我反而比較懷念校計中。

每個戊組同學都會拿到一把辦公室鑰匙，很爛的喇叭鎖那種，那對我的意義就像自己在學校的一個小天地，或是曾經住過的一個家一樣。

反正有免錢的冷氣可以吹+有電腦可以打遊戲上網，而且到二餐買東西、樓下買鬆餅飲料也近，不知道在那裡混了多少個深夜的日子

常常晚上12點附近走到機車停車場，再騎光復路回去睡覺。


論文完全是老師說了算，雖然我本來就是個喜歡主動探索一些莫名其妙東西的人，不過也要感謝老師支持做這個題目也沒有為難

從現在的我來看的話這算是做的很粗糙的。寫著寫著順利的就四月口試五月離校了

我還特地把圖書館寄給我的［電子論文審核通過通知單］給留在gmail裡面，直到現在都還可以體會到我當初收到這封信有多開心

因為這代表我幾十年地獄般的求學生涯終於結束了。


p.s. 沒用到的東西就逐漸忘記，現在很多演算法跟leetcode題目應該都沒辦法快速寫出來了，只剩下到處copy人家程式的能力

看了一下丁戊組的性向測驗感覺好難QQ，我還是只會1/3~1/2，反正工作用到再去學吧，實際上在學校裡也沒學到或用到那麼多東西就是了

大概也是沒資深工程師當mentor的緣故吧或是沒興趣做，什麼都自己摸也沒人帶入門效率就很差，也不會知道有什麼更好的做法或技術。

好像現在一堆校務系統的東西都是Laravel，仔細想想好像跟我(們)脫離不了關係，畢竟當年不知道為啥就決定用了這個抱歉啦。

現在的話我應該完全anti這種動態型別語言吧，我還是喜歡go比較多啦，再不濟也要用.net吧。
