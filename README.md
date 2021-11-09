# RasPi4+Ubuntuの開発環境
ここではRasPi4にUbuntu20.04LTSをインストールし、Python3による開発環境を構築します。

[R2CVに戻る](https://github.com/nishibra/r2cv-1)

### 目次

[1. R2CVのCPUとOS](#1)

[2. RasPi4 SDカードの作り方](#2)

[3. DXHATの設定](#3)

[4. ネットワークなど開発環境の準備](#4)

[5. RasPi ケースの作成](#5)

<a id="1"></a>
## 1. R2CVのCPUとOS
RasPi4+Ubuntu20.04LTS+Python3をインストールします。

![009](/pics-base/cpucases.jpg)

### RaspberyPi4の準備
#### RasPi4
 メモリーは出来れば8GByteのものを準備しましょう。

![010](/pics-base/image010.jpg)
![011](/pics-base/image011.png)

#### SDカード
高速のもので32Gbyte以上のものを使用します。同じ32Gbyteでも容量が異なるので、コピーするときには注意してください。

![012](/pics-base/image012.png)

RasPi4+Ubuntu20.04LTS+Python3+ROS2-Foxy+OpenCV4をインストールしたSDカードのイメージファイルは以下よりダウンロードできます。

>download: [SDCイメージファイル](http://www.arrc.jp/auto/r2cv_20211109.img)

>ユーザー:ubuntu
>
>パスワード:airrcrobo

#### RasPi電源ハット 5V電源/RS485/TTL
>DXHAT(BTE100): [（株）ベストテクノロジー](https://www.besttechnology.co.jp/modules/knowledge/?BTE100%20DXHAT)

DXHATはRaspberry Pi 4用の電源HATです。Raspberry Piへの電源供給は外付けのプッシュスイッチによってON/OFFする事ができ、更にサーボモータなどをコントロールするRS-485とTTL I/Fを装備しています。

![013](/pics-base/image013.jpg)

#### ディスプレイ/HDMIケーブル
ディスプレイはセットアップの時だけ使用するので何でも良いです。ただしHDMIケーブルはシールドがしっかりしているものを使いましょう。WiFi接続トラブルの原因となります。

![014](/pics-base/image014.png)

#### キーボード・マウス
何でも良いです。セットアップのときに使用します。

#### USBステレオカメラ    ------ amazonで購入

![015](/pics-base/image015.png)

#### バッテリー
ここでは7.4Vで使用していますが、使用するサーボモータの電圧仕様に合ったものを使用してください。

![016](/pics-base/image016.png)

#### WiFiルーター
何でも良いです。

<a id="2"></a>
## 2. RasPi4 SDカードの作り方
### RaspberyPi4のEEPROMのアップデート
以下の手順に従いRasPi4のEEPROMの内容を最新版にアップデートします。ディスプレイ、マウス、キーボード、LANケーブルを接続しておいてください。

### Raspberry Pi OSのinstall
   以下のサイトよりRaspberry Pi imagerをインストールします。

Raspberry Pi imagerを起動し、ERACEを選択し、SDカードをフォーマットします。続いて、Raspberry Pi OSをSDカードに書き込みます。

>参考サイト: https://www.raspberrypi.org/downloads/
            
### EEPROMのアップデート
>参考サイト: https://www.raspberrypi.org/documentation/hardware/raspberrypi/booteeprom.md
 
以下のコマンドを実行してEEPROMをアップデートします。
```
$ sudo apt-get update && sudo apt-get dist-upgrade -y
$ sudo rpi-update
$ reboot
$ sudo rpi-eeprom-update -a
$ reboot
```
Ubuntu20.04LTSのインストール
Raspberry Pi imagerからUbuntu Server 20.04.2LTS 64-bit版をSDカードに書き込みます。

![017](/pics-base/image017.jpg)

SDカードをRaspi4にセットし起動します。 アカウント名「ubuntu⁠」⁠，パスワード「ubuntu」でログインします。 まずは以下を実行しアップデートしておきます。
```
$ sudo apt update
$ sudo apt full-upgrade -y
```
次にサーバー版をデスクトップ版に変更します。 ここではDesktopifyを使用します。サーバー版のUbuntu 20.04 LTSがインストールされたRaspberry Pi上で実行することでデスクトップ環境をインストールしてくれます。
```
$ git clone https://github.com/wimpysworld/desktopify.git
$ cd desktopify
$ sudo ./desktopify --de ubuntu
$ sudo reboot
```
以上でディスクトップ環境が完成です。 「設定」にて言語やWiFi、ディスプレイなどの使用環境を設定してください。 その他、以下のサイトをご参照ください。

> 参考サイト: https://gihyo.jp/admin/serial/01/ubuntu-recipe/0624?page=1


<a id="3"></a>
## 3.DXHATの設定
config.txtにDXHATの設定を記述することで以下の機能が有効となります。
config.txtについては以下をご参照下さい。
> 参考サイト: https://www.raspberrypi.com/documentation/computers/config_txt.html

### config.txtの設定
config.txtを修正します。config.txt以外からも設定されている場合がありますのでチェックしておきましょう。
```
$ sudo gedit /boot/firmware/config.txt
$ sudo gedit /boot/firmware/syscfg.txt
$ sudo gedit /boot/firmware/usercfg.txt
```

### 電源
```
[all]
dtoverlay=gpio-shutdown,gpio_pin=17
dtoverlay=gpio-poweroff,gpiopin=4,active_low=1
```

ubuntuをGUIで起動している場合は以下を実行すると自動停止します。
```
$ gsettings set org.gnome.SessionManager logout-prompt false
```

### 冷却ファン
```
[all]
dtoverlay=gpio-fan,gpiopin=19,temp=50000
```

### RTC
```
[all]
dtparam=i2c_arm=on
dtoverlay=i2c-rtc,ds3231
```

以下のhwclockコマンドでRTCが機能しているかの確認ができます。
```
#print hardware clock
$ hwclock -r
#set the hardware clock from the current system time
$ hwclock -w
#set the system time from the hardware clock
$ hwclock -s
```

### Dynamixel I/F
```
[pi4]
init_uart_clock=64000000
#UART1を活性化
dtoverlay=uart1,txd1_pin=14,rxd1_pin=15
#UART2を活性化
dtoverlay=uart2,txd2_pin=0,rxd2_pin=1
#UART4を活性化
dtoverlay=uart4,txd4_pin=8,rxd4_pin=9
```

<a id="4"></a>
## 4. ネットワークなど開発環境の準備
### ネットワーク接続
マウス、キーボード、ディスプレイを接続して、WiFiによるネットワークに接続を行います。

![018](/pics-base/image018.jpg)

右上のボタンからWiFiを選んで接続してください。
以下を実行しnet-toolをインストールすれば
```
$ sudu apt install net-tool
```
下記コマンドでIPアドレスを確認できます。
```
$ ifconfig
```
でIPが表示されます。
このIPアドレスにリモートデスクトップから接続します。

### Windowsリモートデスクトップ接続
WindowsからRasPi4にリモートデスクトップで接続します。
まずXrdp サーバーをRasPi4にインストールして起動します。
```
$ sudo apt -y install xrdp tigervnc-standalone-server
$ sudo systemctl enable xrdp
```
さらに「設定」にてディスプレイの共有を許可しておいてください。
WindowsのリモートデスクトップからIPアドレスを指定して接続します。

>参考サイト:
https://www.server-world.info/query?os=Ubuntu_20.04&p=desktop&f=6

<a id="5"></a>
### 5.RasPi4 + DXHAT ケースの作成
RasPi4 + DXHAT ケースを3D printerで製作するにはraspi_case_stlフォルダーのstlファイルを使ってください。
フラットな面を下にして出力すると良いでしょう。

![only](/pics-base/caseonlys.jpg)

> [RasPi4ケース](/raspi_case_stl/raspi_case.stl)

![case](/raspi_case_stl/case.png)

> [ケース裏ブタ](/raspi_case_stl/raspi_lower.stl)
>
> [プッシュボタンケース](/raspi_case_stl/pushb_case.stl)
>
> [コネクターカバー](/raspi_case_stl/connector_cover.stl)

