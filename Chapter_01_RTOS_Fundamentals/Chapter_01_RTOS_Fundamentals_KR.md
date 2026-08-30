**Volume 03 Real Time Operating Systems**

# 01. RTOS Fundamentals

## 01.01 RTOS Concept: Determinism, Latency, Jitter

![](images/image1.png){width="7.268055555555556in" height="7.268055555555556in"}

실시간 운영체제(RTOS, Real-Time Operating System)는 단순히 높은 평균 연산 성능을 달성하는 것이 아니라, 예측 가능한 시간 제약(Timing Constraints) 내에서 소프트웨어 작업을 실행하도록 설계된다. 따라서 로보틱스(Robotics)와 임베디드 제어(Embedded Control)에서는 논리적 결과뿐만 아니라 그 결과가 언제 제공되는지도 정확성(Correctness)을 결정한다. 이러한 시간 중심 동작은 RTOS를 일반적인 범용 운영체제(General-Purpose Operating System)와 구별하는 핵심적인 특징이다.

RTOS의 핵심 특성은 결정성(Determinism)이며, 이는 중요한 연산이 예측 가능하고 제한된 실행 동작(Bounded Execution Behavior)을 가진다는 의미이다. 결정성은 모든 연산이 항상 정확히 동일한 시간 동안 실행되어야 한다는 의미는 아니다. 대신 설계자는 스케줄링(Scheduling), 인터럽트 응답(Interrupt Response), 태스크 실행(Task Execution), 통신(Communication), 자원 접근(Resource Access)에 대해 의미 있는 최악 조건의 한계(Worst-Case Bound)를 설정할 수 있어야 한다.

결정적 동작(Deterministic Behavior)은 소프트웨어가 센서(Sensor) 및 액추에이터(Actuator)와 지속적으로 상호작용하는 물리 시스템(Physical System)에서 특히 중요하다. 모터 컨트롤러(Motor Controller)는 매 1밀리초마다 새로운 명령을 요구할 수 있으며, 관성 센서(Inertial Sensor)는 일정한 시간 간격으로 샘플링되어야 한다. 이러한 시간 요구사항을 만족하지 못하면 계산 자체가 수학적으로 정확하고 최종적으로 올바른 값을 생성하더라도 제어 품질(Control Quality)이 저하될 수 있다.

지연시간(Latency)은 특정 이벤트(Event)가 발생한 시점부터 그 이벤트에 대응하는 시스템 응답(System Response)이 발생할 때까지의 경과 시간을 의미한다. 외부 인터럽트(External Interrupt), 센서 데이터 도착(Sensor Arrival), 타이머 만료(Timer Expiration), 통신 메시지(Communication Message) 등이 처리를 시작시킬 수 있지만, 관련 태스크가 항상 즉시 실행되는 것은 아니다. 인터럽트 처리, 스케줄러 결정, 높은 우선순위 태스크, 자원 경합(Resource Contention), 메모리 접근, 통신 지연 등이 전체 지연시간에 영향을 줄 수 있다.

따라서 실시간 설계(Real-Time Design)는 평균 지연시간(Average Latency)보다 최악 조건 지연시간(Worst-Case Latency)에 특히 주목한다. 일반적으로 50마이크로초 안에 응답하지만 가끔 5밀리초가 필요한 시스템은 1밀리초 이내 응답이 필요한 제어 기능에는 적합하지 않을 수 있다. 반대로 항상 500마이크로초 이내에서 응답하는 시스템은 시간 동작을 시스템 수준 설계(System-Level Design)에 안정적으로 반영할 수 있기 때문에 더 적합할 수 있다.

지터(Jitter)는 반복적으로 수행되는 작업의 실행 시점이 변화하는 정도를 나타낸다. 주기적 제어 태스크(Periodic Control Task)가 매 1밀리초마다 실행되도록 설계되었다면 이상적인 활성화는 정확히 일정한 간격으로 발생한다. 그러나 실제 실행 간격은 인터럽트, 스케줄링 간섭(Scheduling Interference), 캐시 효과(Cache Effects), 통신 지연 또는 다른 작업의 영향으로 0.98, 1.03, 1.01밀리초와 같이 달라질 수 있다. 이러한 예상 시간으로부터의 편차를 지터라고 한다.

지연시간(Latency)과 지터(Jitter)는 서로 관련되어 있지만 서로 다른 시간 특성을 나타낸다. 지연시간은 응답이 완료되기까지 얼마나 오래 걸리는지를 나타내며, 지터는 이러한 시간이 실행마다 얼마나 변화하는지를 나타낸다. 따라서 시스템은 비교적 큰 지연시간을 가지면서도 매우 안정적인 시간 특성을 가질 수 있고, 반대로 평균 지연시간은 짧지만 상당한 지터를 가질 수도 있다. 피드백 제어(Feedback Control), 동기화 센싱(Synchronized Sensing), 모션 생성(Motion Generation), 산업용 통신(Industrial Communication)에서는 두 특성을 독립적으로 평가해야 한다.

RTOS 스케줄링 메커니즘(Scheduling Mechanism)은 실행 우선순위(Execution Priority)를 지정하고 태스크가 서로를 언제 선점(Preemption)할 수 있는지를 결정함으로써 이러한 시간 특성을 제어하도록 설계된다. 필요한 경우 높은 우선순위의 제어 태스크(Control Task)가 낮은 우선순위의 진단(Diagnostic) 또는 통신 처리를 중단하고 실행될 수 있다. 이것이 지연시간 자체를 제거하는 것은 아니지만 실행 간섭을 보다 관리 가능하게 만들고 중요한 실행 경로(Critical Execution Path)를 정의된 데드라인(Deadline)에 대해 분석할 수 있도록 한다.

인터럽트 아키텍처(Interrupt Architecture) 역시 결정적 성능(Deterministic Performance)에 직접적인 영향을 미친다. 하드웨어 인터럽트(Hardware Interrupt)를 이용하면 긴급한 이벤트를 빠르게 처리할 수 있지만, 과도한 인터럽트 처리는 예정된 태스크를 지연시키고 지터를 증가시킬 수 있다. 따라서 효과적인 RTOS 설계에서는 인터럽트 서비스 루틴(ISR, Interrupt Service Routine)을 짧게 유지하고, 지연시켜 처리할 수 있는 작업(Deferred Work)은 적절한 태스크로 전달하며, 빠른 하드웨어 응답이 중요한 주기적 제어 실행을 방해하지 않도록 우선순위를 신중하게 정의한다.

실시간 요구사항(Real-Time Requirement)은 하드 실시간(Hard Real-Time), 펌 실시간(Firm Real-Time), 소프트 실시간(Soft Real-Time)으로 구분할 수 있다. 하드 실시간 기능에서는 데드라인을 놓치는 것이 시스템 실패(System Failure)로 간주되며 안전하지 않은 상태를 만들 수 있다. 펌 실시간 처리는 데드라인을 놓치면 결과의 실질적인 가치가 사라지며, 소프트 실시간 처리는 품질 저하를 감수하면서 간헐적인 지연을 허용한다. 로봇 시스템에는 일반적으로 이러한 세 가지 유형이 동시에 존재한다.

예를 들어 로봇은 엄격한 시간 제약 아래에서 고주파 모터 전류 루프(Motor Current Loop)를 실행하는 동시에, 인지(Perception), 로깅(Logging), 텔레메트리(Telemetry), 사용자 인터페이스(User Interface) 기능을 더 느리고 덜 결정적인 주기로 실행할 수 있다. RTOS 아키텍처는 낮은 중요도의 작업이 핵심 제어 기능을 방해하지 않도록 해야 한다. 이러한 분리는 임베디드 프로세서(Embedded Processor)가 제어, 네트워킹, 센싱 및 지능형 연산(Intelligent Computation)을 함께 수행할수록 더욱 중요해진다.

결정성(Determinism)은 CPU 스케줄러(CPU Scheduler)의 범위를 넘어 전체 시스템에서 고려되어야 한다. 동적 메모리 할당(Dynamic Memory Allocation), 캐시 미스(Cache Miss), 공유 버스(Shared Bus), DMA 동작, 블로킹 동기화(Blocking Synchronization), 네트워크 큐(Network Queue), 디바이스 드라이버(Device Driver)의 동작 등이 가변적인 지연을 발생시킬 수 있다. 따라서 실시간 엔지니어링(Real-Time Engineering)에서는 단순히 RTOS 커널(Kernel)을 선택하는 것이 아니라 시스템 수준 시간 모델(System-Level Timing Model)을 구축해야 하며, 연산, 통신, 메모리 및 입출력(I/O) 경로 전체에서 예측 가능성을 확보해야 한다.

시간 요구사항(Timing Requirement)은 일반적으로 주기(Period), 데드라인(Deadline), 응답시간(Response Time), 최악 조건 실행시간(WCET, Worst-Case Execution Time)을 이용하여 표현된다. 주기적 태스크는 예상 활성화 간격을 가지며, 데드라인은 해당 처리가 완료되어야 하는 시점을 정의한다. 최악 조건 실행시간은 가장 불리한 조건에서 태스크 실행에 필요한 시간을 추정한다. 이후 스케줄 가능성 분석(Schedulability Analysis)을 통해 모든 중요 태스크가 동시에 자신의 시간 제약을 만족할 수 있는지를 판단한다.

이론적인 시간 보장(Timing Guarantee)은 실제 대상 하드웨어(Target Hardware)에서 검증되어야 하므로 측정(Measurement)은 매우 중요하다. 엔지니어는 현실적인 워크로드(Workload) 조건에서 인터럽트 지연시간(Interrupt Latency), 스케줄링 지연시간(Scheduling Latency), 태스크 응답시간(Task Response Time), 실행시간 분포(Execution-Time Distribution), 제어 루프 지터(Control-Loop Jitter)를 관찰한다. 특히 경쟁 인터럽트, 네트워크 트래픽, 메모리 활동 또는 백그라운드 태스크에 의해 발생하는 드문 지연은 평균 성능 측정에서는 발견되지 않는 문제를 드러낼 수 있으므로 스트레스 조건(Stress Condition)에서의 검증이 중요하다.

로보틱스에서 결정성(Determinism)의 실질적인 목적은 궁극적으로 물리 세계(Physical World)와의 예측 가능한 상호작용을 보장하는 것이다. 안정적인 시간 동작은 반복 가능한 센서 획득(Sensor Acquisition), 동기화된 액추에이터 명령(Synchronized Actuator Command), 신뢰성 있는 안전 반응(Safety Reaction), 컴퓨팅 계층 간의 제어된 통신을 가능하게 한다. 이러한 원리는 이후 다루게 될 스케줄링(Scheduling), 우선순위 역전(Priority Inversion), 컨텍스트 스위칭(Context Switching), 실시간 네트워킹(Real-Time Networking), 모터 제어(Motor Control), 혼합 중요도 시스템(Mixed-Criticality System)의 기초가 된다.

## 01.02 RTOS Core Components: Kernel, Scheduler, IPC Objects

![](images/image2.png){width="7.268055555555556in" height="7.268055555555556in"}

RTOS(Real-Time Operating System)는 여러 소프트웨어 활동을 예측 가능한 방식으로 실행하기 위해 서로 협력하는 핵심 구성 요소(Core Components)들을 중심으로 구성된다. 커널(Kernel)은 전체 제어를 담당하는 중심 계층을 형성하고, 스케줄러(Scheduler)는 각 시점에서 어떤 태스크(Task)가 실행될지를 결정한다. 프로세스 간 통신(IPC, Inter-Process Communication) 메커니즘과 동기화 객체(Synchronization Objects)는 태스크 사이의 데이터 교환과 공유 자원 접근을 조정한다. 이러한 구성 요소들이 함께 실시간 임베디드 시스템(Real-Time Embedded System)과 로봇 시스템에서 요구되는 실행 프레임워크(Execution Framework)를 형성한다.

커널(Kernel)은 프로세서 시간(Processor Time), 태스크(Task), 인터럽트(Interrupt), 동기화(Synchronization), 타이머(Timer) 및 기타 시스템 자원(System Resources)을 관리하는 기본적인 소프트웨어 계층이다. 애플리케이션 태스크(Application Task)는 일반적으로 프로세서 실행을 직접 제어하는 대신 커널 서비스(Kernel Service)를 통해 이러한 기능을 사용한다. 커널이 자원 관리를 중앙에서 수행함으로써 여러 작업이 동시에 실행되는 상황에서도 예측 가능한 시간 관계를 유지할 수 있는 제어된 실행 환경을 제공한다.

태스크(Task)는 RTOS에 따라 스레드(Thread)라고도 하며, 독립적으로 스케줄링될 수 있는 실행 단위를 의미한다. 각 태스크는 일반적으로 고유한 실행 문맥(Execution Context), 스택(Stack), 우선순위(Priority), 상태 정보(State Information)를 가지며, 이러한 정보는 커널에 의해 관리된다. 따라서 로봇 소프트웨어는 모터 제어(Motor Control), 센서 획득(Sensor Acquisition), 통신(Communication), 진단(Diagnostics), 안전 모니터링(Safety Monitoring)을 서로 다른 태스크로 분리하고 RTOS가 사용 가능한 프로세서 자원에서 이들의 실행을 조정하도록 할 수 있다.

태스크(Task)는 실행 조건과 커널 이벤트(Kernel Event)에 따라 여러 상태 사이를 전이한다. 실행 중(Running) 상태의 태스크는 현재 프로세서를 사용하고 있으며, 준비(Ready) 상태의 태스크는 실행할 수 있지만 프로세서 사용을 기다리고 있다. 차단(Blocked) 또는 대기(Waiting) 상태의 태스크는 데이터 도착, 세마포어(Semaphore) 해제, 타이머 만료 또는 자원 사용 가능과 같은 예상 이벤트가 발생할 때까지 실행할 수 없다. 이러한 상태 기반 구조를 통해 커널은 실행할 수 없는 태스크에 프로세서 시간을 낭비하지 않고 다른 태스크를 실행할 수 있다.

스케줄러(Scheduler)는 다음에 실행할 태스크를 선택하는 커널 구성 요소이다. 우선순위 기반 RTOS(Priority-Based RTOS)에서는 일반적으로 현재 준비 상태에 있는 태스크 가운데 가장 높은 우선순위를 가진 태스크를 선택한다. 더 높은 우선순위의 태스크가 준비 상태가 되면 선점형 스케줄러(Preemptive Scheduler)는 현재 실행 중인 낮은 우선순위 태스크를 중단하고 즉시 프로세서 제어권을 넘길 수 있다. 이를 통해 시간에 민감한 기능이 중요도가 낮은 작업보다 먼저 프로세서를 사용할 수 있다.

문맥 교환(Context Switch)은 프로세서의 실행 대상이 한 태스크에서 다른 태스크로 변경될 때 발생한다. RTOS는 나가는 태스크의 실행 상태를 저장하고 들어오는 태스크의 상태를 복원하여 두 태스크가 이후에도 올바르게 실행을 계속할 수 있도록 해야 한다. 문맥 교환은 동시성(Concurrency)을 가능하게 하지만 실행 오버헤드(Execution Overhead)도 발생시킨다. 따라서 실시간 시스템 설계에서는 선점(Preemption)을 통해 얻는 응답성과 빈번한 태스크 전환으로 발생하는 시간 비용을 함께 고려해야 한다.

스케줄러(Scheduler)는 인터럽트(Interrupt)와 독립적으로 동작하지 않는다. 하드웨어 인터럽트(Hardware Interrupt)는 타이머 만료, 센서 데이터 도착, 통신 데이터 수신 또는 액추에이터 피드백(Actuator Feedback)과 같은 이벤트를 알릴 수 있다. 인터럽트 서비스 루틴(ISR, Interrupt Service Routine)은 즉각적인 하드웨어 상태를 처리한 후 RTOS 태스크에 이벤트를 알릴 수 있다. 이 알림으로 더 높은 우선순위 태스크가 준비 상태가 되면 스케줄러는 문맥 교환을 수행하여 인터럽트 처리가 허용하는 즉시 긴급한 작업을 시작할 수 있다.

프로세스 간 통신(IPC, Inter-Process Communication) 메커니즘은 독립적으로 실행되는 태스크나 소프트웨어 구성 요소 사이에서 정보를 교환할 수 있도록 한다. RTOS 환경에서 일반적인 IPC 개념에는 메시지 큐(Message Queue), 메일박스(Mailbox), 공유 메모리(Shared Memory), 이벤트 메커니즘(Event Mechanism), 동기화 객체(Synchronization Object)가 포함된다. 적절한 메커니즘은 데이터 크기, 통신 방향, 시간 요구사항, 소유권 규칙(Ownership Rules), 실제 데이터를 전달할 것인지 단순히 이벤트 발생만 알릴 것인지에 따라 달라진다.

