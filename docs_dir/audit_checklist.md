# Audit 내부 Process
## Smart Contract가 deploy -> audit 팀에 알림 ->  


# ICON 요구사항
## Timeout
* 코드 시작과 끝에 시간체크하도록 자동삽입?? 얼마나 걸리는지 체크. limit값을 어딘가에 설정 가능하도록 해야하나?
* param에 값을 주고, timeout을 계산해보아야 하는 것인가?
=> external로 정의된 것만 정의. 인자를 몇개 잡아서 호출.
=> type checking을 안하게 되면 iconservice에서 처리해주는 것인지?

## for/while 없도록
* recursive call로 바꿀 수 있나? recursive는 무한으로 안빠지는지 어떻게 체크하지? stack limit??

## 시스템 콜, 파일시스템, 랜덤함수, 외부네트워크 접근 불가 (import 체크??)
* import 체크??

## Import 금지 (시스템 콜, 파일시스템)
. iconservice외에 import 하는 것을 막는다.
. IIPS에서 transfer에 대해서 처리 방안은 어떻게? 이건 개발자가 audit할 때, parameter이름이 동일하게 해달라. 아니면 reject해달라.

## 이더리움에서 본 것 기반으로 보면
. SCORE에서 사용하는 어떤 function이 return값에 따라서, 후처리를 해주어야 하면 이에 대한 처리가 필요를 체크. 단순 호출로 끝나지 말고.

## Code Coverage를 테스트하겠다고 하면
## Symbolic Execution을 하겠다고 하면
=> 기존 소스 코드에 추가햐여야 함.

ExternalContract(someAddress).deposit.value(100);

# 기타
- python에서 class간 hirearchy를 보여줄 수 있는 것이 있으면 좋겠음. 오픈 소스로 된거 없음?
- asset는 remaining gas를 소비하고, require는 내부적으로 revert를 이용? revert는 remaining gas를 돌려줌. asset같은 걸로 overflow/underflow 체크하는 것도 방법
- 얼마나 loop(gas)값을 소모하는지를 알 수 있는 방법이 있나? audit하는 쪽에서 할 수 있으면 좋은 자료인듯.
- 이더리움에서 한 block에 들어가는 gas의 최대값이 정해진 것처럼 loopchain에서도 이에 대한 값이 있어야 하지 않을까?
- 잘못된 contract를 pause할 수 있는 방안 (black list로? 이걸 auditor가 아니라 운영자가 하는 것이라면 audit에서는 상관하지 않아도 됨?)
- 특정 용도(예, CrowdSale)를 위한 UI (rule engine based)를 만들어서 제공하는 것은 어떨지? 특정 개발사들은 ICO를 위해서 한다고 하면 좋지 않을까? , 정부 과제용도 보면 금융권에서 사용하는 것도 패턴이 정해져 있는 것이 아닐까?
- testnet에서 bug bounty프로그램을 돌리는 것은 괜찮은 방법?
- 이용 포인트: 단순 security이슈 뿐 아니라 loop(gas)비가 많이 드는 것을 줄여준다.
- 사업 모델: audit report 제공해주는 것 외에 smart contract를 대신 작성해주는것도 사업모델
- library 제공 - safeMath, Ownable, require/assert revert를 포함한 함수

이런 코드가 있는데, require같은 거 하나로 revert를 포함하도록 하여 실수를 줄임?

>   def fallback(self):
>     if self._crowdsale_closed.get():
>     self.revert('CrowdSale is closed.')

# Ethereum Smart Contract Best practices
https://consensys.github.io/smart-contract-best-practices <-- 여기 자료가 많지만 다 정리하지 못했음
https://github.com/ethereum/wiki/wiki/Safety <-- 여기에서 Security-related Documentation and Procedures 이 부분은 Audit Process에 포함하는 것이 좋을거 같음

## General Philosophy
### 실패에 대비
- Smart Contract가 잘못되었으면 해당 Contract를 멈춤.
- 위기시 money값을 관리 ( rate limiting, maximum usage )
- 버그 픽스나 기능 향상을 위한 upgrade path를 제공.

### Rollout Carefully
- 각 단계별 rollout을 두어서, 빠져나올 수 있도록 함?

### Keep SmartContract Simple
-

### Stay up to date
- 새로운 버그에 대해 Smart Contract확인
- 최신 library 와 tool사용
- 새로운 security 기술 적용

### Be aware of blockchain properties
- external contract call을 할때 특히 주의
- public function은 public이고, private data도 모두에게 보여질 수 있다
- gas cost와 block gas limit을 항상 염두에 둠

### Fundamental Tradeoffs: Simplicity versus Complexity cases
- Rigid versus Upgradeable
Monolithic versus Modular
Duplication versus Reuse

