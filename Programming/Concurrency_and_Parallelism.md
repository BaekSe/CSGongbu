# 동시성과 병렬성
둘 다 여러 개의 스레드를 대상으로 하는 개념이지만 조금 다름.  
병렬성은 동시성을 필요로 하지만, 동시성이 병렬성을 필요로 하는 것은 아님.  

[동시성(Concurrency)와 병렬성(Parallelism)](https://oaksong.github.io/2017/12/23/concurrency-and-parallelism/)  
[Python 동시 프로그래밍](https://nachwon.github.io/asyncio-futures/)  
[Async Programming in Python with asyncio](https://dev.to/welldone2094/async-programming-in-python-with-asyncio-12dl)


## 동시성(Concurrency)
* 한번에 여러 스레드를 **다루는** 것.
* 하나의 코어에서 여러 스레드가 번갈아가면서 실행됨.
* 논리적 관점.

## 병렬성(Parallelism)
* 한번에 여러 스레드를 **실행하는** 것.
* 여러 개의 코어의 각 스레드가 동시에 실행됨.
* 물리적 관점

## 파이썬에서
파이썬은 [GIL](https://github.com/BaekSe/PythonGongbu/blob/master/PythonBasics/GIL.ipynb) 정책 때문에 여러 쓰레드를 다루기 위해 별도의 모듈을 사용해야함.  

* asyncio
  * 태스크가 언제 스위칭할지 결정함
  * 1개의 프로세서(코어) 사용
* threading
  * 파이썬 인터프리터가 언제 스위칭할지 결정함
  * 1개의 프로세서 사용
* multiprocessing
  * 프로세스들이 서로 다른 프로세서에서 병렬적으로 실행됨
  * 여러 개의 프로세서 사용

asyncio는 특정 작업(task)을 멈추지 않고 별도 공간에서 계속 실행하는 비동기 방식.  
threading 모듈은 다수의 쓰레드가 번갈아가면서 실행되는 동시성을 띔.  
multiprocessing 모듈은 다수의 프로세서가 동시에 실행되는 병렬성을 띔.

## asyncio-비동기

### 실행 방식
이벤트 루프라는 파이썬 객체에 task를 생성해 넣어둠.  
가장 오래 대기한 task부터 작업 시작.  
이전 task로부터 제어권이 돌아오면(이전 task의 I/O 작업이 시작되면) 이벤트 루프가 다른 task에 권한 부여.
### 장점
* I/O 작업이 실행되기 기다리는 동안 다른 작업 시작할 수 있음.  
* thrading과 비교했을 때 여러 작업을 동시 실행해도 속도 저하 X
### 단점
* task의 제어권을 담당하는 await와 async를 적절한 곳에 넣어줘야 함.  
* 하나의 쓰레드를 사용하기 때문에 task가 프로세스를 오래 점유할 경우 다른 task가 모두 묶여버림


## threading-동시성
### 실행방식
threading 모듈의 경우 threading.local() 하나만 전역적으로 설정해놓으면 할당된 각 쓰레드가 다른 객체에 접근하도록 처리함.
### 장점
* I/O 바운드 작업이 실행되는 시간이 겹치게 할 수 있음.  
* I/O 바운드 작업이 많은 경우 작업시간 줄어들음.
### 단점
* 동시성의 컨셉에 맞게 총 작업 분량을 잘 나눠야함
* 공유되는 객체에 각 쓰레드가 접근하는지 확인이 필요함. (Race Condition 주의)


## multi-processing-병렬성
### 실행방식
위 두 모듈과 다르게 GIL을 우회해 여러 프로세서 사용 가능.  
새 인터프리터를 띄워(여러 개의 프로세스 실행) 물리적으로 병렬 작업 가능.
### 장점
* CPU 연산이 많은 경우 여러 개의 CPU를 활용 가능함.
* 구현이 편리함
### 단점
* 각 프로세스는 독립적인 메모리 공간을 가짐. Session 객체 공유 불가능.
* 새 프로세스를 생성하고 정리하는 시간이 오래 걸림.