# cdk 開発環境(参考)

- クライアント PC (windows10, macOS, その他)
- 実環境 (EC2)

## クライアント環境

### ssh するための Terminal の準備

- windows: putty, WSL,WSL2(Windows Terminal)
- macOS: terminal, [iTerm2](https://iterm2.com)

WSL, Windows Terminal は Microsoft Store から install できる

### IDE の準備

- [vscode](https://code.visualstudio.com/)

IDE はほぼ [vscode](https://code.visualstudio.com/) 一択なので download と install

### 実環境作成のための準備

- AWS アカウント
- IAM の credencials の用意
- EC2 のための Key Pair の作成
- クライントと EC2 の通信のための設定(securitygroup など)

## 実環境

- Amazon Linux 2

### 設定

#### bash 環境を整える

bash の複数 terminal の実行ヒストリを残す設定

```
echo "export PROMPT_COMMAND='history -a'" >> ~/.bashrc
echo 'HISTSIZE=100000' >> ~/.bashrc
```

yum の update と reboot

```
sudo yum update -y
sudo reboot
```

#### aws cli 環境を整える

```
sudo yum install python3-pip
```

```
pip3 install --user awscli
```

```
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

<details><summary>credencialsの設定</summary>

ヒストリを残さないように一時的に設定

```
HISTCONTROL=ignoreboth
HISTIGNORE=*
```

変数化

```
awskey=AKIAIOSFODNN7EXAMPLE
awssec=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

設定

```
aws configure set aws_access_key_id $awskey
aws configure set aws_secret_access_key $awssec
aws configure set cli_follow_urlparam false
```

profile で assumerole する場合
適宜に profile と sessionname を変更

```
myprofile=myprofile
myses=mysessionname
```

```
aws configure --profile $myprof set role_arn <roleARN>
aws configure --profile $myprof set source_profile  <SourceProfile>
aws configure --profile $myprof set role_session_name  $myses
```

必要に応じてデフォルトに設定

```
echo "export AWS_DEFAULT_PROFILE=$myprof" >> ~/.bashrc
source ~/.bashrc
```

ヒストリ隠蔽解除

```
unset HISTIGNORE
```

credencials 設定確認

```
aws sts get-caller-identity
```

</p>
</details>

#### node 環境を整える

nvm をインストール

```
curl -SsL -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.1/install.sh | bash
source ~/.bashrc
```

node の lts と最新版を install

```
nvm install --lts
nvm install node
```

default を最新版にする

```
nvm alias default node
```