###

## Recommendations for Smart Contract Security in Solidity
### Use caution when making external calls¶
### Mark untrusted contracts¶
### Avoid state changes after external calls¶
- external contract call이 자체는 위험하지 않더라도 race condition이 발생하거나 할 수 있음. (reentrancy도 이에 해당?)
-

### Be aware of the tradeoffs between send(), transfer(), and call.value()()
- someaddress.send(), .transfer()는 reentrancy에 대해 안전.
- .transfer()는 require(x.send())와 동일. 실패하면 자동으로 revert()
- someAddress.call.value(y)() 는 ether를 보내고, code를 실행. reentrancy에 노출




# Attacks on Ethereum Smart Contracts (https://www.youtube.com/watch?v=jDbQ7roq6Qg)
## Call to the Unknown
- type check - 만약 function의 type이 다를 경우 오류 발생. interface를 미리 정의되어 있다면, 해당 interface와 다르게 호출되는 부분이 있다면 미리 확인?
##

# 이더리움 Ethernaut에서 나오는 문제
## Fallback,Fallout
* fallback 함수의 조건을 자세히 설펴봐야 함. owner권한을 가질 수 있도록 하는 코드는 면밀히 살펴보아야 함.
* fallout - owner권한을 가지는 코드는 면밀히 살펴야 한다. constructor는 remix에 나타나지 않는다.

## CoinFlip - 랜덤함수 문제
* 문제 해결: 다른 contract를 동일한 address에 있는 것과 동일한 블록에 속한 contract에서 호출하게 되면 동일한 난수가 생긴다.
- 이더리움에서 블록정보를 활용해 난수를 만드는 것은 위험하다.
- 난수가 필요한 상황에서는 다른 쪽에서 난수를 받아오거나 Oracle 문제 등으로 풀어야 함.

## Telephone
- 문제 해결 : tx.origin와 msg.sender는 다르다. A(EOA)->B(Contract)->C(Contract)형태로 function이 불렸을 때, B의 입장에서 tx.origin과 msg.sender는 동일하나, C의 입장에서 tx.origin은 A고, msg.sender는 B이다.
- tx.origin과 msg.sender를 사용할때 주의요함.뭔가 proxy contract를 사용해서,기존 코드가 달라지게 호출할 수 있다.

## Token (Arithmetic 문제와 동일)
- 문제 해결: underflow 문제. uint는 항상 0보다 크다. 이럴 경우에 비교문을 처리할 때 주의할 필요가 있다. underflow와 overflow를 주의해야 함. 비교문에서 사용할 경우, a > b는 가능하지만, 이 비교를 a - b로 하게 되면 문제가 발생할 수 있다.
- SafeMath 대한 처리를 해주지 않으면 곳곳에서 이런 문제가 발생할 가능성 있음.

## Delegate Calls
- 문제 해결: call, callcode, delegatecall과 같은 lowlevel call을 정의할 때 주의 필요.
(http://ihpark92.tistory.com/54?category=747041) <-- delegatecall에 대한 설명 블로그
A->B로 함수 호출 시, 일반적인 call은 B의 context를 따르나, delegatecall은 A의 context를 따름.
deletegatecall을 할 떄에는 context가 달라질 수 있다는 것을 염두에 두어야함.

## Force
- 문제 해결: 일반적으로 payable이 없는 빈 contract에 이더를 보내면, 에러 발생. (이럴 경우, ether는 어떻게 되지?, 가스도 차감? ). selfdestruct 함수는 자체 계약을 블록체인에서 삭제? 하는 함수로 쓰이는 것으로 보임.
- contract A의 selfdesctuct(contract B Address)를 호출하게 되면, A는 selfdestruct되어버려서 이 호출하는 순간, contract address B로 계정이 옮겨진다.
https://medium.com/loom-network/how-to-secure-your-smart-contracts-6-solidity-vulnerabilities-and-how-to-avoid-them-part-2-730db0aa4834 도 참고

## Vault
- 문제 해결: password에 저장된걸 따로 decoding할 수 있다는 의미. ethereum에서는 eth.getStorageAt을 통해 해당 contract를 알 수 있고, 여기에 있는 storage value를 접근할 수 있는 것처럼 얘기함.
모든 정보는 public blockchain에 public하게 저장되고 있다. 비밀번호 같은 것은 web3의 getStorageAt명령을 통해서 index순서대로 variable을 받아올 수 있다. 그래서 이런 password관련된 정보는 public blockchain에 저장해서는 안된다?
비밀번호와 같은 정보를 blockchain에 적는 것은 적절한 것이 아니다. public blockchaing의 data는 모두가 공유되고 투명하게 보인다는걸 염두에 두어야 한다.

## King
- 문제 해결: 외부 contract가 해당 contract를 호출하면 이 문제를 해결할수 있따는건데. 어떻게?

## Reentrancy
- 문제 해결: msg.sender.call.value때문에 recursive가 됨.
- 차감되기 전에 호출
- 1) call 대신 send, transfer를 사용 ( call은 transaction의 모든 가스를 전달하지만, send, transfer는 2300가스만들 전달). recursive호출시 2300가스 모두 소모되어 gas 부족 에러 발생하고 트랜잭션 중지됨.
- 2) account의 보유 잔액을 0으로 만든 후에 call, send, transfer를 요청해서 송금하는 방법. check-effects-interaction patter.
transfer는 실패하면 예외를 발생시키기 떄문에 전체 트랜잭션이 실패. send는 실패하면 false를 반환하기 때문에 트랜잭션이 실패하지 않음.


