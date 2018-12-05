# space_include_group_name

グループ名にスペースがあるファイルを作成/管理するケースの検証

Linuxであればグループ名にスペースの利用できないので、こんなケースは存在しないはずです。

ですが、ディレクトリサービスを利用したアカウント管理化であれば存在し、利用できてしまいます。

以下のツールでは問題が発生しません。

  - Ansible
  - Puppet
  - Chef

以下のツールでは問題が発生しています。

  - Itamae
  - MItamae

## Ansible 2.7.4

``` shell
$ ansible-playbook ./file.yaml --check
$ ansible-playbook ./file.yaml
```

作成○、羃等性○、完璧

## Puppet 6.0.4

``` shell
$ puppet apply --noop file.pp
$ puppet apply file.pp
```

作成○、羃等性○、完璧

## Chef 14.7.17

``` shell
$ chef-apply file.rb --why-run
$ chef-apply file.rb
```

作成○、羃等性○、完璧

## Itamae v1.10.1

``` shell
$ itamae local -n file.rb
$ itamae local file.rb
```

### create

Error

``` shell
ERROR :     stderr | chown: INTRADomain: illegal group name
ERROR :     Command `chown hiroki.ohtsuka:INTRA\Domain Users /tmp/itamae_tmp/1544019625.601797` failed. (exit status: 1)
ERROR :   file[/tmp/itamae] Failed.
```


### exist

すでにファイルが存在する状態

``` shell
$ bundle exec itamae local -n file.rb
 INFO : Starting Itamae... (dry-run)
 INFO : Recipe: /Users/hiroki.ohtsuka/src/github.com/hirocaster/space_include_group_name/itamae/file.rb
 INFO :   file[/tmp/itamae] modified will change from 'false' to 'true'
 INFO :   diff:
 INFO :   --- /tmp/itamae       2018-12-05 23:24:36.000000000 +0900
 INFO :   +++ /tmp/itamae_tmp/1544019882.047345 2018-12-05 23:24:42.000000000 +0900
 INFO :   @@ -1 +1 @@
 INFO :   -itamae
 INFO :   +itamae
 INFO :   \ No newline at end of file
```
実際にapplyすると

``` shell
$ bundle exec itamae local file.rb
 INFO : Starting Itamae...
 INFO : Recipe: /Users/hiroki.ohtsuka/src/github.com/hirocaster/space_include_group_name/itamae/file.rb
 INFO :   file[/tmp/itamae] modified will change from 'false' to 'true'
 INFO :   diff:
 INFO :   --- /tmp/itamae       2018-12-05 23:24:36.000000000 +0900
 INFO :   +++ /tmp/itamae_tmp/1544019956.443158 2018-12-05 23:25:56.000000000 +0900
 INFO :   @@ -1 +1 @@
 INFO :   -itamae
 INFO :   +itamae
 INFO :   \ No newline at end of file
ERROR :     stderr | chown: INTRADomain: illegal group name
ERROR :     Command `chown hiroki.ohtsuka:INTRA\Domain Users /tmp/itamae_tmp/1544019956.443158` failed. (exit status: 1)
ERROR :   file[/tmp/itamae] Failed.
```


## MItamae v1.6.2

``` shell
$ mitamae local -n file.rb
$ mitamae local file.rb
```

基本的に実装はItamaeと同じなので...

``` shell
$ mitamae local file.rb
 INFO : Starting mitamae...
 INFO : Recipe: /Users/hiroki.ohtsuka/src/github.com/hirocaster/space_include_group_name/mitamae/file.rb
 INFO :   file[/tmp/mitamae] exist will change from 'false' to 'true'
 INFO :   file[/tmp/mitamae] owner will be 'hiroki.ohtsuka'
 INFO :   file[/tmp/mitamae] group will be 'INTRA\Domain Users'
 INFO :   diff:
 INFO :   --- /dev/null 2018-12-05 23:39:20.000000000 +0900
 INFO :   +++ /tmp/1544020760.7654      2018-12-05 23:39:20.000000000 +0900
 INFO :   @@ -0,0 +1 @@
 INFO :   +mitamae
 INFO :   \ No newline at end of file
ERROR :     stderr | chown: INTRADomain: illegal group name
ERROR :     Command `chown  hiroki.ohtsuka:INTRA\Domain Users /tmp/1544020760.7654` failed. (exit status: 1)
ERROR :   file[/tmp/mitamae] Failed.
```
