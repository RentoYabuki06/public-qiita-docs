---
title: Born2beroot_42tokyo
tags:
  - ''
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: true
---
# new article body

## 用語について

- **VM** (Virtual Machine) : 仮想マシン
- **LVM** (Logical Volume Manager) : 論理ボリュームマネージャ
- **パーティショニング** : ディスクを複数のパーティションに分割すること

## LVM（Logical Volume Management）についての教科書

### 目次
1. [LVMとは](#LVMとは)
2. [LVMの主要コンポーネント](#LVMの主要コンポーネント)
   - [物理ボリューム（PV）](#物理ボリュームPV)
   - [ボリュームグループ（VG）](#ボリュームグループVG)
   - [論理ボリューム（LV）](#論理ボリュームLV)
3. [LVMの利点](#LVMの利点)
4. [LVMの基本的な操作](#LVMの基本的な操作)
   - [物理ボリュームの作成](#物理ボリュームの作成)
   - [ボリュームグループの作成](#ボリュームグループの作成)
   - [論理ボリュームの作成](#論理ボリュームの作成)
   - [論理ボリュームのサイズ変更](#論理ボリュームのサイズ変更)
   - [論理ボリュームの削除](#論理ボリュームの削除)
5. [暗号化された論理ボリュームの作成](#暗号化された論理ボリュームの作成)
6. [LVMのスナップショット機能](#LVMのスナップショット機能)

### LVMとは
LVM（Logical Volume Management）は、Linux環境で柔軟なディスク管理を行うためのシステムです。物理ストレージデバイスを論理ボリュームにまとめて管理することで、ストレージの拡張や縮小、スナップショットの作成などが容易になります。

### LVMの主要コンポーネント
#### 物理ボリューム（PV）
物理ボリューム（Physical Volume, PV）は、ハードディスクやSSDなどの物理的なストレージデバイス、またはパーティションです。LVMの最小単位であり、ボリュームグループにまとめることで使用されます。

#### ボリュームグループ（VG）
ボリュームグループ（Volume Group, VG）は、複数の物理ボリュームをまとめたものです。これにより、複数のディスクを1つの大きなストレージプールとして扱うことができます。

#### 論理ボリューム（LV）
論理ボリューム（Logical Volume, LV）は、ボリュームグループ内に作成される論理的なストレージ領域です。LVは実際にデータが保存される場所で、ファイルシステムを作成したり、マウントしたりします。

### LVMの利点
- **柔軟なディスク管理**：論理ボリュームは簡単にサイズを変更でき、ディスクの追加や削除が容易です。
- **スナップショット機能**：現在の状態をスナップショットとして保存し、バックアップやリカバリに使用できます。
- **ストレージの統合**：複数のディスクを1つの論理ボリュームとして統合し、効率的に使用できます。

### LVMの基本的な操作
#### 物理ボリュームの作成
物理ボリュームを作成するには、`pvcreate`コマンドを使用します。
```bash
pvcreate /dev/sda1 /dev/sda2
```

#### ボリュームグループの作成
ボリュームグループを作成するには、`vgcreate`コマンドを使用します。
```bash
vgcreate myvg /dev/sda1 /dev/sda2
```

#### 論理ボリュームの作成
論理ボリュームを作成するには、`lvcreate`コマンドを使用します。
```bash
lvcreate -L 10G -n lvdata myvg
lvcreate -L 20G -n lvbackup myvg
```

#### 論理ボリュームのサイズ変更
論理ボリュームのサイズを変更するには、`lvresize`コマンドを使用します。拡張する場合は`-L +サイズ`オプション、縮小する場合は`-L -サイズ`オプションを使用します。
```bash
lvresize -L +5G /dev/myvg/lvdata  # 拡張
lvresize -L -5G /dev/myvg/lvdata  # 縮小
```

#### 論理ボリュームの削除
論理ボリュームを削除するには、`lvremove`コマンドを使用します。
```bash
lvremove /dev/myvg/lvdata
```

### 暗号化された論理ボリュームの作成
暗号化された論理ボリュームを作成するには、以下の手順を実行します。

1. 論理ボリュームの作成：
   ```bash
   lvcreate -L 10G -n lvdata myvg
   ```

2. 論理ボリュームの暗号化：
   ```bash
   cryptsetup luksFormat /dev/myvg/lvdata
   ```

3. 暗号化されたボリュームの開放とマウント：
   ```bash
   cryptsetup open /dev/myvg/lvdata lvdata_crypt
   mkfs.ext4 /dev/mapper/lvdata_crypt
   mount /dev/mapper/lvdata_crypt /mnt/data
   ```

### LVMのスナップショット機能
LVMはスナップショット機能を提供しており、現在の論理ボリュームの状態を保存することができます。スナップショットはバックアップやデータのリカバリに役立ちます。

#### スナップショットの作成
スナップショットを作成するには、`lvcreate`コマンドを使用します。
```bash
lvcreate -L 1G -s -n snap_lvdata /dev/myvg/lvdata
```

#### スナップショットのマウント
スナップショットをマウントするには、通常の論理ボリュームと同様にマウントします。
```bash
mount /dev/myvg/snap_lvdata /mnt/snapshot
```

以上が、LVM（Logical Volume Management）の基本的な概要と操作方法です。LVMを使用することで、Linux環境でのディスク管理がより柔軟かつ効率的になります。