메시지 큐(Message Queue)는 한 태스크가 커널이 관리하는 큐에 데이터를 넣고 다른 태스크가 이를 가져갈 수 있도록 하는 구조화된 통신 방식을 제공한다. 생산자(Producer)와 소비자(Consumer)가 반드시 동시에 실행될 필요가 없기 때문에 태스크 사이의 직접적인 시간 의존성을 줄일 수 있다. 예를 들어 로봇 컨트롤러에서는 센서 태스크가 측정 데이터를 큐에 넣고, 제어 태스크가 자신의 스케줄링 동작과 우선순위 요구사항에 따라 데이터를 가져와 처리할 수 있다.

공유 메모리(Shared Memory)는 여러 태스크가 커널이 관리하는 메시지 컨테이너를 통해 데이터를 전달하는 대신 동일한 메모리 영역에 직접 접근하는 통신 모델을 제공한다. 이 방식은 데이터 복사 오버헤드(Copying Overhead)를 줄이고 높은 처리량(High Throughput)의 통신을 지원할 수 있지만 동기화(Synchronization)가 필요하다. 적절한 제어 없이 여러 태스크가 공유 데이터를 동시에 읽거나 수정하면 경쟁 조건(Race Condition), 일관되지 않은 상태(Inconsistent State), 시간 의존적인 소프트웨어 오류가 발생할 수 있다.

세마포어(Semaphore)는 태스크 실행을 조정하거나 자원 및 이벤트의 사용 가능 상태를 나타내기 위해 일반적으로 사용되는 동기화 객체(Synchronization Object)이다. 이진 세마포어(Binary Semaphore)는 단순한 사용 가능 또는 사용 불가능 상태를 표현할 수 있으며, 카운팅 세마포어(Counting Semaphore)는 여러 번의 이벤트 발생이나 여러 개의 동등한 자원을 나타낼 수 있다. 태스크는 세마포어를 기다리는 동안 차단 상태로 전환될 수 있으므로, 지속적인 폴링(Polling)으로 프로세서 시간을 소비하지 않고 스케줄러가 다른 준비 상태의 태스크를 실행하도록 할 수 있다.

뮤텍스(Mutex)는 여러 태스크가 동시에 안전하게 사용할 수 없는 공유 자원(Shared Resource)에 접근할 때 상호 배제(Mutual Exclusion)를 제공한다. 뮤텍스를 획득한 태스크만 보호된 임계 구역(Critical Section)에 진입할 수 있으며, 다른 태스크는 뮤텍스가 해제될 때까지 기다려야 한다. 단순한 신호 메커니즘과 달리 뮤텍스 설계는 태스크 소유권(Task Ownership) 및 우선순위 관리(Priority Management)와 밀접하게 관련되며, 잘못 설계된 잠금(Locking)은 실시간 스케줄링 동작을 방해할 수 있다.

이벤트 객체(Event Object)는 상당한 양의 데이터를 전달하는 것이 아니라 하나 이상의 시스템 조건(System Condition)에 따라 태스크 실행 여부를 결정할 때 효율적인 동기화 방법을 제공한다. 이벤트는 센서 초기화 완료, 통신 사용 가능, 안전 상태 변화 또는 여러 서브시스템 상태가 동시에 유효해졌음을 나타낼 수 있다. 따라서 이벤트 플래그(Event Flag)나 이벤트 그룹(Event Group)은 여러 조건의 조합을 표현하고, 의미 있는 실행 조건이 충족될 때까지 태스크를 차단 상태로 유지할 수 있다.

RTOS 타이머(RTOS Timer)는 각 애플리케이션 태스크가 자체적인 시간 관리 메커니즘을 구현하지 않아도 되도록 시간 기반 커널 서비스(Time-Based Kernel Service)를 제공한다. 주기적 타이머(Periodic Timer)는 반복 작업을 시작할 수 있고, 원샷 타이머(One-Shot Timer)는 지정된 시간이 지난 후 이벤트를 발생시킬 수 있다. 타이머는 통신 타임아웃(Communication Timeout), 감시 기능(Supervision Function), 주기적 유지관리 및 상태 머신 전이(State-Machine Transition)에 유용하지만, 콜백(Callback)과 관련 처리는 스케줄러나 커널에 과도한 부하를 발생시키지 않도록 신중하게 설계해야 한다.

이러한 커널 객체(Kernel Object)들은 서로 독립적으로 동작하는 것이 아니라 상호작용한다. 하드웨어 인터럽트가 세마포어(Semaphore)를 해제하면 제어 태스크가 차단 상태에서 준비 상태로 변경될 수 있다. 스케줄러는 이 태스크의 우선순위가 현재 실행 중인 통신 태스크보다 높다고 판단하여 문맥 교환(Context Switch)을 수행할 수 있다. 이후 제어 태스크는 동기화로 보호되는 공유 센서 데이터를 읽고 출력을 계산한 다음 명령을 큐에 저장하고 다음 이벤트를 기다리면서 다시 차단 상태로 전환될 수 있다.

따라서 RTOS 아키텍처의 효율성은 커널 서비스(Kernel Service), 스케줄링 정책(Scheduling Policy), IPC 메커니즘, 동기화 객체(Synchronization Object)를 어떻게 조합하는지에 따라 결정된다. 지나치게 많은 태스크는 스케줄링 및 문맥 교환 오버헤드를 증가시키고, 부적절한 큐 사용은 불필요한 지연시간(Latency)을 발생시키며, 잘못 설계된 잠금은 블로킹(Blocking)이나 우선순위 문제를 유발한다. 잘 구조화된 설계는 각 태스크에 명확한 책임을 부여하고 시간 요구사항, 데이터 소유권(Data Ownership), 동시성 및 자원 공유 요구사항에 따라 적절한 통신 객체를 선택한다.

로봇 시스템에서 이러한 RTOS 핵심 구성 요소(Core RTOS Components)는 소프트웨어 동시성(Software Concurrency)과 결정적인 물리 제어(Deterministic Physical Control)를 연결하는 기반을 형성한다. 커널(Kernel)은 제어된 실행 환경을 제공하고, 스케줄러(Scheduler)는 연산이 언제 수행될지를 결정하며, IPC는 실행 활동 사이에서 정보를 전달하고, 동기화 객체는 올바른 협조 관계를 유지한다. 이러한 관계를 이해하는 것은 이후 다루게 될 선점형 스케줄링(Preemptive Scheduling), 우선순위 역전(Priority Inversion), 태스크 상태 전이(Task-State Transition), 메모리 관리(Memory Management), 실시간 네트워킹(Real-Time Networking), 고주파 로봇 제어(High-Frequency Robot Control)를 이해하기 위한 기반이 된다.

## 01.03 Preemptive vs Non-Preemptive Scheduling Principles

![](images/image3.png){width="7.268055555555556in" height="7.268055555555556in"}

선점형 스케줄링(Preemptive Scheduling)과 비선점형 스케줄링(Non-Preemptive Scheduling)은 경쟁하는 여러 태스크(Task) 사이에서 RTOS가 프로세서 소유권(Processor Ownership)을 제어하는 두 가지 기본적인 방식을 정의한다. 두 방식 모두 실행 중인 태스크가 언제 CPU를 양보해야 하는지, 그리고 다른 태스크가 언제 실행을 시작할 수 있는지를 결정한다. 이들의 차이는 응답성(Responsiveness), 구현 복잡도, 문맥 교환(Context Switching), 자원 보호(Resource Protection), 그리고 변화하는 워크로드에서 실시간 시스템이 데드라인(Deadline)을 만족할 수 있는 능력에 직접적인 영향을 미친다.

선점형 스케줄링(Preemptive Scheduling)에서는 더 높은 스케줄링 우선순위(Scheduling Priority)를 가진 다른 태스크가 준비 상태(Ready)가 되면 RTOS 스케줄러(Scheduler)가 현재 실행 중인 태스크를 중단할 수 있다. 중단되는 태스크가 현재 작업을 자발적으로 완료할 필요는 없다. 대신 커널(Kernel)이 실행 문맥(Execution Context)을 저장하고 프로세서 제어권을 높은 우선순위 태스크로 넘긴 뒤, 나중에 중단된 태스크의 상태를 복원하여 중단된 지점부터 실행을 계속하도록 한다.

이러한 메커니즘은 서로 다른 소프트웨어 작업들이 상당히 다른 시간 요구사항(Timing Requirements)을 가질 때 특히 유용하다. 낮은 우선순위의 진단(Diagnostics) 또는 로깅(Logging) 태스크가 실행되는 동안 긴급한 모터 제어(Motor Control), 안전 모니터링(Safety Monitoring), 통신(Communication) 태스크가 준비 상태가 될 수 있다. 선점(Preemption)을 사용하면 긴급 태스크가 낮은 우선순위 작업의 완료를 기다리지 않고 신속하게 CPU 시간을 확보할 수 있으므로 시간 중요 기능(Time-Critical Function)의 응답 지연시간(Response Latency)을 줄일 수 있다.

따라서 선점형 스케줄링(Preemptive Scheduling)은 우선순위 기반 실시간 시스템 설계(Priority-Based Real-Time System Design)와 밀접하게 관련된다. 스케줄러는 설정된 스케줄링 정책(Scheduling Policy)에 따라 준비 상태의 태스크 가운데 어떤 태스크가 프로세서 시간을 가장 우선적으로 사용해야 하는지를 지속적으로 판단한다. 인터럽트(Interrupt), 타이머(Timer), 세마포어(Semaphore), 메시지(Message) 또는 기타 커널 이벤트(Kernel Event)에 의해 태스크의 준비 상태가 변경되면 스케줄러는 즉시 프로세서 소유권을 재평가하고, 더 높은 우선순위 태스크가 실행되어야 할 경우 문맥 교환(Context Switch)을 수행할 수 있다.

선점(Preemption)의 주요 장점은 응답성(Responsiveness)이다. 높은 우선순위 태스크는 낮은 우선순위 소프트웨어가 프로세서 시간을 사용하고 있는 상황에서도 외부 이벤트와 주기적 데드라인(Periodic Deadline)에 신속하게 대응할 수 있다. 이러한 특성은 로봇 제어 루프(Robot Control Loop), 액추에이터 감시(Actuator Supervision), 비상 처리(Emergency Handling), 동기화 센싱(Synchronized Sensing)에 중요하다. 그러나 블로킹(Blocking), 과도한 실행시간, 부적절한 우선순위 설정으로 인해 여전히 데드라인을 놓칠 수 있으므로 높은 응답성이 자동으로 올바른 실시간 동작을 보장하는 것은 아니다.

선점(Preemption)은 추가적인 복잡성도 발생시킨다. 태스크가 실행 도중 여러 지점에서 중단될 수 있으므로 공유 데이터(Shared Data)와 공유 자원(Shared Resource)은 동시 접근(Concurrent Access)으로부터 보호되어야 한다. 하나의 태스크가 데이터 구조(Data Structure)를 수정하는 도중 중단되고 다른 태스크가 불완전하게 갱신된 데이터에 접근할 수 있기 때문이다. 따라서 뮤텍스(Mutex), 임계 구역(Critical Section), 원자적 연산(Atomic Operation), 명확하게 설계된 소유권 규칙(Ownership Rule)은 신뢰성 있는 선점형 소프트웨어 아키텍처의 중요한 요소이다.

빈번한 선점은 문맥 교환 오버헤드(Context-Switching Overhead)를 증가시키고 캐시 동작(Cache Behavior)을 방해할 수도 있다. 프로세서 레지스터(Processor Register)의 저장과 복원, 스택(Stack) 전환, 메모리 작업 집합(Memory Working Set)의 변경, 캐시 내용(Cache Contents)의 재적재에는 모두 실행시간이 필요하다. 각각의 문맥 교환이 일반적으로 짧더라도 지나치게 빈번한 전환은 사용 가능한 CPU 처리 능력을 감소시키고 시간 변동(Timing Variation)을 발생시킬 수 있다. 따라서 RTOS 설계자는 빠른 응답성과 불필요한 스케줄링 동작 사이에서 균형을 유지해야 한다.

비선점형 스케줄링(Non-Preemptive Scheduling)에서는 현재 실행 중인 태스크가 자발적으로 실행권을 반환하거나, 이벤트 또는 자원을 기다리면서 차단(Block)되거나, 현재 작업 단위를 완료할 때까지 프로세서 제어권을 유지한다. 더 높은 우선순위의 태스크가 새롭게 준비 상태가 되더라도 현재 실행 중인 태스크를 자동으로 중단할 수 없다. 높은 우선순위 태스크는 현재 태스크가 프로세서 제어권을 커널에 반환할 수 있는 스케줄링 지점(Scheduling Point)에 도달할 때까지 기다려야 한다.

이러한 협력적 실행 모델(Cooperative Execution Model)은 일반적인 태스크 실행 도중 임의적인 중단이 발생하지 않기 때문에 공유 자원(Shared Resource)에 대한 동작을 보다 단순하게 분석할 수 있다. 소프트웨어가 신중하게 구조화되어 있다면 일부 공유 데이터 작업은 다른 태스크가 실행 도중 예기치 않게 개입하지 않은 상태에서 완료될 수 있다. 또한 문맥 교환 횟수가 감소하여 비교적 단순한 실행 순서(Execution Sequence)를 만들고 스케줄링 오버헤드(Scheduling Overhead)를 줄일 가능성도 있다.

비선점형 스케줄링(Non-Preemptive Scheduling)의 주요 한계는 응답 지연시간(Response Latency)이다. 높은 우선순위 태스크가 준비 상태가 되더라도 낮은 우선순위 태스크가 여전히 프로세서를 소유하고 있기 때문에 실행하지 못할 수 있다. 따라서 최악 조건 대기시간(Worst-Case Waiting Time)은 가장 긴 비선점 실행 구간(Non-Preemptive Execution Section)의 길이에 크게 좌우된다. 하나의 태스크가 실행권을 양보하거나 차단되지 않은 상태에서 장시간 계산을 수행하면 우선순위와 관계없이 다른 모든 태스크에 상당한 지연이 발생할 수 있다.

이러한 이유로 비선점형 시스템(Non-Preemptive System)은 엄격하게 관리된 태스크 설계를 요구한다. 실행 구간은 다른 작업의 데드라인에 비해 충분히 짧고 제한된 시간(Bounded Time)을 가져야 한다. 긴 반복문(Long Loop), 블로킹 장치 연산(Blocking Device Operation), 예측하기 어려운 계산, 협력적 태스크 내부의 통제되지 않은 처리는 시스템 응답성을 저하시킬 수 있다. 따라서 비선점형 스케줄링의 단순성은 애플리케이션 동작 자체가 적절한 스케줄링 기회(Scheduling Opportunity)를 제공할 때에만 확보될 수 있다.

두 방식의 차이는 긴급 이벤트(Urgent Event)가 발생하는 상황을 통해 이해할 수 있다. 백그라운드 태스크(Background Task)가 진단 정보를 처리하는 동안 모터 고장(Motor Fault)이 발생했다고 가정할 수 있다. 선점형 스케줄링에서는 인터럽트가 안전 태스크(Safety Task)를 준비 상태로 만들고, 이후 스케줄러가 백그라운드 태스크를 중단하여 안전 기능을 즉시 실행할 수 있다. 반면 비선점형 스케줄링에서는 백그라운드 태스크가 프로세서를 반환할 때까지 안전 태스크가 기다려야 한다.

어느 한 방식이 모든 경우에서 항상 우수하다고 해석해서는 안 된다. 선점형 스케줄링(Preemptive Scheduling)은 일반적으로 더 높은 응답성을 제공하며 서로 다른 중요도(Criticality)와 데드라인을 가진 태스크가 포함된 시스템에 널리 적합하다. 비선점형 스케줄링은 워크로드가 작고 실행시간이 엄격하게 제한되며 협력적 태스크 구조(Cooperative Task Structure)를 적용할 수 있을 때 보다 단순한 실행 동작을 제공할 수 있다. 일부 시스템에서는 일반적으로 선점을 허용하면서 특정한 짧은 실행 영역만 보호함으로써 두 가지 원리를 결합하기도 한다.

임계 구역(Critical Section)은 이러한 혼합형 동작(Hybrid Behavior)을 보여주는 대표적인 사례이다. 선점형 RTOS에서도 소프트웨어가 하드웨어 레지스터(Hardware Register)나 매우 민감한 공유 상태(Shared State)를 조작하는 동안 특정 형태의 선점을 일시적으로 방지할 수 있다. 스케줄링이나 인터럽트를 비활성화하는 것은 실질적으로 비선점형 블로킹(Non-Preemptive Blocking)을 발생시키므로 이러한 영역은 매우 짧게 유지되어야 한다. 따라서 보호 구간의 지속시간은 높은 우선순위 작업이 경험하는 최악 조건 응답시간(Worst-Case Response Time)에 직접적으로 영향을 미친다.

