---
title: Raspberry Pi 4 (ラズパイ4) でユーザーIDとパスワードを変更する
tags:
  - RaspberryPi
private: false
updated_at: '2021-08-25T13:21:42+09:00'
id: 5c40a5bf5c0efaecf9b5
organization_url_name: null
slide: false
ignorePublish: false
---
# 概要
ラズパイは初期設定の状態では
ユーザーIDがPi
パスワードがraspberry
となっており，セキュリティ的によろしくない状態です．なので最初にこれを変更した方が良さそうです．
なおSSHでもGUIでもどちらでもできます．

# 手順
1. 仮のtmpユーザーを作成
2. 自動ログインを無効にする
3. 再起動しtmpユーザーでログインする
4. piユーザーIDの変更，ホームディレクトリの変更
5. 再起動し新しいユーザーIDでログイン後，tmpを削除する
6. パスワードを変更する

## 仮のtmpユーザーを作成
まずpiのユーザーIDを変更するためだけの目的で仮のtmpという名前のユーザーを作成します．
このtmpは後で削除します．

下記コマンドでtmpユーザーを作成し，sudo権限を追加します．
ユーザー作成時にパスワードを要求されます．tmpユーザーは一時的なものなのでなんでも良いですが後でパスワードが必要なので忘れないようにしてください．

```bash:terminal
sudo adduser tmp
sudo gpasswd -a tmp sudo
```

## 自動ログインを無効にする
#### SSHの場合

```bash
sudo raspi-config
```

コマンドを実行します．
「1 System Options」「S5 Boot / Auto Login」「B1 Console」を選択します．
<img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/570280/51f93190-11cc-2120-9a6a-6079234d396f.png" width=70%>
<img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/570280/c4eb8039-bfda-f147-1fa1-8b52b8f2ef4b.png" width=70%>
<img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/570280/492c9f03-e5ec-a89d-77ad-3d90a5ff014c.png" width=70%>

最初の画面に戻るので右下の「Finish」を選択します．
<img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/570280/490430cf-b8e7-ca1f-8780-90e898486e86.png" width=70%>

Would you like to reboot now?と聞かれるので「はい」で再起動します．
<img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/570280/0bc27017-5b01-be3e-fc31-364aaf3fe49b.png" width=50%>

#### GUIの場合
「設定」から「Raspberry Piの設定」を開きます．
<img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/570280/f8f91133-c7a9-cebb-e6d0-4426e2b04419.png" width=60%>

「システム」内の「自動ログイン」を無効にし「OK」.
<img src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/570280/a2140282-3473-fef2-5f1a-6e7fb38f48bb.jpeg" width=70%>
再起動します．


## 再起動しtmpユーザーでログインする
自動ログインを無効にしたので再起動時にログインを要求されます．
この時にpiではなくtmpユーザーでログインします．

## piユーザーIDの変更，ホームディレクトリの変更
piユーザーを変更し，現在piと名前がついているホームディレクトリ名も新しいユーザーIDに変更します．
ここでは新しいユーザーIDをnew_userとします．


```bash:terminal
sudo usermod -l new_user pi
sudo usermod -d /home/new_user -m new_user
sudo groupmod -n new_user pi
```

## 再起動し新しいユーザーIDでログイン後，tmpを削除する
tmpユーザーを削除します．
-rオプションをつけることでホームディレクトリごと削除することができます．

```bash:terminal
sudo userdel -r tmp
```

## パスワードを変更する
ここまででユーザーIDは変更できましたが，パスワードがまだ初期値の「raspberry」のままです．
以下コマンドでパスワードを変更しておきましょう．

```bash:terminal
sudo passwd new_user
```

##### 参考
https://www.souichi.club/raspberrypi/default-user-pi/
https://rs-techdev.com/archives/32
https://qiita.com/NakamoriSuganuma/items/e1bfeb6e46cf09ab1e89
