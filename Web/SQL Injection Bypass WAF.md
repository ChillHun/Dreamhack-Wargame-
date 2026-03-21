# SQL injection bypass WAF-Write UP


# 문제
Exercise: SQL Injection Bypass WAF에서 실습하는 문제입니다.

# 풀이
`init.sql`

<img width="598" height="330" alt="image" src="https://github.com/user-attachments/assets/ad0847b6-e70c-419c-9feb-3fce3b4cc245" />

코드를 보면 컬럼이 3개이고, 플래그는 admin upw에 있는걸 알 수 있다.

`app.py`

<img width="778" height="226" alt="image" src="https://github.com/user-attachments/assets/5bebcb8b-79ae-49cd-8d1e-8413eef83e4f" />

파이썬 파일을 보면 몇 개의 키워드는 필터링 되어 있다

<img width="680" height="275" alt="image" src="https://github.com/user-attachments/assets/b709ebd1-cf77-424e-8618-9ae6c394e703" />

웹사이트에 들어가보니 uid를 입력 하는 곳이 있다.
admin을 입력하니 역시나 필터링이 된다.
ADMIN을 대문자로 입력하면 입력이 되는걸 볼 수 있다.
즉 대문자로 우회가 가능하다.
띄어쓰기는 TAB으로 우회가 가능하다.

<img width="711" height="259" alt="image" src="https://github.com/user-attachments/assets/0b3afbca-3390-4269-9591-3ef7bece9a16" />

웹페이지는 uid에서만 출력이되니 null,upw,null처럼 두번째에 upw를 넣어야 한다.
그래서 UNION을 이용해서 upw 값을 uid가 출력되는 컬럼 위치에 삽입하여 출력한다.
여기서 조심해야 할 점은 SELECT * 기준으로 컬럼 3개를 반환되므로 UNION에서도 3를 맞춰서 입력 해야한다.

사용 코드: 'UNION	SELECT	null,upw,null	FROM	user	WHERE	uid='ADMIN (띄어쓰기는 TAB으로 우회)

<img width="1666" height="281" alt="image" src="https://github.com/user-attachments/assets/3ae46ef0-32ee-4083-a6e1-64092be61b95" />

# 결론
이 문제는 필터링을 우회한 뒤, 
UNION을 이용하여 출력되는 컬럼 위치에플래그가 있는 컬럼 값을 삽입함으로써 플래그를 획득하는 문제이다.







