# vtc_ue_bringup

[furo-org/VTC](https://github.com/furo-org/VTC)を使うためのROSパッケージです。

## 初期設定

Unreal Engineが動くPCとROSが動くPCが必要です。

### 1. VTCシミュレータのセットアップ

[furo-org/VTC](https://github.com/furo-org/VTC)をUnreal Engineが動くPCにセットアップします。Windows 10のバージョン1909とバージョン2004で動作確認をしました。  
[パッケージ済みバイナリが公開されています](https://github.com/furo-org/VTC#%E3%83%91%E3%83%83%E3%82%B1%E3%83%BC%E3%82%B8%E6%B8%88%E3%81%BF%E3%83%90%E3%82%A4%E3%83%8A%E3%83%AA%E3%81%AE%E3%83%80%E3%82%A6%E3%83%B3%E3%83%AD%E3%83%BC%E3%83%89)のでそれを使用すると楽です。

ファイアウォールの設定をしてシミュレータの外部との通信を許可しておきます。

### 2. CageClientのセットアップ

[furo-org/CageClient](https://github.com/furo-org/CageClient)をROSが動くPCにセットアップします。ROS MelodicをUbuntu 18.04のPCにインストールして動作確認をしました。

#### 2.1. ROSのセットアップ

Ubuntu 18.04.4をインストールしたPCにROS Melodicをインストールします。  
ROSのセットアップは[Tiryoh/ros_setup_scripts](https://github.com/Tiryoh/ros_setup_scripts_ubuntu)を使うと楽です。
[Tiryoh/ros_setup_scripts](https://github.com/Tiryoh/ros_setup_scripts_ubuntu)を使ってインストールする場合は以下のコマンドを実行します。

```sh
bash -c "$(curl -SsfL u.ty0.jp/ros-melodic-desktop)"
```

`Success installing ROS melodic`と表示されればインストール完了です。

その後はROSのワークスペースを設定します。ここではインターネット上でよく見る`~/catkin_ws/`をワークスペースとします。

```
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws
catkin init
```

#### 2.2. cage_ros_bridge

[furo-org/CageClient](https://github.com/furo-org/CageClient)をROSのワークスペース以下にダウンロードしてからビルドします。

Gitでバージョン管理されているリポジトリなので`git`コマンドでダウンロードします。  
このリポジトリは[`git submodule`](https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E3%81%95%E3%81%BE%E3%81%96%E3%81%BE%E3%81%AA%E3%83%84%E3%83%BC%E3%83%AB-%E3%82%B5%E3%83%96%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%BC%E3%83%AB)で外部のライブラリを参照しているので`git clone`する際に`--recursive`オプションが必須です。

```
cd ~/catkin_ws/src
git clone --recursive https://github.com/furo-org/CageClient.git
```

もし、`git clone`する際に`--recursive`オプションをつけなかった場合は以下のように`submodule`を更新します。

```
cd CageClient
git submodule update --init --recursive
```

ダウンロード後、ビルドします。

```
cd ~/catkin_ws
catkin build
```

### 3. vtc_ue_bringupのセットアップ

本ROSパッケージをROSのワークスペース以下にダウンロードし、依存パッケージをインストールしてからビルドします。  
cage_ros_bridgeと同様にダウンロードします。

```
cd ~/catkin_ws/src
git clone https://github.com/Tiryoh/vtc_ue_bringup.git
```

依存パッケージをインストールします。

```
cd ~/catkin_ws/src
rosdep install -r -y -i --from-paths vtc_ue_bringup
```

ビルドします。

```
cd ~/catkin_ws
catkin build
```


[3. vtc_ue_bringup](#3-vtc_ue_bringupのセットアップ)までできればセットアップは完了です。


## 使い方

`bringup.launch`で以下の3つのノードと1つのlaunchファイルを呼び出すことができます。

* cage_ros_bridge
* [`rviz`](http://wiki.ros.org/rviz)
* [`rostopic`](http://wiki.ros.org/rostopic)
* [`velodyne_pointcloud`](http://wiki.ros.org/velodyne_pointcloud)の[`VLP16_points.launch`](https://github.com/ros-drivers/velodyne/blob/melodic-devel/velodyne_pointcloud/launch/VLP16_points.launch)

`ip:=`のオプションではシミュレータを起動しているPCのIPアドレス（今回は`192.168.1.110`とします）を指定します。

```
roslaunch vtc_ue_bringup bringup.launch ip:=192.168.1.110
```

furo-org/VTCのレーザスキャナは、台車にコマンドを送ってきたホストにスキャンデータを送信するようになっています。
そのため`bringup.launch`の起動時にシミュレータ宛に台車停止コマンドを`rostopic`で送信しています。
これによりROS上でレーザスキャナのスキャンデータを扱えるようになります。

## ライセンス

(C) 2020 Daisuke Sato \<tiryoh@gmail.com\>

MITライセンスに基づき公開しています。詳細は[LICENSE](./LICENSE)を参照してください。