## Elevator

## Privacy

## Gatekeeper One

## Gatekeeper Two

## Naught Coin

## Preservation

## Locked

## Recovery


DASP

## Reentrancy
출금 등 함수에 다시 재진입하는 경우
* msg.sender.call.value 대신 msg.sender.transfer 사용하자.(이더리움에 한한듯)
아니면 mutex 구현해서 써라.
- (영민): 아니면 일단 차감을 먼저 한후에 보내면 되는 것 아닌지.

## Access Control
초기화되지 않는 파라미터, 생성자 이름 다른경우? 시점에 따라 설정되지 않은 변수가 사용
* visibility 작성 - public일지라도 모든 함수의 visibility를 명시하는 것이 좋다
* delegate 사용 시 - internal 함수가 호출될 수 있음을 명심하고 사용할 수 있는 권한 관리를 강화한다. ex) whitelist, owner 등
* tx.origin을 통한 인증은 사용하지 않는다.
* EXTCODESIZE를 통한 타입 비교 - constructor call을 통해 무력화될 수 있기 때문에 사용하지 않는다. (이더리움은 모든 주소가 동등하게 여겨진다)
* Contract & Constructor - Constructor 예약어를 사용하거나 컨트랙 이름과 생성자 이름을 동일하게 작성한다.
OWASP

## Arithmetic Flows
연산시 오버/언더 플로우
* SafeMath 같은거 쓰면 좋은데 파이썬에서는 어떻게?
* (영민) 없다면, SafeMath를 보고 유사한 library를 python용으로 만드는게 좋을 듯

## Unchecked Low Level Calls
금액 차감 후, 전송에 실패할 경우 커버코드 없으면 차감만 되고 날라감.
* 외부 호출 API는 실패할 수 있음을 명심(아예차단??)
* 결과에 따른 보상 코드를 항상 구현 (rollback되지 않는 경우)
* (영민) send 대신 transfer를 하면 보완할 수 있다고 하네요. (블록집착남)

## Denial of Services
실행이 오래 걸리는 코드
* Loop의 내용을 함수로 빼는 것을 고려
* Atomic한 단위로 함수를 구성
* 외부 호출 API 실패 할 수 있음을 명심
* (영민) 이렇게 loop를 돌다가 GasLimit에 도달하면 기존에 수행되었던 것들은 어떻게 되는건지? revert? 아니면 진행되는 것인가? 다 돌지 못하고 남아있는 것만 문제되는 것인가?

## Front Running
* Pending Transaction을 누구나 확인할 수 있음을 명심
* Transaction간 처리순서에 따라 코어 로직이 영향을 받지 않도록

## Reordering
## Time Manipulation
일정 범위의 시간조작 가능?
* 코어 로직이 블록의 타임스탬프와 관련이 없도록
* 시간이나 기간 대신 블록의 수를 기준으로 로직을 만드는걸 추천

## Pseudo Random Number Generator
* On-Chain에서 완벽한 랜덤함수를 만들겠다는 생각을 버려라. (아예 차단???)

## Replay
## Short address/Parameter
주소의 길이를 줄임으로써 그 뒤의 value값을 임의로 키우는건가??
* 중요한 로직에는 input 값을 항상 검사

## Forcibly Sent Ether
강제로 다른 주소에게 전송??
* 하나 이상의 payable 함수를 선언해 적절한 대응을 한다.

## External Contract Referencing
몇몇 유저는 라이브러리 주소를 변경할 수 있는 권한을 가지고있다(어떻게 믿는가?)
* address 값을 변경 가능하게 하지 말고 생성시 객체에 고정되도록 한다.

## Floating Points and Numerical Precision
0이 될 수 있음
* 곱셈을 우선 연산하고 그 이후 나눗셈을 하도록 한다.

# CVE (Common Vulnerabilities and Exposures) 항목 ??
