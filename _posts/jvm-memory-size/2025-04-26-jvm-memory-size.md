---
title: JVM의 힙 메모리를 어떻게 설정해야할까?
date: 2025-05-24
modified: 2025-05-24
tags: [jvm]
description: JVM 힙 메모리 제한 설정
image: ""
---

 JVM의 `-Xms`, `-Xmx` 를 얼마나 설정해야하고, 설정하지 않으면 어떤 일이 발생하는 지 이해하는 것이 목표이다.

# 1. JVM 힙 메모리를 설정하지 않으면 어떻게 될까?

## 1.1 힙 메모리

<figure>
<img src="/jvm-memory-size/jvm-memory-layout.png" alt="shell">
<figcaption>Fig 1. JVM Memory Layout</figcaption>
</figure>

위 그림에서 JVM Memory 부분을 Runtime Data Area라고도 부른다. 여기서 Heap은 모든 스레드가 공유하는 영역으로 런타임에 생성된 객체들이 저장되는 공간이며,
GC의 주된 관심사이기도 하다. Heap의 크기는 `-Xms`, `-Xmx`로 관여할 수 있다.

- `-Xms`: 자바 프로세스의 최초 힙 크기
- `-Xmx`: 자바 프로세스의 최대 힙 크기

## 1.2 기본값은 뭘까?

JVM은 시작될 때 java 명령어 줄에 있는 옵션을 파싱한다. 일부 명령은 자바 프로그램에서 적절한 JIT 컴파일러를 선택하는 등의 작업을 수행하기 위해 사용되며 일부 명령은 JVM에 전달된다. 만약 **JIT 컴파일러 타입이나 자바 힙 크기 할당 옵션이 지정되지 않는 경우에는 시스템의 상황에 맞게 설정**된다.

<a herf = "https://docs.oracle.com/en/java/javase/17/gctuning/hotspot-virtual-machine-garbage-collection-tuning-guide.pdf" rel="noopener" target="_blank">Oracle - HotSpot VM GC Tuning Guide</a>에 의하면 Oracle HotSpot VM 17의 주요 기본 설정값은 다음과 같다.

- GC : Garbage-First (G1) Collector (메모리 사이즈에 따라서 다른 GC가 선택될 수 있는 걸로 알고 있다.)
- 최대 GC 스레드 : 사용 가능한 CPU 자원과 힙 사이즈로 결정
- 초기 힙 사이즈 : 물리 메모리의 1/64
- 최대 힙 사이즈 : 물리 메모리의 1/4
- JIT 컴파일러 : C1, C2 모두 사용

여기서 글의 주제인 힙 사이즈만을 직접 확인해보기로 했다. `-XX:+PrintFlagsFinal` 옵션을 추가하면 설정된 JVM 플래그<sup id="flag">[[1]](#user-ref)</sup> 목록을 확인할 수 있다.
테스트는 Oracle OpenJDK 17.0.12, 메모리 16GB 환경에서 진행했다.

```
[Global flags]
   size_t InitialHeapSize                          = 268435456                                 {product} {ergonomic}
   size_t MaxHeapSize                              = 4294967296                                {product} {ergonomic}
   size_t MinHeapDeltaBytes                        = 2097152                                   {product} {ergonomic}
   size_t MinHeapSize                              = 8388608                                   {product} {ergonomic}
    uintx MinHeapFreeRatio                         = 40                                     {manageable} {default}
    uintx MaxHeapFreeRatio                         = 70                                     {manageable} {default}
   double InitialRAMPercentage                     = 1.562500                                  {product} {default}
   double MaxRAMPercentage                         = 25.000000                                 {product} {default}
   double MinRAMPercentage                         = 50.000000                                 {product} {default}
```

기본적인 설명은 아래와 같다. 컨테이너 환경인 경우에는 전체 물리 메모리가 아닌 컨테이너 자체 할당된 메모리를 기준으로 계산된다. 위 결과를 보면 InitialHeapSize와 MaxHeapSize를 보면 각각 물리 메모리의 1/64, 1/4로 설정되는 것을 알 수 있다.

- `InitialHeapSize` : 초기 힙 영역 할당 사이즈
- `MaxHeapSize` : 최대 힙 영역 할당 사이즈(해당 사이즈까지 Heap Expansion을 수행)
- `MinHeapDeltaBytes` : GC로 인한 최소 힙 영역 변화
- `MinHeapSize` : 최소 힙 영역 할당 사이즈(Heap Shrinkage의 마지노선으로 생각했다.)
- `MinHeapFreeRatio` : Heap Expansion 수행 임계치(GC 이후 여유 Heap 공간이 40% 미만이면 Heap Expansion 수행)
- `MaxHeapFreeRatio` : Heap Shrinkage 수행 임계치(GC 이후 여유 Heap 공간이 70% 이상이면 Heap Shrinkage 수행)
- `InitialRAMPercentage` : 초기 힙 크기 설정에 사용(ms 설정 시 해당 옵션 무시)
- `MaxRAMPercentage` : 최대 힙 크기 설정에 사용(일반 메모리)
- `MinRAMPercentage` : 최대 힙 크기 설정에 사용(소형 메모리, 기준은 대략 256MB 부근)

# 2. 기본값을 쓰면 안되는건가?

JVM은 GC 이후 남은 여유 공간을 근거로 힙 영역을 MinHeapSize(하한)와 MaxHeapSize(상한)의 범위에서 축소(Shrinkage)하거나 확장(Expansion)한다. **기본값은 초기 힙 사이즈와 최대 힙 사이즈의 간격이 크기 때문에 확장을 위한 오버헤드가 발생하여 성능이 저하될 것으로 생각**한다. 


...작성중...


### 메모

<small id="user-ref"><sup>[[1]](#flag)</sup> JVM에서 플래그는 JVM 내부 동작을 제어하거나 설정을 변경할 수 있는 옵션을 의미한다.</small>

