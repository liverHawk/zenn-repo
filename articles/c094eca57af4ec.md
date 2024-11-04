---
title: "Ansibleとシェルスクリプトの違い"
emoji: "🦓"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["ansible", "bash"]
published: true
---

## ソフトウェアのインストール

通常であれば、`apt`などを使ってソフトウェアをインストールすることが多いと思います。
しかし、Multipass を使って仮想環境を作っては壊しを続けているとインストールが面倒になってきます。
その時に使われるのが`cloud-init.yaml`なのですが、全員が Multipass を使うとは限らないので、今回は違う方式を紹介します。

### インストールの自動化

まとまったコマンドを実行するために使われるのは、代表的なのでいうと Make や Shell Script ですね。
しかしながら、これらには欠点があります。それは、既にインストールされているかを判断してコードを実行してくれないことです。
コードをそのように変えればできるのかもしれませんが、何も書かなければ何もしてくれません。
そのため、インストール時にエラーが起きる可能性があります。

そこで紹介するのが、Ansible というものです。
簡単に言えば、シェルスクリプトを YAML に変換してそのファイルを元にコマンドなどを実行してくれるものです。
わかりにくいかもしれませんので、具体例を挙げます。

## Ansible の具体例

ここに書くコードはあくまでも一例なので、詳しくは公式ドキュメントをご覧ください。

### 環境詳細

Multipass で以下のようにして仮想環境を作成。

```bash
multipass launch -c 4 -d 30G -m 4G -n "ansible" 24.04
```

### コード例

ここからは仮想環境に入って作業を行います。

```bash
sudo apt update
sudo apt upgrade -y

sudo apt install ansible ansible-core -y
```

ここでは、Apache2 をインストールする例を挙げます。

```yaml:playbook.yml
- hosts: localhost
  become: true
  tasks:
    - name: Install system package
      apt:
        pkg:
          - apache2
        state: latest
        update-cache: true
```

作成が終わったら、以下のコードを実行してください。

```bash
ansible-playbook playbook.yml
```

変更があれば`changed`、無ければ`ok`と表示されます。

`ip a`を実行し、IP アドレスを取得。
その後、そのアドレスへアクセスすると初期画面が出てくると思います。

## おわりに

今回はソフトウェアのインストールのみを取り上げましたが、実は Docker のコンテナも作れる優れものなのでぜひ試してみてください。

## 参考

https://www.reddit.com/r/linuxadmin/comments/emcuqm/ansible_versus_bash_script/?rdt=62180
