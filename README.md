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
1.Moleculeの起動
1-1.rolesディレクトリに移動
(.venv) ubutun:~/ansible-molecule$ cd ./roles/[任意のrole]
(.venv) ubutun:~/ansible-molecule/[任意のrole]$ molecule init role [任意のid].[任意のrole] --driver-name docker

1-2.molecule.ymlの修正
(.venv) ubutun:~/ansible-molecule/[任意のrole]$ vi ./molecule/defaults/molecule.yml
[molecule.ymlのplatformに以下を追加(almalinux8.5を使用する場合)]
platforms:
  - name: instance
    image: almalinux:8.5
    privileged: true
    command: /sbin/init
    pre_build_image: true

1-3.tasksとvarsを作成
・[任意のrole]/tasks/main.ymlにtask(処理)を追加する.
・[任意のrole]/defaults/main.ymlにvalue(値)を追加する.
```

・vaultの設定
```
ansible-vault encrypt vault.yml
ansible-vault decrypt vault.yml
```

# Author
# Licence
# Reference