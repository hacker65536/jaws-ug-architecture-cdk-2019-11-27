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

#### vscode の設定

extensions

local に install するもの

- Remote Development(ssh,ssh:editting configruration files)
- vscode-icons
- Vim

remote(実環境)に install するもの

- Prettier - Code formatter
- Git Blame
- Git Graph
- Git History
- GitLens
- TSLint

<details><summary>vscode監視ファイルの一部を除外する設定</summary>
https://code.visualstudio.com/docs/getstarted/settings

`Ctrl+Shift+P`で Command Palette を開き`Open settings(JSON)`を選択

`settings.json`に以下を追加

```
  "files.watcherExclude": {
    "**/.git/objects/**": true,
    "**/.git/subtree-cache/**": true,
    "**/node_modules/*/**": true
  },
```

</details>

### 実環境作成のための準備

- AWS アカウント
- IAM の credencials の用意
- EC2 のための Key Pair の作成
- クライントと EC2 の通信のための設定(securitygroup など)

## 実環境

- Amazon Linux 2

minimal の場合はいずれかのリージョンの default VPC public ip 割り当てた状態で free tier の instance type で起動

default の vpc の subnet はどれでも可(全てが igw への route を持つため)

### 実環境の設定

#### bash 環境を整える

bash の複数 terminal の実行ヒストリを残す設定

```
echo "export PROMPT_COMMAND='history -a'" >> ~/.bashrc
echo 'HISTSIZE=100000' >> ~/.bashrc
```

yum の update と system の reboot

```
sudo yum update -y
sudo reboot
```

#### aws cli 環境を整える

python3 の pip を install

```
sudo yum install python3-pip
```

pip から awscli を install

```
pip3 install --user awscli
```

環境変数に設定

```
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

awscli のバージョン確認

```console
$ aws --version
aws-cli/1.16.277 Python/3.7.4 Linux/4.14.146-120.181.amzn2.x86_64 botocore/1.13.13
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

profile で assumerole する場合は適宜に profile と sessionname を変更

変数化

```
myprofile=myprofile
myses=mysessionname
```

設定

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

#### cdk 環境を整える

```
npm i -g aws-cdk
```

cdk のバージョンを確認

```console
$ cdk --version
1.15.0 (build bdbe3aa)
```

#### vscode のための設定

https://code.visualstudio.com/docs/setup/linux#_visual-studio-code-is-unable-to-watch-for-file-changes-in-this-large-workspace-error-enospc

OS のデフォルトの設定では数値が低いため vscode が多くのファイルの状態を監視しきれないので必要であれば数値上げる、もしくは node_modules を除外などをする

sysctl に設定

```
echo 'fs.inotify.max_user_watches=524288' | sudo tee -a  /etc/sysctl.conf
```

sysctl に適応

```
sudo sysctl -p
```