결과적으로 스케줄링 설계(Scheduling Design)는 단순히 명목상의 스케줄링 방식만을 고려해서는 안 된다. 태스크 우선순위(Task Priority), 실행시간(Execution Time), 블로킹 구간(Blocking Interval), 인터럽트 동작(Interrupt Behavior), 동기화 메커니즘(Synchronization Mechanism), 문맥 교환 오버헤드, 자원 의존성(Resource Dependency)이 함께 실제 실시간 성능을 결정한다. 잠금(Locking)이 잘못 설계된 선점형 커널은 예상보다 낮은 성능을 보일 수 있는 반면, 실행시간이 신중하게 제한된 협력형 설계(Cooperative Design)는 비교적 단순한 임베디드 애플리케이션에서 충분한 결정성(Determinism)을 제공할 수 있다.

로봇 시스템(Robotic System)은 서로 다른 긴급도를 가진 모터 제어, 센싱(Sensing), 통신, 진단, 안전 기능을 동시에 실행하기 때문에 시간 중요 제어(Time-Critical Control)에 일반적으로 선점형 스케줄링을 선호한다. 고주파 제어(High-Frequency Control)와 안전 태스크에는 높은 우선순위를 부여하고, 텔레메트리(Telemetry)나 로깅은 낮은 우선순위에서 실행할 수 있다. 그러나 낮은 계층의 처리가 긴 블로킹 경로(Long Blocking Path)를 발생시켜 의도된 우선순위 구조(Priority Structure)를 무력화하지 않도록 아키텍처를 설계해야 한다.

선점형 스케줄링(Preemptive Scheduling)과 비선점형 스케줄링(Non-Preemptive Scheduling)을 이해하는 것은 보다 발전된 RTOS 스케줄링 주제를 이해하기 위한 개념적 기반을 제공한다. 우선순위 기반 스케줄링(Priority-Based Scheduling), 비율 단조 스케줄링(Rate-Monotonic Scheduling), 최조기 마감시간 우선(EDF, Earliest-Deadline-First), 우선순위 역전(Priority Inversion), 뮤텍스 프로토콜(Mutex Protocol), 태스크 상태 전이(Task-State Transition), 인터럽트 처리(Interrupt Handling), 응답시간 분석(Response-Time Analysis)은 모두 프로세서 소유권이 어떻게 변경될 수 있는지와 관련된다. 따라서 스케줄링 모델(Scheduling Model)은 소프트웨어 동시성(Software Concurrency)과 실시간 로봇 시스템의 예측 가능한 물리적 동작(Predictable Physical Behavior)을 연결하는 핵심적인 아키텍처 결정이 된다.

## 01.04 Priority-Based Scheduling: RM / EDF Theory

![](images/image4.png){width="7.268055555555556in" height="7.268055555555556in"}

우선순위 기반 스케줄링(Priority-Based Scheduling)은 준비 상태(Ready)의 태스크(Task) 가운데 어떤 태스크에 프로세서 시간을 할당할지를 결정하는 RTOS의 기본 메커니즘이다. 각 태스크에는 스케줄링 중요도를 나타내는 우선순위(Priority)가 할당되며, 선점형 커널(Preemptive Kernel)은 일반적으로 준비 상태에 있는 태스크 중 가장 높은 우선순위의 태스크를 실행한다. 따라서 핵심 설계 문제는 단순히 숫자로 우선순위를 부여하는 것이 아니라, 모든 시간 중요 태스크(Time-Critical Task)가 자신의 주기(Period)와 데드라인(Deadline)을 예측 가능하게 만족하도록 하는 우선순위 정책(Priority Policy)을 결정하는 것이다.

실시간 스케줄링 이론(Real-Time Scheduling Theory)에서는 일반적으로 주기적 태스크(Periodic Task)를 실행시간(Execution Time), 주기(Period), 데드라인(Deadline)을 이용하여 모델링한다. 실행시간은 한 번의 활성화에 필요한 프로세서 시간을 의미하고, 주기는 태스크가 얼마나 자주 활성화되는지를 정의하며, 데드라인은 해당 활성화가 언제까지 완료되어야 하는지를 지정한다. 스케줄링 분석(Scheduling Analysis)은 이러한 여러 태스크가 하나의 프로세서를 공유하면서 요구되는 모든 데드라인을 만족할 수 있는지를 평가한다.

프로세서 이용률(Processor Utilization)은 스케줄링 부하(Scheduling Demand)를 나타내는 기본적인 지표이다. 주기적 태스크의 이용률은 해당 태스크의 실행시간이 주기에 비해 차지하는 프로세서 용량의 비율로 이해할 수 있다. 전체 이용률(Total Utilization)은 스케줄링되는 모든 태스크를 함께 고려하여 계산한다. 평균적으로 프로세서 부하가 낮아 보이더라도 여러 태스크가 동시에 준비 상태가 되거나 우선순위 관계에 의해 과도한 간섭(Interference)이 발생하면 데드라인을 놓칠 수 있다.

비율 단조 스케줄링(RM, Rate Monotonic Scheduling)은 주로 주기적 실시간 태스크(Periodic Real-Time Task)를 위해 설계된 고정 우선순위 스케줄링(Fixed-Priority Scheduling) 정책이다. 태스크의 주기에 따라 우선순위를 할당하며, 주기가 짧은 태스크에는 높은 우선순위를 부여하고 주기가 긴 태스크에는 낮은 우선순위를 부여한다. 한번 할당된 우선순위는 일반적으로 동작 중에 변경되지 않으므로 RM은 개념적으로 단순하며 예측 가능한 주기적 제어 워크로드(Periodic Control Workload)에 적합하다.

RM의 기본적인 논리는 자주 실행되는 태스크일수록 연속된 활성화 사이의 시간이 짧기 때문에 신속하게 프로세서에 접근해야 한다는 것이다. 예를 들어 1ms 모터 제어 태스크(Motor-Control Task)는 10ms 센서 처리 태스크(Sensor-Processing Task)보다 높은 우선순위를 가지며, 10ms 센서 처리 태스크는 다시 100ms 진단 태스크(Diagnostic Task)보다 높은 우선순위를 가진다. 여러 태스크가 동시에 준비 상태가 되면 스케줄러는 이러한 고정된 우선순위 순서에 따라 가장 높은 우선순위의 태스크를 실행한다.

고전적인 RM 이론(Classical RM Theory)은 특정한 가정 아래에서 독립적인 주기적 태스크 집합의 스케줄 가능성(Schedulability)을 보장할 수 있는 충분 이용률 한계(Sufficient Utilization Bound)를 제공한다. 태스크 수가 증가함에 따라 이 보수적인 한계는 약 69.3%의 프로세서 이용률에 접근한다. 이 한계를 초과한다고 해서 자동으로 데드라인을 놓친다는 의미는 아니며, 단순한 이용률 테스트(Utilization Test)만으로 더 이상 스케줄 가능성을 보장할 수 없다는 의미이다.

따라서 보다 정확한 분석에서는 RM 이용률 한계에만 의존하지 않고 응답시간 분석(Response-Time Analysis)을 사용할 수 있다. 태스크의 최악 조건 응답시간(Worst-Case Response Time)은 자신의 실행시간뿐만 아니라 대기하거나 실행되는 동안 높은 우선순위 태스크가 실행함으로써 발생하는 간섭도 포함한다. 공유 자원(Shared Resource)에 의한 블로킹(Blocking) 역시 포함해야 할 수 있다. 계산된 응답시간을 태스크의 데드라인과 비교하여 해당 태스크의 스케줄 가능 여부를 판단한다.

최조기 마감시간 우선(EDF, Earliest Deadline First)은 이와 다른 원리를 따른다. 태스크 주기에 따라 우선순위를 영구적으로 할당하는 대신, EDF는 절대 데드라인(Absolute Deadline)이 가장 가까운 준비 상태 태스크에 가장 높은 스케줄링 우선순위를 동적으로 부여한다. 시간이 흐르고 새로운 태스크 인스턴스(Task Instance)가 활성화되면 우선순위 관계도 변경될 수 있다. 따라서 스케줄러는 데드라인을 지속적으로 평가하고 가장 긴급하게 완료되어야 하는 태스크를 선택한다.

예를 들어 준비 상태에 있는 세 개의 작업(Job)이 각각 2ms, 5ms, 10ms 후에 데드라인을 가진다고 가정하면 EDF는 태스크의 명목상 주기(Nominal Period)나 사전에 정의된 정적 우선순위(Static Priority)와 관계없이 2ms 데드라인을 가진 작업을 먼저 선택한다. 해당 작업이 완료되거나 더 빠른 데드라인을 가진 새로운 작업이 도착하면 스케줄러는 준비 상태 집합(Ready Set)을 다시 평가한다. 이러한 동적 동작을 통해 실제 데드라인의 긴급도에 따라 프로세서 시간을 할당할 수 있다.

고전적인 이상적 단일 프로세서 모델(Classical Idealized Uniprocessor Model)에서 EDF는 독립적이고 선점 가능한 주기적 태스크(Preemptible Periodic Task)의 데드라인이 각 태스크의 주기와 동일한 경우, 전체 이용률이 100%를 초과하지 않는 범위에서 스케줄링할 수 있다. 따라서 EDF는 단순한 RM 충분 이용률 한계보다 이론적으로 높은 프로세서 이용 능력을 제공한다. 그러나 실제 시스템에서는 인터럽트 오버헤드(Interrupt Overhead), 문맥 교환(Context Switch), 블로킹, 릴리스 지터(Release Jitter), 통신 지연, 실행시간 변동 등이 발생하므로 실제 사용할 수 있는 처리 용량은 감소한다.

따라서 RM과 EDF는 서로 다른 두 가지 우선순위 철학(Priority Philosophy)을 나타낸다. RM은 태스크 실행 빈도(Task Frequency)를 통해 긴급도를 간접적으로 판단하며 검사와 구현이 비교적 간단한 고정 우선순위 구조를 만든다. EDF는 절대 데드라인을 통해 긴급도를 직접 판단하고 우선순위를 지속적으로 변경한다. 일반적으로 RM은 보다 단순한 동작 특성을 제공하는 반면, EDF는 워크로드가 스케줄링 가정을 만족하는 경우 프로세서 용량을 더욱 효율적으로 사용할 수 있다.

과부하(Overload) 상황에서의 예측 가능성도 중요한 차이점이다. 고정 우선순위 RM 시스템에서는 프로세서 요구량이 일시적으로 과도해질 경우에도 높은 우선순위 태스크가 상대적으로 보호되고 낮은 우선순위 태스크부터 실패하는 경향이 있다. EDF는 실행 가능한 워크로드에서 높은 이용 효율을 달성할 수 있지만, 과부하가 발생하면 별도의 수락 제어(Admission Control) 또는 과부하 관리(Overload Management) 메커니즘이 없는 경우 여러 태스크에서 데드라인 실패가 발생할 수 있다.

스케줄링 이론(Scheduling Theory)은 적용되는 가정에 크게 의존한다. 고전적인 RM 및 EDF 결과는 일반적으로 독립적인 태스크, 알려진 실행시간, 무시할 수 있는 스케줄링 오버헤드, 예측 가능한 활성화 패턴(Release Pattern), 제한되거나 존재하지 않는 자원 블로킹(Resource Blocking)을 가정한다. 그러나 실제 임베디드 소프트웨어에서는 뮤텍스(Mutex), 인터럽트, 공유 버스(Shared Bus), DMA, 통신 스택(Communication Stack), 캐시 동작(Cache Behavior), 가변 실행 경로(Variable Execution Path) 등에 의해 이러한 가정 중 일부가 성립하지 않을 수 있다. 따라서 이론적 분석은 실제 구현을 고려한 시간 분석(Timing Analysis)과 함께 수행되어야 한다.

우선순위 할당(Priority Assignment)은 동기화(Synchronization)와의 상호작용도 고려해야 한다. 높은 우선순위 태스크가 필요한 뮤텍스 또는 공유 자원을 낮은 우선순위 태스크가 소유하고 있기 때문에 차단될 수 있으며, 이로 인해 우선순위 역전(Priority Inversion)이 발생한다. 따라서 이론적으로 올바른 우선순위 순서만으로 예상된 응답시간을 보장할 수는 없다. 스케줄링과 동기화가 상호작용하는 경우 우선순위 상속(Priority Inheritance), 우선순위 상한(Priority Ceiling), 제한된 임계 구역(Bounded Critical Section), 신중한 자원 소유권(Resource Ownership) 설계가 필요하다.

로봇 시스템(Robotic System)은 자연스럽게 우선순위 기반 분석(Priority-Based Analysis)에 적합한 워크로드를 포함한다. 모터 제어 루프(Motor-Control Loop)는 1kHz로 실행되고, 상태 추정(State Estimation)은 수백 Hz, 통신은 수십 Hz, 진단은 이보다 훨씬 낮은 주파수에서 실행될 수 있다. RM은 이러한 주기를 고정 우선순위에 직관적으로 대응시킬 수 있으며, EDF는 태스크마다 다양하거나 변화하는 데드라인을 가지면서 효율적인 프로세서 이용이 특히 중요한 경우 매력적인 선택이 될 수 있다.

실제 RTOS 엔지니어링에서 스케줄링 정책(Scheduling Policy)의 선택은 단순한 이론적 이용률만을 기준으로 하지 않는다. 설계자는 태스크 주기, 데드라인, 최악 조건 실행시간(WCET, Worst-Case Execution Time), 블로킹 시간(Blocking Time), 인터럽트 지연시간(Interrupt Latency), 릴리스 지터, 문맥 교환 오버헤드, 과부하 동작(Overload Behavior), 인증 요구사항(Certification Requirements)을 함께 평가한다. 이후 대상 하드웨어(Target Hardware)에서 시간 특성을 측정하여 스케줄 가능성 분석에서 사용한 가정이 실제 운용 조건에서도 유효한지를 검증한다.

우선순위 기반 스케줄링(Priority-Based Scheduling), RM, EDF는 태스크의 시간 요구사항과 프로세서 자원 할당(Processor Allocation)을 연결하는 이론적 기반을 제공한다. RM은 고정 우선순위를 통해 주기적 시간 요구사항을 예측 가능한 실행 계층(Execution Hierarchy)으로 변환하는 방법을 보여주며, EDF는 동적인 데드라인 정보를 이용하여 효율적인 스케줄링 결정을 수행하는 방법을 보여준다. 이러한 원리는 이후 다루게 될 우선순위 역전 분석(Priority Inversion Analysis), 응답시간 계산(Response-Time Calculation), 혼합 중요도 스케줄링(Mixed-Criticality Scheduling), 결정적 로봇 제어 아키텍처(Deterministic Robot Control Architecture)의 기반을 형성한다.

## 01.05 Priority Inversion and Solutions: PIP / PCP

![](images/image5.png){width="7.268055555555556in" height="7.268055555555556in"}

우선순위 역전(Priority Inversion)은 높은 우선순위 태스크(High-Priority Task)가 동일한 공유 자원(Shared Resource)에 의존하는 낮은 우선순위 태스크(Low-Priority Task)에 의해 간접적으로 지연되는 스케줄링 상태(Scheduling Condition)를 의미한다. 우선순위 기반 스케줄링(Priority-Based Scheduling)은 긴급한 태스크에 빠른 프로세서 접근을 제공하도록 설계되지만, 동기화(Synchronization)로 인해 실제 실행 순서가 일시적으로 역전될 수 있다. 적절한 보호가 없으면 이러한 현상은 최악 조건 응답시간(Worst-Case Response Time)을 증가시키고 실시간 데드라인(Real-Time Deadline) 보장을 위협할 수 있다.

높음(High), 중간(Medium), 낮음(Low)의 세 가지 우선순위를 가진 태스크를 생각해 볼 수 있다. 먼저 낮은 우선순위 태스크가 공유 자원을 보호하는 뮤텍스(Mutex)를 획득한다. 이후 높은 우선순위 태스크가 준비 상태(Ready)가 되어 낮은 우선순위 태스크를 선점(Preempt)하지만, 동일한 뮤텍스를 요청하면서 해당 자원이 여전히 낮은 우선순위 태스크에 의해 소유되고 있기 때문에 차단(Blocked)된다. 이 시점에서 높은 우선순위 태스크는 낮은 우선순위 태스크가 자원을 해제할 때까지 실행을 계속할 수 없다.

