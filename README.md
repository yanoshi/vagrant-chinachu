# vagrant-chinachu
Windows上でChinachuを動かすために作ったVagrantfileです。

## Requirements
- Vagrant `>=2.0.0`
- VirtualBox `>=5.1.28 r117968`
- Mirakurun `>=2.1.0`

## How to use

```
vagrant up
```

上記コマンドを実行すると以下URLでChinachuが起動します。

`http://akari:hirakegoma@localhost:10772`

Chinachuについては [公式Wiki](https://github.com/Chinachu/Chinachu/wiki) を参照しましょう。


## Update

```
vagrant box update && yes | vagrant destroy && vagrant up
```

## Delete

```
yes | vagrant destroy
```

## Directory Structure

- `recorded/...`
  - 録画済みファイル
- `Vagrantfile`
  - Vagrant向け設定ファイル( [書き方](https://www.vagrantup.com/docs/vagrantfile/) )
- `conf/config.json`
  - Chinachu向け設定ファイル( [書き方](https://github.com/Chinachu/Chinachu/wiki/Gamma-Configuration) )
- `conf/rules.json`
  - 録画ルールファイル
- `data/...`
  - データフォルダ
- `log/...`
  - Chinachuのログディレクトリ


## Configurations
### Mirakurunに関する設定
本設定はホストマシン上でMirakurunが動作している事を前提としています。
他のホストにてMirakurunが動作している場合は `Vagrantfile` の以下の部分を適宜変更して下さい。

#### 変更前
```
MIRAKURUN_IP=`netstat -rn | grep "^0.0.0.0 " | cut -d " " -f10`
```

#### 変更後
```
MIRAKURUN_IP="xxx.xxx.xxx.xxx"
```


### 仮想マシンの設定
仮想マシンに割り当てるCPUコア数やメモリー量は適宜変更して下さい。
`Vagrantfile` の以下の部分を編集することで変更できます。

```ruby
  config.vm.provider :virtualbox do |vb|
    vb.customize [
      "modifyvm", :id,
      "--hwvirtex", "on",
      "--nestedpaging", "on",
      "--largepages", "on",
      "--memory", "4096",           # 割り当てるRAM(お好きな値で)
      "--cpus", "4",                # 割り当てるコア数(お好きな値で)
      "--ioapic", "on",
      "--pae", "on",
      "--paravirtprovider", "kvm"
    ]
  end
```

## License
MIT
