## Tpcc-MySQL

### 下载安装tpcc-mysql
```
cd /usr/local
wget https://github.com/Percona-Lab/tpcc-mysql/archive/master.zip
mv tpcc-mysql-master tpcc-mysql
```
### 安装之前需要保证将mysql_config添加到$PATH 环境变量中
```
cd src ; make ( you should have mysql_config available in $PATH)
```

### 初始化数据
```
[mysql@master ~]$ mysqladmin -umysql -p -S /data/mysqldata/3306/mysql.sock create tpcc10
Enter password: 
[mysql@master ~]$ mysql -umysql -pmysql -S  /data/mysqldata/3306/mysql.sock tpcc10 < /usr/local/tpcc-mysql/create_table.sql 
Warning: Using a password on the command line interface can be insecure.
[mysql@master ~]$ mysql -umysql -pmysql -S  /data/mysqldata/3306/mysql.sock -e  " show tables from tpcc10;"
Warning: Using a password on the command line interface can be insecure.
+------------------+
| Tables_in_tpcc10 |
+------------------+
| customer         |
| district         |
| history          |
| item             |
| new_orders       |
| order_line       |
| orders           |
| stock            |
| warehouse        |
+------------------+
[mysql@master ~]$ mysql -umysql -pmysql -S  /data/mysqldata/3306/mysql.sock tpcc10 < /usr/local/tpcc-mysql/add_fkey_idx.sql 
Warning: Using a password on the command line interface can be insecure.
```
装载数据
```
/usr/local/tpcc-mysql/tpcc_load -hlocalhost -d tpcc10 -u mysql -p mysql -w 10
各列含义为： |hostname:port| |dbname| |user| |password| |WAREHOUSES
选项 warehouse 意为指定测试库下的仓库数量, 在生产环境中建议设置至少100以上
这里设置10个仓库
*************************************
*** TPCC-mysql Data Loader        ***
*************************************
option h with value 'localhost'
option d with value 'tpcc10'
option u with value 'mysql'
option p with value 'mysql'
option w with value '10'
<Parameters>
     [server]: localhost
     [port]: 3306
     [DBname]: tpcc10
       [user]: mysql
       [pass]: mysql
  [warehouse]: 10
TPCC Data Load Started...
Loading Item 
.................................................. 5000
.................................................. 10000
.................................................. 15000
.................................................. 20000
.................................................. 25000
.................................................. 30000
...
...DATA LOADING COMPLETED SUCCESSFULLY.
```

