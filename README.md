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

**Представление 2:** Магазины по адресам

```sql
CREATE OR REPLACE VIEW kyzlakov_2261.shops_by_address AS
SELECT shop_number, shop_name, address, floor_space
FROM kyzlakov_2261.shop
ORDER BY shop_name;
```

**Использование**
```sql
SELECT shop_number, shop_name, address
FROM kyzlakov_2261.shops_by_address
WHERE floor_space > 200
ORDER BY shop_name;
```

**Результат использования:**
![upr2](docs/images/upr2.png)
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
**Процедура 2:** Запись пациента на прием  


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

