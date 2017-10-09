# SQL

#### SQL로 할 수 있는 일??

- 데이터 읽기
- 데이터 삽입하기
- 데이터 고치기
- 데이터 삭제하기
- 새로운 데이터베이스 만들기
- 데이터베이스에 테이블 만들기 등등..



#### 관계형 데이터베이스?

Table에 데이터를 저장하고, 관련데이터를 column과 row를 통해 관리한다.



#### SQL cheat-sheet

- **SELECT** - 데이터 추출

  - **SELECT DISTINCT**  - 중복되지 않은 값들만 추출하고 싶을때
  - **SELECT TOP** - TOP 3 와 같은 식으로 숫자 혹은 TOP 30 PERCENT 등으로 데이터 추출의 갯수 조절
  - **SELECT MIN/MAX** - 하나의 값만 추출 가능.. AS, WHERE와 같이 사용
  - **SELECT COUNT/AVG/SUM(column_name)** 

- **WHERE** - 조건 붙일때.. if와 같은 기능, 마찬가지로 **AND**, **OR**, **NOT** 등과 조합 가능

  - =, <>, >, <, <=, >=
  - BETWEEN - condition1 AND condition2의 식으로 사용
  - LIKE - 문자열 패턴 찾을때 사용, %는 파이썬정규표현식의 *, _는 .와 비슷. [abc]의 기능은 같음
  - IN - 파이썬에서 사용하는 in과 비슷
  - **column_name IS NULL** 과 함께 사용하면 해당 column에 자료가 없을 경우!와 같은 조건 만들기 가능

- **ORDER BY** - 뽑은 내용들을 정렬해서 표시

- **UPDATE** - 데이터 변경

- **DELETE** - 데이터 삭제

- **INSERT INTO** - 데이터베이스에 데이터 넣기

- **CREATE DATABASE** - 데이터베이스 만들기

- **ALTER DATABASE** - 데이터베이스 수정

- **CREATE TABLE** - 테이블 만들기

- **ALTER TABLE** - 테이블 수정

- **DROP TABLE** - 테이블 삭제    (**DELETE가 아니다!!**)

- **CREATE INDEX** - creates an index (search key)

- **DROP INDEX** - deletes an index  

  ​

#### 기본적인 데이터 추출 방법 

```sql
SELECT column1, column2, ...
FROM table_name; << ;은 마지막에만 한번 달아준다

ex)
SELECT CustomerName, ContactName
FROM Customers

SELECT * FROM Customers <<웬만하면 SELECT와 FROM은 한 줄에 오는듯
WHERE Country='Germany' AND NOT City='Berlin' <<조건으로는 column의 이름 등이 온다.
ORDER BY Country (ASC or DESC); <<ORDER의 조건으로도 역시 column명 사용
```

#### 데이터 삽입하기

```sql
INSERT INTO table_name (column1, column2, column3, ...)
VALUES (value1, value2, value3, ...); <<넣고 싶은것만 넣으려면 순차적으로 column명과 값 넣어줘야 한다.

INSERT INTO table_name
VALUES (value1, value2, value3, ...); <<이 경우에는 모든 값들을 넣어줘야.. 
```



**주의!  __수정, 삭제__할때**  조건 안걸어주거나 잘못 걸어주면 데이터 뒤집어짐.. 

#### 데이터 수정하기

```sql
UPDATE table_name << 이전과는 달리 테이블 명을 먼저 써주고
SET column1 = value1, column2 = value2, ... <<SET을 이용해서 칼럼의 값들을 수정
WHERE condition;
```

#### 데이터 삭제하기

```sql
DELETE FROM table_name
WHERE condition;
```

#### In 사용하기

```sql
SELECT column_name(s)
FROM table_name
WHERE column_name IN (value1, value2, ...);

ex)
SELECT * FROM Customers
WHERE Country IN ('Germany', 'France', 'UK');

SELECT column_name(s)
FROM table_name
WHERE column_name IN (SELECT STATEMENT);

ex)
SELECT * FROM Customers
WHERE Country IN (SELECT Country FROM Suppliers);
```

#### Alias

```sql
#column이름 변경하기
SELECT CustomerName AS Customer, ContactName AS [Contact Person], Address + ', ' + PostalCode + ' ' + City + ', ' + Country AS Address
FROM Customers;

#table 이름 변경하기
SELECT o.OrderID, o.OrderDate, c.CustomerName
FROM Customers AS c, Orders AS o
WHERE c.CustomerName="Around the Horn" AND c.CustomerID=o.CustomerID;
```

#### JOIN 이해하기

기본적으로 두 개의 다른 테이블을 사용하되, 둘 사이에 연관이 있는 column을 이용하여 추출하고 싶은 column을 재구성해서 뽑아낼 수 있게 도와주는 기능이다.

아래의 예제를 보자.

