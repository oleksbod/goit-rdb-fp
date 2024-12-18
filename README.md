# goit-rdb-hw-07

1. Завантажте дані:
- Створіть схему pandemic у базі даних за допомогою SQL-команди.
- Оберіть її як схему за замовчуванням за допомогою SQL-команди.
- Імпортуйте дані за допомогою Import wizard так, як ви вже робили це у темі 3.
- Продивіться дані, щоб бути у контексті.

```
CREATE DATABASE pandemic;

USE pandemic;

select *
from infectious_cases
```

2. Нормалізуйте таблицю infectious_cases до 3ї нормальної форми. 
Збережіть у цій же схемі дві таблиці з нормалізованими даними.

```
/*3NF*/
USE pandemic;

CREATE TABLE locations (
    id INT PRIMARY KEY AUTO_INCREMENT,
    entity VARCHAR(255) NOT NULL,
    code VARCHAR(10) NOT NULL
);

CREATE TABLE diseases (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL
);

CREATE TABLE infectious_cases_normalized (
    id INT PRIMARY KEY AUTO_INCREMENT,
    location_id INT,
    year INT NOT NULL,
    disease_id INT,
    number_of_cases DECIMAL(20,9) NOT NULL,
    FOREIGN KEY (location_id) REFERENCES locations(id),
    FOREIGN KEY (disease_id) REFERENCES diseases(id)
);

/*insert*/
INSERT INTO locations (entity, code)
SELECT DISTINCT Entity, Code
FROM infectious_cases;

INSERT INTO diseases (name) VALUES
('yaws'),
('polio'),
('guinea_worm'),
('rabies'),
('malaria'),
('hiv'),
('tuberculosis'),
('smallpox'),
('cholera');

-- yaws --
INSERT INTO infectious_cases_normalized (location_id, Year, disease_id, number_of_cases)
SELECT 
    l.id,
    ic.Year,
    d.id,
    CAST(ic.Number_yaws AS DECIMAL(20,9)) AS number_of_cases
FROM 
    infectious_cases ic
JOIN locations l ON ic.Entity = l.entity AND ic.Code = l.code
JOIN diseases d ON d.name = 'yaws'
WHERE ic.Number_yaws IS NOT NULL AND ic.Number_yaws != '';

-- polio --
INSERT INTO infectious_cases_normalized (location_id, Year, disease_id, number_of_cases)
SELECT 
    l.id,
    ic.Year,
    d.id,
    CAST(ic.polio_cases AS DECIMAL(20,9)) AS number_of_cases
FROM 
    infectious_cases ic
JOIN locations l ON ic.Entity = l.entity AND ic.Code = l.code
JOIN diseases d ON d.name = 'polio'
WHERE ic.polio_cases IS NOT NULL AND ic.polio_cases != '';

-- guinea_worm --
INSERT INTO infectious_cases_normalized (location_id, Year, disease_id, number_of_cases)
SELECT 
    l.id,
    ic.Year,
    d.id,
    CAST(ic.cases_guinea_worm AS DECIMAL(20,9)) AS number_of_cases
FROM 
    infectious_cases ic
JOIN locations l ON ic.Entity = l.entity AND ic.Code = l.code
JOIN diseases d ON d.name = 'guinea_worm'
WHERE ic.cases_guinea_worm IS NOT NULL AND ic.cases_guinea_worm != '';

-- rabies 
INSERT INTO infectious_cases_normalized (location_id, Year, disease_id, number_of_cases)
SELECT 
    l.id,
    ic.Year,
    d.id,
    CAST(ic.Number_rabies AS DECIMAL(20,9)) AS number_of_cases
FROM 
    infectious_cases ic
JOIN locations l ON ic.Entity = l.entity AND ic.Code = l.code
JOIN diseases d ON d.name = 'rabies'
WHERE ic.Number_rabies IS NOT NULL AND ic.Number_rabies != '';

-- malaria --
INSERT INTO infectious_cases_normalized (location_id, Year, disease_id, number_of_cases)
SELECT 
    l.id,
    ic.Year,
    d.id,
    CAST(ic.Number_malaria AS DECIMAL(20,9)) AS number_of_cases
FROM 
    infectious_cases ic
JOIN locations l ON ic.Entity = l.entity AND ic.Code = l.code
JOIN diseases d ON d.name = 'malaria'
WHERE ic.Number_malaria IS NOT NULL AND ic.Number_malaria != '';

-- hiv --
INSERT INTO infectious_cases_normalized (location_id, Year, disease_id, number_of_cases)
SELECT 
    l.id,
    ic.Year,
    d.id,
    CAST(ic.Number_hiv AS DECIMAL(20,9)) AS number_of_cases
FROM 
    infectious_cases ic
JOIN locations l ON ic.Entity = l.entity AND ic.Code = l.code
JOIN diseases d ON d.name = 'hiv'
WHERE ic.Number_hiv IS NOT NULL AND ic.Number_hiv != '';

-- tuberculosis --
INSERT INTO infectious_cases_normalized (location_id, Year, disease_id, number_of_cases)
SELECT 
    l.id,
    ic.Year,
    d.id,
    CAST(ic.Number_tuberculosis AS DECIMAL(20,9)) AS number_of_cases
FROM 
    infectious_cases ic
JOIN locations l ON ic.Entity = l.entity AND ic.Code = l.code
JOIN diseases d ON d.name = 'tuberculosis'
WHERE ic.Number_tuberculosis IS NOT NULL AND ic.Number_tuberculosis != '';

-- smallpox --
INSERT INTO infectious_cases_normalized (location_id, Year, disease_id, number_of_cases)
SELECT 
    l.id,
    ic.Year,
    d.id,
    CAST(ic.Number_smallpox AS DECIMAL(20,9)) AS number_of_cases
FROM 
    infectious_cases ic
JOIN locations l ON ic.Entity = l.entity AND ic.Code = l.code
JOIN diseases d ON d.name = 'smallpox'
WHERE ic.Number_smallpox IS NOT NULL AND ic.Number_smallpox != '';

-- cholera --
INSERT INTO infectious_cases_normalized (location_id, Year, disease_id, number_of_cases)
SELECT 
    l.id,
    ic.Year,
    d.id,
    CAST(ic.Number_cholera_cases AS DECIMAL(20,9)) AS number_of_cases
FROM 
    infectious_cases ic
JOIN locations l ON ic.Entity = l.entity AND ic.Code = l.code
JOIN diseases d ON d.name = 'cholera'
WHERE ic.Number_cholera_cases IS NOT NULL AND ic.Number_cholera_cases != '';

```