이러한 상황에서 중간 우선순위 태스크가 준비 상태가 되면 문제가 더욱 심각해진다. 중간 우선순위 태스크는 낮은 우선순위 태스크보다 우선순위가 높기 때문에, 낮은 우선순위 태스크가 높은 우선순위 태스크에 필요한 자원을 보유하고 있음에도 이를 선점할 수 있다. 결과적으로 중간 우선순위 태스크가 낮은 우선순위의 자원 소유자(Resource Owner)를 지연시키고, 이는 다시 높은 우선순위 태스크를 간접적으로 지연시킨다. 따라서 의도했던 우선순위 실행 순서(Priority Ordering)가 실질적으로 역전된다.

이러한 형태의 블로킹(Blocking)은 여러 중간 우선순위 작업이 자원을 소유하고 있는 낮은 우선순위 태스크를 반복적으로 선점하는 경우 특히 위험해질 수 있다. 높은 우선순위 태스크는 임계 구역(Critical Section) 자체의 실행시간보다 훨씬 오랫동안 차단된 상태로 남을 수 있다. 제대로 제어되지 않는 시스템에서는 이러한 블로킹 구간(Blocking Interval)의 상한을 결정하기 어려워져 응답시간 분석(Response-Time Analysis)의 신뢰성이 저하되고 중요한 데드라인을 놓칠 가능성이 발생한다.

우선순위 역전(Priority Inversion)이 발생한다고 해서 뮤텍스(Mutex)나 공유 자원(Shared Resource)을 완전히 피해야 한다는 의미는 아니다. 임베디드 시스템(Embedded System)에서는 여러 태스크가 공통 통신 인터페이스(Common Communication Interface), 센서 버퍼(Sensor Buffer), 디바이스 드라이버(Device Driver), 메모리 구조(Memory Structure), 하드웨어 주변장치(Hardware Peripheral)를 사용해야 하므로 자원 공유가 불가피한 경우가 많다. 따라서 엔지니어링의 목적은 상호 배제(Mutual Exclusion)가 제공하는 논리적 정확성을 유지하면서 블로킹을 예측 가능하고 제한된 형태로 만드는 것이다.

우선순위 상속 프로토콜(PIP, Priority Inheritance Protocol)은 높은 우선순위 태스크가 필요로 하는 자원을 낮은 우선순위 태스크가 보유하고 있을 때 해당 낮은 우선순위 태스크의 우선순위를 일시적으로 높이는 방법으로 이 문제를 해결한다. 앞의 예에서 높은 우선순위 태스크가 뮤텍스를 기다리며 차단되면 낮은 우선순위의 자원 소유자가 높은 우선순위를 상속(Inherit)한다. 이를 통해 필요한 자원을 해제할 때까지 관련 없는 중간 우선순위 태스크에 의해 선점되지 않고 계속 실행할 수 있다.

낮은 우선순위 태스크가 뮤텍스를 해제하면 상속된 우선순위(Inherited Priority)는 제거되고 원래의 우선순위가 복원된다. 이후 차단되어 있던 높은 우선순위 태스크가 해당 자원을 획득하고 실행을 계속할 수 있다. 따라서 PIP는 중간 우선순위 태스크의 간섭이 우선순위 역전 구간(Inversion Interval)을 불필요하게 연장하는 것을 방지한다. 즉, 낮은 우선순위 태스크가 자신의 임계 구역을 완료해야 높은 우선순위 태스크가 실행을 계속할 수 있으므로 일시적으로 더 높은 긴급도를 부여하는 것이다.

PIP는 실제 시스템의 동작을 크게 개선하지만 모든 동기화 문제를 제거하는 것은 아니다. 하나의 태스크가 여러 자원을 동시에 보유할 수 있고, 우선순위 상속이 서로 차단된 태스크들의 체인(Chain)을 따라 전파될 수도 있으며, 중첩 잠금(Nested Locking)은 복잡한 우선순위 관계를 발생시킬 수 있다. 잠금 구조(Locking Structure)에 따라 교착상태(Deadlock)와 반복적인 블로킹이 여전히 발생할 수 있다. 따라서 구현에서는 자원 의존성이 변경될 때 상속된 우선순위를 정확하게 관리하고 복원해야 한다.

우선순위 상한 프로토콜(PCP, Priority Ceiling Protocol)은 보다 예방적인 접근방법을 사용한다. 각각의 공유 자원에는 해당 자원에 접근할 수 있는 모든 태스크 가운데 가장 높은 우선순위와 관련된 우선순위 상한(Priority Ceiling)이 설정된다. 태스크가 자원을 획득하려고 할 때 프로토콜은 이러한 상한을 이용하여 위험한 블로킹 관계를 발생시킬 수 있는 잠금 상황을 제한한다. 즉, 우선순위 역전이 발생한 이후 대응하는 것뿐만 아니라 알려진 우선순위 관계에 따라 자원 접근 자체를 제한한다.

우선순위 상한(Priority Ceiling) 개념을 사용하면 문제가 되는 자원 의존성이 발생하기 전에 시스템이 동기화 관계를 판단할 수 있다. 보호된 임계 구역에 진입하는 태스크는 해당 자원의 상한에서 결정되는 규칙에 따라 실행될 수 있으며, 충돌할 가능성이 있는 특정 자원을 다른 태스크가 획득하지 못하도록 제한할 수 있다. 이를 통해 연쇄 블로킹(Chained Blocking)을 감소시키고 높은 우선순위 태스크가 낮은 우선순위의 자원 소유자에 의해 지연될 수 있는 시간을 보다 강하게 제한할 수 있다.

상한 기반 프로토콜(Ceiling-Based Protocol)의 중요한 장점 중 하나는 교착상태 방지(Deadlock Prevention)와의 관계이다. 여러 태스크가 서로 다른 순서로 여러 자원을 획득하고 상대방이 보유한 자원을 순환적으로 기다리면 교착상태가 발생할 수 있다. PCP는 우선순위 상한과 현재 시스템 상태에 따라 자원을 잠글 수 있는 시점을 제한함으로써 일반적인 뮤텍스 잠금이나 기본적인 우선순위 상속만으로는 본질적으로 제거할 수 없는 많은 순환 대기(Circular Wait) 상황을 방지할 수 있다.

따라서 PIP와 PCP는 동일한 근본 문제를 서로 다른 방향에서 해결한다. PIP는 주로 반응형(Reactive) 방식으로, 높은 우선순위 태스크가 차단되면 자원 소유자가 필요한 우선순위를 상속받아 신속하게 실행을 완료하도록 한다. PCP는 보다 예방형(Preventive) 방식으로, 문제가 되는 간섭이 발생하기 전에 자원 상한(Resource Ceiling)과 획득 규칙(Acquisition Rule)을 통해 잠금 동작을 제한한다. 두 메커니즘 모두 통제되지 않는 우선순위 역전을 예측 가능하고 제한된 블로킹(Bounded Blocking)으로 변환하는 것을 목표로 한다.

어느 프로토콜을 사용하더라도 그 효과는 임계 구역 설계(Critical-Section Design)에 크게 좌우된다. 낮은 우선순위 태스크가 뮤텍스를 보유한 상태에서 장시간의 계산, 네트워크 통신(Network Communication), 저장장치 접근(Storage Access), 또는 예측하기 어려운 작업을 수행하면 여전히 상당한 블로킹이 발생할 수 있다. 따라서 임계 구역은 짧고 결정적(Deterministic)이어야 하며 실제로 상호 배제가 필요한 연산으로 제한해야 한다. 비용이 큰 처리는 가능한 경우 보호 영역(Protected Region) 외부에서 수행하는 것이 바람직하다.

응답시간 분석(Response-Time Analysis)을 통해 데드라인 만족 여부를 판단할 때는 동기화 블로킹(Synchronization Blocking)을 반드시 포함해야 한다. 높은 우선순위 태스크의 응답시간은 자신의 실행시간과 더 높은 우선순위 태스크의 간섭뿐만 아니라 필요한 자원을 보유한 낮은 우선순위 태스크에 의해 발생할 수 있는 최대 블로킹(Maximum Blocking)의 영향도 받는다. PIP 또는 PCP를 사용하면 이러한 블로킹을 보다 구조화할 수 있으므로 설계자가 스케줄 가능성 분석(Schedulability Analysis)에 필요한 의미 있는 최악 조건 한계(Worst-Case Bound)를 설정할 수 있다.

로봇 시스템(Robotic System)에는 우선순위 역전을 발생시킬 수 있는 공유 자원이 자주 존재한다. 낮은 우선순위의 로깅(Logging) 또는 통신 태스크가 버스 인터페이스(Bus Interface), 센서 데이터 구조(Sensor Structure), 드라이버 잠금(Driver Lock)을 보유하고 있는 동안 높은 우선순위의 모터 제어(Motor Control) 또는 안전 태스크(Safety Task)가 동일한 자원을 요구할 수 있다. 이때 중간 우선순위의 인지(Perception)나 내비게이션(Navigation) 처리가 자원 소유자를 반복적으로 선점하면 발생한 지연이 물리적 제어(Physical Control)와 안전 응답(Safety Response)에 직접 전달될 수 있다.

따라서 올바른 RTOS 아키텍처는 적절한 동기화 프로토콜(Synchronization Protocol)과 신중한 자원 소유권(Resource Ownership)을 함께 사용해야 한다. 높은 중요도를 가진 제어 경로(High-Criticality Control Path)는 장시간 실행되는 낮은 우선순위 소프트웨어와 공유하는 자원에 대한 의존성을 최소화해야 한다. 공유가 불가피한 경우 우선순위 상속 또는 우선순위 상한 메커니즘을 지원하는 뮤텍스를 사용하여 간섭을 제한할 수 있다. 또한 잠금 없는 통신(Lock-Free Communication), 전용 버퍼(Dedicated Buffer), 메시지 전달(Message Passing), 아키텍처 분리(Architectural Separation)를 통해 중요한 제어 경로에서의 자원 경합(Contention)을 더욱 감소시킬 수 있다.

우선순위 역전(Priority Inversion)은 태스크의 우선순위만으로는 실시간 성능(Real-Time Performance)을 보장할 수 없다는 사실을 보여준다. 자원 의존성(Resource Dependency)이 실제 실행 순서를 변화시킬 수 있으므로 스케줄링(Scheduling)과 동기화(Synchronization)는 하나의 통합된 시간 시스템(Integrated Timing System)으로 분석되어야 한다. PIP와 PCP는 이러한 영향을 체계적으로 제어하는 메커니즘을 제공하며, 제한된 블로킹(Bounded Blocking), 신뢰성 있는 응답시간 분석, 결정적 동기화(Deterministic Synchronization), 안전한 실시간 로봇 제어(Safe Real-Time Robot Control)를 위한 기반을 형성한다.

## 01.06 Task State Transitions and Context Switching

![](images/image6.png){width="7.268055555555556in" height="7.268055555555556in"}

RTOS의 태스크(Task)는 생성된 이후 완료될 때까지 계속해서 실행되는 것이 아니다. 대신 프로세서 사용 가능 여부, 스케줄링 결정(Scheduling Decision), 동기화 이벤트(Synchronization Event), 자원 상태(Resource Condition)에 따라 정의된 실행 상태(Execution State) 사이를 이동한다. 태스크 상태 전이(Task-State Transition)를 통해 커널(Kernel)은 어떤 태스크가 실행 가능한지, 어떤 태스크가 현재 CPU를 사용하고 있는지, 어떤 태스크가 대기해야 하는지를 표현할 수 있으며, 이는 실시간 임베디드 시스템(Real-Time Embedded System)의 멀티태스킹(Multitasking)을 구성하는 동작 기반이 된다.

준비 상태(Ready State)는 태스크가 프로세서를 사용할 수 있다는 조건을 제외하면 실행에 필요한 모든 조건을 갖춘 상태를 의미한다. 준비 상태의 태스크는 데이터, 동기화 객체(Synchronization Object), 타이머(Timer)를 기다리는 것이 아니라 단순히 다른 준비 상태 태스크들과 CPU 사용 시간을 놓고 경쟁하고 있다. 스케줄러(Scheduler)는 스케줄링 정책(Scheduling Policy)에 따라 이러한 태스크를 평가하며, 우선순위 기반 선점형 RTOS(Priority-Based Preemptive RTOS)에서는 일반적으로 가장 높은 우선순위를 가진 준비 상태 태스크를 선택한다.

실행 상태(Running State)는 현재 프로세서 코어(Processor Core)에서 명령을 실행하고 있는 태스크를 나타낸다. 단일 코어 시스템(Single-Core System)에서는 일반적으로 한 시점에 하나의 태스크만 실행 상태에 있을 수 있지만, 여러 다른 태스크가 준비 상태로 존재할 수 있다. 실행 중인 태스크는 선점(Preemption)되거나, 이벤트 또는 자원을 기다리면서 차단(Block)되거나, 자발적으로 실행권을 양보(Yield)하거나, 지연 조건(Delay Condition)에 도달하거나, 태스크 관리 모델(Task-Management Model)에 따라 실행을 완료할 때까지 계속 실행된다.

차단 상태(Blocked State) 또는 대기 상태(Waiting State)는 특정 조건이 충족될 때까지 태스크가 유용한 작업을 진행할 수 없는 상태를 의미한다. 태스크는 세마포어(Semaphore), 뮤텍스(Mutex), 메시지 큐(Message Queue), 이벤트 플래그(Event Flag), 통신 패킷(Communication Packet), 센서 업데이트(Sensor Update) 또는 기타 자원을 기다릴 수 있다. RTOS는 이러한 조건을 지속적으로 폴링(Polling)하도록 하는 대신 해당 태스크를 CPU 경쟁에서 제외하고 다른 준비 상태 태스크를 실행함으로써 프로세서 효율성과 스케줄링 예측 가능성(Scheduling Predictability)을 향상시킨다.

지연 상태(Delayed State) 또는 시간 대기 상태(Timed-Wait State)는 지정된 시간 간격이 지난 후 또는 특정한 주기적 활성화 시점(Periodic Release Point)에 실행을 다시 시작해야 할 때 일반적으로 사용된다. 예를 들어 모터 제어 태스크(Motor-Control Task)는 1밀리초마다 한 번 실행된 후 다음 활성화 시점까지 지연될 수 있다. RTOS 타이머 메커니즘(Timer Mechanism)은 이러한 지연시간을 추적하고 필요한 시간이 만료되면 태스크를 준비 상태로 전환함으로써 지속적인 바쁜 대기(Busy Waiting) 없이 주기적 실행을 지원한다.

태스크 상태 전이(Task-State Transition)는 태스크 자체의 동작과 외부 이벤트(External Event)에 의해 모두 발생할 수 있다. 실행 중인 태스크는 사용할 수 없는 자원을 요청하면 차단 상태로 전환될 수 있으며, 차단된 태스크는 다른 태스크가 세마포어를 해제하거나 큐에 데이터를 저장하면 준비 상태로 전환될 수 있다. 마찬가지로 타이머 인터럽트(Timer Interrupt)는 지연 상태의 태스크를 준비 상태로 전환할 수 있다. 각각의 상태 전이는 스케줄러가 프로세서 실행 대상으로 고려해야 하는 태스크 집합을 변화시킨다.

선점(Preemption)은 또 다른 중요한 상태 전이를 발생시킨다. 낮은 우선순위 태스크가 실행 중일 때 높은 우선순위 태스크가 준비 상태가 되면 선점형 스케줄러(Preemptive Scheduler)는 현재 태스크를 실행 상태에서 준비 상태로 전환하고 높은 우선순위 태스크를 디스패치(Dispatch)할 수 있다. 낮은 우선순위 태스크는 실행을 완료하거나 차단된 것이 아니라 여전히 실행 가능한 상태이지만, 더 높은 스케줄링 우선순위를 가진 다른 태스크로 인해 일시적으로 프로세서 소유권(Processor Ownership)을 잃은 것이다.

문맥 교환(Context Switch)은 이러한 프로세서 소유권 변경을 가능하게 하는 메커니즘이다. 실행 대상이 하나의 태스크에서 다른 태스크로 변경되면 RTOS는 나가는 태스크(Outgoing Task)가 나중에 마치 중단되지 않았던 것처럼 실행을 재개할 수 있도록 충분한 프로세서 상태(Processor State)를 저장해야 한다. 이후 커널은 들어오는 태스크(Incoming Task)의 저장된 상태를 복원하고 해당 태스크가 실행을 계속해야 하는 명령 위치로 제어권을 전달한다.

저장되는 태스크 문맥(Task Context)에는 일반적으로 프로그램 카운터(Program Counter), 스택 포인터(Stack Pointer), 프로세서 레지스터(Processor Register), 상태 정보(Status Information), 프로세서 아키텍처에 특화된 실행 상태(Architecture-Specific Execution State)가 포함된다. 프로세서 및 RTOS 설계에 따라 부동소수점 레지스터(Floating-Point Register)나 확장 레지스터(Extended Register)도 저장해야 할 수 있다. 각 태스크는 일반적으로 자신만의 스택(Stack)을 가지므로 지역 변수(Local Variable), 함수 호출(Function Call), 반환 주소(Return Address), 저장된 실행 정보를 해당 태스크와 연계하여 유지할 수 있다.

