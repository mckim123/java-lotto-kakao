# java-lotto-kakao

## 기능 요구 사항

1. 로또 구입 금액을 입력하면 구입 금액에 해당하는 로또를 발급해야 한다.
2. 로또 1장의 가격은 1000원이다.
3. __로또를 한 장도 살 수 없거나, 거스름돈이 발생하는 금액에 대해서는 예외를 발생시킨다.__
4. __지난 주 당첨 번호와 보너스 볼을 입력받으며, 규칙에 어긋나는 경우 예외를 발생시킨다.__
5. __당첨 통계를 출력한다.__
6. 현재 로또 생성기는 자동 생성 기능만 제공한다. 사용자가 수동으로 추첨 번호를 입력할 수 있도록 해야 한다. 
7. 입력한 금액, 자동 생성 숫자, 수동 생성 번호를 입력하도록 해야 한다.
8. __구입 금액은 int 범위 내로 받고, 출력은 long으로 한다.__ 
   - 당첨금의 overflow 고려


---
## 설계

- Application

### View
- InputView
- OutputView
- OutputViewMessage

### Controller
- LotteryController : Player, LotteryVendingMachine, LotteryChecker 관리, view와 중계

### domain
- Player : 게임 플레이어; 보유 금액(budget), 총 당첨금(totalPrize), 로또 리스트, 결과 리스트 관리
- LotteryVendingMachine : 로또 판매
- LotteryChecker : 로또 판정; 지난 주 정답을 생성자로 입력 받음

#### domain.lotterynumber
- LotteryNumber : 로또 번호; 1~45의 값 포장, 인스턴스 미리 생성
- LotteryNumberCombination : 로또 번호 조합 클래스; List<LotteryNumber> 포장
- WinningNumbers : 당첨 번호; LotteryNumberCombination + bonus
- RandomLotteryNumberCombinationFactory : 로또 자동 생성

#### domain.rank
- LotteryRank : 등수 정보(상금, 산정 기준)를 가지는 등수의 Enum 클래스
- RankCriteria : 로또 등수 산정 기준
- IsBonusRequired : 참, 거짓, 상관 없음 세 가지 정보를 저장하기 위한 Enum 클래스

### constant
- LotteryConstant : 각종 상수 보관
- ExceptionMessage : 예외 메시지 보관

---

## 기능 목록

- InputView
  - [x] 정수 자료형의 입력을 받는다.
    - [x] 양 끝 공백은 제거한다.
  - [x] 로또 번호를 입력받는다.
  - [x] 당첨 번호를 입력받는다.
  - [x] 보너스 번호를 입력받는다.
- OutputView

### domain
- Player
  - [x] 유효한 금액을 입력받은지 검증한다.
    - [x] 양수인지 검증한다.
    - [x] 로또 개별 가격으로 나누어떨어지는지 검증한다.
  - [x] 수동으로 구매하는 개수가 유효한지 검증한다.
    - [x] 최소 0장이어야 한다.
    - [x] 전체 구매 장수를 넘길 수 없다.
  - [x] 총 당첨금을 계산한다.
  - [x] 수익률을 계산한다.
- LotteryVendingMachine
  - [x] 수동으로 구입할 로또 번호를 입력받아 로또를 생성한다.
  - [x] 자동으로 구입하는 개수에 해당하는 로또를 생성하여 전체 로또 리스트를 반환한다.
- LotteryChecker
  - [x] 한 개의 로또에 대해 등수를 판별한다.
  - [x] 로또 리스트에 대해 등수를 판별하여 등수별 개수 리스트를 반환한다.

#### domain.lotterynumber
- LotteryNumber : 로또 번호; 1~45의 값 포장, 인스턴스 미리 생성
  - [x] 같은 값에 대해서는 하나의 인스턴스를 생성한다.
  - [x] 값의 비교가 가능하다.
  - [x] 1 이상 45 이하의 정수만 가진다.
- LotteryNumberCombination : 로또 번호 조합 클래스; List<LotteryNumber> 포장
  - [x] 출력 시 오름차순으로 정렬한다.
  - [x] 생성시 로또 숫자 규칙을 검증한다.
    - [x] 각 숫자는 중복되지 않는다.
  - [x] 주어진 로또 번호를 포함하는 지 확인한다.
  - [x] 다른 LotteryNumberCombination과 겹치는 LotteryNumber의 개수를 반환한다.
- WinningNumbers : 당첨 번호; LotteryNumberCombination + bonus
  - [x] 당첨 번호 규칙을 검증한다.(보너스 번호는 기존 로또 번호와 중복되지 않는다.)
- RandomLotteryNumberCombinationFactory
  - [x] 한 개의 로또를 정상적으로 생성한다.
  - [x] 여러 개의 로또 리스트를 정상적으로 생성한다.

#### domain.rank
- RankCriteria
  - [x] 매칭 개수, 보너스 포함 여부가 해당 등수 기준에 부합하는지 판별한다.

---

실행 예시
```
구입금액을 입력해 주세요.
14000

수동으로 구매할 로또 수를 입력해 주세요.
3

수동으로 구매할 번호를 입력해 주세요.
8, 21, 23, 41, 42, 43
3, 5, 11, 16, 32, 38
7, 11, 16, 35, 36, 44

수동으로 3장, 자동으로 11개를 구매했습니다.
[8, 21, 23, 41, 42, 43]
[3, 5, 11, 16, 32, 38]
[7, 11, 16, 35, 36, 44]
[1, 8, 11, 31, 41, 42]
[13, 14, 16, 38, 42, 45]
[7, 11, 30, 40, 42, 43]
[2, 13, 22, 32, 38, 45]
[23, 25, 33, 36, 39, 41]
[1, 3, 5, 14, 22, 45]
[5, 9, 38, 41, 43, 44]
[2, 8, 9, 18, 19, 21]
[13, 14, 18, 21, 23, 35]
[17, 21, 29, 37, 42, 45]
[3, 8, 27, 30, 35, 44]

지난 주 당첨 번호를 입력해 주세요.
1, 2, 3, 4, 5, 6
보너스 볼을 입력해 주세요.
7

당첨 통계
---------
3개 일치 (5000원)- 1개
4개 일치 (50000원)- 0개
5개 일치 (1500000원)- 0개
5개 일치, 보너스 볼 일치(30000000원) - 0개
6개 일치 (2000000000원)- 0개
총 수익률은 0.35입니다.(기준이 1이기 때문에 결과적으로 손해라는 의미임)
```
---
