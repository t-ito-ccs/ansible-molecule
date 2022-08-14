# ansible-molecule
# Name
AnsibleとMoleculeを使用したDockerでのCI環境のサンプル

# Requiment
Ubuntu 20.04.4 LTS
python3.8(3.8.10)
python3.8-venv
docker(20.10.14)
docker-compose(1.29.2)

# Structure
ansible 6.2.0
molecule 4.0.1
molecule-docker 2.0.0

# Usage
1.必要なモジュールをインストールしgit clone
ubutun:~/$ sudo apt install python3.8-venv docker docker-compose
ubutun:~/$ git clone https://github.com/t-ito-ccs/ansible-molecule.git

2.venvの仮想環境を作成し,必要モジュールをインストール
ubutun:~/$ cd ./ansible-molecule
ubutun:~/ansible-molecule$ python3.8 -m venv ./.venv
ubutun:~/ansible-molecule$ source ./.venv/bin/activate
(.venv) ubutun:~/ansible-molecule$ pip install -r ./requirements.txt
※以降の手順は[source ./.venv/bin/activate]を行った仮想環境で実行する

3.Ansibleの起動
3-1.docker-composeを起動し
※別ターミナルでdocker-composeを起動するとよい
(.venv) ubutun:~/ansible-molecule$ cd ./docker
(.venv) ubutun:~/ansible-molecule/docker$ docker-compose up -d

3-2.converge(playbook)の実行
(.venv) ubutun:~/ansible-molecule$ ansible-playbook -i ./inventory/hosts.yml ./converge.yml --ask-vault-pass
※Vault passwordにはtest-01を指定



# Note
# Author
# Licence
# Reference