```sql
SELECT Customers.CustomerName, Orders.OrderID
FROM Customers
INNER JOIN Orders ON Customers.CustomerID = Orders.CustomerID
ORDER BY Customers.CustomerName;
```

JOIN 다음에 오는 Orders table은 table2의 역할을 하므로 기준은 Customers가 된다.

Customers의 records 수는 91개, Orders는 196개이다.

이 경우 Orders 안의 CustomerID는 전부 Customers의 CustomerID와 매칭되는 관계다.

(사실상 이 구조에서 집합관계를 보면 부분집합의 관계로 볼 수 있지 않나?)

그렇기 때문에 Orders 안의 196개 값들은 전부 매칭되는 값을 가지게 되고 따라서 결과값도

196개가 된다.

이 방법을 통해서 가능하게 된 점?

Orders 테이블의 CustomerID와 Customers의 CustomerID가 같은 값을 참조한다는 점을 이용해서

기존의 Orders 테이블에 없던 각 Order마다의 CustomerName을 붙일 수 있게 되었다.



만약 INNER JOIN이 아닌 LEFT JOIN 이었다면 Orders의 record 수 196개에, 주문한 적이 없는 Customers.. 즉 Orders의 CustomerID와는 매칭할 수 없는 몇 몇 CustomerName 값들이 추가로 들어오게 된다.(집합의 A-B 부분) 이들의 OrderId는 null로 표시된다

**JOIN 기능을 잘 활용하기 위해서는 우선 table간의 관계를 잘 보고 관계가 있는 두 개의 테이블집합이 어떤 포함관계를 가지고 있는지를 따져보아야 잘 활용이 가능할 것 같다. 직접 코드를 짜볼때는 두 영역의 겹치는 부분을 먼저 생각하고 그 다음 차집합 영역에 대해서 생각해야 할 것 같음..**

##### SELF JOIN

```sql
SELECT A.CustomerName AS CustomerName1, B.CustomerName AS CustomerName2, A.City
FROM Customers A, Customers B
WHERE A.CustomerID <> B.CustomerID
AND A.City = B.City 
ORDER BY A.City;

1. 한 컬럼을 나눈다. 위와 같이 A와 B로
2. Alias를 이용해서 출력물의 column을 보기좋게 바꾼다.
3. 조건문을 이용해서 새로 출력될 row를 조정한다.
```



#### UNION

두 개의 다른 테이블에 있는 값들을 하나의 column으로 합쳐서 뽑아내고 싶을때 사용한다. 두 개의 테이블에서 다른 column을 사용해서 추출해도 되지만 비슷한 type을 가졌는지 확인하자.

```sql
SELECT City, Country FROM Customers
WHERE Country='Germany' --조건 옵션
UNION ALL --ALL 포함하면 중복값들
SELECT City, Country FROM Suppliers
WHERE Country='Germany'
ORDER BY City;

다음과 같은 경우에는 UNION 안의 SELECT 구문에 주의하자..
SELECT 'Customer' As Type, ContactName, City, Country
FROM Customers
UNION
SELECT 'Supplier', ContactName, City, Country
FROM Suppliers;
```



#### GROUP BY, HAVING

COUNT, MAX, MIN, SUM, AVG와 같이 결과가 하나만 나오게 되는 함수를 GROUP BY column_name으로 처리해주면 해당 column에 해당하는 데이터를 얻을 수 있다. HAVING은 WHERE로 충족시키지 못하는 aggregate 함수관련 조건처리를 도와준다.

```sql
SELECT COUNT(CustomerID), Country
FROM Customers
GROUP BY Country
HAVING COUNT(CustomerID) > 5
```



#### EXISTS

WHERE EXISTS 다음에 괄호로 감싼 부분은 WHILE을 포함한 SELECT 구문이다. 괄호로 감싼 부분이 1개 이상의 데이터를 반환하면 참값이 되고 그 조건에 해당되는 records를 최종적으로 추출하게 된다.

아래의 예를 보면 

```sql
SELECT SupplierName
FROM Suppliers
WHERE EXISTS (SELECT ProductName FROM Products WHERE SupplierId = Suppliers.supplierId AND Price < 20);
```



#### ANY, ALL

이건 질문!!!!



#### SELECT INTO

그냥 SELECT와 똑같다. 그런데 추출해낸 데이터를 다른곳에 집어넣는 부분을 INTO로 처리해주면 된다.

```sql
SELECT * INTO CustomersGermany IN 'Backup.mdb' -- 만약 다른 db에 넣고싶다면 
FROM Customers
WHERE Country = 'Germany';
```

### INSERT INTO SELECT

SELECT INTO와 다른점은 column 이름을 새로 정의할 수 있는 점이 다른 것 같다.

```sql
INSERT INTO table2
SELECT * FROM table1
WHERE condition;

INSERT INTO table2 (column1, column2, column3, ...)
SELECT column1, column2, column3, ...
FROM table1
WHERE condition;
```