문맥 교환(Context Switching)은 여러 메커니즘에 의해 시작될 수 있다. 주기적인 시스템 틱(System Tick)이 스케줄러로 하여금 태스크 실행을 재평가하게 할 수 있고, 인터럽트가 높은 우선순위 태스크의 차단 상태를 해제할 수도 있으며, 실행 중인 태스크가 블로킹 커널 서비스(Blocking Kernel Service)를 호출하거나 자발적으로 실행권을 양보할 수도 있다. 현대적인 RTOS 설계에서는 틱리스 구성(Tickless Configuration)을 사용할 수도 있으며, 이 경우 스케줄링 동작은 지속적인 주기적 스케줄러 틱보다 타이머와 실제 이벤트에 의해 보다 직접적으로 구동된다.

인터럽트(Interrupt)가 발생한다고 해서 반드시 태스크 문맥 교환(Task Context Switch)이 발생하는 것은 아니다. 프로세서는 인터럽트 서비스 루틴(ISR, Interrupt Service Routine)에 진입하여 이벤트를 처리한 후 스케줄링 결정에 변화가 없다면 기존 태스크로 다시 돌아갈 수 있다. 그러나 인터럽트가 세마포어를 해제하거나 데이터를 전달하여 높은 우선순위 태스크를 준비 상태로 만든 경우 RTOS는 인터럽트 처리가 완료되는 시점에 문맥 교환을 수행하여 새롭게 준비된 태스크가 즉시 실행되도록 할 수 있다.

문맥 교환은 상태 저장, 태스크 선택, 문맥 복원, 메모리 또는 캐시 작업 집합(Cache Working Set)의 변경에 프로세서 시간이 필요하기 때문에 피할 수 없는 실행 오버헤드(Execution Overhead)를 발생시킨다. 따라서 지나치게 세분화된 태스크(Task Fragmentation)나 불필요한 동기화는 실제 작업에 사용할 수 있는 CPU 처리 능력을 감소시킬 수 있다. 캐시 미스(Cache Miss), 메모리 시스템 효과(Memory-System Effect), 부동소수점 문맥 저장, 프로세서 아키텍처 특성으로 인해 실제 문맥 교환 비용은 기본적인 커널 동작 자체보다 더 커질 수 있다.

따라서 태스크 상태(Task State)와 문맥 교환(Context Switching)의 관계는 결정적 시간 분석(Deterministic Timing Analysis)에서 중요하다. 높은 우선순위 태스크가 준비 상태에 있더라도 인터럽트가 비활성화되어 있거나, 커널이 보호 영역(Protected Region) 내부에 있거나, 다른 비선점 연산(Non-Preemptible Operation)이 실행되고 있으면 즉시 실행되지 못할 수 있다. 스케줄링 지연시간(Scheduling Latency)은 이러한 지연의 일부를 나타내며, 문맥 교환 시간(Context-Switch Time)은 선택된 태스크가 실제로 유용한 처리를 시작하기 전까지 추가적인 지연을 발생시킨다.

잘 설계된 RTOS 소프트웨어는 불필요한 상태 전이를 최소화하는 동시에 태스크가 수행할 유용한 작업이 없을 때는 차단 상태로 전환될 수 있도록 한다. 이벤트 구동 실행(Event-Driven Execution)은 대기 중인 차단 태스크가 프로세서 시간을 소비하지 않으므로 일반적으로 지속적인 폴링보다 효율적이다. 주기적 태스크는 정밀한 지연 메커니즘(Delay Mechanism)을 사용할 수 있고, 통신 태스크는 큐를 기다리면서 차단될 수 있으며, 자원 의존 태스크는 동기화 객체를 기다릴 수 있다. 이를 통해 보다 명확한 시간 관계와 낮은 CPU 사용률을 얻을 수 있다.

로봇 제어(Robotic Control)에서 태스크 상태 전이는 물리적 이벤트(Physical Event)의 흐름을 직접적으로 표현할 수 있다. 센서 인터럽트(Sensor Interrupt)가 획득 태스크(Acquisition Task)를 차단 상태에서 준비 상태로 전환하면 해당 태스크가 진단 태스크(Diagnostic Task)를 선점하고 새로운 측정값을 처리할 수 있다. 이후 획득 태스크가 제어 태스크(Control Task)에 신호를 보내면 제어 태스크는 액추에이터 명령(Actuator Command)을 계산한 후 다음 제어 주기(Control Period)까지 다시 차단될 수 있다. RTOS는 이와 같이 하드웨어 및 소프트웨어 이벤트를 제어된 태스크 상태 전이로 지속적으로 변환한다.

따라서 로봇 소프트웨어를 여러 태스크로 분할할 때 문맥 교환의 빈도(Context-Switch Frequency)를 고려해야 한다. 모든 작은 기능마다 별도의 태스크를 생성하면 스케줄러 동작, 동기화, 메모리 사용량, 시간 변동(Timing Variation)이 증가할 수 있다. 반대로 서로 관련되지 않은 여러 작업을 하나의 장시간 실행 태스크(Long-Running Task)로 결합하면 블로킹과 응답 지연시간(Response Latency)이 증가할 수 있다. 효과적인 아키텍처는 시간 요구사항, 우선순위, 데이터 흐름(Data Flow), 자원 소유권(Resource Ownership), 기능적 책임(Functional Responsibility)에 따라 적절한 태스크 경계(Task Boundary)를 결정한다.

태스크 상태(Task State)와 문맥 교환(Context Switching)은 궁극적으로 RTOS가 동시에 존재하는 여러 소프트웨어 요구사항(Concurrent Software Requirements)을 실제 프로세서의 순차적 실행(Sequential Processor Execution)으로 변환하는 방법을 설명한다. 준비(Ready), 실행(Running), 차단(Blocked), 지연(Delayed) 상태는 태스크의 실행 가능성을 나타내며, 스케줄러와 문맥 교환 메커니즘은 조건이 변화할 때 CPU 소유권을 전달한다. 이러한 메커니즘을 이해하는 것은 실시간 로봇 시스템에서 태스크 설계(Task Design), 인터럽트 상호작용(Interrupt Interaction), 동기화(Synchronization), 시간 분석(Timing Analysis), 결정적인 고주파 제어(Deterministic High-Frequency Control)를 구현하기 위한 기반이 된다.

## 01.07 Timer, Interrupt, DPC Handling Architecture

![](images/image7.png){width="7.268055555555556in" height="7.268055555555556in"}

타이머(Timer), 인터럽트(Interrupt), 지연 처리(Deferred Processing) 메커니즘은 비동기 하드웨어 이벤트(Asynchronous Hardware Event)와 시간 기반 작업(Time-Based Activity)을 스케줄링된 소프트웨어 태스크(Scheduled Software Task)에 연결하기 때문에 RTOS 내부에서 매우 중요한 실행 아키텍처(Execution Architecture)를 형성한다. 이러한 구조의 설계는 시스템이 외부 조건에 얼마나 빠르게 반응하는지, 정상적인 태스크 실행이 얼마나 오랫동안 중단되는지, 그리고 그 결과 발생하는 시간 동작이 얼마나 예측 가능한지를 결정한다. 로봇 시스템에서는 이러한 메커니즘이 센서, 통신 인터페이스, 제어 루프(Control Loop), 액추에이터(Actuator)를 결정적인 소프트웨어 실행(Deterministic Software Execution)에 연결한다.

하드웨어 타이머(Hardware Timer)는 일반적인 애플리케이션 실행과 독립적인 정밀한 시간 기준(Time Reference)을 제공한다. 프로세서 또는 주변장치 타이머(Peripheral Timer)는 클록 이벤트(Clock Event)를 카운트하고 설정된 조건에 도달하면 인터럽트를 발생시킨다. RTOS 커널(Kernel)은 시스템 시간 관리, 주기적 태스크 활성화(Periodic Task Activation), 지연(Delay), 타임아웃(Timeout), 스케줄링 서비스(Scheduling Service)를 위해 타이머를 사용한다. 또한 하드웨어 타이머는 일반적인 애플리케이션 수준의 소프트웨어 지연보다 높은 시간 정확도가 필요한 고주파 제어 기능(High-Frequency Control Function)을 직접 지원할 수도 있다.

전통적인 RTOS 시스템 틱(System Tick)은 주기적인 타이머 인터럽트(Periodic Timer Interrupt)에 의해 생성된다. 각각의 틱에서 커널은 내부 시간 정보를 갱신하고 지연된 태스크(Delayed Task), 소프트웨어 타이머(Software Timer), 타임아웃 조건이 만료되었는지를 판단한다. 지정된 시간 동안 대기하던 태스크는 이에 따라 지연 상태(Delayed State)에서 준비 상태(Ready State)로 전환될 수 있다. 이후 스케줄러(Scheduler)는 새롭게 준비 상태가 된 태스크가 타이머 인터럽트 발생 이전에 실행되고 있던 태스크를 선점(Preempt)해야 하는지를 결정할 수 있다.

주기적 틱(Periodic Tick)은 단순한 시간 관리 모델(Timing Model)을 제공하지만 실제 수행할 작업이 거의 없는 경우에도 반복적인 프로세서 오버헤드(Processor Overhead)를 발생시킨다. 틱리스 RTOS 동작(Tickless RTOS Operation)은 고정된 주파수로 지속적으로 인터럽트를 발생시키는 대신 다음에 필요한 웨이크업 이벤트(Wake-Up Event)의 시점에 맞추어 다음 타이머 인터럽트를 설정함으로써 이러한 오버헤드를 감소시킨다. 이 방식은 정확한 태스크 지연과 예약 이벤트(Scheduled Event)를 유지하면서 CPU 활동과 전력 소비를 줄일 수 있다.

인터럽트(Interrupt)는 소프트웨어가 장치를 지속적으로 폴링(Polling)하지 않아도 하드웨어가 프로세서에 즉각적인 처리를 요청할 수 있도록 한다. 센서, 통신 컨트롤러(Communication Controller), 타이머, DMA 엔진(DMA Engine), 안전 회로(Safety Circuit)는 의미 있는 이벤트가 발생했을 때 인터럽트를 생성할 수 있다. 프로세서는 정상 실행을 일시적으로 중단하고 필요한 실행 상태(Execution State)를 저장한 다음 해당 인터럽트 소스(Interrupt Source)와 연결된 인터럽트 서비스 루틴(ISR, Interrupt Service Routine)으로 제어권을 전달한다.

인터럽트 서비스 루틴(ISR)은 하드웨어 이벤트를 확인하고 안정화하기 위해 즉시 필요한 작업을 수행한다. 디바이스 상태 레지스터(Device Status Register)를 읽거나, 타임스탬프(Timestamp)를 기록하거나, 소량의 수신 데이터를 가져오거나, 인터럽트 조건을 해제하거나, 다른 하드웨어 동작을 시작할 수 있다. 인터럽트는 정상 태스크 실행을 지연시키고 낮은 우선순위 인터럽트의 실행을 방해할 수 있으므로 ISR 처리는 일반적으로 짧고 결정적(Deterministic)이며 제한된 시간(Bounded Time) 안에서 완료되도록 설계해야 한다.

복잡한 처리는 일반적으로 ISR 내부에서 직접 수행하지 않는 것이 바람직하다. 대용량 통신 패킷(Communication Packet)을 파싱하거나, 제어 알고리즘(Control Algorithm)을 실행하거나, 메모리 집약적 계산을 수행하거나, 저장장치에 데이터를 기록하거나, 자원을 기다리는 작업은 과도한 인터럽트 지연시간(Interrupt Latency)과 지터(Jitter)를 발생시킬 수 있다. 따라서 ISR에서는 필요한 최소한의 정보만 확보하고 나머지 작업은 정상적인 스케줄링 규칙에 따라 실행할 수 있는 소프트웨어 실행 문맥(Software Execution Context)으로 지연시키는 것이 일반적이다.

지연 프로시저 호출(DPC, Deferred Procedure Call)은 긴급하지 않은 인터럽트 관련 처리를 즉각적인 인터럽트 문맥(Interrupt Context) 밖으로 이동시키기 위한 일반적인 아키텍처 개념이다. 운영체제에 따라 지연 인터럽트 처리(Deferred Interrupt Handling), 바텀 하프(Bottom Half), 워커 태스크(Worker Task), 지연 콜백(Deferred Callback) 등 서로 다른 명칭을 사용할 수 있다. 공통적인 원리는 인터럽트 처리를 빠른 즉시 처리 단계(Immediate Stage)와 이후에 보다 많은 연산을 수행하는 스케줄링 단계(Scheduled Stage)로 분리하는 것이다.

이러한 아키텍처에서 ISR은 하드웨어에 즉시 필요한 부분만 처리한 후 지연 처리(Deferred Processing)를 예약하거나 신호를 전달한다. 지연 처리 구성 요소는 운영체제에 따라 커널 메커니즘(Kernel Mechanism)으로 실행되거나 전용 RTOS 태스크(Dedicated RTOS Task)로 실행될 수 있다. 이러한 분리는 인터럽트 수준(Interrupt Level)에서 소비되는 시간을 줄여 다른 인터럽트와 중요한 태스크가 프로세서 시간을 확보할 수 있도록 하면서 이후 처리에 필요한 정보를 유지한다.

RTOS 태스크 알림 메커니즘(Task Notification Mechanism)은 지연 처리를 구현하는 일반적인 방법을 제공한다. ISR은 세마포어(Semaphore)를 해제하거나, 이벤트 플래그(Event Flag)를 설정하거나, 큐(Queue)에 데이터를 저장하거나, 링 버퍼(Ring Buffer)를 갱신하거나, 직접 태스크 알림(Direct Task Notification)을 전달할 수 있다. 해당 메커니즘을 기다리고 있던 태스크는 준비 상태가 된다. 깨어난 태스크의 우선순위가 인터럽트 발생 이전에 실행되던 태스크보다 높다면 스케줄러는 인터럽트 처리가 완료된 직후 즉시 문맥 교환(Context Switch)이 이루어지도록 할 수 있다.

인터럽트 문맥(Interrupt Context)에서 태스크 문맥(Task Context)으로 전환하는 것은 ISR 내부에서 사용할 수 있는 커널 서비스(Kernel Service)가 제한되는 경우가 많기 때문에 중요하다. 인터럽트 핸들러(Interrupt Handler)는 일반 태스크와 동일한 방식으로 대기할 수 없으므로 블로킹(Blocking)을 발생시킬 수 있는 연산은 일반적으로 수행해서는 안 된다. 따라서 RTOS 구현에서는 스케줄러나 동기화 규칙을 위반하지 않고 커널 객체(Kernel Object)를 갱신할 수 있도록 특별한 인터럽트 안전 API(Interrupt-Safe API)를 제공하는 경우가 많다.

인터럽트 우선순위(Interrupt Priority)와 태스크 우선순위(Task Priority)는 서로 다른 스케줄링 영역(Scheduling Domain)을 나타내지만 상호 조정되어야 한다. 하드웨어 인터럽트 우선순위는 어떤 인터럽트가 다른 인터럽트 또는 프로세서 작업을 중단할 수 있는지를 결정하고, 태스크 우선순위는 준비 상태에 있는 소프트웨어 태스크 가운데 어떤 태스크를 스케줄러가 실행할지를 결정한다. 따라서 모든 인터럽트에 가장 높은 하드웨어 우선순위를 부여하면 오히려 긴 시간 또는 빈번하게 발생하는 인터럽트가 중요한 RTOS 태스크의 CPU 사용을 방해하는 문제가 발생할 수 있다.

중첩 인터럽트(Nested Interrupt)는 프로세서가 지원하고 시스템에서 적절하게 설정된 경우 높은 우선순위 인터럽트가 낮은 우선순위 ISR을 선점할 수 있도록 한다. 이는 매우 긴급한 하드웨어 이벤트에 대한 응답성을 향상시킬 수 있지만 시간 분석(Timing Analysis)의 복잡성과 스택 요구량(Stack Requirement)을 증가시킨다. 설계자는 최악 조건 인터럽트 지연시간(Worst-Case Interrupt Latency)을 결정할 때 최대 중첩 깊이(Maximum Nesting Depth), 각 핸들러의 실행시간, 인터럽트 마스킹 규칙(Interrupt Masking Rule), 누적 간섭(Cumulative Interference)을 이해해야 한다.

