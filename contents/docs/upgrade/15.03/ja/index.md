CentOS 6.5 (x86_64) での14.12から15.03へのアップグレードについて
========================================================================

***15.03は以前のバージョンからのアップグレードには対応していません。***

もし過去バージョンのデータを保管しておきたい場合は以下の方法でバックアップを取得し，データベースの名前を変更してインポートしてください。またはインポートせずバックアップのまま保管してください。


## バックアップ取得方法

    $ mysqldump -u [MySQL のルートユーザー名] -p [MySQLのルートパスワード] [旧データベース名] > hatohol.sql

## バックアップから別名でデータベースをインポートする方法

    $ mysql -u [MySQL のルートユーザー名] -p [MySQLのルートパスワード] [New database name] < hatohol.sql
