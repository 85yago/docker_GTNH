# GTNHをdockerで立てる

## サーバーのセットアップ

https://www.gtnewhorizons.com/
https://docker-minecraft-server.readthedocs.io/en/latest/mods-and-plugins/

https://wiki.gtnewhorizons.com/wiki/Server_Setup_(Linux)
https://github.com/GTNewHorizons/lwjgl3ify#server

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

## クライアント

MultiMCでやった。

https://wiki.gtnewhorizons.com/wiki/Installing_and_Migrating
https://adoptium.net/temurin/releases/?os=windows&arch=x64&version=21

Java 21のJREを落としてそれに対応したMultiMC向けのzipを落とす。
読めばわかるが追加のJRE引数を指定する必要あり。

https://wiki.gtnewhorizons.com/wiki/Main_Page

一部翻訳されている、ここからダウンロードすること。
https://github.com/GTNewHorizons/GTNH-Translations/blob/master/readmes/README_ja_JP.md

画面上の左上の情報が小さくなってしまう。
Mod Optionsから`Ingame`で検索し、`Config`→`General`→`scale(new)`を10くらいにすればよい。
