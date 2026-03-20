# Rev-Basic-3 write-up

# 문제
이 문제는 사용자에게 문자열 입력을 받아 정해진 방법으로 입력값을 검증하여 correct 또는 wrong을 출력하는 프로그램이 주어집니다.
해당 바이너리를 분석하여 correct를 출력하는 입력값을 찾으세요!

# 풀이
Search String 해서 Wrong나 Correct 찾아 메인으로 들어간다.

<img width="850" height="151" alt="image" src="https://github.com/user-attachments/assets/10407440-7f55-4dc1-ab59-8049309dc2fd" />

Correct 문자열의 XREF를 따라가서 해당 문자열을 출력하는 함수를 확인했다.

<img width="534" height="263" alt="image" src="https://github.com/user-attachments/assets/bbde7f92-757a-41c8-a6a6-c8396a51b87f" />

메인문을 확인해 보면 0 이면 Wrong 아니면 Correct를 출력하는 것을 확인할 수 있다.
또한 uVar1는 반환값이 FUN_140001000의 결과이다.

FUN_140001000 따라가 들어가면 입력값에 대한  연산 방식이 나와있다.
   
<img width="769" height="107" alt="image" src="https://github.com/user-attachments/assets/acc3fdb0-0516-4493-8b35-88d6b595ccca" />

DAT[i] = (input[i] ^ i) + (i*2) 
또한 DAT 정보는 DAT_140003000에 저장되어 있는걸 확인할 수 있다

<img width="289" height="554" alt="image" src="https://github.com/user-attachments/assets/ecd69db8-3ff4-43d6-98c3-4d64d09bf6ce" />

파이썬으로 역연산을 하면 플래그를 획득할 수 있다.
DAT[i] = (input[i] - (i*2)) ^ i

<img width="881" height="132" alt="image" src="https://github.com/user-attachments/assets/e433e3a5-9422-4c55-958c-74ef62f79fce" />

# 결론
입력값 각 바이트에 대해 간단한 연산을 수행한 결과를 테이블 값과 비교하는 방식이다.
따라서 검증식을 찾고 이를 역연산하면 플래그를 획득할 수 있다.
