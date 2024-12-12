1. Вроаналезировав данные проекта, я пришел к выводу, что типы данных БД меняться нре будут. [Структура БД.png](https://github.com/dvlonkin/database_otus/blob/8ace7ce100213dec2729f27713f54223ad23a84a/%D0%A1%D1%82%D1%80%D1%83%D0%BA%D1%82%D1%83%D1%80%D0%B0%20%D0%91%D0%94.png)  
2. Вижу смысл добавить josn к таблице Products для хранения отзывов о товаре

```
ALTER TABLE Products
ADD COLUMN reviews JSON;
```