타이머 콜백(Timer Callback)과 소프트웨어 타이머(Software Timer) 역시 신중한 아키텍처 설계가 필요하다. 이러한 메커니즘은 편리한 시간 기반 실행을 제공하지만 콜백은 독립적인 애플리케이션 태스크가 아니라 공유 타이머 서비스 문맥(Shared Timer-Service Context)에서 실행될 수 있다. 따라서 하나의 긴 콜백이 다른 타이머 이벤트를 지연시킬 수 있다. 좋은 설계에서는 타이머 콜백을 짧은 신호 전달이나 상태 갱신에 사용하고 비용이 큰 연산은 적절한 우선순위를 가진 태스크로 전달한다.

인터럽트 지연시간(Interrupt Latency)은 하드웨어 인터럽트 요청이 발생한 시점부터 해당 인터럽트 핸들러가 실행을 시작할 때까지의 지연을 의미한다. 즉각적인 핸들러 처리가 완료된 이후 지연 태스크(Deferred Task)가 실제 유용한 처리를 시작할 때까지 추가적인 지연시간이 발생한다. 인터럽트 마스킹(Interrupt Masking), 높은 우선순위 인터럽트, 임계 구역(Critical Section), 커널 동작, 문맥 교환, 캐시 동작(Cache Behavior), 프로세서 아키텍처가 모두 전체 이벤트-응답 시간 경로(Event-to-Response Timing Path)에 영향을 준다.

로봇 센서 파이프라인(Robotic Sensor Pipeline)은 이러한 아키텍처를 명확하게 보여주는 사례이다. 센서가 새로운 데이터를 생성하고 인터럽트를 발생시키면 ISR은 장치의 인터럽트를 확인하고, 시간 정보를 기록하며, 수신 데이터 또는 데이터 디스크립터(Data Descriptor)를 버퍼(Buffer)에 저장한다. 이후 센서 처리 태스크(Sensor-Processing Task)를 깨우고 빠르게 종료한다. 스케줄러는 적절한 태스크를 선택하고 해당 태스크는 인터럽트 문맥 외부에서 필터링(Filtering), 디코딩(Decoding), 상태 추정(State Estimation), 제어 관련 처리를 수행한다.

이와 유사한 구조는 액추에이터 및 통신 시스템에도 적용된다. CAN, CANopen, 이더넷(Ethernet), SPI, UART 컨트롤러는 송수신 인터럽트를 발생시킬 수 있으며, 고주파 타이머(High-Frequency Timer)는 주기적인 제어 작업을 트리거(Trigger)할 수 있다. 즉각적인 하드웨어 처리는 짧은 ISR 내부에서 수행하고, 프로토콜 처리(Protocol Processing), 명령 생성(Command Generation), 진단(Diagnostics), 애플리케이션 로직(Application Logic)은 스케줄링된 태스크에서 실행한다. 이러한 분리는 하드웨어의 긴급성(Hardware Urgency)과 소프트웨어 워크로드(Software Workload) 사이에 명확한 경계를 형성한다.

따라서 전체 타이머-인터럽트-지연 처리(Timer-Interrupt-Deferred Processing) 아키텍처는 하드웨어 또는 시간 이벤트(Hardware or Time Event), 인터럽트 요청(Interrupt Request), 짧은 ISR, 지연 알림(Deferred Notification), 스케줄러 결정(Scheduler Decision), 태스크 수준 처리(Task-Level Processing)로 이어지는 하나의 이벤트 파이프라인(Event Pipeline)으로 이해할 수 있다. 각 단계를 제한된 시간 내에서 수행하도록 설계하면 이벤트 발생부터 응답까지의 지연시간(Event-to-Response Latency)을 분석할 수 있다. 이러한 아키텍처는 RTOS 기반 로봇 시스템에서 결정적 센서 획득(Deterministic Sensor Acquisition), 실시간 통신(Real-Time Communication), 주기적 모터 제어(Periodic Motor Control), 낮은 지터 스케줄링(Low-Jitter Scheduling), 신뢰성 있는 물리적 상호작용(Reliable Physical Interaction)을 구현하기 위한 기반을 제공한다.

## 01.08 Memory Management: Static vs Dynamic Allocation

![](images/image8.png){width="7.268055555555556in" height="7.268055555555556in"}

RTOS에서 메모리 관리(Memory Management)는 제한된 RAM을 태스크(Task), 스택(Stack), 커널 객체(Kernel Object), 통신 버퍼(Communication Buffer), 디바이스 드라이버(Device Driver), 애플리케이션 데이터(Application Data)에 어떻게 할당할지를 결정하면서 동시에 예측 가능한 시간 동작(Predictable Timing Behavior)을 유지하도록 한다. 범용 시스템과 달리 실시간 임베디드 소프트웨어(Real-Time Embedded Software)는 충분한 메모리가 존재하는지만 고려하는 것이 아니라, 언제 할당되는지, 할당에 얼마나 시간이 걸리는지, 단편화(Fragmentation)가 발생할 수 있는지, 장시간 동작에서도 메모리 동작이 결정적(Deterministic)으로 유지되는지를 함께 고려해야 한다.

임베디드 메모리(Embedded Memory)는 일반적으로 프로그램 코드(Program Code), 상수 데이터(Constant Data), 초기화된 전역 데이터(Initialized Global Data), 초기화되지 않은 전역 데이터(Uninitialized Global Data), 스택(Stack), 그리고 선택적으로 힙(Heap)을 포함하는 영역으로 구분된다. 플래시 메모리(Flash Memory)와 같은 비휘발성 메모리(Nonvolatile Memory)는 일반적으로 실행 코드와 영구 정보를 저장하며, RAM에는 실행 중 변화하는 상태 정보(Runtime State)가 저장된다. 링커(Linker)와 시작 소프트웨어(Startup Software)는 스케줄러가 동작하기 전에 이러한 구조의 상당 부분을 설정하므로 메모리 맵(Memory Map)은 RTOS 기반 시스템의 중요한 아키텍처 요소가 된다.

정적 메모리 할당(Static Memory Allocation)은 시스템 초기화 이전 또는 초기화 과정에서 메모리를 예약하며, 그 크기는 시스템 동작 중에도 알려진 상태로 유지된다. 전역 변수(Global Variable), 정적으로 할당된 태스크 스택(Task Stack), 고정 통신 버퍼(Fixed Communication Buffer), 미리 정의된 커널 객체 등이 대표적인 예이다. 실시간 실행이 시작되기 전에 메모리 소유권(Memory Ownership)과 용량을 결정할 수 있으므로 정적 할당은 매우 예측 가능한 동작을 제공하며 엄격한 시간 요구사항이나 안전 요구사항을 가진 기능에 널리 사용된다.

정적 할당의 주요 장점은 결정적 시간 동작(Deterministic Timing)이다. 태스크가 메모리를 필요로 할 때 런타임 실행(Runtime Execution) 과정에서 힙을 검색하거나, 사용 가능한 메모리 블록을 분할하거나, 예측하기 어려운 할당 관리 작업(Allocation Bookkeeping)을 수행할 필요가 없다. 중요한 객체의 위치와 크기가 이미 결정되어 있으므로 최악 조건 실행시간 분석(Worst-Case Execution-Time Analysis)이 쉬워지고 중요한 제어 경로(Critical Control Path)에서 발생할 수 있는 여러 런타임 메모리 할당 실패를 제거할 수 있다. 다만 필요한 메모리 용량을 사전에 충분히 확보해야 한다.

정적 할당은 메모리 블록을 임의의 패턴으로 반복해서 생성하고 해제하지 않으므로 외부 힙 단편화(External Heap Fragmentation)도 방지할 수 있다. 이는 장시간 연속 동작해야 하는 로봇이나 산업용 컨트롤러(Industrial Controller)에서 특히 중요하다. 그러나 버퍼나 태스크 스택을 실제로는 거의 발생하지 않는 최악 조건(Worst-Case Condition)에 맞추어 크게 설정하면 RAM이 낭비될 수 있으므로 메모리가 제한된 마이크로컨트롤러(Microcontroller)에서는 정확한 용량 계획(Capacity Planning)이 중요하다.

동적 메모리 할당(Dynamic Memory Allocation)은 일반적으로 힙(Heap)이라고 하는 관리된 메모리 영역에서 런타임에 필요한 메모리를 확보한다. malloc과 free와 개념적으로 유사한 함수 또는 RTOS 전용 할당 서비스(Allocation Service)를 이용하면 소프트웨어가 객체를 필요한 시점에 생성하고 사용이 끝난 이후 메모리를 반환할 수 있다. 따라서 동적 할당은 가변적인 워크로드(Variable Workload), 선택적으로 활성화되는 서브시스템(Optional Subsystem), 동적으로 생성되는 메시지, 빌드 시점(Build Time)에 메모리 요구량을 완전히 결정할 수 없는 애플리케이션에 높은 유연성을 제공한다.

실시간 시스템에서 동적 할당의 가장 큰 문제는 예측 가능성(Predictability)이다. 메모리 할당시간은 할당자 알고리즘(Allocator Algorithm), 현재 힙 구조(Heap Structure), 요청된 블록 크기, 이전의 메모리 할당 이력(Allocation History)에 따라 달라질 수 있다. 요청된 메모리를 확보하기 위해 자유 목록(Free List)을 검색하거나 블록을 분할하거나 해제된 영역을 병합해야 할 수도 있다. 이러한 작업의 실행시간이 일정하지 않다면 엄격한 데드라인(Deadline)을 만족해야 하는 태스크에 지연시간(Latency)과 지터(Jitter)를 발생시킬 수 있다.

단편화(Fragmentation) 역시 중요한 문제이다. 외부 단편화(External Fragmentation)는 전체적으로는 충분한 여유 메모리가 존재하지만 여러 개의 분리된 작은 블록으로 나뉘어 있어 큰 연속 메모리 요청(Contiguous Memory Request)을 만족시키지 못하는 현상이다. 내부 단편화(Internal Fragmentation)는 실제 필요한 크기보다 큰 메모리 블록이 할당되면서 발생한다. 불규칙한 메모리 할당과 해제를 장기간 반복하는 시스템에서는 일반적인 메모리 누수(Memory Leak)가 없어도 실질적으로 사용할 수 있는 메모리 용량이 감소할 수 있다.

메모리 누수(Memory Leak)는 소프트웨어가 메모리를 할당한 이후 이를 해제하거나 다시 사용할 수 있는 방법을 잃어버릴 때 발생한다. 반복적인 누수는 사용 가능한 힙 공간을 점진적으로 감소시키고 결국 메모리 할당 실패(Allocation Failure)를 발생시킬 수 있다. 이러한 문제는 몇 시간 또는 며칠 동안 동작한 이후에만 나타날 수도 있기 때문에 자율 로봇(Autonomous Robot)에서는 특히 위험하다. 따라서 동적 할당을 허용하는 경우 정적 분석(Static Analysis), 소유권 규칙(Ownership Rule), 런타임 모니터링(Runtime Monitoring), 장시간 스트레스 테스트(Long-Duration Stress Testing)가 중요하다.

메모리 할당 알고리즘(Allocation Algorithm)에 따라 속도, 메모리 효율성, 단편화, 결정성(Determinism) 사이에 서로 다른 절충 관계(Tradeoff)가 존재한다. 일반적인 힙 할당자(Heap Allocator)는 유연성을 중시하는 반면, 고정 크기 메모리 풀(Fixed-Size Memory Pool)은 동일한 크기의 메모리 블록들을 미리 확보하고 이를 효율적으로 할당하고 반환할 수 있도록 한다. 풀 기반 할당(Pool-Based Allocation)은 할당 동작의 실행시간을 제한할 수 있고 임의 크기의 힙 요청보다 단편화를 훨씬 쉽게 제어할 수 있기 때문에 실시간 시스템에서 유용하다.

메모리 풀(Memory Pool)은 통신 메시지(Communication Message), 센서 샘플(Sensor Sample), 네트워크 패킷(Network Packet), 명령 구조체(Command Structure)처럼 최대 크기를 미리 알 수 있는 객체에 특히 유용하다. 태스크는 임의 크기의 힙 메모리를 요청하는 대신 미리 정의된 블록을 메모리 풀에서 획득하고 사용이 끝나면 반환한다. 전체 용량이 명확하게 제한되므로 메모리 고갈(Memory Exhaustion)을 제어되지 않는 런타임 메모리 증가가 아니라 명시적인 아키텍처 한계(Architectural Limit)로 관리할 수 있다.

태스크 스택(Task Stack)은 각 RTOS 태스크가 일반적으로 함수 호출 정보(Function-Call Information), 지역 변수(Local Variable), 저장된 레지스터(Saved Register), 임시 실행 상태(Temporary Execution State)를 포함하는 독립적인 스택을 사용하기 때문에 특별한 관리가 필요하다. 스택이 너무 작으면 스택 오버플로(Stack Overflow)가 발생하여 인접 메모리를 손상시킬 수 있고, 지나치게 큰 스택은 제한된 RAM을 낭비한다. 따라서 스택 크기는 측정된 최고 사용량(High-Water Mark), 최악 조건 호출 깊이(Worst-Case Call Depth), 인터럽트 동작, 충분한 안전 여유(Safety Margin)를 고려하여 결정해야 한다.

인터럽트 처리(Interrupt Handling)는 프로세서 및 RTOS 아키텍처에 따라 스택 요구량을 추가로 증가시킬 수 있다. 일부 시스템은 전용 인터럽트 스택(Dedicated Interrupt Stack)을 사용하는 반면 다른 시스템에서는 현재 실행 중인 태스크의 스택을 일시적으로 사용할 수 있다. 중첩 인터럽트(Nested Interrupt), 부동소수점 문맥(Floating-Point Context), 큰 지역 변수, 깊은 함수 호출은 최악 조건 스택 사용량을 크게 증가시킬 수 있다. 따라서 스택 분석(Stack Analysis)은 일반적인 애플리케이션 수준의 함수 호출뿐 아니라 전체 실행 경로(Complete Execution Path)를 고려해야 한다.

메모리 보호(Memory Protection)는 메모리 할당 또는 스택 오류로 인해 발생하는 영향을 줄일 수 있다. 메모리 보호 장치(MPU, Memory Protection Unit)가 탑재된 프로세서는 개별 태스크가 접근할 수 있는 메모리 영역을 제한하고 특정한 불법 메모리 접근(Illegal Memory Access)을 감지할 수 있다. RTOS는 MPU를 사용하여 태스크 스택, 커널 메모리(Kernel Memory), 주변장치 영역(Peripheral Region), 안전 중요 데이터(Safety-Critical Data)를 격리함으로써 감지되지 않는 메모리 손상(Silent Memory Corruption)을 정의된 복구 전략(Recovery Strategy)으로 처리할 수 있는 감지 가능한 오류로 전환할 수 있다.

많은 실시간 아키텍처(Real-Time Architecture)는 정적 방식과 동적 방식 가운데 하나만 선택하기보다는 두 방식을 결합한다. 안전 중요 태스크(Safety-Critical Task), 제어 루프(Control Loop), 인터럽트 관련 버퍼, 필수 커널 자원(Essential Kernel Resource)은 시작 과정에서 정적으로 할당하고 중요도가 낮은 진단(Diagnostics)이나 설정 기능(Configuration Function)에는 제한적인 동적 할당을 사용할 수 있다. 또 다른 일반적인 전략은 초기화 단계에서만 동적 할당을 허용하고 시스템이 정상 실시간 동작(Normal Real-Time Operation)에 진입한 이후에는 힙 연산(Heap Operation)을 금지하는 것이다.

따라서 정적 할당과 동적 할당의 선택은 시간 중요도(Timing Criticality), 메모리 용량(Memory Capacity), 객체 수명(Object Lifetime), 워크로드 변동성(Workload Variability), 실패 동작(Failure Behavior), 검증 요구사항(Verification Requirement)을 기준으로 결정해야 한다. 정적 할당은 예측 가능성과 제한된 자원 사용(Bounded Resource Usage)을 중시하는 반면 동적 할당은 유연성과 평균적인 메모리 사용 효율성을 중시한다. 메모리 풀과 초기화 시점 할당(Initialization-Time Allocation)은 런타임 불확실성을 제한하면서 일정 수준의 유연성을 유지할 수 있는 중간적인 접근 방법을 제공한다.

로봇 RTOS 아키텍처에서 결정적 메모리 동작(Deterministic Memory Behavior)은 결정적 물리 동작(Deterministic Physical Behavior)과 직접적으로 연결된다. 모터 제어 태스크(Motor-Control Task)가 예상보다 오래 걸리는 힙 검색 때문에 데드라인을 놓쳐서는 안 되며, 안전 기능(Safety Function)이 메모리 단편화로 인해 할당에 실패해서도 안 된다. 따라서 신중한 스택 크기 설정, 제한된 버퍼(Bounded Buffer), 명확한 메모리 소유권, 메모리 풀, 보호 메커니즘(Protection Mechanism), 통제된 할당 정책(Controlled Allocation Policy)은 그 자체로 실시간 제어 아키텍처의 일부가 된다.

