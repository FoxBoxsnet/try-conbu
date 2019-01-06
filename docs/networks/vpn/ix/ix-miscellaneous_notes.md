# 雑記

## ソフトウェアウェアアップデート

### 概要

ファームウエアのバージョンはできるだけ最新にしたほうがよいので、古い物のアップデートをする。
UNIVERGE IX シリーズはファームウエアをインターネット上では公開しておらず、販売元などに依頼することでダウンロードできる認証情報が発行されるらしい。

ここでは、 Ubuntu Server 18.04 に TFTP サーバーを準備してファームウエアアップデートをする手順を紹介する。

### TFTP サーバーの準備

#### Ubuntu

先述の通り、 Ubuntu 18.04 に TFTP サーバーを準備してファームウエアアップデートを実施するため、
Ubuntu Server を準備しOSを最新版にアップデートしておく

!!! info
    以降の作業は `sudo` ができる環境で実施していきいます。

```shell
sudo apt update
sudo apt upgrade -y

```

`tftpd-hpa` をインストールし起動, お好みで自動起動設定をします。

!!! memo
    一度だけなら自動起動は不要ですが、 config のダウンロードなどにも利用できるためあっても損はないと思います。<br>
    また、そのさいはセキュリティーにはお気おつけください。(ユーザー側から利用できるなどが起こらぬよう)

```shell
sudo apt install -y tftpd-hpa
sudo systemctl start tftpd-hpa
sudo systemctl status tftpd-hpa
sudo systemctl restart tftpd-hpa

```

```shell
# 自動起動設定
sudo systemctl enable tftpd-hpa
sudo systemctl is-enabled tftpd-hpa

```

##### ファイルの置き場所

`/var/lib/tftpboot` に配置します。

!!! memo
    ファイルの所有者は `root` グループは `nogroup` が望ましいです。<br>
    また、権限は `0644` が良いでしょう

```shell
sudo chown -R root:nogroup /var/lib/tftpboot
sudo chmod 0644 /var/lib/tftpboot/<対象ファイル>

```

ファームウエアをダウンロードし、 SCP などで転送する。

#### MacOS

##### TFTPサーバの起動
Macであれば、以下のコマンドでTFTPサーバが起動します。

```shell
sudo launchctl unload -w /System/Library/LaunchDaemons/tftp.plist

```


### アップデートイメージを配置する

* Ubuntu: `/var/lib/tftpboot/`
* MacOS: `/private/tftpboot/`



### 現在のバージョン確認

コンソールを刺して、電源を入れます。

```shell
Router# show version
NEC Portable Internetwork Core Operating System Software
IX Series IX2105 (magellan-sec) Software, Version 8.9.17B, MAINTENANCE RELEASE SOFTWARE
Compiled May 17-Fri-2013 16:27:00 JST #2 by sw-build, coregen-8.9(17)

ROM: System Bootstrap, Version 5.1
System Diagnostic, Version 5.1
Initialization Program, Version 5.1

System uptime is 0 minute
System woke up by reload, caused by power-on
System started at Jan 02-Wed-2019 15:43:36 JST
System image file is "ix2105-ms-8.9.17.b.ldc"

Processor board ID <0>
IX2105 (MPC8314E) processor with 131072K bytes of memory.
2 GigaEthernet/IEEE 802.3 interfaces
512K bytes of non-volatile configuration memory.
16384K bytes of processor board System flash (Read/Write)

```


### IP設定

サーバーとの疎通ができるように、IP 設定をする

SW HUB 側に LAN ケーブルを刺し、IP 設定をしていく

|             | vale                                  |
| :---------- | :------------------------------------ |
| IP Address  | 192.168.101.140/24                    |
| TFTP Server | 192.168.101.66/24                     |
| filename    | ix2105-boot-15.3-gate-ms-9.6.12.a.rap |

今回のファームウエアファイルは `ix2105-boot-15.3-gate-ms-9.6.12.a.rap` です。

Interface は SW HUB の部分を使って設定します。

|           | IX2215 | IX2106 | IX2105 |
| :-------- | :----- | :----- | :----- |
| Interface | GE2    | GE1    | GE1    |

!!! info 
    今回は IX2105 を利用してサンプルを記述します。<br>
    また、コマンドは省略形で記述します。


**`software-update` の欄は各自で変更すること**

```shell
en
int GigaEthernet1.0
ip add 192.168.101.140/24
no shut
exit

software-update tftp://192.168.101.66/ix2105-boot-15.3-gate-ms-9.6.12.a.rap
show flash
reload

```

```shell
show version

```

