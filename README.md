Лабораторные работы по БД

# Постановка задачи (вариант 5)

## Содержание

1. Постановка задачи
2. Концептуальная модель (ER)
3. Логическая модель
4. Физическая модель
5. SQL DDL
6. Примеры запросов
7. Проверка нормальных форм

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
#Лабораторная работа 1

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

### Индексы для оптимизации

```sql
-- Для быстрого поиска по адресу магазина
CREATE INDEX idx_shop_address ON Shop(address);

-- Для быстрого поиска товаров по названию
CREATE INDEX idx_product_name ON Product(product_name);

-- Для быстрого поиска товаров в магазине
CREATE INDEX idx_inventory_shop ON Inventory(shop_number);

-- Для быстрого поиска магазинов с товаром
CREATE INDEX idx_inventory_product ON Inventory(product_id);

-- Комбинированный индекс для частых запросов
CREATE INDEX idx_inventory_shop_product ON Inventory(shop_number, product_id);
```

---

## SQL DDL

### Полный скрипт создания базы данных

```sql
-- Таблица магазинов
CREATE TABLE Shop (
    shop_number INTEGER PRIMARY KEY,
    shop_name VARCHAR(150) NOT NULL UNIQUE,
    address VARCHAR(255) NOT NULL,
    area NUMERIC(10, 2) NOT NULL CHECK (area > 0)
);

-- Таблица товаров
CREATE TABLE Product (
    product_id SERIAL PRIMARY KEY,
    product_name VARCHAR(150) NOT NULL,
    variety VARCHAR(100),
    UNIQUE(product_name, variety)
);

-- Таблица инвентаря (товары в магазинах)
CREATE TABLE Inventory (
    inventory_id SERIAL PRIMARY KEY,
    shop_number INTEGER NOT NULL,
    product_id INTEGER NOT NULL,
    unit_of_measure VARCHAR(50) NOT NULL,
    unit_price NUMERIC(10, 2) NOT NULL CHECK (unit_price > 0),
    quantity NUMERIC(10, 2) NOT NULL CHECK (quantity >= 0),
    FOREIGN KEY (shop_number) REFERENCES Shop(shop_number) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES Product(product_id) ON DELETE CASCADE,
    UNIQUE(shop_number, product_id)
);

-- Создание индексов для оптимизации запросов
CREATE INDEX idx_shop_address ON Shop(address);
CREATE INDEX idx_product_name ON Product(product_name);
CREATE INDEX idx_inventory_shop ON Inventory(shop_number);
CREATE INDEX idx_inventory_product ON Inventory(product_id);
CREATE INDEX idx_inventory_shop_product ON Inventory(shop_number, product_id);
```

---

## Примеры запросов

### 1. Товары в заданных магазинах со стоимостью

```sql
-- Для магазинов с номерами 1, 2, 3
SELECT
    s.shop_number AS "Номер магазина",
    s.shop_name AS "Наименование магазина",
    p.product_name AS "Товар",
    p.variety AS "Сорт",
    i.unit_of_measure AS "Единица измерения",
    i.unit_price AS "Цена за единицу",
    i.quantity AS "Количество",
    (i.unit_price * i.quantity) AS "Стоимость"
FROM Inventory i
JOIN Shop s ON i.shop_number = s.shop_number
JOIN Product p ON i.product_id = p.product_id
WHERE s.shop_number IN (1, 2, 3)
ORDER BY s.shop_number, p.product_name;
```

### 2. Информация о магазинах с средней площадью

```sql
SELECT
    s.shop_number AS "Номер магазина",
    s.shop_name AS "Наименование магазина",
    s.address AS "Адрес",
    s.area AS "Площадь (м²)",
    ROUND(AVG(s.area) OVER (), 2) AS "Средняя площадь всех магазинов"
FROM Shop s
ORDER BY s.address;
```

### 3. Товары в конкретном магазине с итоговой стоимостью

```sql
-- Для магазина с номером 1
SELECT
    p.product_name AS "Товар",
    p.variety AS "Сорт",
    i.unit_of_measure AS "Единица",
    i.unit_price AS "Цена",
    i.quantity AS "Кол-во",
    (i.unit_price * i.quantity) AS "Стоимость"
FROM Inventory i
JOIN Product p ON i.product_id = p.product_id
WHERE i.shop_number = 1
ORDER BY p.product_name;
```

### 4. Общая стоимость товаров в каждом магазине

```sql
SELECT
    s.shop_number AS "Номер магазина",
    s.shop_name AS "Наименование магазина",
    ROUND(SUM(i.unit_price * i.quantity), 2) AS "Общая стоимость товаров"
FROM Inventory i
JOIN Shop s ON i.shop_number = s.shop_number
GROUP BY s.shop_number, s.shop_name
ORDER BY s.shop_name;
```

### 5. Магазины, где есть конкретный товар

```sql
-- Поиск товара "Молоко"
SELECT
    s.shop_number AS "Номер магазина",
    s.shop_name AS "Магазин",
    s.address AS "Адрес",
    i.unit_price AS "Цена",
    i.quantity AS "Количество"
FROM Inventory i
JOIN Shop s ON i.shop_number = s.shop_number
JOIN Product p ON i.product_id = p.product_id
WHERE p.product_name = 'Молоко'
ORDER BY i.unit_price;
```

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

*Проект разработан для PostgreSQL 12+*
*Последнее обновление: 17 ноября 2025*