결국 메모리 관리(Memory Management)는 단순히 데이터를 저장하기 위한 메커니즘이 아니라 자원 및 시간 규율(Resource and Timing Discipline)로 이해해야 한다. 정적 할당(Static Allocation)은 알려진 메모리 용량과 예측 가능한 시간 동작을 제공하며, 동적 할당(Dynamic Allocation)은 추가적인 불확실성과 실패 가능성을 대가로 런타임 적응성(Runtime Adaptability)을 제공한다. 이러한 절충 관계를 이해하는 것은 임베디드 및 로봇 시스템에서 장시간 동작하더라도 안정적이고 분석 가능하며 결정적인 RTOS 소프트웨어를 설계하기 위한 기반이 된다.

## 01.09 RTOS Selection Criteria for Robot Applications

![](images/image9.png){width="7.268055555555556in" height="7.268055555555556in"}

로봇 애플리케이션(Robotic Application)을 위한 RTOS 선택은 단순히 소프트웨어 커널(Software Kernel)을 선택하는 것이 아니라 시스템 아키텍처(Architecture)를 결정하는 과정이다. 운영체제(Operating System)는 로봇의 타이밍(Timing), 통신(Communication), 메모리(Memory), 안전(Safety), 하드웨어 제어(Hardware Control) 구조의 일부가 된다. 따라서 적절한 RTOS는 로봇의 제어 주파수(Control Frequency), 센서 인터페이스(Sensor Interface), 프로세서 아키텍처(Processor Architecture), 통신 요구사항, 안전 목표(Safety Objective), 소프트웨어 복잡도, 예상 제품 수명주기(Product Lifecycle)를 기준으로 평가해야 한다.

실시간 성능(Real-Time Performance)은 가장 중요한 선택 기준이다. RTOS는 실제적인 최악 조건 워크로드(Worst-Case Workload)에서도 예측 가능한 인터럽트 지연시간(Interrupt Latency), 스케줄링 지연시간(Scheduling Latency), 문맥 교환(Context Switch) 동작을 제공해야 한다. 평균적인 성능만으로는 충분하지 않은데, 액추에이터 명령이나 안전 대응이 간헐적으로 너무 늦게 전달되는 것만으로도 로봇에 문제가 발생할 수 있기 때문이다. 중요한 것은 통신, 센싱, 진단, 제어 작업이 동시에 발생하는 상황에서도 핵심 태스크가 지속적으로 데드라인(Deadline)을 만족할 수 있는지 여부이다.

스케줄러 기능(Scheduler Capability)은 로봇의 태스크 아키텍처(Task Architecture)와 일치해야 한다. 높은 우선순위의 제어 및 안전 태스크가 중요도가 낮은 처리를 중단할 수 있어야 하므로 우선순위 기반 선점형 스케줄링(Priority-Based Preemptive Scheduling)이 일반적으로 필요하다. RTOS는 충분한 우선순위 단계(Priority Level), 예측 가능한 선점(Preemption), 주기적 태스크(Periodic Task) 지원, 적절한 태스크 차단 및 웨이크업(Task Blocking and Wake-Up) 메커니즘을 제공해야 한다. 복잡한 워크로드를 가진 시스템에서는 프로세서 친화도(Processor Affinity), 멀티코어 스케줄링(Multicore Scheduling), 또는 보다 고급 스케줄링 정책이 추가로 필요할 수 있다.

타이머 해상도(Timer Resolution)와 타이밍 서비스(Timing Service)는 고주파 로봇 제어(High-Frequency Robotic Control)에서 특히 중요하다. 모터 제어 루프(Motor-Control Loop), 센서 샘플링(Sensor Sampling), 궤적 실행(Trajectory Execution), 통신 감시(Communication Supervision)는 마이크로초에서 밀리초 단위의 시간 범위에서 동작할 수 있다. RTOS는 하드웨어 타이머 통합(Hardware-Timer Integration), 정확한 지연(Delay), 타임아웃(Timeout), 주기적 활성화(Periodic Activation)를 제공해야 하며, 필요한 경우 틱리스 동작(Tickless Operation)을 지원하는 것이 바람직하다. 타이밍 해상도는 독립적인 사양으로만 평가해서는 안 되며 실제 지터(Jitter)와 지연시간을 함께 고려해야 한다.

인터럽트 아키텍처(Interrupt Architecture) 역시 로봇이 비동기 하드웨어(Asynchronous Hardware)와 지속적으로 상호작용하기 때문에 중요한 선택 기준이다. RTOS는 엔코더(Encoder), 관성 측정 장치(IMU, Inertial Measurement Unit), LiDAR 인터페이스, 모터 컨트롤러(Motor Controller), 통신 주변장치(Communication Peripheral), 안전 장치(Safety Device), 타이머에서 발생하는 인터럽트를 효율적으로 지원해야 한다. 짧은 인터럽트 서비스 루틴(ISR, Interrupt Service Routine), 인터럽트 안전 커널 API(Interrupt-Safe Kernel API), 지연 처리(Deferred Processing) 메커니즘, 설정 가능한 인터럽트 우선순위, 제어된 중첩 인터럽트(Nested Interrupt)는 즉각적인 하드웨어 응답과 복잡한 태스크 수준 처리를 분리하는 데 도움이 된다.

동기화(Synchronization)와 태스크 간 통신(Inter-Task Communication) 기능은 동시에 실행되는 로봇 소프트웨어가 얼마나 안전하게 정보를 교환할 수 있는지를 결정한다. 세마포어(Semaphore), 뮤텍스(Mutex), 이벤트 메커니즘(Event Mechanism), 큐(Queue), 알림(Notification), 메시지 버퍼(Message Buffer)는 예측 가능한 동작과 낮은 오버헤드를 제공해야 한다. 공유 자원(Shared Resource)으로 인해 우선순위 역전(Priority Inversion)이 발생할 수 있는 경우 우선순위 상속(Priority Inheritance) 또는 관련 메커니즘이 중요하다. 사용 가능한 프로세스 간 통신(IPC, Inter-Process Communication) 모델은 태스크 사이의 통제되지 않은 공유보다 명확한 소유권과 제한된 데이터 흐름(Bounded Data Flow)을 지원해야 한다.

메모리 관리 동작(Memory-Management Behavior) 역시 애플리케이션에서 요구하는 결정성(Determinism)에 적합해야 한다. 안전 중요 제어 기능(Safety-Critical Control Function)에는 태스크 스택(Task Stack), 버퍼(Buffer), 커널 객체(Kernel Object)의 정적 할당(Static Allocation)이 필요할 수 있으며, 중요도가 낮은 구성 요소에서는 통제된 동적 할당(Controlled Dynamic Allocation)이 유용할 수 있다. 정적 할당, 메모리 풀(Memory Pool), 스택 모니터링(Stack Monitoring), 힙 진단(Heap Diagnostics), 선택적인 메모리 보호(Memory Protection)를 제공하는 RTOS는 설계자가 장시간 안정성과 런타임 실패 동작(Runtime Failure Behavior)을 더욱 효과적으로 제어할 수 있도록 한다.

하드웨어 지원(Hardware Support)은 기능적으로 우수한 RTOS라도 실제 적용 가능한지를 결정할 수 있다. 프로세서 아키텍처, 인터럽트 컨트롤러(Interrupt Controller), 타이머, 메모리 보호 장치(MPU, Memory Protection Unit), 부동소수점 장치(FPU, Floating-Point Unit), 멀티코어 기능(Multicore Feature), 주변장치 드라이버(Peripheral Driver)가 안정적으로 지원되어야 한다. 보드 지원 패키지(BSP, Board Support Package), 디바이스 드라이버(Device Driver), 공급업체가 유지관리하는 포트(Vendor-Maintained Port)는 통합 위험(Integration Risk)을 크게 줄일 수 있다. 실제 사용하려는 MCU 또는 SoC에 대한 성숙한 지원은 제한적인 하드웨어 통합 기능만 제공하는 이론적으로 우수한 커널보다 더 높은 가치를 가질 수 있다.

프로세서 등급(Processor Class) 역시 RTOS 선택에 영향을 준다. 모터 드라이브(Motor Drive)나 분산 입출력 노드(Distributed I/O Node)에 사용되는 소형 마이크로컨트롤러(Microcontroller)는 RAM과 플래시 사용량이 매우 적은 경량 커널(Compact Kernel)이 필요할 수 있다. 반면 더 강력한 로봇 컨트롤러(Robot Controller)에서는 네트워킹(Networking), 파일 시스템(File System), 보안(Security), 멀티코어 실행, 메모리 보호, 풍부한 미들웨어(Middleware)가 필요할 수 있다. 선택하는 RTOS는 로봇의 모든 컨트롤러에 동일한 운영체제 아키텍처를 강제로 적용하기보다 해당 컴퓨팅 계층(Computing Layer)의 특성에 맞아야 한다.

통신 지원(Communication Support)은 분산 로봇 시스템(Distributed Robotic System)에서 특히 중요하다. CAN, CAN FD, CANopen, EtherCAT, Ethernet, TCP/IP, UDP, UART, SPI 등의 인터페이스는 센서, 모터 컨트롤러, 엣지 디바이스(Edge Device), 외부 컴퓨터를 연결할 수 있다. RTOS 자체가 반드시 모든 프로토콜(Protocol)을 직접 구현해야 하는 것은 아니지만, 필요한 통신 스택(Communication Stack)을 지원하면서 중요한 제어 타이밍을 방해하지 않도록 드라이버, 네트워킹, 타이밍, 동기화 아키텍처가 구성되어야 한다.

RTOS가 상위 수준 소프트웨어와 상호작용해야 한다면 미들웨어 및 로보틱스 프레임워크 통합(Middleware and Robotics-Framework Integration)도 고려해야 한다. 저수준 컨트롤러(Low-Level Controller)는 정의된 프로토콜을 통해 리눅스(Linux) 또는 엣지 컴퓨터(Edge Computer)와 통신할 수 있으며, 보다 강력한 임베디드 플랫폼에서는 ROS 2 또는 micro-ROS 구성 요소를 통합할 수도 있다. DDS 관련 통신(DDS-Related Communication), 직렬화(Serialization), 네트워킹, 시간 동기화(Time Synchronization)가 중요해질 수 있지만, 이러한 기능이 하드 실시간 제어(Hard Real-Time Control) 책임을 방해하도록 해서는 안 된다.

안전 및 기능 안전 인증(Safety and Functional Certification) 요구사항은 선택 가능한 RTOS의 범위를 크게 제한할 수 있다. 사람, 산업 기계, 차량 또는 안전 중요 장비 주변에서 동작하는 로봇은 IEC 61508, ISO 26262, IEC 62304 또는 관련 분야별 프로세스(Domain-Specific Process)를 지원하는 근거가 필요할 수 있다. 따라서 인증 산출물(Certification Artifact), 결정적인 커널 동작, 개발 프로세스 문서(Development-Process Documentation), 추적성(Traceability), 장기 공급업체 지원(Long-Term Vendor Support)이 단순한 기능의 수보다 더 중요해질 수 있다.

로봇이 네트워크에 점점 더 많이 연결됨에 따라 보안(Security)도 평가해야 한다. 메모리 보호, 권한 분리(Privilege Separation), 보안 부팅(Secure Boot) 통합, 암호화 라이브러리(Cryptographic Library), 네트워크 보안(Network Security), 업데이트 메커니즘(Update Mechanism), 주변장치에 대한 통제된 접근은 공격 표면(Attack Surface)을 줄일 수 있다. RTOS가 물리적인 액추에이터를 제어하는 경우 소프트웨어 침해(Software Compromise)는 단순한 정보보안 문제가 아니라 물리적 안전 문제(Physical Safety Problem)로 이어질 수 있기 때문에 보안 기능이 특히 중요하다.

개발 생태계(Development Ecosystem)의 품질은 엔지니어링 비용과 개발 일정에 직접적인 영향을 준다. 컴파일러(Compiler)와 디버거(Debugger) 지원, 추적 도구(Tracing Tool), 런타임 통계(Runtime Statistics), 스택 분석(Stack Analysis), 타이밍 측정(Timing Measurement), 시뮬레이션(Simulation), 문서, 예제, 진단 기능(Diagnostic Facility)은 엔지니어가 실시간 동작을 얼마나 쉽게 이해할 수 있는지를 결정한다. 성숙한 생태계는 디버깅 주기를 단축하고 별도의 대규모 계측 시스템(Custom Instrumentation)을 구축하지 않고도 타이밍 검증(Timing Validation)에 필요한 근거를 제공할 수 있다.

라이선스(Licensing)와 상업적 조건(Commercial Condition)은 구현이 완료된 이후가 아니라 프로젝트 초기 단계에서 검토해야 한다. 오픈소스 RTOS(Open-Source RTOS)는 소스 코드 가시성(Source Visibility), 유연성, 폭넓은 커뮤니티를 제공할 수 있으며, 상용 시스템(Commercial System)은 인증된 구성 요소(Certified Component), 전문 기술지원, 특화된 개발 도구, 계약 기반 유지보수(Contractual Maintenance)를 제공할 수 있다. 라이선스 의무(License Obligation), 재배포 조건(Redistribution Condition), 안전 패키지(Safety Package), 제품별 비용, 장기 공급 가능성을 예상되는 로봇 생산 및 유지보수 모델과 함께 평가해야 한다.

신뢰성(Reliability)과 생태계 성숙도(Ecosystem Maturity)는 수천 시간 동안 동작해야 하는 로봇에서 특히 중요하다. 커널 안정성(Kernel Stability), 알려진 실패 모드(Known Failure Mode), 커뮤니티 또는 공급업체의 대응성, 릴리스 품질(Release Quality), 하위 호환성(Backward Compatibility), 업데이트 정책(Update Policy)은 제품 수명주기 위험(Lifecycle Risk)에 영향을 준다. 실제 현장에서 폭넓게 사용되어 장시간 안정성이 검증된 RTOS는 더 많은 기능을 제공하지만 안정적인 장시간 운용에 대한 근거가 부족한 새로운 플랫폼보다 적합할 수 있다.

전력 소비(Power Consumption)는 이동 로봇(Mobile Robot)과 배터리 기반 임베디드 노드(Battery-Powered Embedded Node)에서 중요한 선택 요소가 될 수 있다. 틱리스 스케줄링(Tickless Scheduling), 프로세서 슬립(Processor Sleep) 통합, 웨이크업 타이머(Wake-Up Timer), 주변장치 전력 관리(Peripheral Power Management), 낮은 커널 오버헤드(Low-Overhead Kernel Operation)를 활용하면 워크로드가 없는 동안 컨트롤러의 에너지 소비를 줄일 수 있다. 이러한 기능은 지속적으로 동작하는 고성능 컨트롤러에서는 상대적으로 중요도가 낮을 수 있지만 분산 센싱(Distributed Sensing)과 보조 모듈에서는 동작 시간을 크게 연장할 수 있다.

하나의 RTOS가 로봇의 모든 컴퓨팅 계층에서 반드시 최적일 필요는 없다. 경량 RTOS(Compact RTOS)는 모터 컨트롤러와 센서 노드 내부에서 동작하고, 다른 실시간 플랫폼(Real-Time Platform)이 결정적인 차량 제어(Deterministic Vehicle Control)를 담당하며, 엣지 컴퓨터에서는 리눅스가 인지(Perception), 계획(Planning), 인공지능(AI), 사용자 애플리케이션을 실행할 수 있다. 따라서 전체 로봇 아키텍처에서는 먼저 각 계층의 타이밍 및 책임 경계(Timing and Responsibility Boundary)를 정의한 후 각각에 적합한 운영 환경(Operating Environment)을 선택해야 한다.

결국 RTOS 선택은 결정성(Determinism), 기능성(Functionality), 하드웨어 호환성(Hardware Compatibility), 안전(Safety), 보안(Security), 생태계 성숙도, 자원 소비(Resource Consumption), 수명주기 지원(Lifecycle Support) 사이의 균형을 결정하는 과정이다. 벤치마크(Benchmark) 결과와 기능 비교표는 초기 평가에 유용하지만 최종 결정은 대상 하드웨어(Target Hardware)에서 실제 로봇을 대표하는 워크로드(Representative Robot Workload)를 실행하여 검증해야 한다. 가장 적합한 RTOS는 로봇의 핵심 물리적 동작(Critical Physical Behavior)을 전체 운용 수명 동안 제한 가능하고(Bounded), 측정 가능하며(Measurable), 유지보수 가능하고(Maintainable), 신뢰할 수 있는(Reliable) 상태로 유지할 수 있도록 하는 RTOS이다.

