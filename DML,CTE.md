#### Создайте таблицу и наполните ее данными  
```
CREATE TABLE statistic(
player_name VARCHAR(100) NOT NULL,
player_id INT NOT NULL,
year_game SMALLINT NOT NULL CHECK (year_game > 0),
points DECIMAL(12,2) CHECK (points >= 0),
PRIMARY KEY (player_name,year_game));
```
```
INSERT INTO statistic(player_name, player_id, year_game, points) VALUES ('Mike',1,2018,18), ('Jack',2,2018,14), ('Jackie',3,2018,30), ('Jet',4,2018,30), ('Luke',1,2019,16), ('Mike',2,2019,14), ('Jack',3,2019,15), ('Jackie',4,2019,28), ('Jet',5,2019,25), ('Luke',1,2020,19), ('Mike',2,2020,17), ('Jack',3,2020,18), ('Jackie',4,2020,29), ('Jet',5,2020,27); 
```

| player_name | player_id | year_game | points |
|-----------| ---------|---------|------|
|Mike|1|2018|18.00|
|Jack|2|2018|14.00|
|Jackie|3|2018|30.00|
|Jet|4|2018|30.00|
|Luke|1|2019|16.00|
|Mike|2|2019|14.00|
|Jack|3|2019|15.00|
|Jackie|4|2019|28.00|
|Jet|5|2019|25.00|
|Luke|1|2020|19.00|
|Mike|2|2020|17.00|
|Jack|3|2020|18.00|
|Jackie|4|2020|29.00|
|Jet|5|2020|27.00|

#### Написать запрос суммы очков с группировкой и сортировкой по годам
```
select year_game, sum(s.points) as total_points
from statistic s
group by s.year_game
order by s.year_game;
```
|year_game	|total_points|
|-----------|------------|
|2018	|92.00|
|2019	|98.00|
|2020	|110.00|

#### написать cte показывающее тоже самое
```
WITH YearlyPoints AS (
    SELECT year_game, SUM(points) AS total_points
    FROM statistic
    GROUP BY year_game
)
SELECT year_game, total_points
FROM YearlyPoints
ORDER BY year_game;
```
|year_game	|total_points|
|-----------|------------|
|2018	|92.00|
|2019	|98.00|
|2020	|110.00|

#### используя функцию LAG вывести кол-во очков по всем игрокам за текущий код и за предыдущий.
```
WITH YearlyPoints AS (
    SELECT
        year_game,
        player_id,
        player_name,
        SUM(points) AS total_points
    FROM
        statistic
    GROUP BY
        year_game, player_id, player_name
),
PlayerPointsWithLag AS (
    SELECT
        year_game,
        player_id,
        player_name,
        total_points,
        LAG(total_points) OVER (PARTITION BY player_id ORDER BY year_game) AS previous_year_points
    FROM
        YearlyPoints
)
SELECT
    year_game,
    player_id,
    player_name,
    total_points,
    previous_year_points
FROM
    PlayerPointsWithLag
ORDER BY
    player_id, year_game;
```
|year_game|player_id|player_name|total_points|previous_year_points|
|-|-|-|-|-|
|2018|1|Mike|18.00|NULL|
|2019|1|Luke|16.00|18.00|
|2020|1|Luke|19.00|16.00|
|2018|2|Jack|14.00|NULL|
|2019|2|Mike|14.00|14.00|
|2020|2|Mike|17.00|14.00|
|2018|3|Jackie|30.00|NULL|
|2019|3|Jack|15.00|30.00|
|2020|3|Jack|18.00|15.00|
|2018|4|Jet|30.00|NULL|
|2019|4|Jackie|28.00|30.00|
|2020|4|Jackie|29.00|28.00|
|2019|5|Jet|25.00|NULL|
|2020|5|Jet|27.00|25.00|
