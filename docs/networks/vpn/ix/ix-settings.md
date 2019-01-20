# IX 設定

IX 設定はコマンドリファレンスに従って、コマンドを順番に設定していく。

!!! info "コマンドリファレンスマニュアル"
    [コマンドリファレンスマニュアル | NEC Manuals](https://www.manuals.nec.co.jp/contents/node/350)


## Parameter


## Configuration

### configure

設定 するために 特権設定モードに入ります

```
configure

```

!!! example "Console sample"

    ```
    Router# configure
    Enter configuration commands, one per line. End with CNTL/Z.
    Router(config)#

    ```


### hostname

ホスト名の設定

```
hostname ix01

```

!!! example "Console sample"

    ```
    Router(config)#
    Router(config)# hostname ix01
    ix01(config)#

    ```


### timezone

タイムゾーンの設定

タイムゾーンは `JST` である `+09 00` をセットする。

```
timezone +09 00

```

!!! example "Console sample"

    ```
    ix01(config)# timezone +09 00
    ix01(config)#

    ```

### terminal 
#### terminal suppress-emanon

ログイン認証抑止の設定

コンソールや TELNET ログイン時のユーザアカウント要求でリターン入力のみを行った場合に、認証動作を抑止します。

```
terminal suppress-emanon

```


### syslog 設定

|                 |           |
| :-------------- | :-------- |
| facility        | local3    |
| syslog サーバー | 192.0.2.1 |
| ip source       | 192.0.2.2 |

```
syslog facility local3
syslog id hostname
syslog ip enable
syslog ip host 192.168.2.1
syslog ip source 192.0.2.2
syslog timestamp datetime

```

!!! example "Console sample"

    ```
    ix01(config)# syslog facility local3
    ix01(config)# syslog id hostname
    ix01(config)# syslog ip enable
    ix01(config)# syslog ip host 192.168.2.1
    ix01(config)# syslog ip source 192.0.2.2
    ix01(config)# syslog timestamp datetime
    ix01(config)# 

    ```


#### syslog facility

syslog のファシリティを設定します。

IXは `local3` を指定する。

```
syslog facility local3

```


#### syslog id hostname

syslog メッセージにホスト名を付け、メッセージ を表示する時に、ホスト名が表示されるようにします。

```
syslog id hostname

```


#### syslog ip enable

SYSLOG の IPv4 での送信/停止を行います。

```
syslog ip enable

```

#### syslog ip host

syslog server の ip 指定 `192.168.101.66` をしてい。

```
syslog ip host 192.168.101.66

```


#### syslog ip source

SYSLOG の IPv4 ソースアドレスを設定します。

```
syslog ip source 192.168.100.254

```


#### syslog timestamp datetime

syslog メッセージにタイムスタンプを付け、メッセージを表示する時に、日時が表示されるようにします。

```
syslog timestamp datetime

```


### username

ユーザ・アカウントを作成します。

|          |               |
| :------- | :------------ |
| username | user01        |
| password | mekabu        |
| level    | administrator |


```
username user01 password plain 1 mekabu administrator

```

!!! example "Console sample"

    ```
    ix01(config)# username user01 password plain 1 mekabu administrator
    % User 'user01' has been added.
    ix01(config)#
    ```


### NTP 設定

timeout は デフォルト 60 秒で長いので調整。
priority は MFEED, NICT の順で設定
retry, source, interval は一括設定のコマンドがあるのでそちらを使用。

```
ntp server 210.173.160.87 timeout 10 priority 21
ntp server 133.243.238.164 timeout 10 priority 11
ntp source GigaEthernet1.0
ntp retry 10
ntp interval 3600

```

!!! warning "コマンドの警告"

    **`ntp server` は IP アドレスを指定する必要があります。**

    NTP は下記を利用すると良いでしょう

    * [インターネットマルチフィード時刻情報サービス for Public](http://www.jst.mfeed.ad.jp/index.html)
    * [NICT 公開 NTP サービス](http://jjy.nict.go.jp/ntp/)

    `dig` などで IP を確認してください。

    ```
    $ dig @1.1.1.1 ntp1.jst.mfeed.ad.jp
    210.173.160.27
    
    $ dig @1.1.1.1 ntp2.jst.mfeed.ad.jp +short
    210.173.160.57

    $ dig @1.1.1.1 ntp3.jst.mfeed.ad.jp +short
    210.173.160.87

    ```

    ```
    $ dig @1.1.1.1 ntp.nict.jp +short
    133.243.238.244
    133.243.238.163
    133.243.238.164
    133.243.238.243

    ```

    おすすめは `インターネットマルチフィード時刻情報サービス`, `NICT 公開 NTP サービス` を一つずつ設定する

    IX は同期したサーバーに毎回行くので、

    * インターネットマルチフィード時刻情報サービス
    * NICT 公開 NTP サービス

    の順番で入れると、マルチフィードが使えない場合 NICT をみにいくので、メンテナンスでマルチフィードが接続できなくても問題なく同期できます。

!!! warning "コマンドの警告"

    ```
    ntp ip enable

    ```

    は IX を NTP サーバーとして利用するための起動コマンドのため、ユーザーにNTPを提供しない場合は必要ない

!!! example "Console sample"

    ```
    ix01(config)# ntp server 210.173.160.87 timeout 10 priority 21
    ix01(config)# ntp server 133.243.238.164 timeout 10 priority 11
    ix01(config)# ntp source GigaEthernet1.0
    ix01(config)# ntp retry 10
    ix01(config)# ntp interval 3600

    ```


#### ntp interval

NTP クライアントの時刻同期間隔を設定します。

```
ntp interval 3600

```


#### ntp retry

リトライ回数を設定します。何も設定しなかった場合は、リトライは行いません。

```
ntp retry 10

```


#### ntp server

ルータ(NTP クライアント)の時刻同期をとるために参照する NTP サーバを設定します。

```
ntp server ADDRESS

```


#### ntp source

NTP で使用する要求送信元のインタフェースを設定します。

```
ntp source GigaEthernet0.0

```


### logging

```
logging buffered 131072 cyclic
logging subsystem all warn
logging timestamp datetime

```

!!! example "Console sample"

    ```
    ix01(config)# logging buffered 131072 cyclic
    ix01(config)# logging subsystem all warn
    ix01(config)# logging timestamp datetime
    ix01(config)#

    ```


#### logging buffered

イベントメッセージを指定サイズの文字数分メモリに保存します。

* 131072 byte (128kb) のバッファーを作成

```
logging buffered 131072 cyclic

```


#### logging subsystem

すべてのサブシステムのメッセージ表示を有効にします。

SEVERITY は `warn` にします

```
logging subsystem all warn

```

!!! memo "テスト時利用するよ良さそう"

    ```
    logging subsystem dhr notice
    logging subsystem ike notice
    logging subsystem ntp notice

    ```


#### サブシステム名

一部利用しそうな物を抜粋した

|      |                                    |
| :--- | :--------------------------------- |
| acl  | Access Control List                |
| all  | All subsystems                     |
| dh6  | DHCP for IPv6                      |
| dhc  | DHCP Client for IPv4               |
| dhr  | DHCP Relay Agent for IPv4          |
| dhs  | DHCP Server for IPv4               |
| dns  | Domain Name System                 |
| eap  | Extensible Authentication Protocol |
| gw   | Router Base                        |
| ike  | Internet Key Exchange Protocol     |
| ike2 | IKEv2                              |
| key  | IPSec Key Manager                  |
| ntp  | Network Time Protocol              |
| sec  | IPSec Traffic                      |
| snmp | Simple Network Management Protocol |
| ssh  | SSH Server                         |


#### ロギングレベル

!!! info "ロギングレベルについて"
    **IX2105/IX2106/IX2207 では、工場出荷時状態の初 期設定で全て `warn` レベルに設定されています。**

|        |                                             |
| :----- | :------------------------------------------ |
| error  | 1.Error conditions (important message only) |
| warn   | 2.Warning conditions                        |
| notice | 3.Normal but significant conditions         |
| info   | 4.Informational messages                    |
| debug  | 5.Debugging messages (all messages)         |


#### logging timestamp

メッセージにタイムスタンプを付ける

* `datetime` : 日時(datetime)を追加する

```
logging timestamp datetime

```


### ip ufs-cache

UFS（Unified Forwarding Service）キャッシュを有効にします。

```
ip ufs-cache enable
ip ufs-cache max-entries 65535
ipv6 ufs-cache enable
ipv6 ufs-cache max-entries 65535

```

!!! example "Console sample"

    ```
    ix01(config)# ip ufs-cache enable
    ix01(config)# ip ufs-cache max-entries 65535
    ix01(config)# ipv6 ufs-cache enable
    ix01(config)# ipv6 ufs-cache max-entries 65535
    ix01(config)# 

    ```


### ip route

* `default` は、 `Tunnel0.0` に向けます
* `192.0.2.1/32` は Cloud 側の VPN ルーターの IP アドレスに変更してください

```
ip route default Tunnel0.0
ip route 192.0.2.1/32 GigaEthernet0.0 dhcp

```

!!! example "Console sample"

    ```
    ix01(config)# ip route default Tunnel0.0
    ix01(config)# ip route 192.0.2.1/32 GigaEthernet0.0 dhcp
    ix01(config)#

    ```


### ip dhcp-relay

リレーが必要な場合に enable にします。

|                        |      |
| :--------------------- | :--- |
| 最大リレー回数         | 1    |
| 最低リレー経過時間設定 | 5 秒 |

* `maximum-hop` : リレー回数を 1 にすることで IX 以外でリレーされた物を再リレーされないようにしてみる。
* `minimum-retry-time` :リレーで帯域を圧迫したことがあるため、少し delay の設定を追加。


```
ip dhcp-relay enable
ip dhcp-relay maximum-hop 1
ip dhcp-relay minimum-retry-time 5

```

!!! example "Console sample"

    ```
    ix01(config)# ip dhcp-relay enable
    ix01(config)# ip dhcp-relay maximum-hop 1
    ix01(config)# ip dhcp-relay minimum-retry-time 5
    ix01(config)#

    ```

#### interface 設定

|                           |             |
| :------------------------ | :---------- |
| Cloud 側 DHCP             | 10.1.11.254 |
| 会場 管理インターフェイス | 10.2.11.254 |

```
interface GigaEthernet1.1
ip dhcp-relay server 10.1.11.254
  no shutdown
!

```

!!! info "ソースアドレスについて"

    下記のように記述して、 source アドレスを指定すること可能ですが、

    ```
    interface GigaEthernet1.1
      ...(sinp)...
      ip dhcp-relay server 10.1.11.254 source 10.2.11.254
      no shutdown
    !

    ```

    コマンドリファレンスには

    ```
    ソースアドレスを省略した場合、送信インタフェースのアドレスを使用します。
    ```

    と記載があったので、誤爆防止のため source 指定しないほうが無難かなーと思います。


### ip access-list

CONBU では、下記のようにネットワークを構成することが多いので参考までに記述します。

| region    | subnet     | CIRD            |
| :-------- | :--------- | :-------------- |
| **Cloud** |            | 10.200.0.0/16   |
|           | management | 10.200.200.0/24 |
|           | user       | 10.200.100.0/24 |
|           |
| **Venue** |            | 10.11.0.0/16    |
|           | management | 10.11.200.0/24  |
|           | user       | 10.11.0.0/20    |


* DHCP: `10.200.100.11`, `10.200.100.12`
* DNS: `10.200.100.21`, `10.200.100.22`


#### user_acl

* allow
    * Cloud
        * subnet: user
            * 10.200.100.0/24
    * Venue
        * WAN access
  * deny
    * Cloud
        * subnet: management
            * 10.200.200.0/24
    * Venue
        * subnet: user
            * 10.11.200.0/24

```
! allow cloud user DHCP, DNS...
ip access-list user_acl permit ip src 10.11.0.0/20 dest 10.200.100.0/24

! disallow user to cloud mgmt...
ip access-list user_acl permit ip src 10.11.0.0/20 dest 10.200.200.0/24

! disallow user to venue mgmt...
ip access-list user_acl deny ip src 10.11.0.0/20 dest 10.11.200.0/24

! allow user WAN access
ip access-list user_acl permit ip src any dest any

```

!!! example "Console sample"

    ```
    ix01(config)# ! allow cloud user DHCP, DNS...
    ix01(config)# ip access-list user_acl permit ip src 10.11.0.0/20 dest 10.200.10
                  0.0/24
    ix01(config)#
    ix01(config)# ! disallow user to cloud mgmt...
    ix01(config)# ip access-list user_acl permit ip src 10.11.0.0/20 dest 10.200.20
                  0.0/24
    ix01(config)#
    ix01(config)# ! disallow user to venue mgmt...
    ix01(config)# ip access-list user_acl deny ip src 10.11.0.0/20 dest 10.11.200.0
                  /24
    ix01(config)#
    ix01(config)# ! allow user WAN access
    ix01(config)# ip access-list user_acl permit ip src any dest any
    ix01(config)#

    ```

#### mgmt_acl

* allow
  * cloud: any
  * venue: any

```
! allow cloud ...
ip access-list mgmt_acl permit ip src 10.200.200.0/24 dest any

! allow venue...
ip access-list mgmt_acl permit ip src 10.11.200.0/24 dest any

```

!!! example "Console sample"

    ```
    ix01(config)# ip access-list mgmt_acl permit ip src 10.200.200.0/24 dest any
    ix01(config)#
    ix01(config)# ! allow venue...
    ix01(config)# ip access-list mgmt_acl permit ip src 10.11.200.0/24 dest any
    ix01(config)#

    ```


#### IPsec Tunnel

```
ip access-list ipsec_acl permit ip src any dest any

```

!!! example "Console sample"

    ```
    ix01(config)# ip access-list ipsec_acl permit ip src any dest any
    ix01(config)#

    ```

### IPsec


|                |                |
| :------------- | :------------- |
| イニシエータ側 | クライアント側 |
| レスポンダー側 | サーバ側       |


!!! memo "ハードウェア処理に対応している暗号/認証アルゴリズム"

    | 機種            | IX2105/IX2106/IX2207/IX2215/IX3315 |
    | :-------------- | :--------------------------------- |
    | 暗号            | DES,3DES,AES(128/192/256)          |
    | 認証            | MD5,SHA1,SHA2(256/384/512)         |
    | 秘密鍵生成(IKE) | Diffie-Hellman                     |

    上記の `IX2105/IX2106/IX2207/IX2215/IX3315` はハードウェア処理できるようです。

    また、 AES(128/192/256) はほぼ同等の性能が出るそうです。

    出典: [IPsec/IKE : FAQ : UNIVERGE IXシリーズ | NEC](https://jpn.nec.com/univerge/ix/faq/ipsec-ike.html#Q1-1)

```
ike initial-contact always
ike proposal ike-proposal encryption aes-256 hash sha2-512 group 2048-bit lifetime 28800
ike policy ike-policy peer 192.0.2.1 key pki-mekabu mode aggressive ike-proposal
ike keepalive ike-policy 10 3
ike local-id ike-policy fqdn onsite01.example.net
ike remote-id ike-policy fqdn cloud01.example.net
ike suppress-dangling ike-policy
ike nat-traversal policy ike-policy

ipsec autokey-map ipsec-mgmt ipsec_acl peer 192.0.2.1 ipsec-proposal pfs 2048-bit
ipsec autokey-proposal ipsec-proposal esp-aes-256 esp-sha2-512 lifetime time 3600
ipsec rekey remaining-lifetime default second 60
no ipsec ike-passthru
ipsec commit-bit ipsec-mgmt
ipsec local-id ipsec-mgmt 10.11.0.0/16
ipsec remote-id ipsec-mgmt 0.0.0.0/0

```

!!! example "Console sample"

    ```
    ix01(config)# ike initial-contact always
    ix01(config)# ike proposal ike-proposal encryption aes-256 hash sha2-512 group 2048-
                  bit lifetime 28800
    ix01(config)# ike policy ike-policy peer 192.0.2.1 key pki-mekabu mode aggressi
                  ve ike-proposal
    ix01(config)# ike keepalive ike-policy 10 3
    ix01(config)# ike local-id ike-policy fqdn onsite01.example.net
    ix01(config)# ike remote-id ike-policy fqdn cloud01.example.net
    ix01(config)# ike suppress-dangling ike-policy
    ix01(config)# ike nat-traversal policy ike-policy
    ix01(config)#
    ix01(config)# ipsec autokey-map ipsec-mgmt ipsec_acl peer 192.0.2.1 ipsec-prop pfs 2048-bit
                  osal
    ix01(config)# ipsec autokey-proposal ipsec-proposal esp-aes-256 esp-sha2-512 lifetim
                  e time 3600
    ix01(config)# ipsec rekey remaining-lifetime default second 60
    ix01(config)# no ipsec ike-passthru
    ix01(config)# ipsec commit-bit ipsec-mgmt
    ix01(config)# ipsec local-id ipsec-mgmt 10.11.0.0/16
    ix01(config)# ipsec remote-id ipsec-mgmt 0.0.0.0/0

    ```

#### ike initial-contact

IKE を始動する際に、常に INITIAL-CONTACT を送信します。

```
ike initial-contact always

```


#### ike proposal

IKE(Internet Key Exchange Security Protocol)で使用する IKE プロポーザル(アルゴリズム、認証手段、DH グループ、ライフタイム)を作成するためのコマンドです。
本コマンドで作成したIKEプロポーザルをIKEポリシー(ike policy)に登録します。

|                     |                |
| :------------------ | :------------- |
| IKE プロポーザル名  | `ike-proposal` |
| 暗号化アルゴリズム  | `aes-256`      |
| 認証アルゴリズム    | `sha2-512`     |
| DH グループの設定   | `2048-bit`     |
| SA が有効である時間 | `28800`        |

```
ike proposal ike-proposal encryption aes-256 hash sha2-512 group 2048-bit lifetime 28800

```


#### ike policy

|                         |                |
| :---------------------- | :------------- |
| IKE ポリシー名          | `ike-policy`   |
| SA を張る相手のアドレス | `192.0.2.1`    |
| 事前共有鍵              | `pki-mekabu`   |
| IKE プロポーザル名      | `ike-proposal` |


```
ike policy ike-policy peer 192.0.2.1 key pki-mekabu mode aggressive ike-proposal

```


#### ike keepalive

* IKE-POLICY IKE ポリシー名: `ike-policy`
* SECONDS キープアライブメッセージ送信間隔: `10` 秒
* SECONDS キープアライブメッセージ送信間隔: `3` 回


```
ike keepalive ike-policy 10 3

```


#### ike local-id

IKE フェーズ1 で送信する ID ペイロードの内容を設定するコマンドです。

|                |                        |
| :------------- | :--------------------- |
| IKE ポリシー名 | `ike-policy`           |
| ドメイン名     | `onsite01.example.net` |

```
ike local-id ike-policy fqdn onsite01.example.net

```


#### ike remote-id

IKE フェーズ1 で相手側から受信する ID ペイロードの内容を設定するコマンドです。

|                |                       |
| :------------- | :-------------------- |
| IKE ポリシー名 | `ike-policy`          |
| ドメイン名     | `cloud01.example.net` |

```
ike remote-id ike-policy fqdn cloud01.example.net

```


#### ike suppress-dangling

IPsec SA のダングリングを許可/抑止を設定するコマンドです。

|                |              |
| :------------- | :----------- |
| IKE ポリシー名 | `ike-policy` |


```
ike suppress-dangling ike-policy

```

#### ike nat-traversal

NAT-TRAVERSAL を有効にし、NAT-Keepalive パケット送信間隔を設定します。

|                                |              |
| :----------------------------- | :----------- |
| IKE ポリシー名                 | `ike-policy` |
| NAT-Keepalive パケット送信間隔 | `20` 秒      |

```
ike nat-traversal policy ike-policy 20

```

#### ipsec autokey-map

自動鍵ポリシーマップに、アクセスリスト名( IPsec フィルタ条件)、自動鍵プロポーザル、PFS 有無を登録するためのコマンドです。

|                                |                  |
| :----------------------------- | :--------------- |
| 自動鍵ポリシーマップ名         | `ipsec-mgmt`     |
| アクセスリスト名               | `ipsec_acl`      |
| SA を張る相手のアドレス        | `192.0.2.1`      |
| 自動鍵プロポーザル名           | `ipsec-proposal` |
| PFS（Perfect Forward Secrecy） | `2048-bit`       |

!!! warning
    PFS（Perfect Forward Secrecy） ですが、 デフォルトでは `off` のため、 pfSense での設定を一致せず、 phase2 が失敗することがあります。

    `2048-bit` を明示的に指定して有効化します。


```
ipsec autokey-map ipsec-mgmt ipsec_acl peer 192.0.2.1 ipsec-proposal pfs 2048-bit

```

#### ipsec autokey-proposal

IPsec 自動鍵交換時における自動鍵プロポーザル(アルゴリズム、ライフタイム)を作成するためのコマンドです。

!!! warning

    自動鍵交換を実行するには、ike proposal、ike policy コマンドで IKE(Internet Key Exchange Security Protocol)のポリシー設定を同時に行う必要があります。

|                      |                  |
| :------------------- | :--------------- |
| 自動鍵プロポーザル名 | `ipsec-proposal` |
| ESP 暗号アルゴリズム | `esp-sha2-512`   |
| SA が有効である時間  | `3600` 秒        |

```
ipsec autokey-proposal ipsec-proposal esp-aes-256 esp-sha2-512 lifetime time 3600

```


#### ipsec rekey remaining-lifetime default second

IPsec SA を更新するタイミングをグローバルに設定します。

|                     |         |
| :------------------ | :------ |
| lifetime の残り時間 | `60` 秒 |

```
ipsec rekey remaining-lifetime default second 60

```


#### ipsec ike-passthru

IKE パケット(ポート 500 番)に対して IPsec を適用するか否かを設定します。

!!! warning
    参加者が外部に IPsec で外部にVPNを貼る場合、 IKE パケットを IPsec でカプセル化せず、送信してしまうため接続できない。

    また、デフォルトでは **有効。IKE パケットに対して IPsec を適用しません。** のため、明示的に `no ipsec ike-passthru` をする必要あり。

    出典: [CONBU LL Diver/YAPC::Asia 2014 Network](https://www.slideshare.net/YuyaTakahashi8241/conbu-ll-diveryapcasia-2014-network)

```
no ipsec ike-passthru

```


#### ipsec commit-bit

IKE フェーズ 2 でのコミットビット機能を有効にするコマンドです。

|                        |              |
| :--------------------- | :----------- |
| 自動鍵ポリシーマップ名 | `ipsec-mgmt` |

```
ipsec commit-bit ipsec-mgmt

```

#### ipsec local-id

IKE フェーズ 2 で送信する ID ペイロード(自装置側 ID )の内容を設定するコマンドです。

|                        |                |
| :--------------------- | :------------- |
| 自動鍵ポリシーマップ名 | `ipsec-mgmt`   |
| 自装置側の ID 設定     | `10.11.0.0/16` |

```
ipsec local-id ipsec-mgmt 10.11.0.0/16

```

#### ipsec remote-id

IKE フェーズ 2 で送信する ID ペイロード(相手装置 ID)の内容を設定するコマンドです。

|                        |              |
| :--------------------- | :----------- |
| 自動鍵ポリシーマップ名 | `ipsec-mgmt` |
| 自装置側の ID 設定     | `0.0.0.0/0`  |

```
ipsec remote-id ipsec-mgmt 0.0.0.0/0

```


### SNMP

SNMP 設定をします。
トラフィックデータなどを取得するのが目的です。

```
snmp-agent contact FoxBoxsnet
snmp-agent ip enable
snmp-agent ip community try-conbu mgmt_acl
snmp-agent ip host 192.168.101.66 try-conbu version 2 
snmp-agent ip host 192.168.101.67 try-conbu version 2 
snmp-agent ip trap-source GigaEthernet1.1
snmp-agent ip trap-port 162
snmp-agent location home

```

!!! example "Console sample"

    ```
    ix01(config)# snmp-agent contact FoxBoxsnet
    ix01(config)# snmp-agent ip enable
    ix01(config)# snmp-agent ip community try-conbu mgmt_acl
    ix01(config)# snmp-agent ip host 192.168.101.66 try-conbu version 2
    ix01(config)# snmp-agent ip host 192.168.101.67 try-conbu version 2
    ix01(config)# snmp-agent ip trap-source GigaEthernet1.1
    ix01(config)# snmp-agent ip trap-port 162
    ix01(config)# snmp-agent location home
    ix01(config)#

    ```


#### snmp-agent contact

連絡先（sysContact）を設定します。

|                      |              |
| :------------------- | :----------- |
| 連絡先（sysContact） | `FoxBoxsnet` |


```
snmp-agent contact FoxBoxsnet

```


#### snmp-agent ip enable

SNMP エージェントにおけるSNMP を有効にします。

```
snmp-agent ip enable

```


#### snmp-agent ip community

コミュニティの設定を行います(コミュニティ名がない場合は追加を行います)。

|                  |             |
| :--------------- | :---------- |
| コミュニティ名   | `try-conbu` |
| アクセスリスト名 | `mgmt_acl`  |

```
snmp-agent ip community try-conbu mgmt_acl

```


#### snmp-agent ip host

トラップ送信先の IP アドレスを追加します。

|                            |                  |
| :------------------------- | :--------------- |
| トラップ送信先 IP アドレス | `192.168.101.66` |
| コミュニティ名             | `try-conbu`      |
| SNMP バージョン            | `2`              |

```
snmp-agent ip host 192.168.101.66 try-conbu version 2 
snmp-agent ip host 192.168.101.67 try-conbu version 2 


```


#### snmp-agent ip trap-source

SNMP エージェントのトラップ送信元アドレス、エージェントアドレスを設定します。

|                  |                   |                                         |
| :--------------- | :---------------- | :-------------------------------------- |
| インタフェース名 | `GigaEthernet1.1` | management vlan の interface を指定する |

```
snmp-agent ip trap-source GigaEthernet1.1

```


#### snmp-agent location

|                                 |        |
| :------------------------------ | :----- |
| 装置の物理的位置（sysLocation） | `home` |

```
snmp-agent location home

```


### ip name-server

DNS サーバアドレスを登録します。

|                    |                         |
| :----------------- | :---------------------- |
| DNS サーバアドレス | `1.1.1.1`<br> `1.0.0.1` |

参考に CloudFlare の物を使います

```
ip name-server 1.1.1.1
ip name-server 1.0.0.1

```

!!! example "Console sample"

    ```
    ix01(config)# ip name-server 1.1.1.1
    ix01(config)# ip name-server 1.0.0.1
    ix01(config)#

    ```


### ssh 設定

ssh の設定をしていきます。

リモートでアクセスできることが必要になることがあるため、実施。

```
ssh-server ip access-list mgmt_acl
ssh-server ip enable
pki private-key generate rsa
ssh-server ip port 22

```

!!! example "Console sample"

    ```
    ix01(config)# ssh-server ip access-list mgmt_acl
    ix01(config)# ssh-server ip enable
    ix01(config)# pki private-key generate rsa
    Generating RSA private key ........
    ix01(config)# ssh-server ip port 22
    ix01(config)#

    ```



#### ssh-server ip access-list

ssh サーバで使用するアクセスリストを指定します。


|                  |            |
| :--------------- | :--------- |
| アクセスリスト名 | `mgmt_acl` |

```
ssh-server ip access-list mgmt_acl

```


#### ssh-server ip enable

IPv4 ssh サーバを起動します。 

```
ssh-server ip enable
pki private-key generate rsa

```

!!! warning
    ホスト鍵として使用する秘密鍵を別途コマンド (pki private-key generate)で生成しなければ ssh 接続は出来ません。


#### ssh-server ip port

ssh サーバで使用する TCP ポート番号を指定します。

|            |      |
| :--------- | :--- |
| ポート番号 | `22` |

```
ssh-server ip port 22

```


### watch-group

Watch Group を作成し、 Watch Group コンフィグモードへ遷移します。

```
watch-group cloud 10
  event 10 ip unreach-host 192.168.101.254 Tunnel0.0 source Tunnel0.0
  action 10 ipsec clear-sa Tunnel0.0 policy ipsec-mgmt
  probe-counter variance 3 
  probe-counter restorer 5
  probe-counter watch 3
  probe-timer restorer 60
  probe-timer variance 5
  probe-timer wait 3
!
network-monitor cloud enable

```

!!! example "Console sample"

    ```
    ix01(config)# watch-group cloud 10
    ix01(config-watch-cloud-10)#   event 10 ip unreach-host 192.168.101.254 Tunnel0
                                .0 source Tunnel0.0
    ix01(config-watch-cloud-10)#   action 10 ipsec clear-sa Tunnel0.0 policy ipsec-
                                mgmt
    ix01(config-watch-cloud-10)#   probe-counter variance 3
    ix01(config-watch-cloud-10)#   probe-counter restorer 5
    ix01(config-watch-cloud-10)#   probe-counter watch 3
    ix01(config-watch-cloud-10)#   probe-timer restorer 60
    ix01(config-watch-cloud-10)#   probe-timer variance 5
    ix01(config-watch-cloud-10)#   probe-timer wait 3
    ix01(config-watch-cloud-10)# !
    ix01(config-watch-cloud-10)# network-monitor cloud enable
    ix01(config-watch-cloud-10)# exit
    ix01(config)#

    ```


#### event

指定した状態で event を起動します。

本 event は即座に実行します。

|                        |                   |
| :--------------------- | :---------------- |
| シーケンス NO          | `10`              |
| IPSec Cloud 側         | `192.168.101.254` |
| Tunnelインターフェイス | `Tunnel0.0`       |


```
event 10 ip unreach-host 192.168.101.254 Tunnel0.0 source Tunnel0.0

```


#### action

event 発生/復旧時に、アクションリストに登録した実行コマンドを実行します。

|                           |              |
| :------------------------ | :----------- |
| シーケンス NO             | `10`         |
| IPsec SA をクリアーにする | `clear-sa`   |
| IPsec ポリシー名          | `ipsec-mgmt` |

```
action 10 ipsec clear-sa Tunnel0.0 policy ipsec-mgmt

```


#### probe-counter variance

障害が発生していると見なすまでの連続監視失敗回数。

|                                |        |
| :----------------------------- | :----- |
| event 発生とする監視回数（回） | `3` 回 |

```
  probe-counter variance 3

```

#### probe-counter restorer

障害が復旧したと見なすまでの連続監視成功回数。

|                                 |        |
| :------------------------------ | :----- |
| pevent 復旧とする監視回数（回） | `5` 回 |

```
probe-counter restorer 5

```


#### probe-counter watch

一回の監視で送信するpingの数。

|                                   |        |
| :-------------------------------- | :----- |
| 監視 1 回に送出する ping 数（回） | `3` 回 |

```
probe-counter watch 3

```

#### probe-timer restorer

正常な状態の監視間隔。

|                      |      |
| :------------------- | :--- |
| event 復旧周期（秒） | `60` |


```
probe-timer restorer 60

```


#### probe-timer variance

障害発生時の監視間隔。

|                      |      |
| :------------------- | :--- |
| event 発生周期（秒） | `5`  |

```
probe-timer variance 5

```


#### probe-timer wait

Ping監視のタイムアウト時間。

|                                            |      |
| :----------------------------------------- | :--- |
| ホスト監視用 ping のタイムアウト時間（秒） | `3`  |

```
probe-timer wait 3

```


### network-monitor

指定した Watch Group の監視を起動します。

|                |         |
| :------------- | :------ |
| Watch Group 名 | `cloud` |

```
network-monitor cloud enable

```

### interface


#### WAN

上流とは dhcp でipアドレスを管理するのでそのように設定します

!!! warning

    ```
    no shutdown
    ```

    を忘れずに実施しましょう。<br>(繋がんなくてオロオロすることになる)

```
interface GigaEthernet0.0
  ip address dhcp
  no shutdown

```

!!! example "Console sample"

    ```
    ix01(config)# interface GigaEthernet0.0
    ix01(config-GigaEthernet0.0)# ip address dhcp
    ix01(config-GigaEthernet0.0)# no shutdown
    ix01(config-GigaEthernet0.0)# exit
    ix01(config)#

    ```


#### MGMT, USER

vlan で切るため、 その設定を実施します。
各インターフェイスに割り当てる IP アドレス, サブネットマスクに注意してください。

CONBU では通例, vlan の降り方が大体あります、今回は下記のように振ります

| subnet | vlan id | description  |
| :----- | :------ | :----------- |
| MGMT   | 3000    | 管理VLAN     |
| USER   | 3001    | ユーザーVLAN |


!!! info
    よく、 MGMT と USER ののサブネットマスクが間違っていて悲しくなることがあります。


```
interface GigaEthernet1.1
  description MGMT VLAN
  encapsulation dot1q 3000 tpid 8100
  auto-connect
  ip address 10.11.200.1/24
  ip filter mgmt_acl 10 in
  no shutdown
!
interface GigaEthernet1.2
  description USER VLAN
  encapsulation dot1q 3001 tpid 8100
  auto-connect
  ip address 10.11.16.1/20
  ip filter user_acl 10 in
  no shutdown

```

!!! example "Console sample"

    ```
    ix01(config)# interface GigaEthernet1.1
    ix01(config-GigaEthernet1.1)#   description MGMT VLAN
    ix01(config-GigaEthernet1.1)#   encapsulation dot1q 3000 tpid 8100
    % You must restart the router for this configuration to take effect.
    ix01(config-GigaEthernet1.1)#   auto-connect
    ix01(config-GigaEthernet1.1)#   ip address 10.11.200.1/24
    ix01(config-GigaEthernet1.1)#   ip filter mgmt_acl 10 in
    ix01(config-GigaEthernet1.1)#   no shutdown
    ix01(config-GigaEthernet1.1)# !
    ix01(config-GigaEthernet1.1)# interface GigaEthernet1.2
    ix01(config-GigaEthernet1.2)#   description USER VLAN
    ix01(config-GigaEthernet1.2)#   encapsulation dot1q 3001 tpid 8100
    % You must restart the router for this configuration to take effect.
    ix01(config-GigaEthernet1.2)#   auto-connect
    ix01(config-GigaEthernet1.2)#   ip address 10.11.16.1/20
    ix01(config-GigaEthernet1.2)#   ip filter user_acl 10 in
    ix01(config-GigaEthernet1.2)#   no shutdown
    ix01(config-GigaEthernet1.2)#
    ix01(config-GigaEthernet1.2)# exit
    ix01(config)#
    ```


#### Tunnel

ipsec トンネルインターフェイスを作成します。

* `ip mtu 1280` は参考値であり、変更が必要です。
* `ip tcp adjust-mss auto` を利用することで MSS を MTU から自動計算することができます。

```
interface Tunnel0.0
  description To CLoud
  tunnel mode ipsec
  ip unnumbered GigaEthernet1.1
  ip mtu 1280
  ip tcp adjust-mss auto
  ipsec policy tunnel ipsec-mgmt out
  no shutdown

```

!!! example "Console sample"

    ```
    ix01(config)# interface Tunnel0.0
    ix01(config-Tunnel0.0)#   description To CLoud
    ix01(config-Tunnel0.0)#   tunnel mode ipsec
    ix01(config-Tunnel0.0)#   ip unnumbered GigaEthernet1.1
    ix01(config-Tunnel0.0)#   ip mtu 1280
    ix01(config-Tunnel0.0)#   ip tcp adjust-mss auto
    ix01(config-Tunnel0.0)#   ipsec policy tunnel ipsec-mgmt out
    ix01(config-Tunnel0.0)#   no shutdown
    ix01(config-Tunnel0.0)#
    ix01(config-Tunnel0.0)# exit
    ix01(config)#
    ```

## サンプル設定

!!! info
    TBD