## 01.10 RTOS Certification and Safety-Critical Operation

![](images/image10.png){width="7.268055555555556in" height="7.268055555555556in"}

인증(Certification)과 안전 중요 동작(Safety-Critical Operation)은 RTOS의 역할을 단순히 편리한 소프트웨어 플랫폼(Software Platform)에서 시스템 안전 논증(Safety Argument)의 일부로 변화시킨다. 안전 관련 로봇(Safety-Related Robot)에서는 예측 가능한 스케줄링(Scheduling), 인터럽트 처리(Interrupt Handling), 메모리 보호(Memory Protection), 동기화(Synchronization), 고장 대응(Fault Response)이 소프트웨어가 의도한 대로 동작한다는 증거에 의해 뒷받침되어야 한다. 따라서 인증은 커널(Kernel)의 기능뿐만 아니라 개발 프로세스(Development Process), 검증(Verification), 추적성(Traceability), 문서화(Documentation), 형상 관리(Configuration Control), 운용 가정(Operational Assumption)까지 포함한다.

안전 중요 동작은 전체 시스템 수준(System Level)의 위험원 분석(Hazard Analysis)과 위험 평가(Risk Assessment)에서 시작된다. 엔지니어는 위험한 로봇 동작을 식별하고 잠재적인 심각도(Severity)와 발생 가능성(Likelihood)을 평가한 후 허용할 수 없는 위험을 감소시키는 안전 기능(Safety Function)을 정의한다. 비상 정지(Emergency Stop), 안전 토크 차단(Safe Torque Removal), 속도 제한(Speed Limitation), 충돌 방지(Collision Prevention), 통신 감시(Communication Supervision), 제어된 성능 저하(Controlled Degradation) 등이 안전 요구사항이 될 수 있다. RTOS는 이러한 기능을 지원하지만 RTOS 자체만으로 전체 로봇 시스템의 안전을 보장할 수는 없다.

안전 무결성 요구사항(Safety Integrity Requirement)은 소프트웨어 기능을 어떻게 분류하고 개발해야 하는지에 영향을 준다. 표준마다 서로 다른 용어와 보증 수준(Assurance Level)을 사용하지만 기본 원리는 유사하다. 잠재적인 사고 결과가 심각할수록 더욱 강력한 개발 및 검증 증거가 요구된다. 따라서 RTOS와 관련 소프트웨어 구성 요소는 로봇의 모든 부분에 동일한 인증 수준을 적용한다고 가정하기보다 각 구성 요소가 담당하는 안전 역할(Safety Role)에 따라 선택해야 한다.

IEC 61508은 전기·전자·프로그래머블 전자 안전 관련 시스템(Electrical, Electronic and Programmable Electronic Safety-Related System)을 위한 일반적인 기능 안전(Functional Safety) 프레임워크를 제공한다. 이 표준은 안전 무결성 수준(SIL, Safety Integrity Level)을 정의하고 사양(Specification), 설계(Design), 구현(Implementation), 검증(Verification), 유효성 확인(Validation), 운용(Operation), 유지보수(Maintenance)를 포함하는 수명주기 프로세스(Lifecycle Process)를 규정한다. 산업용 로봇 시스템은 IEC 61508을 직접 적용하거나 RTOS 선택, 소프트웨어 아키텍처, 개발 증거에 영향을 주는 파생 산업 표준(Sector Standard)을 적용할 수 있다.

다른 산업 분야에서는 특화된 표준(Specialized Standard)을 적용한다. 자동차 로봇 또는 자율주행 차량 기능은 ISO 26262와 자동차 안전 무결성 수준(ASIL, Automotive Safety Integrity Level)의 영향을 받을 수 있으며, 의료 소프트웨어에서는 IEC 62304 프로세스를 사용할 수 있다. 산업용 로봇은 추가적으로 기계 및 로봇 전용 안전 표준(Robot-Specific Safety Standard)의 적용을 받을 수 있다. 적용되는 프레임워크는 제품, 운용 환경, 의도된 기능(Intended Function), 관할 지역(Jurisdiction), 안전 아키텍처(Safety Architecture)에 따라 달라지므로 인증 요구사항은 시스템 개발 초기 단계에서 결정해야 한다.

안전 지향 RTOS(Safety-Oriented RTOS)는 개발 기록, 검증 결과, 형상 정보(Configuration Information), 안전 매뉴얼(Safety Manual), 문서화된 사용 제약조건(Usage Constraint)을 포함하는 인증 증거(Certification Evidence) 또는 안전 패키지(Safety Package)와 함께 제공될 수 있다. 이러한 증거는 인증 제품에서 운영체제의 적합성을 입증하기 위해 필요한 작업량을 줄일 수 있다. 그러나 인증되었거나 인증 가능한 RTOS를 사용한다고 해서 최종 로봇이 자동으로 인증되는 것은 아니다. 애플리케이션 소프트웨어, 하드웨어, 시스템 통합, 설정, 시스템 수준 동작은 여전히 제품 개발자의 책임이다.

결정적 스케줄링(Deterministic Scheduling)은 안전 기능이 제한된 응답시간(Bounded Response Time)에 의존하는 경우가 많기 때문에 기본적으로 중요하다. 안전 감시 태스크(Safety-Monitoring Task)는 위험 상태를 감지하고 지정된 데드라인(Deadline) 이전에 시스템을 안전 상태(Safe State)로 전환하도록 명령해야 할 수 있다. 따라서 인터럽트 지연시간(Interrupt Latency), 스케줄러 지연시간(Scheduler Latency), 블로킹 시간(Blocking Time), 문맥 교환 오버헤드(Context-Switch Overhead), 실행시간(Execution Time)을 함께 고려해야 한다. 안전 분석(Safety Analysis)은 유리한 평균 측정값만 사용하는 것이 아니라 방어 가능한 최악 조건 타이밍(Defensible Worst-Case Timing)을 중심으로 수행한다.

우선순위 할당(Priority Assignment)과 동기화(Synchronization)는 낮은 중요도의 작업이 안전 관련 처리를 허용 가능한 범위를 넘어 지연시키지 않도록 설계되어야 한다. 우선순위 역전(Priority Inversion), 과도하게 긴 임계 구역(Critical Section), 인터럽트 마스킹(Interrupt Masking), 통제되지 않은 자원 공유(Resource Sharing), 제한되지 않은 블로킹(Unbounded Blocking)은 기존의 타이밍 가정을 무효화할 수 있다. 우선순위 상속(Priority Inheritance), 우선순위 상한 기법(Priority Ceiling Technique), 제한된 동기화 메커니즘(Bounded Synchronization Mechanism), 신중하게 설계된 소유권 규칙(Ownership Rule)은 높은 우선순위 안전 태스크가 프로세서 시간과 공유 자원에 예측 가능하게 접근하도록 지원한다.

메모리 동작(Memory Behavior) 역시 중요하다. 메모리 손상(Memory Corruption)은 즉각적이고 명확한 고장을 발생시키지 않으면서 제어 판단을 변경할 수 있기 때문이다. 중요 객체(Critical Object)는 정상 동작 이전에 용량과 소유권을 확인할 수 있기 때문에 정적 할당(Static Allocation)이 선호되는 경우가 많다. 스택 오버플로 감지(Stack Overflow Detection), 제한된 버퍼(Bounded Buffer), 메모리 풀(Memory Pool), MPU 기반 격리(MPU-Based Isolation), 커널 및 안전 데이터 보호는 고장 전파(Failure Propagation)를 추가로 줄일 수 있다. 동적 할당(Dynamic Allocation)을 허용하는 경우에는 명확하게 정의된 제약조건과 실패 처리(Failure Handling)가 필요하다.

서로 다른 중요도(Criticality)를 가진 소프트웨어가 하나의 프로세서를 공유할 경우 공간적 격리(Spatial Isolation)와 시간적 격리(Temporal Isolation)가 중요해진다. 공간적 격리는 하나의 구성 요소가 다른 구성 요소의 메모리에 접근하는 것을 방지하고, 시간적 격리는 실행시간에 대한 간섭을 제한한다. 메모리 보호 장치(MPU) 또는 메모리 관리 장치(MMU, Memory Management Unit)는 메모리 분리를 지원할 수 있으며, 스케줄링 정책과 실행 예산(Execution Budget)은 프로세서 접근을 제어할 수 있다. 혼합 중요도 아키텍처(Mixed-Criticality Architecture)는 비안전 중요 기능이 필수적인 안전 동작을 방해하지 않도록 이러한 경계에 의존한다.

고장 감지(Fault Detection)는 정의된 대응 전략(Response Strategy)과 결합되어야 한다. 워치독(Watchdog), 스택 모니터(Stack Monitor), 메모리 검사(Memory Check), 타이밍 감시(Timing Supervision), 통신 타임아웃(Communication Timeout), 태스크 상태 감시(Task-Health Monitoring), 하드웨어 진단(Hardware Diagnostics)을 통해 비정상 상태를 식별할 수 있다. 그러나 감지만으로는 충분하지 않으며 이후 시스템이 어떻게 동작할지를 아키텍처에서 정의해야 한다. 위험의 특성에 따라 로봇은 정지하거나, 액추에이터 토크를 차단하거나, 속도를 감소시키거나, 서브시스템을 격리하거나, 구성 요소를 재시작하거나, 제어된 성능 저하 모드(Controlled Degraded Mode)로 전환할 수 있다.

워치독 아키텍처(Watchdog Architecture)는 단순한 고장 감지와 안전 공학(Safety Engineering)의 차이를 보여주는 대표적인 사례이다. 하나의 태스크가 주기적으로 워치독을 리셋하는 구조에서는 다른 중요 기능이 실패했음에도 해당 태스크만 계속 실행될 수 있기 때문에 시스템의 정상성을 충분히 입증하지 못한다. 보다 견고한 설계에서는 워치독을 서비스하기 전에 여러 실행 조건, 태스크 하트비트(Task Heartbeat), 타이밍 윈도(Timing Window), 통신 상태, 시스템 상태(System State)를 감시한다. 소프트웨어 고장이 최종 보호 기능까지 제거해서는 안 되는 경우 독립적인 하드웨어 감시(Independent Hardware Supervision)를 추가적인 보호 계층으로 사용할 수 있다.

간섭으로부터의 독립성(Freedom from Interference)은 진단(Diagnostics), 네트워킹(Networking), 로깅(Logging), 사용자 인터페이스(User Interface), 인공지능(AI) 기능이 결정적 제어(Deterministic Control)와 함께 동작할 때 특히 중요하다. 비안전 중요 소프트웨어(Noncritical Software)가 무제한으로 CPU 시간을 사용하거나, 장시간 인터럽트를 비활성화하거나, 메모리를 고갈시키거나, 안전 태스크에 필요한 자원을 점유해서는 안 된다. 아키텍처 분할(Architectural Partitioning), 우선순위 규칙, 자원 예산(Resource Budget), 통신 경계(Communication Boundary), 보호 메커니즘은 복잡한 로봇 기능이 안전 제어 경로(Safety-Control Path)를 예기치 않게 방해하지 않도록 한다.

안전 관련 RTOS 애플리케이션의 검증(Verification)은 일반적인 기능 테스트(Functional Testing)를 넘어선다. 요구사항 기반 테스트(Requirements-Based Testing), 경계값 테스트(Boundary Testing), 타이밍 분석(Timing Analysis), 결함 주입(Fault Injection), 코드 분석(Code Analysis), 커버리지 측정(Coverage Measurement), 인터페이스 검증(Interface Verification), 장시간 스트레스 테스트(Long-Duration Stress Testing) 등이 검증 증거를 구성할 수 있다. 필요한 엄격성은 적용되는 표준과 보증 수준에 따라 달라진다. 테스트는 정상적인 동작뿐만 아니라 고장, 과부하(Overload), 잘못된 입력(Invalid Input), 자원 고갈(Resource Exhaustion) 상황에서도 시스템이 제어된 방식으로 동작한다는 것을 입증해야 한다.

추적성(Traceability)은 안전 요구사항(Safety Requirement)을 아키텍처, 구현, 테스트, 검증 결과와 연결한다. 예를 들어 지정된 시간 내에 통신 장애를 감지해야 한다는 요구사항은 감시 기능(Monitoring Function), RTOS 타이밍 메커니즘(Timing Mechanism), 태스크 우선순위, 타임아웃 설정, 테스트 절차(Test Procedure), 그리고 그 결과로 생성된 증거와 연결되어야 한다. 이러한 연결 관계를 통해 검토자는 소프트웨어에 대한 막연한 신뢰에 의존하지 않고 각각의 안전 요구사항이 실제로 구현되고 검증되었는지를 판단할 수 있다.

형상 관리(Configuration Management)는 인증 증거가 특정 소프트웨어 버전, 컴파일러 옵션(Compiler Option), 프로세서 대상(Processor Target), RTOS 설정, 라이브러리, 개발 도구에 적용되기 때문에 필수적이다. 스케줄러 설정, 컴파일러, 커널 버전, 드라이버, 하드웨어 플랫폼을 변경하면 이전에 확립된 가정에 영향을 줄 수 있다. 따라서 안전 프로젝트에서는 기준선(Baseline)을 관리하고, 변경사항을 기록하며, 영향 분석(Impact Analysis)을 수행하고, 변경된 구성을 출시하기 전에 어떤 검증 활동을 다시 수행해야 하는지를 결정한다.

개발 또는 검증 도구가 오류를 발생시키거나 안전 증거가 의존하는 오류를 발견하지 못할 가능성이 있는 경우 도구 적격성(Tool Qualification) 역시 중요해질 수 있다. 컴파일러(Compiler), 코드 생성기(Code Generator), 정적 분석 도구(Static-Analysis Tool), 테스트 도구(Test Tool), 모델 기반 개발 환경(Model-Based Development Environment)은 적용되는 안전 프로세스에 따라 문서화된 신뢰 근거를 요구할 수 있다. 목적은 모든 엔지니어링 도구를 독립적으로 인증하는 것이 아니라 도구의 실패가 최종 소프트웨어의 무결성(Integrity)에 어떠한 영향을 줄 수 있는지를 이해하는 것이다.

안전 중요 RTOS 동작은 시작(Startup), 종료(Shutdown), 복구(Recovery)에 대해서도 엄격한 처리를 요구한다. 시스템은 하드웨어와 소프트웨어를 알려진 상태(Known State)로 초기화하고 위험한 물리적 동작을 허용하기 전에 중요 자원이 정상인지 확인해야 하며, 비정상적인 시작 조건에서도 안전한 출력(Safe Output)을 유지해야 한다. 종료 및 재시작 절차 역시 안전 가정(Safety Assumption)을 유지해야 한다. 복구는 시스템 상태를 충분히 확인할 수 있을 때만 수행해야 하며, 단순히 자동 재시작하여 제어되지 않은 물리적 동작으로 복귀해서는 안 된다.

로봇 시스템에서는 상위 수준 지능(High-Level Intelligence)과 최종 안전 제어 권한(Final Safety-Control Authority)을 분리하는 것이 가장 안전한 아키텍처가 되는 경우가 많다. 리눅스(Linux) 기반의 인지(Perception), 계획(Planning), 네트워킹, 인공지능(AI)이 원하는 동작을 생성하고, 결정적 RTOS(Deterministic RTOS) 또는 전용 안전 컨트롤러(Dedicated Safety Controller)가 운동 제한(Motion Limit), 통신 감시, 액추에이터 제약(Actuator Constraint), 비상 대응(Emergency Response)을 강제할 수 있다. 이러한 계층 구조는 모든 복잡한 상위 알고리즘의 완전한 정확성이 기본적인 물리적 안전을 유지하기 위한 필수조건이 되는 것을 방지한다.

따라서 인증(Certification)과 안전 중요 동작(Safety-Critical Operation)은 결정적인 RTOS 동작, 아키텍처 격리(Architectural Isolation), 통제된 자원(Controlled Resource), 고장 관리(Fault Management), 검증 증거(Verification Evidence), 체계적인 수명주기 프로세스(Disciplined Lifecycle Process)의 결합을 요구한다. 목적은 단순히 소프트웨어 충돌(Software Crash)을 방지하는 것이 아니라 예측 가능한 소프트웨어 및 하드웨어 고장이 허용할 수 없는 물리적 동작으로 이어지지 않도록 보장하는 것이다. RTOS의 동작을 제한 가능하고(Bounded), 정당화할 수 있으며(Justified), 검증 가능하고(Verified), 추적 가능하게(Traceable) 만들고 이를 완전한 시스템 수준 안전 논증(System-Level Safety Case)에 통합할 수 있을 때 해당 RTOS는 안전 중요 로봇 시스템에 적합하다고 할 수 있다.