### 压力测试
```
[mysql@master tpcc-mysql]$ /usr/local/tpcc-mysql/tpcc_start -h 172.16.120.130 -P 3306 -d tpcc10 -u mysql -p mysql -w 10 -c 10 -r 10 -l 360 -i 10
各列含义为： |hostname| |port| |dbname| |user| |password|  |WAREHOUSES| |CONNECTIONS| |WARMUP TIME| |BENCHMARK TIME|

***************************************
*** ###easy### TPC-C Load Generator ***
***************************************
option h with value '172.16.120.130'
option P with value '3306'
option d with value 'tpcc10'
option u with value 'mysql'
option p with value 'mysql'
option w with value '10'
option c with value '10'
option r with value '10'
option l with value '360'
option i with value '10'
<Parameters>
     [server]: 172.16.120.130
     [port]: 3306
     [DBname]: tpcc10
       [user]: mysql
       [pass]: mysql
  [warehouse]: 10
 [connection]: 10
     [rampup]: 10 (sec.)
    [measure]: 360 (sec.)

RAMP-UP TIME.(10 sec.)

MEASURING START.

  10, trx: 99, 95%: 1276.031, 99%: 1651.666, max_rt: 1900.823, 99|2176.948, 9|453.968, 9|2345.216, 9|4414.681
  20, trx: 118, 95%: 820.803, 99%: 1096.353, max_rt: 1103.197, 118|756.232, 13|256.137, 12|1141.656, 14|2324.185
  30, trx: 154, 95%: 641.000, 99%: 725.352, max_rt: 806.858, 157|607.721, 15|459.590, 15|1153.335, 15|2572.398
  40, trx: 207, 95%: 493.000, 99%: 600.507, max_rt: 655.846, 207|329.540, 20|248.943, 21|980.139, 19|1454.455
  50, trx: 225, 95%: 508.894, 99%: 1075.549, max_rt: 1184.488, 221|303.606, 23|263.231, 22|1438.822, 24|1140.145
  60, trx: 276, 95%: 375.332, 99%: 444.495, max_rt: 702.731, 281|374.427, 28|332.414, 28|868.740, 28|1169.240
  70, trx: 276, 95%: 412.445, 99%: 561.394, max_rt: 613.595, 275|331.192, 27|254.446, 27|1023.836, 28|986.823
  80, trx: 346, 95%: 285.151, 99%: 385.697, max_rt: 480.219, 343|465.637, 36|446.044, 35|663.491, 34|836.714
  90, trx: 361, 95%: 304.106, 99%: 418.414, max_rt: 454.055, 365|261.836, 35|306.751, 36|706.509, 37|451.790
 100, trx: 352, 95%: 329.310, 99%: 379.285, max_rt: 596.875, 350|228.144, 35|314.441, 35|874.110, 35|434.005
 110, trx: 391, 95%: 264.116, 99%: 364.263, max_rt: 524.591, 393|300.455, 40|237.326, 39|870.451, 39|368.716
 120, trx: 390, 95%: 283.110, 99%: 380.422, max_rt: 796.105, 390|209.881, 39|212.044, 39|649.781, 39|361.857
 130, trx: 481, 95%: 211.891, 99%: 273.448, max_rt: 320.599, 478|205.557, 48|401.033, 48|545.170, 48|299.260
 140, trx: 394, 95%: 261.676, 99%: 355.964, max_rt: 500.034, 397|457.157, 40|228.973, 40|921.951, 39|338.248
 150, trx: 383, 95%: 283.788, 99%: 454.587, max_rt: 566.393, 384|280.592, 37|183.698, 38|848.897, 39|399.204
 160, trx: 451, 95%: 231.870, 99%: 340.538, max_rt: 484.727, 448|260.694, 46|361.268, 46|660.337, 44|466.192
 170, trx: 462, 95%: 228.493, 99%: 344.949, max_rt: 514.245, 459|181.838, 45|238.782, 44|635.492, 47|380.900
 180, trx: 501, 95%: 201.077, 99%: 264.590, max_rt: 367.453, 504|187.646, 51|209.077, 51|592.223, 50|312.695
 190, trx: 487, 95%: 208.182, 99%: 248.247, max_rt: 385.329, 487|156.600, 48|268.224, 49|599.426, 48|369.467
 200, trx: 549, 95%: 198.565, 99%: 246.544, max_rt: 289.273, 547|125.297, 55|149.780, 55|579.936, 55|288.928
 210, trx: 525, 95%: 183.039, 99%: 252.746, max_rt: 348.508, 527|151.534, 53|157.159, 53|538.393, 53|342.113
 220, trx: 498, 95%: 212.272, 99%: 454.723, max_rt: 649.610, 499|247.239, 49|373.921, 49|850.496, 50|307.415
 230, trx: 593, 95%: 165.673, 99%: 224.628, max_rt: 254.276, 592|161.555, 60|217.229, 59|554.132, 59|274.591
 240, trx: 562, 95%: 172.712, 99%: 222.220, max_rt: 462.752, 563|119.360, 56|363.567, 56|444.219, 57|438.265
 250, trx: 613, 95%: 156.842, 99%: 210.249, max_rt: 336.516, 609|210.124, 61|162.821, 63|443.805, 60|302.938
 260, trx: 581, 95%: 160.451, 99%: 234.803, max_rt: 310.168, 584|134.567, 58|205.125, 57|503.892, 58|354.136
 270, trx: 657, 95%: 142.558, 99%: 184.193, max_rt: 236.276, 657|114.116, 66|163.854, 66|398.327, 66|302.011
 280, trx: 625, 95%: 154.188, 99%: 325.488, max_rt: 402.501, 627|305.710, 62|220.527, 62|490.638, 63|306.516
 290, trx: 648, 95%: 138.147, 99%: 186.747, max_rt: 257.152, 641|176.512, 66|97.194, 65|459.044, 65|268.244
 300, trx: 587, 95%: 157.030, 99%: 207.498, max_rt: 356.098, 589|177.653, 58|96.041, 58|439.606, 58|304.784
 310, trx: 606, 95%: 158.067, 99%: 200.416, max_rt: 261.933, 609|190.645, 61|147.659, 61|957.198, 61|312.263
 320, trx: 641, 95%: 149.149, 99%: 190.587, max_rt: 377.275, 640|111.658, 64|86.510, 64|398.594, 64|255.898
 330, trx: 616, 95%: 141.707, 99%: 186.970, max_rt: 325.279, 612|127.490, 61|143.177, 62|539.403, 61|329.158
 340, trx: 616, 95%: 146.626, 99%: 205.335, max_rt: 424.379, 621|144.290, 62|114.400, 62|521.001, 62|460.688
 350, trx: 659, 95%: 139.143, 99%: 187.250, max_rt: 274.776, 660|121.001, 66|115.034, 66|370.938, 66|260.560
 360, trx: 675, 95%: 136.871, 99%: 165.425, max_rt: 249.625, 673|147.049, 67|131.501, 67|462.549, 68|260.771

STOPPING THREADS..........

<Raw Results>
  [0] sc:9 lt:16596  rt:0  fl:0 avg_rt: 118.3 (5)
  [1] sc:678 lt:15928  rt:0  fl:0 avg_rt: 37.0 (5)
  [2] sc:201 lt:1459  rt:0  fl:0 avg_rt: 51.4 (5)
  [3] sc:0 lt:1659  rt:0  fl:0 avg_rt: 349.9 (80)
  [4] sc:1 lt:1661  rt:0  fl:0 avg_rt: 265.4 (20)
 in 360 sec.

<Raw Results2(sum ver.)>
  [0] sc:9  lt:16596  rt:0  fl:0 
  [1] sc:678  lt:15928  rt:0  fl:0 
  [2] sc:201  lt:1459  rt:0  fl:0 
  [3] sc:0  lt:1659  rt:0  fl:0 
  [4] sc:1  lt:1661  rt:0  fl:0 

<Constraint Check> (all must be [OK])
 [transaction percentage]
        Payment: 43.48% (>=43.0%) [OK]
   Order-Status: 4.35% (>= 4.0%) [OK]
       Delivery: 4.34% (>= 4.0%) [OK]
    Stock-Level: 4.35% (>= 4.0%) [OK]
 [response time (at least 90% passed)]
      New-Order: 0.05%  [NG] *
        Payment: 4.08%  [NG] *
   Order-Status: 12.11%  [NG] *
       Delivery: 0.00%  [NG] *
    Stock-Level: 0.06%  [NG] *

<TpmC>
                 2767.500 TpmC  --每分钟 除60就是tps
```
含义
```
10, trx: 12920, 95%: 9.483, 99%: 18.738, max_rt: 213.169, 12919|98.778, 1292|101.096, 1293|443.955, 1293|670.842
10 - 从基准测试开始到现在的秒数
trx: 12920 - 在给定的间隔内（在这种情况下,在过去的10秒）内执行的新订单交易. 基本上这是每个间隔的吞吐量. 越多越好
95％：9.483： - 每次给定间隔的新订单交易的95％响应时间. 在这种情况下是9.483秒
99％：18.738： - 每次给定间隔的新订单交易的99％响应时间. 在这种情况下是18.738秒
max_rt：213.169： - 每个给定间隔的新订单交易的最大响应时间. 在这种情况下是213.169秒
其余的：12919 | 98.778,1292 | 101.096,1293 | 443.955,1293 | 670.842是其他类型的事务的吞吐量和最大响应时间,可以忽略
```

