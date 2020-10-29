# Higher-Order Function
[Higher-Order-Function이란 무엇인가](https://medium.com/@la.place/higher-order-function-%EC%9D%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80-1c61e0bea79)  
[Python: Why is functools.partial necessary?](https://stackoverflow.com/questions/3252228/python-why-is-functools-partial-necessary)

지인 개발자끼리 모인 슬랙 채널에서 친구가 작업하다 생긴 버그를 공유해줬다.  
``` Python
dep_factories = []
for dep in di_type.deps:
    if dep.as_mock:
        # dep_factory = lambda: MagicMock(spec.dep.type_) error!
        dep_factory = partial(MagicMock, spec=dep.type_)
    else:
        dep_di_type = self._container.get(dep.type_)
        dep_factory = self._get_factory(dep_di_type)

    dep_factories.append(dep_factory)
```

```
님들 이거 왜 오동작하는지 알겠음? lambda 쓰니까 하이오더펑션 돼서 마지막 dep을 바라보게 되니까 그럼 ㅋㅋ
```

하이오더펑션이 뭔지 몰라서 바로 검색해봤다. 

## 정의
아래 두 가지 조건 중 하나 이상을 만족하면 HOF라고 한다.  
* 하나 이상의 함수를 인자로 받는다.
* 함수를 결과로 반환한다.

## 특징

### 제어패턴 추상화


함수의 흐름을 인자로 넘기는 함수로 제어함.

```Python
# n까지 출력
def print_n(n):
    for i in range(n):
        print(n)

# n까지 리스트에 추가
def append_n(n):
    result = []
    for i in range(n):
        result.append(n)
    return result
```
위 코드에서 for 반복문 안의 작업이 계속해서 변경됨.  
변경이 있을만한 부분의 로직을 추상화하여 함수로 제공.

```Python
def repeat(n, fn):
    for i in n:
        fn(i)

# 1000까지 출력
repeat(1000, print)

# 1000까지 리스트에 추가
result = []
repeat(1000, result.append)
```
* 다양한 요구사항에 대응하기 쉬워짐
* 로직의 캡슐화 -> 재사용성 증가
* 부작용(side effect) 줄어듦

### 결과값을 함수로 반환
```Python
def create_adder(x):  
    def adder(y):  
        return x + y  
    
    return adder  
    
add_15 = create_adder(15)  
    
print(add_15(10)) 
```

매개변수 x는 클로저로 남음

## 그래서 버그 원인
적고 나서야 이게 HOF 특성 때문인가 싶음.  
람다는 *표현*에 중점을 둠 -> 평가되는 시점의 변수를 참조하기 때문에 생긴 버그.  
early vs late binding dilemma 라는 표현도 있음.

```Python
result = []
for i in range(5):
    result.append(lambda: print(i))
result[0]()
```
결과 4
```Python
from functools import partial

result = []
for i in range(5):
    result.append(partial(print, i))
result[0]()
```
결과 0  

\#FunctionalProgramming, \#HigherOrderFunction, \#Lambda, \#Partial