# pfSense

クラウド上に、 VPN サーバーを構築します。

pfSense とありますが、 pfSense を利用している理由は、pfSense の利用実績が多いので pfSense を利用しています。

## 初期設定

pfSense は、[Download pfSense Community Edition](https://www.pfsense.org/download/) でダウンロードできます。

64bit 版をダウンロードして、解凍して iso イメージの状態にまで頑張ってください。


### セットアップウィザード


#### pfSense Setup

フェルカムページです。

#### Netgate® Global Support is available 24/7

有償サポートの宣伝です

#### General Information

Hostname, Domain, Primary DNS Server, Secondary DNS Server, Override DNS を設定します。

|                      |                                                                             |
| :------------------- | :-------------------------------------------------------------------------- |
| Hostname             |
| Domain               |
| Primary DNS Server   |
| Secondary DNS Server |
| Override DNS         | uncheck<br>DNSサーバーがWAN上のDHCP / PPPによって上書きされることを許可する |