!!! example "参考コンソール出力"

    ```shell
    Router#
    Router# en
    Enter configuration commands, one per line. End with CNTL/Z.
    Router(config)#
    Router(config)# int GigaEthernet1.0
    Router(config-GigaEthernet1.0)# ip add 192.168.101.140/24
    Router(config-GigaEthernet1.0)# no shu
    Router(config-GigaEthernet1.0)# exit

    Router(config)# software-update tftp://192.168.101.66/ix2105-boot-15.3-gate-ms-
                    9.6.12.a.rap
    % Downloading ...........................................................
    TFTP transfer complete, 6456479 bytes, MD5 = 4a0e9c53c37d6bc47eabd9b208742b00
    % Check ...... done
    % Update file name is ix2105-ms-9.6.12.a.ldc
    % Writing ......................................................... done
    % Software update completed.


    Router(config)# show flash
    Codes: M - Main-side, B - Backup-side, N - Newfile, R - Runnable
          A - Active-file, + - Next-boot, * - Bootmode-entry
    Length     Name                          Status
    4283502    ix2105-ms-8.9.17.b.ldc        MA
    5537649    ix2105-ms-9.6.12.a.ldc        N+

    [9954784 bytes used, 4191378 available, 14146162 total]
    13824 Kbytes of processor board System flash (Read/Write)


    Router(config)# reload
    % Warning: current running-configuration is not saved yet.
    Notice: The router will be RELOADED. This is to ensure that
            the peripheral devices are properly initialized.
    Are you sure you want to reload the router? (Yes or [No]): yes

    ```

    再起動後

    ```shell

    Router# show version
    NEC Portable Internetwork Core Operating System Software
    IX Series IX2105 (magellan-sec) Software, Version 9.6.12A, MAINTENANCE RELEASE SOFTWARE
    Compiled Oct 05-Thu-2017 19:27:09 JST #2 by sw-build, coregen-9.6(12)

    ROM: System Bootstrap, Version 15.3
    System Diagnostic, Version 15.3
    Initialization Program, Version 5.1

    System uptime is 1 minute
    System woke up by reload, caused by command execution
    System started at Jan 02-Wed-2019 17:38:49 JST
    System image file is "ix2105-ms-9.6.12.a.ldc"

    Processor board ID <0>
    IX2105 (MPC8314E) processor with 131072K bytes of memory.
    2 GigaEthernet/IEEE 802.3 interfaces
    512K bytes of non-volatile configuration memory.
    16384K bytes of processor board System flash (Read/Write)

    ```

## スーパーリセット

コンソールケーブルを接続し、電源を入れ、 `Loading: ##############` 中に `Ctrl + C` することで、  boot モードに入れます。

ここで下記を実行します

``` shell
cc
dc
b

```

!!! example "参考コンソール出力"

    ```shell
    NEC Diagnostic Software
    Copyright (c) NEC Corporation 2001-2013. All rights reserved.

    %DIAG-INFO: Starting System POST(Power On Self Test)

                  DRAM TEST 1: Pass
                  DRAM TEST 2: Pass
                    NVRAM TEST: Pass
                      CPU TEST: Pass
                      PLD TEST: Pass
                      GE0 TEST: Pass
          GE1(SW-HUB)1-4 TEST: Pass
            1.0 VOLTAGE STATUS: 0.973V Pass
            1.8 VOLTAGE STATUS: 1.763V Pass
            2.5 VOLTAGE STATUS: 2.457V Pass
            3.3 VOLTAGE STATUS: 3.251V Pass
            5.0 VOLTAGE STATUS: 5.018V Pass
            TEMPERATURE STATUS: +35.0degC Pass

    NEC Bootstrap Software
    Copyright (c) NEC Corporation 2001-2013. All rights reserved.

    %BOOT-INFO: Trying flash load, exec-image [ix2105-ms-8.9.17.b.ldc].
    Loading: ##########
    NEC Bootstrap Software, Version 5.1
    Copyright (c) NEC Corporation 2001-2013. All rights reserved.
    boot[0]> cc
    Enter "Y" to clear startup configuration: y
    % Startup configuration is cleared.

    NEC Bootstrap Software, Version 5.1
    Copyright (c) NEC Corporation 2001-2013. All rights reserved.
    boot[0]> dc
    Enter "Y" to clear default configuration: y
    % Default configuration is cleared.

    NEC Bootstrap Software, Version 5.1
    Copyright (c) NEC Corporation 2001-2013. All rights reserved.
    boot[0]> b

    NEC Bootstrap Software
    Copyright (c) NEC Corporation 2001-2013. All rights reserved.

    %BOOT-INFO: Trying flash load, exec-image [ix2105-ms-8.9.17.b.ldc].
    Loading: ################################################################# [OK]


    Starting at 0x20000

    Configuring router subsystems (before IDB proc): done.
    Constructing IDB(Interface Database): done.
    Configuring router subsystems (after IDB proc): done.
    Initializing router subsystems: done.
    Starting router subsystems: done.

    All router subsystems coming up.




    NEC Portable Internetwork Core Operating System Software
    Copyright Notices:
    Copyright (c) NEC Corporation 2001-2013. All rights reserved.
    Copyright (c) 1985-1998 OpenROUTE Networks, Inc.
    Copyright (c) 1984-1987, 1989 J. Noel Chiappa.
    Router#

    ```


コマンド内容は下記参照

| command | description                     |
| :------ | :------------------------------ |
| b       | Boot using stored configuration |
| bm      | Boot using console queries      |
| uc      | Display hardware configuration  |
| cc      | Clear startup configuration     |
| dc      | Clear default configuration     |
| sc      | Set calendar                    |
| rc      | Read calendar                   |
| rm      | Read MAC address                |
| rs      | Read serial number              |
| sa      | SA activate/deactivate          |
| ver     | Show version                    |
