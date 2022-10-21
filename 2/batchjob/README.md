# バッチジョブ
#### 国立遺伝学研究所　森宙史

### バッチジョブとは?

- コマンド入力と応答というようなインタラクティブな逐次処理ではなく、一連の処理を最初に入力した後、順次処理を実行するような処理の形式  
- 上記は、UNIXコマンドを一つ一つ入力して処理を実行する形式と、シェルスクリプトを記述して一気に処理を実行する形式、として考えることができる    
- つまり、バッチジョブの一例が、シェルスクリプトを記述して一気に処理を実行することである  　 
　  
### 遺伝研スパコンにおけるバッチジョブの実行方法
シェルスクリプトで処理を記述した上で、Altair Grid Engine(AGE)を用いてqsubコマンドでシェルスクリプトを実行させる。

詳しくは、  
- [Grid Engineの概要](https://sc.ddbj.nig.ac.jp/software/grid_engine/)  
- [バッチジョブの使い方](https://sc.ddbj.nig.ac.jp/software/grid_engine/batch_jobs/)
- [その他のコマンド](https://sc.ddbj.nig.ac.jp/software/grid_engine/other_commands/)
の3つを読めば大体わかる。  
　  
### 遺伝研スパコンにおけるバッチジョブの実行

1. FileZillaで遺伝研スパコンのホームディレクトリ上に、下記のbatch1.shファイルを作成

batch1.sh
```
#!/bin/bash
#$ -S /bin/bash
#$ -N knowhost
#$ -cwd
sleep 7
hostname > hos.txt

```

2. TeraTerm or ターミナル等を用いてsshで遺伝研スパコンにログインし、qlogin後、下記のコマンドを実行  
`chmod u+x batch1.sh`  
`./batch1.sh`  
`cat hos.txt`  
`qsub batch1.sh`  
`qstat`  
`cat hos.txt`  

3. コードの解説  
`#$` はqsubコマンドに渡すオプションを記述  
`-S /bin/bash` はqsubでスクリプトの解釈にbashシェルを用いることを指定  
`-N knowhost` はジョブの名前をジョブスクリプト名ではなくknowhostに変更  
`-cwd` はジョブをカレントディレクトリで実行することを指定  
`>` は左側のコマンドの結果を右側のファイルに書き込む


### ジョブの実行状態および遺伝研スパコンの混雑状況の確認方法
`qstat` 

がジョブの実行状態を確認するコマンド

r	ジョブ実行中

qw	ジョブは実行待機中

t	ジョブは実行ホストへ転送処理中

順番待ちの状況だとqwになる。


どのくらい遺伝研スパコンが混んでいるかは、

`qstat -g c` 

で見ることができる。講習会当日の朝(2022年10月20日朝6時30分)の時点では以下。
```
CLUSTER QUEUE                   CQLOAD   USED    RES  AVAIL  TOTAL aoACDS  cdsuE  
--------------------------------------------------------------------------------
epyc.q                            0.41   1374     32   1633   3200    256      0 
gpu.q                             0.39     20      0     31     56      8      0 
intel.q                           0.60    759    178    295   1056     64      0 
login.q                           0.03     84      0    300    384      0      0 
login_gpu.q                       0.04      0      0     24     24      0      0 
medium.q                          2.65    358     91    266    800    400      0 
short.q                           0.39     15      0     81    112     16      0 
```

USED :現在使用中のスロット数

RES :事前予約(Advance Reservation)のスロット数

AVAIL :使用可能なスロット数

TOTAL :スロット総数

大まかには、AVAIL/TOTALの値が小さいほど混雑している。


### ジョブの削除方法
既に実行中または順番待ち中のジョブを終了を待たずに削除したい場合は qdel コマンドを使用する。 

`qdel ジョブID`

でそのジョブを削除可能。ジョブIDをqstatで確認した上で削除する。
