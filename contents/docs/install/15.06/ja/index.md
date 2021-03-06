CentOS 7 (x86_64)でのyumレポジトリを用いたインストール方法
===========================================================

必要なパッケージのインストール
------------------------------
### Project Hatohol公式yumレポジトリの登録
以下のコマンドを実行し、Project Hatohol公式が提供するyumレポジトリの登録をしてください。

    # wget -P /etc/yum.repos.d/ http://project-hatohol.github.io/repo/hatohol-el7.repo

### EPELレポジトリの登録
以下のコマンドを実行し、EPELレポジトリの登録をしてください。

    # yum install epel-release

### Hatohol Serverのインストール
次のコマンドでインストールしてください。

    # yum install hatohol-server

### Hatohol Web Frontendのインストール
次のコマンドでインストールしてください。

    # yum install hatohol-web

### 依存パッケージ
上記のコマンドでインストールされるパッケージは以下のとおりです。

- CentOSのレポジトリから提供されるパッケージ
    - glib2
    - libsoup
    - sqlite
    - mariadb
    - libuuid
    - qpid-cpp-client
    - MySQL-python
    - httpd
    - mod_wsgi
    - python-argparse
    - python-pip
- EPELのレポジトリから提供されるパッケージ
    - librabbitmq
    - python-daemon
- Project Hatoholのレポジトリから提供されるパッケージ
    - json-glib
    - Django

### 必要パッケージ
以下のパッケージがHatoholの正常な動作に必要です。

- CentOSのレポジトリから提供されるパッケージ
    - mariadb-server
    - qpid-cpp-server

次のコマンドでインストールしてください。

    # yum install mariadb-server qpid-cpp-server

セットアップ
------------
### MariaDB serverのセットアップ
すでにMariaDB serverを使用している場合、このステップは省略できます。

    # systemctl enable mariadb
    # systemctl start mariadb

### Hatohol DBの初期化
以下のコマンドを実行しHatohol DBを初期化してください。

    $ hatohol-db-initiator --db_user <MariaDBのrootユーザー名> --db_password <MariaDBのrootパスワード>

TIPS:

- もしMariaDBのrootパスワードが指定されていない場合、""を利用してください。

- 生成されるDBのユーザ名とパスワードを--hatohol-db-userと--hatohol-db-passwordオプションを利用して変更することができます。
    - その時は/etc/hatohol/hatohol.confファイルを修正してください。
- 15.03では、hatohol-db-initiatorはhatoholデータベースを作成後、コマンドライン引数を必要としなくなりました。db_name、 db_userとdb_passwordはhatohol.confから読み込まれます。hatohol.confは${prefix}/etc/hatohol/hatohol.confに配置されています。

### Hatohol Webのセットアップ
- Hatohol Web用DBを用意する

以下のMySQLクエリを実行してデータベースとユーザを作成してください。

    MariaDB> CREATE DATABASE hatohol_client;
    MariaDB> GRANT ALL PRIVILEGES ON hatohol_client.* TO hatohol@localhost IDENTIFIED BY 'hatohol';

- テーブルをDBに追加する

以下のコマンドを実行してテーブルをDBの中に追加してください。

    # /usr/libexec/hatohol/client/manage.py syncdb

### Hatohol serverの開始

    # systemctl enable hatohol
    # systemctl start hatohol

### Hatohol Webの開始

    # systemctl enable httpd
    # systemctl start httpd

Webブラウザを使ったアクセス
--------------------------
### SELinuxとfirewallの設定確認
デフォルトでは、SELinuxやfirewallのようないくつかのセキュリティ機構によって他のコンピュータからのアクセスが妨げられます。
必要に応じて、それらを解除しなければなりません。

【警告】
下記の設定を行うにあたり、セキュリティリスクについてよく理解してください。

現在のSELinuxの状態は次のコマンドで確認できます。

    # getenforce
    Enforcing

もし'Enforcing'であれば、次のコマンドでSELinuxポリシールールの強制を解除できます。

    # setenforce 0
    # getenforce
    Permissive

【ヒント】
/etc/selinux/configを編集することで、恒久的な無効化を行えます。

firewallについては、以下のコマンドを実行することで許可ポートを追加できます。
以下の例は80番ポートを許可する例です。

    # firewall-cmd --zone=public --add-port=80/tcp --permanent
    # firewall-cmd --zone=public --add-port=80/tcp

### Hatohol情報の閲覧
例えば、Hatohol clientが192.168.1.1で動作している場合、
ブラウザを用いて次のURLを開いてください。

- http://192.168.1.1/

デフォルトのユーザ名： admin パスワード： hatohol でログインすることで、
各種画面の閲覧が可能になります。

【メモ】
現在、上記ページは、Google ChromeおよびFirefoxを使ってチェックされています。
Internet Explorerを使用する場合は、ご使用のバージョンによっては、
表示レイアウトが崩れる場合があります。（IE11では正常に表示されることを確認しています）

Hatohol Arm Plugin Interfaceを使用する場合
-----------------------------------------
あなたがHatohol Arm Plugin Interface(HAPI)を使用する場合、'/etc/qpidd.conf'を以下のように修正してください。

    -auth=yes
    +auth=no

【メモ】
先頭の'+'印は、新たに追加する行を意味します。
先頭の'-'印は、削除する行を意味します。
