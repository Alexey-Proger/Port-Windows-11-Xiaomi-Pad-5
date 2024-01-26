<img align="right" src="https://raw.githubusercontent.com/erdilS/Port-Windows-11-Xiaomi-Pad-5/main/nabu.png" width="425" alt="Windows 11 Running On A Xiaomi Pad 5">


# Xiaomi Pad 5 で Windows を動かす
> [!WARNING]
> YouTube などのビデオガイドは古いので、使わないでください。

## インストール手順

## Windows のインストール

### 必要なもの
- 脳みそ
  
- [Windows on ARM イメージ](https://uupdump.net/)
  
- [UEFI イメージ](https://raw.githubusercontent.com/erdilS/Port-Windows-11-Xiaomi-Pad-5/main/images/xiaomi-nabu_20240115.img)
  
- [ドライバー](https://github.com/map220v/MiPad5-Drivers/releases/latest)

### リカバリーを起動して、Windowsのインストールを開始する


```cmd
fastboot boot <recovery.img>
```

#### msc スクリプトを実行する

> もう一度実行するように指示されたら、実行してください。

```cmd
adb shell msc
```
### ディスクにレターを割り合てる
  

#### Windowsのディスク管理(diskpart)を起動する

> Xiaomi Pad 5 がディスクとして検出されたら、

```cmd
diskpart
```


#### `X` を Windows ボリューム として割り合てる

#### タブレットのWindows用ボリュームを選択する
> `list volume` をすると、"WINNABU"があると思います。

```diskpart
select volume <number>
```

#### ディスクレター `X` を割り合てる
```diskpart
assign letter=x
```

### ESP ボリューム を `Y` として割り合てる

#### タブレットの ESP ボリューム を選択する
> Use `list volume` to find it, it's the one named "ESPNABU"

```diskpart
select volume <number>
```

#### ディスクレター `Y` を割り合てる

```diskpart
assign letter=y
```

#### diskpartを終了する
```diskpart
exit
```

  
  

### インストール

> `<path/to/install.wim>` を実際のinstall.wimのパスに書き換えてください。

> `install.wim` is located in sources folder inside your ISO
> `install.wim` はWindowsのISO内にある、sourcesフォルダにあります。
> ISOファイルをマウントするか、展開することで入手できます。

> `install.esd` という名前の場合もあります。それぞれパスを変更してください。

```cmd
dism /apply-image /ImageFile:<path/to/install.wim> /index:1 /ApplyDir:X:\
```

### ドライバのインストール

> [ここ](https://github.com/map220v/MiPad5-Drivers/releases/latest) でドライバをダウンロードできます。

```cmd
 ドライバが入ったフォルダを開き、OfflineUpdater.cmd を実行します。
```

> "Enter Drive letter..." と表示されたら、Xを入力します。 

### EFI に Windows の bootloaderファイルを作成する。

```cmd
bcdboot X:\Windows /s Y: /f UEFI
```

## phantom ドライブレターの出現を避けるために、ESPNABUのドライブレターを削除します。
> これが機能しない場合は、無視して次のコマンドに進みます。これらのphantom ドライブは、次回PCを再起動すると消えます。
> 
```cmd
mountvol y: /d
```


## Windows を起動する

### Android の boot イメージをバックアップする

```cmd
adb shell "dd if=/dev/block/platform/soc/1d84000.ufshc/by-name/boot$(getprop ro.boot.slot_suffix) of=/tmp/boot.img"
```

### PCにバックアップをpullする

```cmd
adb pull /tmp/boot.img
```



### bootloader を起動する

```cmd
adb reboot bootloader
```

### UEFI イメージをダウンロードして、flashする
> [UEFI image](https://raw.githubusercontent.com/erdilS/Port-Windows-11-Xiaomi-Pad-5/main/images/xiaomi-nabu_20240115.img) をダウンロードする。

```cmd
fastboot flash boot <path to image>
```
## Windows を起動する
```cmd
fastboot reboot
```

> [!NOTE]
> Windows の初回起動の際に、Wi-Fiネットワークが表示されません。
> 電源ボタンを押して再起動した後に、ネットワークに接続しようとすると、「アイスクリーム」が表示されます。
>「再試行」を7回タップすると、Wi-Fiネットワークが表示されます。
> または、ネットワークに接続する前に、`Shift+F10`を押下し、`.\oobe\bypassNRO.cmd`を実行し、ネットワーク接続を回避することもできます。(オンスクリーン キーボードも使用できます。)

### Android を起動する
> タブレットをfastbootモードで起動し、バックアップした Android の boot イメージをflashします。

```cmd
fastboot flash boot boot.img
```

## 完了!
> [Telegram chat](https://t.me/nabuwoa) に参加して、このプロジェクトに関するニュースを受けとることができます。

### [最後の手順: デュアルブートのセットアップ](dualboot-ja.md)