3. Проаналізуйте дані:
- Для кожної унікальної комбінації Entity та Code або їх id порахуйте середнє, 
мінімальне, максимальне значення та суму для атрибута Number_rabies.

💡 Врахуйте, що атрибут Number_rabies може містити порожні значення ‘’ — вам попередньо необхідно їх відфільтрувати.

- Результат відсортуйте за порахованим середнім значенням у порядку спадання.
- Оберіть тільки 10 рядків для виведення на екран.
```
USE pandemic;

SELECT 
    Entity,
    Code,
    AVG(CAST(Number_rabies AS UNSIGNED)) AS avg_rabies,
    MIN(CAST(Number_rabies AS UNSIGNED)) AS min_rabies,
    MAX(CAST(Number_rabies AS UNSIGNED)) AS max_rabies,
    SUM(CAST(Number_rabies AS UNSIGNED)) AS sum_rabies
FROM 
    infectious_cases
WHERE 
    Number_rabies != ''
GROUP BY 
    Entity, Code
ORDER BY 
    avg_rabies DESC
LIMIT 10;
```

4. Побудуйте колонку різниці в роках.
Для оригінальної або нормованої таблиці для колонки Year побудуйте з використанням вбудованих SQL-функцій:

	- атрибут, що створює дату першого січня відповідного року,
💡 Наприклад, якщо атрибут містить значення ’1996’, то значення нового атрибута має бути ‘1996-01-01’.
	- атрибут, що дорівнює поточній даті,
	- атрибут, що дорівнює різниці в роках двох вищезгаданих колонок.
```
USE pandemic;

SELECT 
    Year,
    CONCAT(Year, '-01-01') AS year_start_date,
    CURDATE() AS cur_date,
    TIMESTAMPDIFF(YEAR, CONCAT(Year, '-01-01'), CURDATE()) AS year_difference
FROM 
    infectious_cases;
```

5. Побудуйте власну функцію.
- Створіть і використайте функцію, що будує такий же атрибут, як і в попередньому завданні: 
функція має приймати на вхід значення року, а повертати різницю в роках між поточною датою та датою, 
створеною з атрибута року (1996 рік → ‘1996-01-01’).
```
USE pandemic;

DELIMITER $$

CREATE FUNCTION year_difference(year INT) 
RETURNS INT
DETERMINISTIC
BEGIN
    DECLARE start_date DATE;
    SET start_date = CONCAT(year, '-01-01');
    RETURN TIMESTAMPDIFF(YEAR, start_date, CURDATE());
END $$

DELIMITER ;

USE pandemic;
SELECT Year, year_difference(Year) AS year_difference
FROM infectious_cases;
```