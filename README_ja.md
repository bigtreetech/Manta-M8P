# [英語版はこちら](./README.md)
# [中文版请点击这里](./README_zh_cn.md)

# Manta M8PでKlipperを使用する方法

## 注意:

* このマザーボードには、MCU SDカードを介してファームウェアを更新できるブートローダーが付属しています。

## ファームウェアのビルド

1. 事前にコンパイルされたファームウェア（使用されるソースコードのバージョンは[2022年7月1日のコミット](https://github.com/Klipper3d/klipper/commit/1636a9759bc2d5f162312ac8bf5823e95e0ad053)です）
   * [firmware-USB.bin](./firmware-USB.bin) USBを使用してSoCと通信します。M8PのMCUのUSBはボード上でCB1/CM4と接続されています。ファームウェアを更新した後、追加の配線なしで使用できます。

2. 自分でファームウェアをビルドする<br/>
   1. [klipperの公式インストールガイド](https://www.klipper3d.org/Installation.html)に従って、klipperのソースコードをRaspberry Piにダウンロードします。
   2. 以下の設定を使用してファームウェアをビルドします。（以下の設定を選択できない場合は、klipperのソースコードを更新してください）
      * [*] 追加の低レベル設定オプションを有効にする
      * マイクロコ��トローラーアーキテクチャ = `STMicroelectronics STM32`
      * プロセッサモデル = `STM32G0B1`
      * ブートローダーオフセット = `8KiBブートローダー`
      * クロックリファレンス = `8 MHzクリスタル`
      * 通信インターフェース = `USB (on PA11/PA12)`
      * USB to CANブリッジを使用する場合（M8P V1.1+のみ）、以下の2行を使用します：
      * 通信インターフェース = `USB to CAN bus bridge (USB (on PA11/PA12))`
      * CANバスインターフェース = `(CAN bus (on PD12/PD13))`

      <img src=Images/menuconfig.png width="800" /><br/>
   3. 設定が完了したら、`q`を押して終了し、保存するかどうかを尋ねられたら「Yes」を選択します。
   4. `make`コマンドを実行します。
   5. `make`コマンドが完了すると、`home/pi/kliiper/out`フォルダに`klipper.bin`ファイルが生成されます。同じLAN内のWindowsコンピュータを使用して、CMDターミナルで`pscp`コマンドを使用してRaspberry Piからコンピュータに`klipper.bin`をコピーできます。例：`pscp -C pi@192.168.0.101:/home/pi/klipper/out/klipper.bin c:\klipper.bin`（ターミナルは`The server's host key is not cached`と表示し、`Store key in cache?((y/n)`と尋ねる場合があります。`y`を入力して保存し、次にRaspberry Piのデフォルトパスワード`raspberry`を入力します）

## ファームウェアのインストール
1. [ファームウェアのビルド 2.5](#ファームウェアのビルド)の方法を使用するか、`cyberduck`や`winscp`などのツールを使用して、Raspberry Piからコンピュータに`klipper.bin`ファイルをコピーします。
2. `firmware-USB.bin`または`klipper.bin`（`home/pi/kliiper/out`フォルダにビルドされたもの）を`firmware.bin`にリネームします。<br/>
**重要:** ファイル名を変更しないと、ブートローダーが正しく更新されません。
3. `firmware.bin`をMCU SDカードのルートディレクトリにコピーします（SDカードがFAT32形式であることを確認してください）。
4. マザーボードの電源を切ります。
5. MCU SDカードを挿入します。
6. マザーボードの電源を入れます。
7. 数秒後、マザーボードがフラッシュされます。
8. `ls /dev/serial/by-id`コマンドを実行して、フラッシュが成功したかどうかを確認できます。フラッシュが成功すると、次のようにklipperデバイスが表示されます：

   <img src=Images/stm32g0b1_id.png width="600" /><br/>

## プリンタのパラメータを設定する
### 基本設定
1. [klipperの公式インストールガイド](https://www.klipper3d.org/Installation.html)に従って、`Configuring OctoPrint to use Klipper`を行います。
2. [klipperの公式インストールガイド](https://www.klipper3d.org/Installation.html)に従って、`Configuring Klipper`を行います。そして、`printer.cfg`の基礎として、提供された設定ファイル[generic-bigtreetech-manta-m8p.cfg](./generic-bigtreetech-manta-m8p.cfg)を使用します。このファイルには、Octopusのすべての正しいピン配置が含まれています。
3. [klipperの公式Config_Reference](https://www.klipper3d.org/Config_Reference.html)に従って、必要な機能を設定します。
4. USBを使用してRaspberry Piと通信する場合、Raspberry Piで`ls /dev/serial/by-id/*`コマンドを実行して、マザーボードの正しいID番号を取得し、`printer.cfg`に正しいID番号を設定します。
    ```
    [mcu]
    serial: /dev/serial/by-id/usb-Klipper_stm32g0b1xx_190028000D50415833323520-if00
    ```
