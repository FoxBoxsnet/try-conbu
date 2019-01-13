# pfSense

クラウド上に、 VPN サーバーを構築します。

pfSense とありますが、 pfSense を利用している理由は、pfSense の利用実績が多いので pfSense を利用しています。

## 初期設定

pfSense は、[Download pfSense Community Edition](https://www.pfsense.org/download/) でダウンロードできます。

64bit 版をダウンロードして、解凍して iso イメージの状態にまで頑張ってください。


### さくらのクラウドでインスタンス作成

!!! info
    TBD


### コンソールセットアップ

#### インストール

![](img/pfsense-settings/console-setup/install/001.png)
![](img/pfsense-settings/console-setup/install/002.png)
![](img/pfsense-settings/console-setup/install/003.png)
![](img/pfsense-settings/console-setup/install/004.png)
![](img/pfsense-settings/console-setup/install/005.png)
![](img/pfsense-settings/console-setup/install/006.png)
![](img/pfsense-settings/console-setup/install/007.png)


#### VLAN 設定

起動したら下記のような画面になります。

![](img/pfsense-settings/console-setup/vlan-setting/001.png)

```
Should VLANs be set up now [y|n]? n

```

は、VLAN を利用してセットアップするかの設定になります。
さくらのクラウドや他のクラウドでも、 VLAN に対応しているクラウドはすくないため、 `n` と押します。


#### interface 設定

![](img/pfsense-settings/console-setup/interface-setting/002.png)

次に順番にインターフェイスの割当をしていきます。

| interface nemae | subnet    | description                                         |
| :-------------- | :-------- | :-------------------------------------------------- |
| vtnet0          | WAN       | グローバルアドレスがつくスイッチ                    |
| vtnet1          | LAN(MGMT) | `LAN` となってるが管理側のNIC(あとで名前を変える!!) |
| vtnet2          | LAN       | ユーザー側のNIC                                     |

!!! warning
    `vtnet` の部分の文字列は環境によって変更になることがあります。

順番、に設定していきます。


#### LAN(MGMT) interface の IP 設定

![](img/pfsense-settings/console-setup/interface-setting/003.png)

**`2) Set interface(s) IP address`** を選択したいので `2` を入力します。

![](img/pfsense-settings/console-setup/interface-setting/004.png)
![](img/pfsense-settings/console-setup/interface-setting/005.png)

!!! warning
    ②, ③ は毎回変わるはずなので要確認

| No.  |                                                                  |
| :--- | :--------------------------------------------------------------- |
| ①    | 上のリストから LAN(MGMT) に当たる `2` 番のインターフェイスを選択 |
| ②    | クラウド管理ネットワークのアドレス: `10.1.11.254`                |
| ③    | クラウド管理ネットワークのサブネットマスク: `24`                 |
| ④    | 何も設定せずに `Enter`                                           |
| ⑤    | 何も設定せずに `Enter`                                           |
| ⑥    | (DHCPサーバーは別途用意するので): `n`                            |
| ⑦    | 今後の設定は WebConfigurator を利用して実施するので: `y`         |
| ⑧    | ここに記載されているアドレスにブラウザでアクセス                 |
| ⑨    | 何も設定せずに `Enter`                                           |



![](img/pfsense-settings/console-setup/interface-setting/006.png)

この画面になれば、 赤枠の部分に設定したアドレスが表示されていることを確認

VPCルーターに VPN を張り、 今回だと `http://10.1.11.254` で管理画面が表示されることを確認してください。


### セットアップウィザード

pfSesne にログインします

!!! info
    pfSense の初期ユーザー, パスワードは下記の通りです

    | username | password |
    | :------- | :------- |
    | admin    | pfsense  |

#### pfSense Setup

フェルカムページです。

![](img/pfsense-settings/007.png)


#### Netgate® Global Support is available 24/7

有償サポートの案内です

![](img/pfsense-settings/008.png)

#### General Information

Hostname, Domain, Primary DNS Server, Secondary DNS Server, Override DNS を設定します。

|                      |                     |
| :------------------- | :------------------ |
| Hostname             | 指定の物を確認      |
| Domain               | 指定の物を確認      |
| Primary DNS Server   | 1.1.1.1             |
| Secondary DNS Server | 1.0.0.1             |
| Override DNS         | :fa-square: uncheck |

![](img/pfsense-settings/009.png)


#### NTP

NTPの設定と TImezone を設定します

|                      |                     |
| :------------------- | :------------------ |
| Time server hostname | ntp.jst.mfeed.ad.jp |
| Timezone             | Asia/Tokyo          |

![](img/pfsense-settings/010.png)


#### Configure WAN Interface

|                             |        |
| :-------------------------- | :----- |
| **Configure WAN Interface** |
| SelectedType                | Static |



![](img/pfsense-settings/011_01.png)

|                  |      |
| :--------------- | :--- |
| IP Address       | 適宜 |
| Subnet Mask      | 適宜 |
| Upstream Gateway | 適宜 |

![](img/pfsense-settings/011_02.png)

|                                |                         |
| :----------------------------- | :---------------------- |
| Block RFC1918 Private Networks | :fa-check-square: check |
| Block bogon networks           | :fa-check-square: check |

![](img/pfsense-settings/011_03.png)


#### Set Admin WebGUI Password

パスワード変更

![](img/pfsense-settings/012.png)


#### Wizard completed.

設定完了

![](img/pfsense-settings/013.png)


## バージョンアップ確認&アップデート

`System` / `Update` をクリックしていく

![](img/pfsense-settings/014.png)

Status が `Up to date.` でなく下記のように `confirm` と出ている場合はボタンを押してアップデートを実施

![](img/pfsense-settings/015.png)

ダウンロードが完了したら自動的に再起動する。

ログイン画面が出ればアップデート完了.

## interface 設定

### Assignments

`Interfaces` / `Assignments` の順にクリック

下記の表になるように `Add` する

| interface | Network port |
| :-------- | :----------- |
| WAN       | vtnet0       |
| LAN       | vtnet1       |
| OPT1      | vtnet2       |

![](img/pfsense-settings/016.png)


### WAN

先程、セットアップウィザード内で変更したので特に変更無し


### OPT1(MGMT)

インターフェイスが有効になっていることを確認

|                         |                         |
| :---------------------- | :---------------------- |
| Enable                  | :fa-check-square: check |
| Description             | `MGMT` に変更           |
| IPv4 Configuration Type | `Static IPv4`           |

![](img/pfsense-settings/017_01.png)

|              |                                            |
| :----------- | :----------------------------------------- |
| IPv4 Address | クラウド管理ネットワークのIPアドレス       |
| subnet       | クラウド管理ネットワークのサブネットマスク |

![](img/pfsense-settings/017_02.png)

### USER

一つ上の MGMT のインターフェイスを参考に ユーザー側のインターフェイス設定を実施してください。
