## Terraform based provisioning examples

```bash
# デフォルトのVariables
export AWS_DEFAULT_REGION="ap-northeast-1"
export TF_VAR_region="ap-northeast-1"
export TF_VAR_cluster_name="teleport"
# 最新版を指定する
export TF_VAR_teleport_version="3.0.0-rc.3"
# AWS上にある公開鍵の名前を指定する
export TF_VAR_key_name="aws-teleport-ap-northeast-1"
# ローカルにあるライセンスファイルを指定する
export TF_VAR_license_path="/var/lib/teleport/license.pem"
# デモ用のUbuntuAMI（初期設定済み）
export TF_VAR_ami_name="debian-stretch-hvm-x86_64-gp2-2018-08-14-82175-572488bb-fc09-4638-8628-e1e1d26436f4-ami-00bbb68c7e6ca73ce.4"
# 利用するドメイン名（自アカウントのRoute53にて管理しているもの）
export TF_VAR_route53_zone="cloudnative.co.jp"
# Teleportにて利用するサブドメイン
export TF_VAR_route53_domain="teleport.cloudnative.co.jp"
# ログを保存するバケット名
export TF_VAR_s3_bucket_name="teleport.cloudnative.co.jp"
# メールアドレス指定
export TF_VAR_email="teleport@cloudnative.co.jp"
# AWS Profile
export AWS_PROFILE="saml"
# Grafanaに利用するパスワード指定
export TF_VAR_grafana_pass="XXXXXXXXXXXX"
```

# var.tf
VPCアドレスなど必要であれば修正する

# saml2aws
https://github.com/Versent/saml2aws
```
saml2aws configure
saml2aws login
```

# plan
make plan
```

## Upgrade examples

All examples are run from `ansible` directory and are to illustrate
upgrade order of the provisioned infrastructure.

**Install python deps**

```
pip install boto3==1.0.0 ansible==2.3.0.0
```

**Configure AWS**

Make sure to configure [your aws creds](https://boto3.readthedocs.io/en/latest/guide/quickstart.html#configuration).

**Generate SSH config**

```
# generate SSH config for ansible to go through bastion
# this will write bastion
python ec2.py --ssh --ssh-key=~/.ssh/aws-teleport-ap-northeast-1.pem
# make sure ansible works by pinging the nodes
ansible -vvv -i ec2.py -u admin auth -m ping --private-key=~/.ssh/aws-teleport-ap-northeast-1.pem
```


**Launch an upgrade**

```
ansible-playbook -vvv -i ec2.py --private-key=~/.ssh/aws-teleport-ap-northeast-1.pem --extra-vars "teleport_version=3.0.0-rc.3" upgrade.yaml
```