### gnuplot出图
/usr/local/tpcc-mysql/scripts 目录下有一个analyze.sh

修改了一下,如果用gnuplot出图,则为
```
 cat tpcc.txt | grep -v HY000 | grep -v payment | grep -v neword | awk -v timeslot=1 ' BEGIN { FS="[,():]"; s=0; cntr=0 } /MEASURING START/ { s=1} /STOPPING THREADS/ {s=0} /0/ { if (s==1) {
 cntr++; } if ( cntr==timeslot ) { printf ("%d %d\n",$1,$3) ; cntr=0;}}' >> tpcc-graphic-data.txt

10 99
20 118
30 154
40 207
50 225
```
只输出时间和trx列
gnuplot.cnf
```
set terminal gif small size 800,600 #指定输出成gif图片，且图片大小为550×25
set output "tpcc.gif"    #指定输出gif图片的文件名
set title "MySQL Performance"    #图片标题
set style data lines    #显示网格
set xlabel "Time/s"    #X轴标题
set ylabel "Transactions"    #Y轴标题
set grid    #显示网格
plot \
"tpcc-graphic-data.txt" using 1:2 title "Total throughput" with lines #从tpcc-graphic-data.txt文件中读取第一列和第二列作为X轴和Y轴数据，
示例名"Total throughput"
```
出图
cat gnuplot.cnf | gnuplot
![image](https://github.com/Fanduzi/Tpcc-MySQL-guid/blob/master/image/Snip20170709_3.png)
 

### highcharts出图
修改analyze.sh
```
 cat tpcc.txt | grep -v HY000 | grep -v payment | grep -v neword | awk -v timeslot=1 ' BEGIN { FS="[,():]"; s=0; cntr=0 } /MEASURING START/ { s=1} /STOPPING THREADS/ {s=0} /0/ { if (s==1) {
 cntr++; } if ( cntr==timeslot ) { printf ("'{\'time\':'%d',\'trx\':'%d}\n",$1,$3) ; cntr=0;}}'
 
{'time':10,'trx':99}
{'time':20,'trx':118}
{'time':30,'trx':154}
{'time':40,'trx':207}
{'time':50,'trx':225}
{'time':60,'trx':276}
```

出图
```
import charts
def getData(file):
    with open(file,'r') as data:
        dicList=[]
        for line in data:
            dic=eval(line.strip())
            dicList.append(dic)
    timeList,trxList=[],[]
    for i in dicList:
        timeList.append(i['time'])
        trxList.append(i['trx'])
    dataList=list(zip(timeList,trxList))
    return dataList
    

options = {
    'title':{'text':'TPCC-MySQL'},
    'yAxis':{'title':{'text':'trx'}}
    }

charts.plot(getData('C:\\Users\\Fan\\tpcc.txt'),name='10 warehouse 10 connect',options=options,show='inline',)
```
![image](https://github.com/Fanduzi/Tpcc-MySQL-guid/blob/master/image/Snip20170709_4.png)
 不过要注意的是 这里是 trx是10秒的累计,每十秒一个trx


### 关于tpcc_load
tpcc_load用上面的方法是很慢的,实际上tpcc-mysql/下有一个脚本load_multi_schema.sh. 修改一下后
```
tpcc_load -h127.0.0.1 -d tpcc10 -u root -p "" -w 1000
各列含义为： |hostname:port| |dbname| |user| |password| |WAREHOUSES
选项 warehouse 意为指定测试库下的仓库数量, 在生产环境中建议设置至少100以上

$1 将要创建的数据库名
$2 仓库数量
$3 schema数量,这里为1
$4 数据库ip
这里mysql用户名密码socket写死了
[root@master2 tpcc-mysql]# more load_multi_schema.sh 
#export LD_LIBRARY_PATH=/data/opt/bin/mysql-5.7.11-linux-glibc2.5-x86_64/lib/
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/mysql/lib/
DBNAME=$1
WH=$2
NSCHEMA=$3
HOST=$4
STEP=5 #step应该小于WH

schema=0

while [ $schema -lt $NSCHEMA ] #主循环
do
 DBFULLNAME=${DBNAME}_${schema}
 echo "Creating schema $DBFULLNAME"
 mysqladmin -umysql -pmysql -S /data/mysqldata/3306/mysql.sock -f drop $DBFULLNAME
 mysqladmin -umysql -pmysql -S /data/mysqldata/3306/mysql.sock create $DBFULLNAME
 mysql -umysql -pmysql -S /data/mysqldata/3306/mysql.sock $DBFULLNAME  < create_table.sql 
 mysql -umysql -pmysql -S /data/mysqldata/3306/mysql.sock $DBFULLNAME  < add_fkey_idx.sql
 mkdir -p out
#以上是创建数据库,跑初始化脚本
./tpcc_load -h$HOST -d $DBFULLNAME -u mysql -p mysql -w $WH -l 1 -m 1 -n $WH >> out/1_$DBFULLNAME.out &
#Usage: tpcc_load -h server_host -P port -d database_name -u mysql_user -p mysql_password -w warehouses -l part -m min_wh -n max_wh
#* [part]: 1=ITEMS 2=WAREHOUSE 3=CUSTOMER 4=ORDERS
#-m 1 就是建ITEMS

x=1
#在循环里建剩下的-m 1 -n 步长, 一次循环建多少个
while [ $x -le $WH ] #主循环里的嵌套循环
do
 echo $x $(( $x + $STEP - 1 ))
./tpcc_load -h$HOST -d $DBFULLNAME -u mysql -p mysql -w $WH -l 2 -m $x -n $(( $x + $STEP - 1 ))  >> out/2_$DBFULLNAME.$x.out &
./tpcc_load -h$HOST -d $DBFULLNAME -u mysql -p mysql -w $WH -l 3 -m $x -n $(( $x + $STEP - 1 ))  >> out/3_$DBFULLNAME.$x.out &
./tpcc_load -h$HOST -d $DBFULLNAME -u mysql -p mysql -w $WH -l 4 -m $x -n $(( $x + $STEP - 1 ))  >> out/4_$DBFULLNAME.$x.out &
 x=$(( $x + $STEP ))
done

for job in `jobs -p`
do
echo $job
    wait $job
done

schema=$(( $schema + 1 ))

done
```
现在load是并行的
```
[mysql@master2 tpcc-mysql]$ ./load_multi_schema.sh tpcc 10 1 192.168.98.128
Creating schema tpcc_0
Warning: Using a password on the command line interface can be insecure.
Database "tpcc_0" dropped
Warning: Using a password on the command line interface can be insecure.
Warning: Using a password on the command line interface can be insecure.
Warning: Using a password on the command line interface can be insecure.
1 5
6 10
28496
28497
28498
28499
28500
28501
28502
[mysql@master2 tpcc-mysql]$ 

out目录有日志
```
