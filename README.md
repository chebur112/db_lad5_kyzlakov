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
(docs/images/tables.png)
(docs/images/columns.png)
---
## Заполнение таблиц данными

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
(docs/images/output1.png)
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
(docs/images/output1.png)

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
(docs/images/output2.png)

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
(docs/images/output3.png)

---

## Представления (Views) для выходных документов

### Представление 1: Товары в магазинах со стоимостью

```sql
CREATE OR REPLACE VIEW products_in_shops_view AS
SELECT
    s.shop_number AS "Номер магазина",
    s.shop_name AS "Наименование магазина",
    p.product_name AS "Товар",
    p.variety AS "Сорт",
    i.unit_of_measure AS "Единица измерения",
    i.unit_price AS "Цена за единицу",
    i.quantity AS "Количество",
    ROUND(i.unit_price * i.quantity, 2) AS "Стоимость"
FROM Inventory i
JOIN Shop s ON i.shop_number = s.shop_number
JOIN Product p ON i.product_id = p.product_id
ORDER BY s.shop_number, p.product_name;
```

### Представление 2: Информация о магазинах со средней площадью

```sql
CREATE OR REPLACE VIEW shops_with_avg_area_view AS
SELECT
    s.shop_number AS "Номер магазина",
    s.shop_name AS "Наименование магазина",
    s.address AS "Адрес",
    s.area AS "Площадь (м²)",
    ROUND(AVG(s.area) OVER (), 2) AS "Средняя площадь магазинов"
FROM Shop s
ORDER BY s.address;
```

---

## Примеры тестовых данных

```sql
-- Вставка магазинов
INSERT INTO Shop (shop_number, shop_name, address, area) VALUES
(1, 'Магазин №1', 'ул. Ленина, 10', 250.50),
(2, 'Магазин №2', 'ул. Советская, 25', 180.75),
(3, 'Магазин №3', 'пр. Мира, 5', 320.00),
(4, 'Магазин №4', 'ул. Пушкина, 15', 200.25);

-- Вставка товаров
INSERT INTO Product (product_name, variety) VALUES
('Молоко', 'Коровье'),
('Молоко', 'Козье'),
('Хлеб', 'Пшеничный'),
('Хлеб', 'Ржаной'),
('Яйца', 'Куриные'),
('Масло', 'Сливочное');

-- Вставка инвентаря
INSERT INTO Inventory (shop_number, product_id, unit_of_measure, unit_price, quantity) VALUES
(1, 1, 'л', 80.00, 50),
(1, 3, 'шт', 45.00, 100),
(1, 5, 'дз', 120.00, 20),
(2, 1, 'л', 78.00, 60),
(2, 2, 'л', 150.00, 15),
(2, 4, 'шт', 50.00, 80),
(3, 3, 'шт', 42.00, 150),
(3, 6, 'кг', 250.00, 30),
(4, 1, 'л', 82.00, 45),
(4, 5, 'дз', 125.00, 25);
```

---

## Проверка нормальных форм

| Нормальная форма | Состояние | Комментарий |
|---|---|---|
| **1NF** | ✅ Выполнена | Все атрибуты атомарны, нет повторяющихся групп |
| **2NF** | ✅ Выполнена | Нет зависимостей неключевых атрибутов от части составного ключа |
| **3NF** | ✅ Выполнена | Нет транзитивных зависимостей между неключевыми атрибутами |
| **BCNF** | ✅ Выполнена | Все детерминанты являются потенциальными ключами |

### Подробная проверка

**Первая нормальная форма (1NF)**
- Таблица Shop: Все значения атомарны (shop_number - целое число, shop_name - строка, address - строка, area - число)
- Таблица Product: Все значения атомарны (product_id - число, product_name - строка, variety - строка)
- Таблица Inventory: Все значения атомарны (inventory_id - число, shop_number - число, product_id - число, unit_of_measure - строка, unit_price - число, quantity - число)

**Вторая нормальная форма (2NF)**
- В таблице Inventory составной ключ (shop_number, product_id) уникален
- Все неключевые атрибуты (unit_of_measure, unit_price, quantity) зависят от всего ключа, а не от его части

**Третья нормальная форма (3NF)**
- Нет транзитивных зависимостей
- shop_name зависит только от shop_number, а не от других атрибутов Shop
- product_name и variety зависят только от product_id

**Нормальная форма Бойса-Кодда (BCNF)**
- Единственным детерминантом является первичный ключ каждой таблицы
- Все функциональные зависимости имеют детерминант, являющийся потенциальным ключом

---

## Преимущества модели

1. **Гибкость**: Товар может продаваться в разных магазинах с разными ценами
2. **Масштабируемость**: Легко добавлять новые магазины, товары и записи инвентаря
3. **Целостность данных**: FOREIGN KEY constraints гарантируют консистентность
4. **Производительность**: Индексы оптимизируют частые запросы
5. **Избежание дублирования**: Нормализованная структура минимизирует дублирование данных
6. **Гибкость цен**: Один товар может иметь разные цены в разных магазинах

---

## Расширение модели (опционально)

Для более расширенной функциональности можно добавить:

```sql
-- Таблица категорий товаров
CREATE TABLE Category (
    category_id SERIAL PRIMARY KEY,
    category_name VARCHAR(100) NOT NULL UNIQUE
);

-- Модификация таблицы Product с категорией
ALTER TABLE Product ADD COLUMN category_id INTEGER REFERENCES Category(category_id);

-- Таблица истории цен
CREATE TABLE PriceHistory (
    price_history_id SERIAL PRIMARY KEY,
    inventory_id INTEGER NOT NULL REFERENCES Inventory(inventory_id),
    old_price NUMERIC(10, 2),
    new_price NUMERIC(10, 2),
    change_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---


