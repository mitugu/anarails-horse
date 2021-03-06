w# README
## URL
* https://anarails-horse.herokuapp.com/insights

## About
* JRA data 
* Rails / MySQL / Bootstrap / Heroku
* [Sample data](./hanshinhinba19-utf8.csv)

## Reference

* Keywords Reference
  - http://www.jra.go.jp/keiba/overseas/yougo/c10040_list.html

## Getting Started 

* Add mysql2 gem

```bash
vi Gemfile # add mysql2
```

* Configure database  / Create database

```bash
vi config/database.yml  # assume  host is loadlhost, port is 3306, dbname is anarails_horse_{environment}, using root user/password
bundle exec rails db:create
```

* Scaffold / Migrate(Insights table will be created)

```bash
bundle exec rails g scaffold Insight year:text month:text day:text location:text race_number:integer race_name:text a0:text course_status:text distance:integer candidacy:integer post_time:string a1:text a2:text a3:text horse_name:text horse_gender:text horse_age:integer jockey:text weight_carry:float order_finish:integer order_difference:float popular:integer odds:float record_time:text a4:text a5:text a6:text a7:text position:text up_time:text a8:text trainer:text traing_center:text a9:text  owner:text farm:text sire:text broodmare_sire:text
bundle exec rails db:migrate
```

## Data Sample

```
19, # 年
01, # 月
05, # 日
京都, # 場所
11, # レース
京都金杯ＨG3, # 競馬場
芝, # 馬場状態
1600, # 距離
17, # 頭数	
15:45, # 出走時間
2, # a1
1, # a2
1, # a3
サラキア, # 名前
牝, # 性別
4, # 年齢
池添謙一, # 騎手
53.0, # 斤量
7, # 着順
0.4, # 着差
2, # 人気
4.8, # オッズ
95.3, # タイム
0, # a4
0, # a5
6,9, # a6
中団, # 位置取り
35.0, # 上りタイム
8, # a7
池添学, # 調教師
栗, # 施設
0, # a8
シルクレーシング, # 馬主
ノーザンファーム, # 牧場
ディープインパクト, # 血統（父）
Lomitas # 血統（母父）
```


## Load data command (run load-data.sql, sample data(197 records) will be loaded into insights table on your local mysql server)

' command 

```bash
MYSQL_PWD={password} mysql -hlocalhost -uroot -Danarails_horse_dev --local-infile=1 < load-data.sql
MYSQL_PWD={password} mysql -hlocalhost -uroot -Danarails_horse_dev
```

* load-data.sql is following (exist in this repository with hanshinhinba19-utf8.csv)

```sql
LOAD DATA LOCAL INFILE
  './hanshinhinba19-utf8.csv'
INTO TABLE
  insights
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\n' (
  year,
  month,
  day,
  location,
  race_number,
  race_name,
  course_status,
  distance,
  candidacy,
  post_time,
  a1,
  a2,
  a3,
  horse_name,
  horse_gender,
  horse_age,
  jockey,
  weight_carry,
  order_finish,
  order_difference,
  popular,
  odds,
  record_time,
  a4,
  a5,
  a6,
  a7,
  position,
  up_time,
  a8,
  trainer,
  traing_center,
  a9,
  owner,
  farm,
  sire,
  broodmare_sire
)
SET
  created_at = CURRENT_TIMESTAMP,
  updated_at = CURRENT_TIMESTAMP;
```

## SQL for selecting data and checking on mysql-cli or any client software

```
select
  concat(location, race_name, " ", race_number, "R") as race_display,
  concat(year, "-", month, "-", day, " ", post_time) as race_at_display,
  concat(course_status, distance, "m") as course_display,
  concat(order_finish, " 着") as order_display,
  concat(horse_name, horse_gender, horse_age) as horse_display,
  concat(popular, "番人気") as popular_display,
  odds,
  record_time,
  up_time,
  position,
  sire,
  broodmare_sire,
  jockey,
  trainer
from
  insights
where
  order_finish != 0;
```

## CSS

```
bundle exec rails assets:precompile
```

## Heroku

```bash
heroku login # only first
heroku create anarails-horse # only first
heroku addons:add cleardb:ignite # only first
heroku config:get CLEARDB_DATABASE_URL # only first
heroku config:set DATABASE_URL={value of above CLEARDB_DATABASE_URL after replace mysql:// to mysql2://} # only first
git add ./ # if you have any changes
git commit -m "init" # if you have any changes
git push heroku master # first time you need to do, also needed if you have any changes.
heroku run rails db:create # only first
$ mysqldump -hlocalhost -uroot -proot --no-create-info anarails_horse_dev insights  > anarails_horse_dev.sql # only first / check database anarails_horse_dev exists(and csv data is loaded)
$ mysql -h{DATABASE_URL host} -u{DATABASE_URL user} -p{DATABASE_URL password} -D{DATABASE_URL dbname} < anarails_horse_dev.sql # only first
```

