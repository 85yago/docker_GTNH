# GTNHをdockerで立てる

## サーバーのセットアップ

- https://www.gtnewhorizons.com/
- https://docker-minecraft-server.readthedocs.io/en/latest/mods-and-plugins/
- https://wiki.gtnewhorizons.com/wiki/Server_Setup_(Linux)
- https://github.com/GTNewHorizons/lwjgl3ify#server

1. `GT_New_Horizons_2.6.0_Server_Java_17-21.zip`を落とす
2. `unzip GT_New_Horizons_2.6.0_Server_Java_17-21.zip -d data/`
3. こんな感じにしたら起動
```yml
VERSION: "1.7.10"
JVM_XX_OPTS: "@java9args.txt"
TYPE: CUSTOM
CUSTOM_SERVER: lwjgl3ify-forgePatches.jar
```

2回目以降の起動にはOCIのarmインスタンスで5分ほどかかった。

- serverutilities
    - `data/serverutilities/serverutilities.cfg`からバックアップを有効にすること。
    - `chunk_loading`と`chunk_claiming`をtrueにすればチャンクローダーが使えるので嬉しい。
    - task.cleanup.enabledをオンにすると敵対mobとかを定期的に消せる、ネザーポータルからあふれてくる対策とかにいいかも
- Thaumcraft
    - `config/Thaumcraft.cfg`の`glowing_taint`をfalseにする。


起動したら参加を試みて`docker container attach <container id>`から`whitelist add <playername>`する。
コンソールから`Ctrl+p Ctrl+q`で抜ける。

## サーバーのアプデ・バックアップ

https://wiki.gtnewhorizons.com/wiki/Server_Setup#Server_Update

1. 全てのコピーを取る
1. https://downloads.gtnewhorizons.com/ServerPacks/ から`curl -# -O url`で落とす、`curl -# -O -C - url`で再開できる
1. `unzip -d new_data/ GT_New_Horizons_[ver]_Server_Java_17-21.zip`
1. `rm -rf ./data/config/ ./data/libraries/ ./data/mods/`
1. `cp -r new_data/config/ data/config/`、`cp -r new_data/libraries/ data/libraries/`、`cp -r new_data/mods/ data/mods/`
1. configを直す
1. `cp -r backup_data/config/JourneyMapServer/ data/config/JourneyMapServer/`
1. `rm lwjgl3ify-forgePatches.jar java9args.txt startserver-java9.bat startserver-java9.sh`
1. `cp lwjgl3ify-forgePatches.jar java9args.txt startserver-java9.bat startserver-java9.sh ../data`

### JourneyMapについて

`config/JourneyMapServer`にcfgファイルが生成され、この中にWorld IDが書かれている。
Mapの情報はクライアントに保存されてて、このidが一致すればそのデータを表示する。

https://yukieiji.hatenablog.com/entry/ar1647251

とりあえず、バックアップを取り、ユーザーがログインしてきたときに生成されたcfgファイルのidを書き換えればよい。

## クライアント

MultiMCでやった。

- https://wiki.gtnewhorizons.com/wiki/Installing_and_Migrating
- https://github.com/GTNewHorizons/lwjgl3ify?tab=readme-ov-file#multimc-setup-gtnh-and-non-gtnh
- https://adoptium.net/temurin/releases/?os=windows&arch=x64&version=21

1. Java 21のJREを落としてそれに対応したMultiMC向けのzipを落とす
1. MultiMCで取り込んでinstanceを生成
1. inscanceを右クリでjavaをauto detectから21を指定、RAM(6GB～、体感10～12GBあればあまりプチフリしない)や引数も上のリンクから指定する
1. 起動する

一部翻訳されている、ここからダウンロード・導入すること。

https://github.com/GTNewHorizons/GTNH-Translations/blob/master/readmes/README_ja_JP.md

試しに翻訳しているものがある。もしあれば
`.minecraft/config/txloader/load/betterquesting/lang/ja_JP.lang`を置き換えること。

画面上の左上の情報が小さくなってしまう。
Mod Optionsから`Ingame`で検索し、`Config`→`General`→`scale(new)`を10くらいにすればよい。

日本語キーボードではクエストブックのショートカットが設定されていないので適当なキーに設定しておくこと。

## クライアントのアプデ

https://wiki.gtnewhorizons.com/wiki/Installing_and_Migrating#The_direct_process

これをやった後、日本語翻訳を入れなおすこと。

コピるべきもの一覧
https://wiki.gtnewhorizons.com/wiki/Installing_and_Migrating#Files_to_Copy_when_Migrating_or_Backing_Up

インスタンス
1. `instance.cfg`

1. `options.txt`, `optionsnf.txt`
1. `.minecraft\journeymap\data\mp`
あとは鉱石とTCのノード、スクショなど。

## thaumのwarpを消す

流石につらいので。

[ここ](https://gtnh.miraheze.org/wiki/Commands_and_Configurations#Thaumcraft)を参照した。

1. `docker container attach <container id>`
2. `list`で一覧表示
3. `/tc warp <playername> set <value>`
4. コンソールから`Ctrl+p Ctrl+q`で抜ける

/tc warp [player_name] set [value] TEMP - change temporary warp
/tc warp [player_name] set [value] - change normal warp
/tc warp [player_name] set [value] PERM - change permanent warp

## バックアップからの復帰

***作業前に必ずdataフォルダをバックアップすること！！！***

dataごとコピーして、バックアップからWorldのみ移植する。
権限直す必要あり。`sudo chown ubuntu:ubuntu -R data/`
World/session.lockを削除して起動。

### java.lang.RuntimeException: Failed to check session lock, aborting

わっから～ん
dataごとコピってないと、退避したWorldすら読めなくなるので注意。

多分権限がダメそう。
`sudo chown ubuntu:ubuntu -R data/`で解決した。
