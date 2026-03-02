# PHP-1 Write-up

## 문제 설명
php로 작성된 Back Office 서비스이다.  
LFI(Local File Inclusion) 취약점을 이용해 플래그를 획득해야 한다.  

플래그 위치:  
`/var/www/uploads/flag.php`

## 페이지 구조 분석

접속 URL:  
`?page=list`

<img width="552" height="352" src="https://github.com/user-attachments/assets/4c48be87-1c94-4927-a92e-fa7b17d3945c">

- page라는 GET 파라미터가 존재한다.
- list를 view로 변경하면 정상 이동이 가능하다.
- 이를 통해 page 값에 따라 동적으로 페이지를 include하는 구조임을 알 수 있다.
- 내부적으로 `.php`가 자동으로 붙는 것으로 추정된다.

예상 코드:

```php
include($_GET['page'] . ".php");
```

## flag.php 직접 접근 시도

시도 URL:  
`?page=view&file=../uploads/flag.php`

<img width="711" height="280" src="https://github.com/user-attachments/assets/c824ac18-9fd6-48c8-8d50-af3945bcabf6">

결과: **Permission denied**  
→ 직접 접근은 차단되어 있음

## 경로 조작 시도

단순 디렉토리 트래버설을 시도했으나 반응 없음.

<img width="745" height="299" src="https://github.com/user-attachments/assets/0942fb71-e9c9-48ba-8ece-07d17f2e27b2">

→ 단순 경로 조작만으로는 우회 불가능

## PHP Wrapper 우회

`.php` 자동 확장을 우회하기 위해 php://filter 사용

시도: 
`?page=php://filter/convert.base64-encode/resource=../uploads/flag`

<img width="1189" height="90" src="https://github.com/user-attachments/assets/1c772c2d-cc0c-417e-b10b-e7161c578522">

Base64 인코딩된 문자열이 화면에 출력됨

## 디코딩

출력된 문자열을 디코딩하여 flag 값을 확인

<img width="494" height="177" src="https://github.com/user-attachments/assets/22eb692d-1467-443b-81e9-494026ac84ca">

## 결론

- page GET 파라미터를 통해 파일을 include 하는 구조
- `.php` 자동 확장으로 직접 접근은 불가능
- php://filter wrapper를 이용해 파일 내용을 Base64로 인코딩하여 출력
- 디코딩을 통해 플래그 획득 성공

## 핵심 요약

PHP include 구조에서 발생한 LFI 취약점을 이용해  
php://filter wrapper로 flag.php 내용을 Base64 인코딩 후 디코딩하여 플래그를 획득하였다.

'DH{bb9db1f303cacf0f3c91e0abca1221ff}'







