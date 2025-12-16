**Лабораторные работы по БД**

Перечень [лабораторных работ](https://edu.irnok.net/doku.php?id=db:main#%D0%BB%D0%B0%D0%B1%D0%BE%D1%80%D0%B0%D1%82%D0%BE%D1%80%D0%BD%D0%B0%D1%8F_%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D0%B0_5_%D1%82%D1%80%D0%B8%D0%B3%D0%B3%D0%B5%D1%80%D1%8B)

Telegram: [at]chebur1811

# Постановка задачи (вариант 5)

*Сущности:*
Магазин (номер, наименование, адрес, площадь)
Товар (наименование, сорт)
Товар в магазине (единица измерения, цена за единицу, количество)

*Процессы:*
Для каждого магазина фиксируются сведения о доступных товарах: их цена, единица измерения и количество.
Регистрируются все товары, представленные в магазинах, с учетом фактических количеств и стоимости.

*Выходные документы:*
Для каждого магазина из заданного списка номеров выдать информацию о товарах, с подсчетом их стоимости, упорядоченную по наименованиям товаров.

Выдать информацию о магазинах, упорядоченную по их адресам, с подсчетом средней площади всех магазинов.

# Лабораторная работа 1

## Содержание

1. [Постановка задачи](#постановка-задачи)
2. [Концептуальная модель (ER)](#концептуальная-модель-er)
3. [Логическая модель](#логическая-модель)
4. [Физическая модель](#физическая-модель)
5. [SQL DDL](#sql-ddl)
6. [Примеры запросов](#примеры-запросов)
7. [Проверка нормальных форм](#эволюция-проекта)

---

## Постановка задачи

### Цель проекта

Разработать реляционную базу данных для учета товаров в магазинах с информацией о ценах, количестве и стоимости товаров.

### Бизнес-требования

- Учет магазинов с основной информацией (номер, наименование, адрес, площадь)
- Учет товаров (наименование, сорт)
- Ведение информации о товарах в магазинах (единица измерения, цена, количество)
- Формирование отчетов по товарам в магазинах и информации о магазинах

### Ограничения

- ✅ Один товар может продаваться в нескольких магазинах
- ✅ Один магазин может содержать много товаров
- ✅ Каждый товар в магазине имеет свою цену и количество
- ✅ Магазин идентифицируется номером (уникален)

### Выходные документы

1. Для каждого магазина из заданного списка номеров выдать информацию о товарах с подсчетом их стоимости, упорядоченную по наименованиям товаров
2. Выдать информацию о магазинах, упорядоченную по их адресам, с подсчетом средней площади всех магазинов

---

### Концептуальная модель (ER)

![Концептуальная модель ER](docs/images/er-diagram.png)

### Описание сущностей

- **SHOP** - информация о магазинах (номер, наименование, адрес, площадь)
- **PRODUCT** - информация о товарах (наименование, сорт)
- **INVENTORY** - записи о наличии товаров в магазинах (единица измерения, цена, количество)

# Лабораторная работа 2

### Логическая модель

![Логическая модель](docs/images/logical-model.png)

### Связи между сущностями

- **Shop → Inventory**: 1:N (один магазин может содержать много записей инвентаря)
- **Product → Inventory**: 1:N (один товар может быть в инвентаре нескольких магазинов)
- **Shop ↔ Product**: M:N (через таблицу Inventory - много магазинов, много товаров)

---

### Физическая модель

![Физическая модель](docs/images/physical-model.png)



---

## SQL DDL

### Полный скрипт создания базы данных

```sql
CREATE TABLE kyzlakov_2261.Shop (
    shop_number SERIAL PRIMARY KEY,
    shop_name VARCHAR(100) NOT NULL,
    address VARCHAR(200) NOT NULL,
    floor_space NUMERIC(8,2) CHECK (floor_space > 0)
);

CREATE TABLE kyzlakov_2261.Product (
    product_id SERIAL PRIMARY KEY,
    product_name VARCHAR(150) NOT NULL,
    variety VARCHAR(100),
    UNIQUE(product_name, variety)
);

CREATE TABLE kyzlakov_2261.Inventory (
    inventory_id SERIAL PRIMARY KEY,
    shop_number INTEGER NOT NULL,
    product_id INTEGER NOT NULL,
    unit_of_measure VARCHAR(50) NOT NULL,
    unit_price NUMERIC(10,2) NOT NULL CHECK (unit_price > 0),
    quantity NUMERIC(10,2) NOT NULL CHECK (quantity >= 0),
    FOREIGN KEY (shop_number) REFERENCES kyzlakov_2261.Shop(shop_number) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES kyzlakov_2261.Product(product_id) ON DELETE CASCADE,
    UNIQUE(shop_number, product_id)
);
```
---
![Таблица](docs/images/tables.png)
![Колонки](docs/images/columns.png)
---
## 

```sql
INSERT INTO kyzlakov_2261.Shop (shop_name, address, floor_space) VALUES
('Продукты №1', 'ул. Ленина 10, Москва', 250.50),
('Супермаркет №2', 'пр. Мира 25, Санкт-Петербург', 450.00),
('Мини-маркет Центр', 'ул. Советская 5, Екатеринбург', 120.75),
('Гастроном Элитный', 'ул. Победы 1, Новосибирск', 320.00),
('Дисконт №3', 'ш. Энтузиастов 15, Казань', 180.25);
 
INSERT INTO kyzlakov_2261.Product (product_name, variety) VALUES
('Молоко', '2.5% жирности'),
('Хлеб', 'Белый'),
('Яйца', 'Куриные 10шт'),
('Масло сливочное', '82%'),
('Сахар', 'Белый песок'),
('Кефир', '1% жирности');

INSERT INTO kyzlakov_2261.Inventory (shop_number, product_id, unit_of_measure, unit_price, quantity) VALUES
(1, 1, 'литр', 85.50, 120.00),
(1, 2, 'кг', 45.20, 80.50),
(1, 3, 'упаковка', 95.00, 45.00),
(2, 1, 'литр', 82.90, 200.00),
(2, 4, 'кг', 650.00, 25.50),
(2, 5, 'кг', 54.90, 75.00),
(3, 3, 'упаковка', 92.50, 30.00),
(3, 6, 'литр', 78.00, 90.00),
(4, 2, 'кг', 47.00, 60.00),
(4, 4, 'кг', 660.00, 18.00),
(5, 1, 'литр', 84.00, 150.00),
(5, 5, 'кг', 53.50, 110.00);

```
---

## Примеры запросов

### 1. Товары в заданных магазинах со стоимостью

```sql
SELECT 
    s.shop_number AS "Номер магазина",
    s.shop_name AS "Наименование магазина",
    p.product_name AS "Товар",
    p.variety AS "Сорт",
    i.unit_of_measure AS "Единица измерения",
    i.unit_price AS "Цена за единицу",
    i.quantity AS "Количество",
    (i.unit_price * i.quantity)::NUMERIC(12,2) AS "Стоимость"
FROM kyzlakov_2261.inventory i
JOIN kyzlakov_2261.shop s ON i.shop_number = s.shop_number
JOIN kyzlakov_2261.product p ON i.product_id = p.product_id
WHERE s.shop_number IN (1, 2, 3)
ORDER BY s.shop_number, p.product_name;
```
![Вывод 1](docs/images/output1.png)

### 2. Самые дорогие товары каждого магазина

```sql
SELECT 
    s.shop_name AS "Магазин",
    p.product_name AS "Товар",
    p.variety AS "Сорт",
    i.unit_price AS "Цена за ед.",
    i.quantity AS "Количество"
FROM kyzlakov_2261.inventory i
JOIN kyzlakov_2261.shop s ON i.shop_number = s.shop_number
JOIN kyzlakov_2261.product p ON i.product_id = p.product_id
WHERE i.unit_price = (
    SELECT MAX(unit_price) 
    FROM kyzlakov_2261.inventory i2 
    WHERE i2.shop_number = i.shop_number
)
ORDER BY s.shop_number;

```
![Вывод 2](docs/images/output2.png)

### 3. Магазины с запасом молочных продуктов

```sql
SELECT DISTINCT
    s.shop_number AS "№",
    s.shop_name AS "Магазин",
    s.address AS "Адрес"
FROM kyzlakov_2261.inventory i
JOIN kyzlakov_2261.shop s ON i.shop_number = s.shop_number
JOIN kyzlakov_2261.product p ON i.product_id = p.product_id
WHERE p.product_name IN ('Молоко', 'Кефир')
ORDER BY s.shop_number;
```
![Вывод 3](docs/images/output3.png)

---
# Лабораторная работа 3

## 1. Создание представлений для выходных документов

**Представление 1:** Товары магазинов 1,2,3
```sql
CREATE OR REPLACE VIEW kyzlakov_2261.shop1_2_3_report AS
SELECT 
    s.shop_number, 
    s.shop_name, 
    p.product_name, 
    p.variety,
    i.unit_of_measure, 
    i.unit_price, 
    i.quantity,
    i.unit_price * i.quantity AS total_cost
FROM kyzlakov_2261.inventory i
JOIN kyzlakov_2261.shop s ON i.shop_number = s.shop_number
JOIN kyzlakov_2261.product p ON i.product_id = p.product_id
WHERE s.shop_number IN (1, 2, 3)
ORDER BY s.shop_number, p.product_name;

```

**Использование**
```sql
SELECT shop_number, shop_name, product_name, total_cost
FROM kyzlakov_2261.shop1_2_3_report
WHERE shop_number = 1
ORDER BY total_cost DESC;


```

**Результат использования:**
![upr1](docs/images/upr1.png)
---

**Представление 2:** Самый дорогой товар в каждом магазине

```sql
CREATE OR REPLACE VIEW kyzlakov_2261.shops_by_address AS
SELECT
    s.shop_number,
    s.shop_name,
    s.address,
    s.floor_space,
    p.product_name AS most_expensive_product,
    p.variety,
    i.unit_price,
    i.quantity
FROM kyzlakov_2261.inventory i  
JOIN kyzlakov_2261.shop s ON i.shop_number = s.shop_number  
JOIN kyzlakov_2261.product p ON i.product_id = p.product_id  
WHERE i.unit_price = (  
    SELECT MAX(unit_price)  
    FROM kyzlakov_2261.inventory i2  
    WHERE i2.shop_number = i.shop_number  
)  
ORDER BY s.shop_name;
```

**Использование**
```sql
SELECT 
    shop_number,
    shop_name, 
    address,
    floor_space,
    most_expensive_product,
    unit_price
FROM kyzlakov_2261.shops_by_address
WHERE floor_space > 200
ORDER BY shop_name;
```

**Результат использования:**
![123](docs/images/123.png)
---

## 2. Разработка хранимых процедур с параметрами
**Процедура 1:** Анализ магазина по номеру

```sql
CREATE OR REPLACE PROCEDURE kyzlakov_2261.get_shop_stats(
    p_shop_number INTEGER
)
LANGUAGE plpgsql
AS $$
BEGIN
    SELECT 
        COUNT(*) as total_items,
        SUM(unit_price * quantity) as total_cost
    FROM kyzlakov_2261.inventory 
    WHERE shop_number = p_shop_number;
END;
$$;
```
**Процедура 2:** 


```sql
CREATE OR REPLACE PROCEDURE kyzlakov_2261.get_shop_stats(
    p_shop_number INTEGER
)
LANGUAGE plpgsql
AS $$
BEGIN
    SELECT 
        COUNT(*) as total_items,
        SUM(unit_price * quantity) as total_cost
    FROM kyzlakov_2261.inventory 
    WHERE shop_number = p_shop_number;
END;
$$;

```

---
## Проверка работы

**Проверка представлений:**
```sql
SELECT * FROM kyzlakov_2261.shop1_2_3_report LIMIT 3;
SELECT * FROM kyzlakov_2261.shops_by_address LIMIT 3;
```
![ppr1](docs/images/ppr1.png)
![ppr2](docs/images/ppr2.png)

**Проверка процедур:**
```sql
CALL kyzlakov_2261.get_shop_stats(1);
CALL kyzlakov_2261.add_product_to_shop(2, 'Чай', 'Зеленый');

```
![call1](docs/images/call1.png)
![call2](docs/images/call2.png)
---
## 3. Представление сложных запросов при помощи представления
**Сложное представление:**ТОП товаров по выручке всех магазинов
```sql
CREATE OR REPLACE VIEW kyzlakov_2261.top_products_by_revenue AS
SELECT 
    s.shop_number,
    s.shop_name,
    p.product_name,
    p.variety,
    SUM(i.quantity) as total_quantity,
    SUM(i.unit_price * i.quantity) as total_revenue,
    RANK() OVER (ORDER BY SUM(i.unit_price * i.quantity) DESC) as revenue_rank
FROM kyzlakov_2261.inventory i
JOIN kyzlakov_2261.shop s ON i.shop_number = s.shop_number
JOIN kyzlakov_2261.product p ON i.product_id = p.product_id
GROUP BY s.shop_number, s.shop_name, p.product_name, p.variety
HAVING SUM(i.unit_price * i.quantity) > 5000
ORDER BY total_revenue DESC;

```

**Использование сложного представления (анализ загрузки):**
```sql
SELECT shop_name, product_name, total_revenue, revenue_rank
FROM kyzlakov_2261.top_products_by_revenue
WHERE revenue_rank <= 5;
```
![flex](docs/images/flex.png)

# Лабораторная работа 4. Анализ производительности

## 1. Создание генератора данных (20 000 записей в каждой таблице)

```sql
TRUNCATE TABLE kyzlakov_2261.inventory CASCADE;
TRUNCATE TABLE kyzlakov_2261.product CASCADE;
TRUNCATE TABLE kyzlakov_2261.shop CASCADE;

ALTER SEQUENCE IF EXISTS kyzlakov_2261.shop_shop_number_seq RESTART WITH 1;
ALTER SEQUENCE IF EXISTS kyzlakov_2261.product_product_id_seq RESTART WITH 1;
ALTER SEQUENCE IF EXISTS kyzlakov_2261.inventory_inventory_id_seq RESTART WITH 1;

INSERT INTO kyzlakov_2261.shop (shop_name, address, floor_space)
SELECT 
    'Магазин ' || seq,
    'Адрес ' || seq,
    ROUND((RANDOM() * 450 + 50)::NUMERIC, 2)
FROM generate_series(1, 20000) AS seq;

INSERT INTO kyzlakov_2261.product (product_name, variety)
SELECT 
    'Товар_' || seq,
    'Вид_' || (seq % 100)
FROM generate_series(1, 20000) AS seq;

INSERT INTO kyzlakov_2261.inventory (shop_number, product_id, unit_of_measure, unit_price, quantity)
SELECT 
    seq,
    seq,
    CASE (seq % 6)
        WHEN 0 THEN 'шт'
        WHEN 1 THEN 'кг'
        WHEN 2 THEN 'л'
        WHEN 3 THEN 'упак'
        WHEN 4 THEN 'г'
        WHEN 5 THEN 'мл'
    END,
    ROUND((RANDOM() * 990 + 10)::NUMERIC, 2),
    ROUND((RANDOM() * 1000)::NUMERIC, 2)
FROM generate_series(1, 20000) AS seq;

SELECT 
    (SELECT COUNT(*) FROM kyzlakov_2261.shop) as shops_count,
    (SELECT COUNT(*) FROM kyzlakov_2261.product) as products_count,
    (SELECT COUNT(*) FROM kyzlakov_2261.inventory) as inventory_count;
```

**Результат использования:**
![outgen](docs/images/outgen.png)

## 2. Анализ планов выполнения запросов (EXPLAIN ANALYZE)


### Исходные индексы (только первичные ключи)
```sql
SELECT 
    tablename,
    indexname,
    indexdef
FROM pg_indexes
WHERE schemaname = 'kyzlakov_2261'
ORDER BY tablename, indexname;
```
**Результат использования:**

![1](docs/images/1.png)

**Начальное состояние:** Только индексы первичных ключей (PRIMARY KEY).

### Тест 1: Поиск товаров в магазине по ID
```sql
-- Запрос 1: Найти все товары в магазине 1500
EXPLAIN ANALYZE
SELECT i.*, p.product_name, p.variety
FROM kyzlakov_2261.inventory i
JOIN kyzlakov_2261.product p ON i.product_id = p.product_id
WHERE i.shop_number = 1500;
```
**Результат использования:**

![2](docs/images/2.png)

Анализ результатов (до оптимизации):
Index Scan на inventory - использует индекс для поиска по shop_number
Nested Loop - вложенный цикл для соединения с таблицей product
Общее время: ~25.7 мс - неплохо, но можно улучшить

### Тест 2: Поиск магазинов с товарами определенной категории
```sql
-- Запрос 2: Найти магазины, где есть товары категории "Премиум"
EXPLAIN ANALYZE
SELECT DISTINCT s.*
FROM kyzlakov_2261.shop s
JOIN kyzlakov_2261.inventory i ON s.shop_number = i.shop_number
JOIN kyzlakov_2261.product p ON i.product_id = p.product_id
WHERE p.variety = 'Премиум'
ORDER BY s.shop_name
LIMIT 100;
```
**Результат использования:**

![3](docs/images/3.png)

Анализ результатов (до оптимизации):
Seq Scan на product с фильтром - проверяет все 20000 строк для фильтрации по variety
Hash Join - создает хеш-таблицы для соединения всех трех таблиц
Сортировка 20000 результатов - дорогая операция
Общее время: ~186.5 мс - медленно из-за полных сканирований

### Тест 3: Аналитический запрос - статистика по магазинам
```sql
-- Запрос 3: Статистика товаров по магазинам
EXPLAIN ANALYZE
SELECT 
    s.shop_number,
    s.shop_name,
    COUNT(i.product_id) as total_products,
    SUM(i.quantity) as total_quantity,
    AVG(i.unit_price) as avg_price,
    SUM(i.quantity * i.unit_price) as total_value
FROM kyzlakov_2261.shop s
LEFT JOIN kyzlakov_2261.inventory i ON s.shop_number = i.shop_number
GROUP BY s.shop_number, s.shop_name
HAVING SUM(i.quantity) > 500
ORDER BY total_value DESC
LIMIT 20;
```
**Результат использования:**

![4](docs/images/4.png)

Анализ результатов (до оптимизации):
Seq Scan на обе таблицы - полное чтение 40000 строк
HashAggregate - агрегация всех 20000 магазинов
Сортировка всех результатов - перед LIMIT 20
Общее время: ~262.9 мс - очень медленно для аналитики


## 3. Оптимизация БД через индексы и настройки

### Создание оптимизирующих индексов
```sql
-- Индекс для поиска товаров по категории (variety)
CREATE INDEX IF NOT EXISTS idx_product_variety ON kyzlakov_2261.product(variety);

-- Индекс для поиска по названию товара (для LIKE запросов)
CREATE INDEX IF NOT EXISTS idx_product_name ON kyzlakov_2261.product(product_name);

-- Индекс для поиска магазинов по площади
CREATE INDEX IF NOT EXISTS idx_shop_floor_space ON kyzlakov_2261.shop(floor_space);

-- Индекс для поиска магазинов по названию
CREATE INDEX IF NOT EXISTS idx_shop_name ON kyzlakov_2261.shop(shop_name);

-- Индекс для поиска в инвентаре по shop_number (дополнительный)
CREATE INDEX IF NOT EXISTS idx_inventory_shop ON kyzlakov_2261.inventory(shop_number);

-- Индекс для поиска в инвентаре по product_id
CREATE INDEX IF NOT EXISTS idx_inventory_product ON kyzlakov_2261.inventory(product_id);

-- Составной индекс для аналитических запросов
CREATE INDEX IF NOT EXISTS idx_inventory_shop_quantity_price ON kyzlakov_2261.inventory(shop_number, quantity, unit_price);

-- Частичный индекс для товаров с высоким количеством
CREATE INDEX IF NOT EXISTS idx_inventory_high_quantity ON kyzlakov_2261.inventory(shop_number, product_id)
WHERE quantity > 100;
```

### Настройка параметров сессии
```sql
-- Установка параметров для текущей сессии
SET work_mem = '16MB';
SET max_parallel_workers_per_gather = 4;
SET random_page_cost = 1.1;
SET effective_cache_size = '1GB';

-- Сбор статистики для оптимизатора
ANALYZE kyzlakov_2261.shop;
ANALYZE kyzlakov_2261.product;
ANALYZE kyzlakov_2261.inventory;
```

## 4. Сравнение производительности до/после оптимизации

### Тест 1: Поиск товаров в магазине по ID (после оптимизации)
```sql
EXPLAIN ANALYZE
SELECT i.*, p.product_name, p.variety
FROM kyzlakov_2261.inventory i
JOIN kyzlakov_2261.product p ON i.product_id = p.product_id
WHERE i.shop_number = 1500;
```
**Результат использования:**

![5](docs/images/5.png)

| Метрика | До оптимизации | После оптимизации | Улучшение |
|---------|----------------|-------------------|-----------|
| Время выполнения | 25.710 ms | 0.060 ms | 428x |
| Стоимость (cost) | 2004.78 | 16.62 | 120x |
| Тип скана | Index Scan | Index Scan | - |

**Тест 2: Поиск магазинов с товарами категории "Премиум" (после оптимизации)**
```sql
EXPLAIN ANALYZE
SELECT DISTINCT s.*
FROM kyzlakov_2261.shop s
JOIN kyzlakov_2261.inventory i ON s.shop_number = i.shop_number
JOIN kyzlakov_2261.product p ON i.product_id = p.product_id
WHERE p.variety = 'Премиум'
ORDER BY s.shop_name
LIMIT 100;
```
**Результат использования:**

![6](docs/images/6.png)

**Анализ ПОСЛЕ оптимизации:**
| Метрика | До оптимизации | После оптимизации | Улучшение |
|---------|----------------|-------------------|-----------|
| Время выполнения | 186.511 ms | 8.868 ms | 21x |
| Стоимость (cost) | 20633.68 | 858.16 | 24x |
| Скан product | Seq Scan + Filter | Bitmap Index Scan | |

### Тест 3: Аналитический запрос - статистика по магазинам (после оптимизации)
```sql
EXPLAIN ANALYZE
SELECT 
    s.shop_number,
    s.shop_name,
    COUNT(i.product_id) as total_products,
    SUM(i.quantity) as total_quantity,
    AVG(i.unit_price) as avg_price,
    SUM(i.quantity * i.unit_price) as total_value
FROM kyzlakov_2261.shop s
LEFT JOIN kyzlakov_2261.inventory i ON s.shop_number = i.shop_number
GROUP BY s.shop_number, s.shop_name
HAVING SUM(i.quantity) > 500
ORDER BY total_value DESC
LIMIT 20;
```
**Результат использования:**

![7](docs/images/7.png)

**Сравнение производительности Тест 3:**
| Метрика | До оптимизации | После оптимизации | Улучшение |
|---------|----------------|-------------------|-----------|
| Время выполнения | 262.939 ms | 98.824 ms | 2.7x |
| Стоимость (cost) | 27891.66 | 27911.66 | Незначительно |
| Основное улучшение | - | Ускорение HashAggregate | За счет индексов |


### Итоговая статистика производительности
```sql
-- Сводка по созданным индексам
SELECT 
    tablename,
    indexname,
    pg_size_pretty(pg_relation_size(indexname::regclass)) as index_size,
    indexdef
FROM pg_indexes
WHERE schemaname = 'kyzlakov_2261'
ORDER BY tablename, indexname;
```

## **Результат создания индексов:**

Таблица        | Индекс                          | Размер   | Назначение
---------------|---------------------------------|----------|-------------------
shop          | idx_shop_floor_space           | 440 kB   | Поиск по площади
shop          | idx_shop_name                  | 440 kB   | Поиск по названию
shop          | shop_pkey                      | 440 kB   | Первичный ключ
product       | idx_product_name               | 440 kB   | Поиск по названию
product       | idx_product_variety            | 440 kB   | Поиск по категории
product       | product_pkey                   | 440 kB   | Первичный ключ
inventory     | idx_inventory_high_quantity    | 216 kB   | Товары >100 ед.
inventory     | idx_inventory_product          | 440 kB   | Поиск по товару
inventory     | idx_inventory_shop             | 440 kB   | Поиск по магазину
inventory     | idx_inventory_shop_quantity_price | 656 kB | Аналитические запросы
inventory     | inventory_pkey                 | 440 kB   | Первичный ключ
inventory     | inventory_shop_number_product_id_key | 440 kB | Уникальный индекс
| Статистика посещений | 280 мс | 45 мс | 6.2x |
| Аналитический запрос | 350 мс | 65 мс | 5.4x |


# Лабораторная работа 5. Триггеры и аудит


## Задачи
1. Триггеры каскадного удаления для связей "один-ко-многим"
2. Триггеры аудита изменений (INSERT, UPDATE, DELETE) в таблицах
3. Создание таблицы-журнала для отслеживания изменений
   
## Триггеры каскадного удаления

### 1. Каскадное удаление инвентаря при удалении магазина
```sql
-- Функция для каскадного удаления записей инвентаря при удалении магазина
CREATE OR REPLACE FUNCTION kyzlakov_2261.delete_inventory_cascade_shop()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
    RAISE NOTICE 'Каскадное удаление: удаляем инвентарь магазина % (%)', OLD.shop_number, OLD.shop_name;
    DELETE FROM kyzlakov_2261.inventory WHERE shop_number = OLD.shop_number;
    RETURN OLD;
END;
$$;

-- Триггер каскадного удаления
CREATE OR REPLACE TRIGGER trigger_delete_inventory_on_shop_delete
    BEFORE DELETE ON kyzlakov_2261.shop
    FOR EACH ROW
    EXECUTE FUNCTION kyzlakov_2261.delete_inventory_cascade_shop();
```

### 2. Каскадное удаление инвентаря при удалении товара
```sql
-- Функция для каскадного удаления записей инвентаря при удалении товара
CREATE OR REPLACE FUNCTION kyzlakov_2261.delete_inventory_cascade_product()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
    RAISE NOTICE 'Каскадное удаление: удаляем инвентарь товара % (%)', OLD.product_id, OLD.product_name;
    DELETE FROM kyzlakov_2261.inventory WHERE product_id = OLD.product_id;
    RETURN OLD;
END;
$$;

-- Триггер каскадного удаления
CREATE OR REPLACE TRIGGER trigger_delete_inventory_on_product_delete
    BEFORE DELETE ON kyzlakov_2261.product
    FOR EACH ROW
    EXECUTE FUNCTION kyzlakov_2261.delete_inventory_cascade_product();
```


## Система аудита изменений
###1. Создание таблиц аудита
```sql
-- Таблица аудита для магазинов
CREATE TABLE IF NOT EXISTS kyzlakov_2261.shop_audit (
    audit_id SERIAL PRIMARY KEY,
    operation CHAR(1) NOT NULL,  -- I=INSERT, U=UPDATE, D=DELETE
    changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    changed_by TEXT DEFAULT CURRENT_USER,
    
    shop_number INTEGER,
    shop_name VARCHAR(100),
    address VARCHAR(200),
    floor_space NUMERIC(8,2),
    
    old_shop_name VARCHAR(100),
    old_address VARCHAR(200),
    old_floor_space NUMERIC(8,2)
);

-- Таблица аудита для товаров
CREATE TABLE IF NOT EXISTS kyzlakov_2261.product_audit (
    audit_id SERIAL PRIMARY KEY,
    operation CHAR(1) NOT NULL,
    changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    changed_by TEXT DEFAULT CURRENT_USER,
    
    product_id INTEGER,
    product_name VARCHAR(150),
    variety VARCHAR(100),
    
    old_product_name VARCHAR(150),
    old_variety VARCHAR(100)
);

-- Таблица аудита для инвентаря
CREATE TABLE IF NOT EXISTS kyzlakov_2261.inventory_audit (
    audit_id SERIAL PRIMARY KEY,
    operation CHAR(1) NOT NULL,
    changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    changed_by TEXT DEFAULT CURRENT_USER,
    
    inventory_id INTEGER,
    shop_number INTEGER,
    product_id INTEGER,
    unit_of_measure VARCHAR(50),
    unit_price NUMERIC(10,2),
    quantity NUMERIC(10,2),
    
    old_unit_price NUMERIC(10,2),
    old_quantity NUMERIC(10,2)
);
```

### 2. Триггеры аудита для таблицы shop
```sql
-- Функция аудита для таблицы shop
CREATE OR REPLACE FUNCTION kyzlakov_2261.shop_audit_function()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
    IF (TG_OP = 'INSERT') THEN
        INSERT INTO kyzlakov_2261.shop_audit(operation, shop_number, shop_name, address, floor_space)
        VALUES ('I', NEW.shop_number, NEW.shop_name, NEW.address, NEW.floor_space);
        RETURN NEW;
    ELSIF (TG_OP = 'UPDATE') THEN
        INSERT INTO kyzlakov_2261.shop_audit(operation, shop_number, shop_name, address, floor_space,
                                            old_shop_name, old_address, old_floor_space)
        VALUES ('U', NEW.shop_number, NEW.shop_name, NEW.address, NEW.floor_space,
                OLD.shop_name, OLD.address, OLD.floor_space);
        RETURN NEW;
    ELSIF (TG_OP = 'DELETE') THEN
        INSERT INTO kyzlakov_2261.shop_audit(operation, shop_number, shop_name, address, floor_space)
        VALUES ('D', OLD.shop_number, OLD.shop_name, OLD.address, OLD.floor_space);
        RETURN OLD;
    END IF;
    RETURN NULL;
END;
$$;

-- Триггер аудита
CREATE OR REPLACE TRIGGER shop_audit_trigger
    AFTER INSERT OR UPDATE OR DELETE ON kyzlakov_2261.shop
    FOR EACH ROW
    EXECUTE FUNCTION kyzlakov_2261.shop_audit_function();
```


### 3. Триггеры аудита для таблицы product
```sql
-- Функция аудита для таблицы product
CREATE OR REPLACE FUNCTION kyzlakov_2261.product_audit_function()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
    IF (TG_OP = 'INSERT') THEN
        INSERT INTO kyzlakov_2261.product_audit(operation, product_id, product_name, variety)
        VALUES ('I', NEW.product_id, NEW.product_name, NEW.variety);
        RETURN NEW;
    ELSIF (TG_OP = 'UPDATE') THEN
        INSERT INTO kyzlakov_2261.product_audit(operation, product_id, product_name, variety,
                                               old_product_name, old_variety)
        VALUES ('U', NEW.product_id, NEW.product_name, NEW.variety,
                OLD.product_name, OLD.variety);
        RETURN NEW;
    ELSIF (TG_OP = 'DELETE') THEN
        INSERT INTO kyzlakov_2261.product_audit(operation, product_id, product_name, variety)
        VALUES ('D', OLD.product_id, OLD.product_name, OLD.variety);
        RETURN OLD;
    END IF;
    RETURN NULL;
END;
$$;

-- Триггер аудита
CREATE OR REPLACE TRIGGER product_audit_trigger
    AFTER INSERT OR UPDATE OR DELETE ON kyzlakov_2261.product
    FOR EACH ROW
    EXECUTE FUNCTION kyzlakov_2261.product_audit_function();
```

### 4. Триггеры аудита для таблицы inventory
```sql
-- Функция аудита для таблицы inventory
CREATE OR REPLACE FUNCTION kyzlakov_2261.inventory_audit_function()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
    IF (TG_OP = 'INSERT') THEN
        INSERT INTO kyzlakov_2261.inventory_audit(operation, inventory_id, shop_number, product_id, 
                                                 unit_of_measure, unit_price, quantity)
        VALUES ('I', NEW.inventory_id, NEW.shop_number, NEW.product_id, 
                NEW.unit_of_measure, NEW.unit_price, NEW.quantity);
        RETURN NEW;
    ELSIF (TG_OP = 'UPDATE') THEN
        INSERT INTO kyzlakov_2261.inventory_audit(operation, inventory_id, shop_number, product_id,
                                                 unit_of_measure, unit_price, quantity,
                                                 old_unit_price, old_quantity)
        VALUES ('U', NEW.inventory_id, NEW.shop_number, NEW.product_id,
                NEW.unit_of_measure, NEW.unit_price, NEW.quantity,
                OLD.unit_price, OLD.quantity);
        RETURN NEW;
    ELSIF (TG_OP = 'DELETE') THEN
        INSERT INTO kyzlakov_2261.inventory_audit(operation, inventory_id, shop_number, product_id,
                                                 unit_of_measure, unit_price, quantity)
        VALUES ('D', OLD.inventory_id, OLD.shop_number, OLD.product_id,
                OLD.unit_of_measure, OLD.unit_price, OLD.quantity);
        RETURN OLD;
    END IF;
    RETURN NULL;
END;
$$;

-- Триггер аудита
CREATE OR REPLACE TRIGGER inventory_audit_trigger
    AFTER INSERT OR UPDATE OR DELETE ON kyzlakov_2261.inventory
    FOR EACH ROW
    EXECUTE FUNCTION kyzlakov_2261.inventory_audit_function();
```
## Триггеры бизнес-логики
### 1. Проверка неотрицательного количества товара
```sql
-- Функция проверки неотрицательного количества
CREATE OR REPLACE FUNCTION kyzlakov_2261.check_quantity_non_negative()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
    IF NEW.quantity < 0 THEN
        RAISE EXCEPTION 'Количество товара не может быть отрицательным. Попытка установить: %', NEW.quantity;
    END IF;
    RETURN NEW;
END;
$$;

-- Триггер проверки
CREATE OR REPLACE TRIGGER check_quantity_trigger
    BEFORE INSERT OR UPDATE ON kyzlakov_2261.inventory
    FOR EACH ROW
    EXECUTE FUNCTION kyzlakov_2261.check_quantity_non_negative();
```

## 2. Проверка положительной цены товара

```sql
-- Функция проверки положительной цены
CREATE OR REPLACE FUNCTION kyzlakov_2261.check_price_positive()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
    IF NEW.unit_price <= 0 THEN
        RAISE EXCEPTION 'Цена товара должна быть положительной. Попытка установить: %', NEW.unit_price;
    END IF;
    RETURN NEW;
END;
$$;

-- Триггер проверки
CREATE OR REPLACE TRIGGER check_price_trigger
    BEFORE INSERT OR UPDATE ON kyzlakov_2261.inventory
    FOR EACH ROW
    EXECUTE FUNCTION kyzlakov_2261.check_price_positive();
```

## Проверка работы триггеров
### Тестирование триггеров аудита
```sql
-- Тест 1: Вставка нового магазина
INSERT INTO kyzlakov_2261.shop (shop_name, address, floor_space)
VALUES ('Тестовый магазин', 'г. Тест, ул. Тестовая, д. 1', 150.50);

-- Тест 2: Обновление магазина
UPDATE kyzlakov_2261.shop 
SET floor_space = 200.75, address = 'г. Тест, ул. Новая, д. 2'
WHERE shop_name = 'Тестовый магазин';

-- Тест 3: Вставка нового товара
INSERT INTO kyzlakov_2261.product (product_name, variety)
VALUES ('Тестовый товар', 'Премиум');

-- Тест 4: Обновление товара
UPDATE kyzlakov_2261.product 
SET variety = 'Стандарт'
WHERE product_name = 'Тестовый товар';

-- Тест 5: Вставка записи инвентаря
INSERT INTO kyzlakov_2261.inventory (shop_number, product_id, unit_of_measure, unit_price, quantity)
SELECT 
    (SELECT shop_number FROM kyzlakov_2261.shop WHERE shop_name = 'Тестовый магазин'),
    (SELECT product_id FROM kyzlakov_2261.product WHERE product_name = 'Тестовый товар'),
    'шт', 100.50, 25;
```
![11](docs/images/11.png)
![22](docs/images/22.png)
![33](docs/images/33.png)
