# ansible-molecule
# Name
AnsibleとMoleculeを使用したDockerでのCI環境のサンプル

# Requiment
```
Ubuntu 20.04.4 LTS
python3.8(3.8.10)
python3.8-venv
docker(20.10.14)
docker-compose(1.29.2)
```

# Structure
```
ansible 6.2.0
molecule 4.0.1
molecule-docker 2.0.0
```

# Usage
```
1.必要なモジュールをインストールしgit clone
ubutun:~/$ sudo apt install python3.8-venv docker docker-compose
ubutun:~/$ sudo usermod -aG docker [userのgroup]
ubutun:~/$ git clone https://github.com/t-ito-ccs/ansible-molecule.git

2.venvの仮想環境を作成し,必要モジュールをインストール
ubutun:~/$ cd ./ansible-molecule
ubutun:~/ansible-molecule$ python3.8 -m venv ./.venv
ubutun:~/ansible-molecule$ source ./.venv/bin/activate
(.venv) ubutun:~/ansible-molecule$ pip install --upgrade pip
(.venv) ubutun:~/ansible-molecule$ pip install -r ./requirements.txt
※以降の手順は[source ./.venv/bin/activate]を行った仮想環境で実行する

3.Ansibleの起動
3-1.docker-composeを起動
※別ターミナルでdocker-composeを起動するとよい
(.venv) ubutun:~/ansible-molecule$ cd ./docker
(.venv) ubutun:~/ansible-molecule/docker$ docker-compose up -d

3-2.converge(playbook)の実行
(.venv) ubutun:~/ansible-molecule$ ansible-playbook -i ./inventory/hosts.yml ./converge.yml --ask-vault-pass
※Vault passwordにはtest-01を指定

3-3.docker-composeを終了(コンテナの破棄)
(.venv) ubutun:~/ansible-molecule/docker$ docker-compose down
※再度3-1.から実行できる

4.Moleculeの起動
4-1.rolesディレクトリに移動
(.venv) ubutun:~/ansible-molecule$ cd ./roles/[任意のrole]

4-2.moleculeの一連のtestを実行
(.venv) ubutun:~/ansible-molecule/run_all_roles$ molecule test
```
# Note
・ディレクトリ構成
```
/home/ubuntu/dev/ansible-molecule
|--converge.yml
|--docker
|  |--docker-compose.yml
|--files
|--inventory
|  |--group_vars
|  |  |--docker
|  |  |  |--vars.yml
|  |  |  |--vault.yml
|  |--hosts.yml
|  |--host_vars
|  |  |--test-01.yml
|--README.md
|--requirements.txt
|--roles
|  |--add_yum_repository(省略)
|  |--change_yum_items(省略)
|  |--create_users_groups(省略)
|  |--run_all_roles
|  |  |--defaults
|  |  |  |--main.yml
|  |  |--files
|  |  |--handlers
|  |  |  |--main.yml
|  |  |--meta
|  |  |  |--main.yml
|  |  |--molecule
|  |  |  |--default
|  |  |  |  |--converge.yml
|  |  |  |  |--molecule.yml
|  |  |  |  |--verify.yml
|  |  |--README.md
|  |  |--tasks
|  |  |  |--main.yml
|  |  |--templates
|  |  |--tests
|  |  |  |--inventory
|  |  |  |--test.yml
|  |  |--.travis.yml
|  |  |--vars
|  |  |  |--main.yml
|  |  |--.yamllint
```

・Moleculeのrole作成
```
1.rolesディレクトリに移動
(.venv) ubutun:~/ansible-molecule$ cd ./roles/[任意のrole]
(.venv) ubutun:~/ansible-molecule/[任意のrole]$ molecule init role [任意のid].[任意のrole] --driver-name docker

2.molecule.ymlの修正
(.venv) ubutun:~/ansible-molecule/[任意のrole]$ vi ./molecule/defaults/molecule.yml
[molecule.ymlのplatformに以下を追加(almalinux8.5を使用する場合)]
platforms:
  - name: instance
    image: almalinux:8.5
    privileged: true
    command: /sbin/init
    pre_build_image: true

3.tasksとvarsを作成
・[任意のrole]/tasks/main.ymlにtask(処理)を追加する.
・[任意のrole]/defaults/main.ymlにvalue(値)を追加する.
```

・Vaultの設定
```
ansible-vault encrypt vault.yml
ansible-vault decrypt vault.yml
```

・Moleculeのその他コマンド
```
・moleculeのターゲットインスタンス作成
molecule create
・moleculeのテスト部分の実行
molecule converge
・moleculeのターゲットインスタンスへのログイン
molecule login
・moleculeのターゲットインスタンス破棄
molecule destroy
```

・Ansibleのその他コマンド
```
・ansibleの環境変数確認
ansible-config dump > env.txt
・ansibleのインベントリ変数の確認
ansible-inventory -i hosts.yml --list --yaml
```

・Dockerコマンドでのインスタンス作成例
```
・docker-composeで起動する物と同等のコマンド
docker run -itd --privileged=true --name test-01 almalinux:8.5 /sbin/init
```

・run_all_rolesへの追加
```
1.converge.ymlへのinclude_roleの追加
/run_all_roles/molecule/default/converge.ymlに[任意のrole]を追加.
2./vars/main.ymlへの値の追加
/run_all_roles/vars/main.ymlに[任意の値]を追加.
```

# Author
# Licence
# Reference
・参考サイト
```
・Ansible：Role の基礎（Ansible 2.9.6）
https://nwengblog.com/ansible-role/

・molecule でansible の role と playbook をテストする
https://techblog.szksh.cloud/ansible-molecule/

・Moleculeに入門してみたよ(v3差分確認編)
https://qiita.com/answer_d/items/78b047eb5708dda1375d

・Ansible Roleを開発、テストするためのMoleculeを試す
https://kazuhira-r.hatenablog.com/entry/2020/01/18/220000

・インフラCI/CDを学ぶ-molecule編
https://lightwell-solution.com/blog/ansible/20210712_721/

・Ansible Vaultの使い方の調査
https://redj.hatenablog.com/entry/2020/05/02/044527#Ansible%E3%81%AE%E3%83%99%E3%82%B9%E3%83%88%E3%83%97%E3%83%A9%E3%82%AF%E3%83%86%E3%82%A3%E3%82%B9

・Ansible controller/target を Docker コンテナで構築する
https://qiita.com/zettaittenani/items/4c5844962ae85f851c29
```