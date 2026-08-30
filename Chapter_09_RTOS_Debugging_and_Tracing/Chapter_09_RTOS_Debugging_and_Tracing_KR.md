**Volume 03 Real Time Operating Systems**

# 09. RTOS Debugging and Tracing

## 09.01 RTOS Debug Environment: JTAG, SWD, GDB Remote

![](images/image1.png){width="7.268055555555556in" height="7.268055555555556in"}

RTOS 디버그 환경(RTOS Debug Environment)은 조사하려는 타이밍 동작(Timing Behavior)을 지나치게 훼손하지 않으면서 소프트웨어 실행 상태를 관찰할 수 있어야 한다. JTAG, SWD, 원격 GDB(Remote GDB)는 호스트 디버거(Host Debugger)가 하드웨어 프로브(Hardware Probe) 또는 디버그 서버(Debug Server)를 통해 대상 프로세서(Target Processor)와 통신하는 계층형 디버깅 구조를 형성한다. 이를 통해 RTOS가 임베디드 대상 시스템(Embedded Target)에서 실행되는 동안 CPU 레지스터(Register), 메모리(Memory), 중단점(Breakpoint), 감시점(Watchpoint), 펌웨어 상태(Firmware State)에 제어된 방식으로 접근할 수 있다.

JTAG(Joint Test Action Group) 디버깅은 전용 신호를 통해 프로세서 내부의 디버그 로직(Debug Logic)에 접근할 수 있는 표준화된 하드웨어 인터페이스(Hardware Interface)를 제공한다. RTOS 시스템에서 JTAG 프로브(JTAG Probe)는 프로세서를 정지시키고 레지스터를 검사하며, 메모리를 읽거나 수정하고, 플래시(Flash)를 프로그래밍하며, 명령어 실행을 제어할 수 있다. 이러한 연결은 응용 소프트웨어 계층(Application Software Layer)보다 낮은 수준에서 동작하므로 스케줄러(Scheduler), 통신 스택(Communication Stack), 디바이스 드라이버(Device Driver), 응용 태스크(Application Task)가 실패한 상황에서도 유용하다.

SWD(Serial Wire Debug)는 더 적은 수의 물리적 신호를 사용하면서 유사한 프로세서 수준 디버깅 기능을 제공하며, ARM Cortex-M 마이크로컨트롤러(Microcontroller)에서 널리 사용된다. SWD는 일반적으로 리셋(Reset) 및 기준 연결과 함께 클록(Clock)과 양방향 데이터 신호(Bidirectional Data Signal)만 필요로 한다. 이러한 적은 핀 요구사항은 커넥터 크기와 사용 가능한 MCU 핀이 제한되는 소형 로봇 제어기(Robot Controller), 센서 노드(Sensor Node), 모터 제어기(Motor Controller) 등의 임베디드 시스템(Embedded System)에 특히 적합하다.

일반적인 하드웨어 디버그 경로(Hardware Debug Path)는 개발 워크스테이션(Development Workstation), 디버거 소프트웨어(Debugger Software), 디버그 서버(Debug Server), 하드웨어 프로브(Hardware Probe), 대상 MCU(Target MCU)로 구성된다. 상용 또는 오픈소스 프로브(Open-Source Probe)는 호스트에서 전달된 명령을 JTAG 또는 SWD 트랜잭션(Transaction)으로 변환한다. 프로브는 프로세서 내부에 구현된 아키텍처 디버그 자원(Architectural Debug Resource)에 접근할 수 있으므로 RTOS 스케줄러가 시작되기 전에도 실행 상태를 검사할 수 있으며, 이는 부팅 실패(Boot Failure)와 초기화 오류(Initialization Fault)를 분석할 때 특히 중요하다.

GDB(GNU Debugger)는 개발자가 이러한 디버깅 과정을 제어할 수 있도록 소프트웨어 수준 인터페이스(Software-Level Interface)를 제공한다. 디버깅 심볼(Debugging Symbol)이 포함된 ELF 이미지를 사용하면 GDB는 머신 주소(Machine Address)를 소스 파일(Source File), 함수(Function), 변수(Variable), 데이터 구조(Data Structure)와 연결할 수 있다. 개발자는 중단점을 설정하고 명령어를 단계별로 실행하며 호출 스택(Call Stack), 메모리, 변수를 검사하거나 수정할 수 있다. RTOS 디버깅에서는 디버그 서버가 커널별 스레드(Thread) 및 태스크(Task) 구조를 이해할 때 GDB의 활용성이 더욱 높아진다.

원격 디버깅(Remote Debugging)은 GDB 원격 직렬 프로토콜(GDB Remote Serial Protocol)을 이용하여 GDB와 실제 대상 하드웨어(Target Hardware)를 분리한다. 워크스테이션에서 실행되는 GDB는 OpenOCD와 같은 GDB 서버(GDB Server) 또는 프로브 전용 서버와 통신하고, 해당 서버가 하드웨어 디버그 인터페이스를 제어한다. 이러한 분리 구조를 통해 디버거 프런트엔드(Debugger Front End), 대상 접근 메커니즘(Target Access Mechanism), 하드웨어 프로브를 독립적으로 구성할 수 있으며 명령줄 도구(Command-Line Tool), IDE, 자동화 테스트 시스템(Automated Test System), 원격 개발 환경(Remote Development Environment)이 유사한 작업 흐름을 공유할 수 있다.

RTOS 인식 디버깅(RTOS-Aware Debugging)은 커널 데이터 구조(Kernel Data Structure)를 이용해 스케줄러 정보를 재구성함으로써 일반적인 프로세서 디버깅을 확장한다. 현재 실행 중인 하나의 스택(Stack)만 확인하는 것이 아니라 여러 태스크 또는 스레드의 상태(State), 우선순위(Priority), 스택 포인터(Stack Pointer), 실행 컨텍스트(Execution Context)를 확인할 수 있다. 이를 통해 블록된 태스크(Blocked Task), 비정상적인 일시 중단(Suspension), 우선순위 상호작용(Priority Interaction), 교착상태(Deadlock), 스택 손상(Stack Corruption), 스케줄링 이상을 분석할 수 있다.

중단점(Breakpoint)은 강력한 기능이지만 실시간 시스템(Real-Time System)에서는 신중하게 사용해야 한다. 일반적인 중단점은 프로세서 실행 전체를 정지시키므로 스케줄링, 인터럽트 처리(Interrupt Processing), 통신 데드라인(Communication Deadline), 워치독 서비스(Watchdog Servicing), 제어 루프 타이밍(Control-Loop Timing)도 함께 중단된다. 따라서 관찰되는 시스템은 원래의 실시간 동작과 달라질 수 있다. 중단점은 초기화 결함과 결정론적 기능 오류(Deterministic Functional Error)에는 효과적이지만 경쟁 조건(Race Condition), 데드라인 위반(Deadline Violation), 타이밍 의존적 오류(Timing-Dependent Failure)를 숨기거나 변화시킬 수 있다.

하드웨어 중단점(Hardware Breakpoint)은 프로세서의 디버그 자원을 사용하여 구현되므로 플래시의 실행 코드를 수정하지 않고도 실행을 정지시킬 수 있다. 사용 가능한 개수는 일반적으로 프로세서 아키텍처에 의해 제한된다. 감시점(Watchpoint)은 특정 메모리 접근을 감시하고 지정된 변수나 주소가 읽히거나 쓰일 때 실행을 정지시킨다. 이는 예상하지 못한 메모리 변경, 손상된 RTOS 객체(RTOS Object), 잘못된 주변장치 접근(Peripheral Access), 알 수 없는 태스크나 인터럽트 핸들러(Interrupt Handler)에 의해 변경되는 변수를 찾는 데 특히 유용하다.

인터럽트 기반 RTOS 소프트웨어(Interrupt-Driven RTOS Software)를 디버깅하려면 태스크 컨텍스트(Task Context)와 예외 컨텍스트(Exception Context)의 관계를 이해해야 한다. 태스크 내부에서 발견된 오류가 실제로는 공유 상태(Shared State)를 변경하거나 예상 실행 시간을 초과하거나 동기화 규칙(Synchronization Rule)을 위반한 인터럽트 서비스 루틴(ISR)에서 발생했을 수 있다. 예외 레지스터(Exception Register), 인터럽트 중첩(Interrupt Nesting), 저장된 스택 프레임(Saved Stack Frame), 대기 중인 인터럽트 상태를 조사하면 스케줄러 문제와 하드웨어 이벤트 처리 문제를 구분하고 오류 직전의 실제 실행 경로를 추적할 수 있다.

결함 처리기(Fault Handler)는 리셋이나 복구 과정에서 진단 정보(Diagnostic Information)가 사라지기 전에 이를 보존해야 한다. Cortex-M 시스템에서는 HardFault 또는 관련 예외가 발생할 때 레지스터 상태(Register State), 스택 포인터, 결함 상태 레지스터(Fault Status Register), 프로그램 카운터(Program Counter), 링크 레지스터(Link Register), 관련 메모리 위치를 저장할 수 있다. 이러한 정보를 심볼 정보(Symbol Information)와 결합하면 장애 발생 당시 대화형 디버깅(Interactive Debugging)이 연결되어 있지 않았더라도 충돌을 발생시킨 명령어와 함수를 재구성할 수 있다.

멀티코어(Multi-Core) 및 이기종 로봇 제어기(Heterogeneous Robot Controller)는 한 프로세서를 정지시키는 것이 다른 프로세서의 동작까지 변화시킬 수 있기 때문에 디버깅이 더욱 복잡하다. MCU가 결정론적 모터 제어(Deterministic Motor Control)를 수행하는 동안 Linux 프로세서는 인지(Perception), 네트워킹(Networking), 계획(Planning)을 수행할 수 있다. 따라서 각 자원과 통신 채널을 어느 프로세서가 담당하는지 식별해야 하며, 공유 메모리(Shared Memory), 메일박스(Mailbox), RPMsg, CAN, Ethernet, 동기화 신호(Synchronization Signal)는 프로세서 경계를 넘어 발생하는 장애를 분석하는 중요한 근거가 된다.

리셋 동작(Reset Behavior) 역시 의도적으로 제어해야 한다. 디버그 프로브는 시스템 리셋(System Reset), 코어 리셋(Core Reset), 리셋 후 정지(Reset-and-Halt), 이미 실행 중인 대상 시스템에 대한 연결(Attach-without-Reset) 등을 지원할 수 있다. 각 방식은 서로 다른 종류의 문제를 분석하는 데 사용된다. 리셋 후 정지는 부팅 초기화 분석에 유용하며, 리셋 없이 연결하는 방식은 일정 시간 동작한 이후 비정상 상태에 진입한 시스템을 조사할 때 중요하다. 장애가 발생한 로봇 제어기를 자동으로 리셋하면 근본 원인 분석(Root-Cause Analysis)에 필요한 실행 상태가 사라질 수 있다.

컴파일러 최적화(Compiler Optimization)는 디버거에서 관찰할 수 있는 정보에 큰 영향을 준다. 높은 수준의 최적화에서는 명령어 순서가 변경되고 변수가 제거되며 함수가 인라인화(Inlining)되고 레지스터가 재사용되기 때문에 소스 수준 단계 실행(Source-Level Stepping)이 실제 실행과 일치하지 않는 것처럼 보일 수 있다. 따라서 개발 빌드(Development Build)는 일반적으로 디버깅 심볼을 유지하면서 적절한 수준의 최적화를 사용하며, 릴리스 빌드(Release Build)도 사후 분석(Postmortem Analysis)에 필요한 심볼을 보존하는 것이 바람직하다. 중요한 타이밍 결함은 최종적으로 실제 제품과 동일한 최적화 조건에서 재현해야 한다.

실용적인 RTOS 디버그 작업 흐름(Debug Workflow)은 하나의 기법에 의존하기보다 침습적 디버깅(Intrusive Debugging)과 비침습적 관찰(Non-Intrusive Observation)을 결합한다. JTAG 또는 SWD는 저수준 하드웨어 접근을 제공하고, GDB는 심볼 기반 검사(Symbolic Inspection)를 제공하며, RTOS 인식 확장 기능은 스케줄러 상태를 보여준다. 프로세서를 정지시키면 타이밍 동작이 왜곡되는 상황에서는 트레이스(Trace), 타임스탬프 로그(Timestamped Logging), 런타임 통계(Runtime Statistics), 하드웨어 카운터(Hardware Counter), 결함 기록(Fault Record)을 함께 활용해야 한다.

로보틱스(Robotics)에서는 장애가 발생한 이후에 디버깅 기능을 추가하기보다 제어기 설계 단계부터 디버깅 아키텍처(Debugging Architecture)를 포함하는 것이 중요하다. 접근 가능한 디버그 커넥터(Debug Connector), 제어 가능한 리셋 회로(Reset Circuit), 심볼 보존(Symbol Retention), 영구 결함 기록(Persistent Fault Record), 테스트 포인트(Test Point), 문서화된 프로브 설정(Probe Configuration)을 준비하면 진단 시간을 크게 줄일 수 있다. 이를 통해 모터 제어기, 센서 노드, 안전 프로세서(Safety Processor), 통신 게이트웨이(Communication Gateway)에 대해 보드 초기 구동(Board Bring-Up)부터 통합 시험과 현장 장애 분석까지 일관된 디버깅 절차를 적용할 수 있다.

Chapter_09의 구조에서는 이러한 하드웨어 및 디버거 환경을 런타임 통계(Runtime Statistics), 시각적 트레이싱(Visual Tracing), 커널 수준 성능 분석(Kernel-Level Performance Analysis), 하드웨어 타임스탬프 측정(Hardware Timestamp Measurement), 지연시간 분석(Latency Analysis), 동시성 오류 검출(Concurrency Bug Detection), 프로파일링(Profiling), 현장 근본 원인 분석(Field Root-Cause Analysis)보다 먼저 배치한다. 따라서 JTAG, SWD, 원격 GDB는 이후의 비침습적이고 타이밍 중심적인 RTOS 진단 기법을 구축하기 위한 기본적인 검사 계층(Foundational Inspection Layer)으로 볼 수 있다.

## 09.02 FreeRTOS Runtime Stats and Task Stack Analysis [w/Code]

![](images/image2.png){width="7.268055555555556in" height="7.268055555555556in"}

FreeRTOS 런타임 통계(Runtime Statistics)는 실제 실행 중 프로세서 시간(Processor Time)이 각 태스크(Task)에 어떻게 분배되는지를 정량적으로 보여준다. 중단점(Breakpoint)에서 태스크 상태만 검사하는 것과 달리 런타임 통계는 관찰 구간(Observation Interval) 동안 실행 시간을 누적한다. 따라서 스케줄러 동작(Scheduler Behavior), CPU 사용률(CPU Utilization), 작업 부하 균형(Workload Balance), 비정상적인 태스크 활동, 그리고 실시간 제어 태스크가 시스템 설계에서 예상한 만큼의 처리 시간을 확보하는지 분석하는 데 유용하다.

FreeRTOS는 \`configGENERATE_RUN_TIME_STATS\`와 같은 설정 옵션(Configuration Option)과 RTOS 틱(RTOS Tick)보다 충분히 높은 주파수로 동작하는 타이밍 소스(Timing Source)를 통해 런타임 측정(Runtime Measurement)을 지원한다. 타이머(Timer)는 실행 시간 카운터(Execution-Time Counter)로 동작하며 커널(Kernel)은 누적된 카운터 값을 개별 태스크와 연결한다. 짧은 제어, 통신, 인터럽트 관련 작업 부하를 정확하게 구분하려면 스케줄러 틱보다 고해상도 하드웨어 타이머(High-Resolution Hardware Timer)를 사용하는 것이 일반적으로 적합하다.

런타임 통계는 전체 측정 기간 동안 예측 가능한 방식으로 동작하는 단조 증가 카운터(Monotonic Counter)를 필요로 한다. 카운터는 관련 태스크 실행 구간을 구분할 수 있을 만큼 충분한 해상도(Resolution)를 가져야 하면서도 지나치게 복잡한 오버플로 처리(Overflow Handling)를 요구하지 않아야 한다. 이 타이머는 스케줄링이 아니라 측정을 위한 것이므로 시스템에 미치는 간섭을 최소화해야 하며, 하드웨어 카운터(Hardware Counter), 주변장치 타이머(Peripheral Timer), 프로세서 사이클 카운터(Processor Cycle Counter)가 소프트웨어 기반 타이밍보다 실제 동작을 더 잘 반영할 수 있다.

런타임 통계가 활성화되면 FreeRTOS는 개별 태스크에 누적된 실행 정보를 제공할 수 있다. \`vTaskGetRunTimeStats()\`와 같은 함수는 태스크별 런타임 값과 상대적인 프로세서 사용량을 형식화할 수 있으며, \`uxTaskGetSystemState()\`는 사용자 정의 모니터링 소프트웨어(Custom Monitoring Software)에 적합한 저수준 태스크 상태 정보를 제공한다. 특히 로봇 펌웨어(Robotics Firmware)가 제어기에서 직접 진단 문자열을 생성하는 대신 텔레메트리(Telemetry)를 통해 통계를 전송해야 하는 경우 후자의 방식이 유용하다.

CPU 사용률은 하나의 순간적인 샘플(Instantaneous Sample)이 아니라 의미 있는 관찰 구간을 기준으로 해석해야 한다. 모터 제어 태스크(Motor-Control Task)는 매우 자주 실행되지만 한 번의 실행 시간이 짧을 수 있으며, 통신 또는 진단 태스크는 실행 빈도는 낮더라도 한 번에 더 긴 시간 동안 CPU를 점유할 수 있다. 런타임 통계는 이러한 차이를 보여주며 처리 요구량이 의도된 태스크 주기(Task Period), 우선순위(Priority), 연산 책임(Computational Responsibility)에 부합하는지 판단하는 데 도움을 준다.

FreeRTOS 유휴 태스크(Idle Task)는 프로세서의 여유 처리 능력(Processor Headroom)을 추정하는 유용한 기준을 제공한다. 실행 준비 상태인 응용 태스크가 없으면 스케줄러가 유휴 태스크를 실행하므로 유휴 태스크의 누적 런타임은 사용되지 않는 프로세서 용량을 대략적으로 나타낸다. 유휴 비율(Idle Percentage)이 감소하면 연산 부하가 증가하고 있음을 의미할 수 있다. 그러나 평균 CPU 용량에 여유가 있더라도 국부적인 데드라인 위반(Deadline Violation)이 발생할 수 있으므로 유휴 시간만으로 스케줄 가능성(Schedulability)을 보장할 수는 없다.

런타임 통계는 태스크 상태 정보(Task State Information)와 함께 사용할 때 특히 유용하다. 예상보다 CPU 시간을 적게 사용하는 태스크는 처리 능력이 부족한 것이 아니라 큐(Queue), 세마포어(Semaphore), 뮤텍스(Mutex), 알림(Notification), 외부 이벤트(External Event)를 기다리며 블록(Blocked)되어 있을 수 있다. 반대로 과도한 실행 시간은 의도하지 않은 반복 루프, 과도한 폴링(Polling), 비효율적인 연산, 예상보다 빈번한 웨이크업(Wakeup), 특정 태스크가 프로세서를 과도하게 점유하도록 만드는 우선순위 설정을 의미할 수 있다.

태스크 스택 분석(Task Stack Analysis)은 또 다른 중요한 RTOS 자원인 태스크별 스택 메모리(Stack Memory)를 대상으로 한다. 각 FreeRTOS 태스크는 지역 변수(Local Variable), 함수 호출(Function Call), 저장된 프로세서 컨텍스트(Processor Context), 임시 실행 상태를 위한 스택을 필요로 한다. 스택을 너무 작게 할당하면 메모리 손상(Memory Corruption)과 예측하기 어려운 장애가 발생할 수 있으며, 지나치게 크게 할당하면 제한된 SRAM이 낭비된다. 따라서 신뢰성 높은 임베디드 설계에서는 임의의 고정 여유값보다 측정 근거에 기반한 스택 크기 결정이 필요하다.

FreeRTOS는 실행 중 관찰된 최소 미사용 스택 공간을 추정하기 위한 스택 하이워터마크(Stack High-Water Mark) 메커니즘을 제공한다. \`uxTaskGetStackHighWaterMark()\` 또는 관련 API를 이용하면 태스크가 할당된 스택 한계에 얼마나 가까이 접근했는지 확인할 수 있다. 남아 있는 여유가 매우 작으면 해당 태스크에 추가적인 스택이 필요할 수 있으며, 지속적으로 큰 여유가 관찰된다면 충분한 시험 이후 스택 할당량을 줄여 메모리를 최적화할 수 있다.

하이워터마크는 관찰된 과거 사용량(Historical Usage)을 나타낼 뿐 미래의 최대 요구량을 보장하는 값은 아니므로 신중하게 해석해야 한다. 특정 태스크는 드물게 발생하는 오류 처리(Error Handling), 깊게 중첩된 함수 호출, 비정상적인 통신 경로, 예외적인 운용 조건에서만 추가 스택을 사용할 수 있다. 따라서 스택 크기 시험은 시작 과정(Startup), 정상 운용(Nominal Operation), 최대 연산 부하(Peak Computational Load), 통신 장애, 복구 절차, 진단 과정 등 최대 호출 깊이(Call Depth)가 발생할 가능성이 있는 경로를 포함해야 한다.

스택 사용량(Stack Consumption)은 눈에 보이는 지역 변수만으로 결정되지 않는다. 중첩 함수 호출(Nested Function Call), 라이브러리 루틴(Library Routine), 부동소수점 연산(Floating-Point Operation), 컴파일러가 생성하는 임시 저장 공간, 인터럽트 상호작용, 아키텍처별 컨텍스트 저장(Context Saving)도 메모리 요구량을 증가시킬 수 있다. 특히 큰 자동 배열(Automatic Array)이나 구조체(Structure)는 즉시 상당한 스택 공간을 소비할 수 있으므로, 큰 영구 버퍼(Persistent Buffer)를 정적 저장소(Static Storage)나 제어된 메모리 풀(Memory Pool)로 이동하면 스택 요구량을 더욱 예측 가능하게 만들 수 있다.

\`configCHECK_FOR_STACK_OVERFLOW\`와 같은 옵션을 활성화하면 FreeRTOS 스택 오버플로 검사(Stack Overflow Checking)를 추가적인 보호 계층으로 사용할 수 있다. 선택한 검사 방식과 포트 구현(Port Implementation)에 따라 커널은 스택 경계(Stack Boundary) 또는 알려진 패턴을 검사하고 손상이 감지되면 \`vApplicationStackOverflowHook()\`을 호출할 수 있다. 이 기능은 개발과 장애 격리(Fault Containment)에 유용하지만 체계적인 하이워터마크 측정을 대신하는 것이 아니라 이를 보완하는 수단으로 사용해야 한다.

런타임 통계와 스택 측정(Stack Measurement)은 함께 수집할 때 훨씬 더 유용하다. 하나의 진단 스냅샷(Diagnostic Snapshot)에 각 태스크의 상태, 우선순위, 런타임 사용량, 스택 할당량(Stack Allocation), 남은 스택 여유(Stack Margin), 스케줄링 역할을 연결할 수 있다. 이를 통해 RTOS의 간결한 운용 프로파일(Operational Profile)을 구성하고 CPU 시간을 과도하게 소비하면서 동시에 스택 한계에 접근하는 태스크나, 거의 실행되지 않으면서 많은 메모리를 예약한 태스크를 식별할 수 있다.

주기적인 진단 정보 수집(Periodic Diagnostic Collection) 자체도 실시간 제약(Real-Time Constraint)을 준수해야 한다. 긴 텍스트 테이블을 형식화하거나 모든 태스크 제어 블록(Task Control Block)을 순회하고, 느린 직렬 인터페이스(Serial Interface)로 통계를 전송하거나 지나치게 자주 스케줄러 정보를 수집하면 측정 대상 시스템의 동작을 방해할 수 있다. 따라서 제품 수준의 모니터링에서는 제어된 주기로 압축된 수치 데이터를 수집하고, 가능하면 형식화와 시각화(Visualization)를 외부 호스트, 로거(Logger), 상위 제어 컴퓨터(Supervisory Computer)에 맡기는 것이 바람직하다.

로봇 제어기(Robot Controller)에서 런타임 통계는 짧은 실험실 시험에서 발견하기 어려운 변화를 드러낼 수 있다. 통신 작업 부하 증가, 추가 센서 처리, 장애 복구 활동, 새롭게 통합된 제어 기능은 점진적으로 CPU 여유를 감소시킬 수 있다. 실제 움직임을 대표하는 운용 시나리오에서 태스크 사용률을 기록하면 유휴 상태(Idle), 정상 부하(Nominal Load), 최대 부하(Peak Load)를 비교하고 새로운 펌웨어를 배포하기 전에 충분한 실행 여유가 남아 있는지 판단할 수 있다.

스택 분석 역시 로봇 시스템이 모터 제어, 센서 획득(Sensor Acquisition), 통신, 진단, 안전 감시(Safety Supervision), 시스템 관리 등을 위한 많은 동시 태스크를 포함하기 때문에 중요하다. 각 태스크에서 작은 양을 과다 할당하더라도 전체적으로는 상당한 SRAM을 소비할 수 있다. 반대로 측정 없이 스택 할당량을 줄이면 드문 실행 경로에서만 나타나는 장애가 발생할 수 있다. 하이워터마크 측정 결과는 메모리 효율성과 신뢰성 사이의 균형을 결정하는 실용적인 근거가 된다.

런타임 및 스택 정보는 독립적인 개발 측정값으로 남겨두기보다 더 광범위한 RTOS 디버깅 전략(RTOS Debugging Strategy)의 일부로 통합해야 한다. JTAG, SWD, GDB는 실행을 정지할 수 있는 상황에서 상세한 상태 검사를 제공하는 반면, 런타임 통계와 스택 모니터링(Stack Monitoring)은 시스템이 계속 실행되는 동안 정보를 제공한다. 이후 트레이싱(Tracing), 하드웨어 타임스탬핑(Hardware Timestamping), 지연시간 측정(Latency Measurement), 동시성 분석(Concurrency Analysis), 프로파일링(Profiling)을 이용하면 의심스러운 CPU 또는 스택 동작이 발생하는 이유와 그것이 실시간 성능에 미치는 영향을 분석할 수 있다.

Chapter_09의 디버깅 구조에서 FreeRTOS 런타임 통계와 태스크 스택 분석은 기본적인 JTAG, SWD, 원격 GDB(Remote GDB) 환경 다음에 위치하는 최초의 체계적인 런타임 관찰 계층(Runtime-Observation Layer)을 형성한다. 이를 통해 디버깅은 개별 장애를 조사하는 수준에서 태스크 자원 동작(Task Resource Behavior)을 지속적으로 정량화하는 수준으로 확장되며, 스케줄러 튜닝(Scheduler Tuning), 스택 크기 최적화(Stack Sizing), 성능 최적화(Performance Optimization), 고급 RTOS 트레이싱과 근본 원인 분석(Root-Cause Analysis)을 위한 정량적 근거를 제공한다.

## 09.03 Visual RTOS Analysis with Tracealyzer [w/Code]

![](images/image3.png){width="7.268055555555556in" height="7.268055555555556in"}

Tracealyzer는 저수준 실행 이벤트(Low-Level Execution Event)를 타임라인(Timeline), 태스크 뷰(Task View), 상호작용 다이어그램(Interaction Diagram), 통계 요약(Statistical Summary)으로 변환하여 RTOS를 시각적으로 분석하는 방법을 제공한다. 프로세서를 정지시키고 하나의 실행 상태만 조사하는 대신, 태스크(Task), 인터럽트(Interrupt), 동기화 객체(Synchronization Object), 커널 서비스(Kernel Service)가 시간에 따라 어떻게 상호작용하는지 관찰할 수 있다. 따라서 일반적인 중단점(Breakpoint)으로 시스템을 정지하면 사라질 수 있는 타이밍 의존적 문제(Timing-Dependent Problem)를 분석하는 데 특히 유용하다.

Tracealyzer의 기본 개념은 이벤트 트레이싱(Event Tracing)이다. RTOS와 통합된 계측 기능(Instrumentation)은 태스크 전환(Task Switch), 태스크 활성화(Task Activation), 블로킹(Blocking), 동기화 연산(Synchronization Operation), 인터럽트 활동(Interrupt Activity), 커널 서비스 호출(Kernel Service Call)과 같은 중요한 이벤트를 기록한다. 각 이벤트에는 타이밍 및 컨텍스트 정보(Context Information)가 포함되며, 결과적으로 하나의 순간적인 상태가 아니라 시스템의 시간적 실행 이력(Temporal Execution History)을 표현하는 트레이스(Trace)가 만들어진다.

FreeRTOS에서는 트레이스 계측(Trace Instrumentation)을 통해 스케줄러 동작(Scheduler Operation)과 커널 객체(Kernel Object)를 관찰하면서 이벤트와 개별 태스크 사이의 관계를 유지할 수 있다. 스케줄러가 실행 태스크를 변경하면 어떤 태스크가 실행을 중단했고 어떤 태스크가 새롭게 활성화되었는지를 기록한다. 큐(Queue), 세마포어(Semaphore), 뮤텍스(Mutex), 이벤트 그룹(Event Group) 등의 RTOS 메커니즘도 함께 기록할 수 있으므로 특정 스케줄링 전환이 왜 발생했는지를 재구성할 수 있다.

트레이스 데이터(Trace Data)는 스냅샷(Snapshot) 또는 스트리밍(Streaming) 방식으로 수집할 수 있다. 스냅샷 트레이싱(Snapshot Tracing)은 제한된 대상 시스템 버퍼(Target-Side Buffer)에 이벤트를 저장하여 최근 실행 구간을 나중에 분석할 수 있도록 한다. 스트리밍 트레이싱(Streaming Tracing)은 이벤트를 호스트나 외부 저장장치로 지속적으로 전송하여 훨씬 긴 시간 동안 시스템을 관찰할 수 있다. 적절한 방식은 사용 가능한 RAM, 통신 대역폭(Communication Bandwidth), 필요한 관찰 시간, 허용 가능한 계측 오버헤드(Instrumentation Overhead)에 따라 결정된다.

타임라인 뷰(Timeline View)는 기록된 스케줄러 이벤트를 직관적인 태스크 실행 형태로 변환한다. 개발자는 각 태스크가 언제 실행되고, 선점(Preemption)되고, 블록되며, 다시 실행되는지를 확인할 수 있다. 인터럽트 서비스 루틴(Interrupt Service Routine)도 태스크와 함께 표시할 수 있으므로 인터럽트 활동으로 인해 중요한 제어 소프트웨어의 실행이 지연되었는지를 판단할 수 있다. 텍스트 로그(Text Log)만으로 이해하기 어려운 반복적인 공백, 실행 버스트(Burst), 긴 실행 구간, 예상하지 못한 선점 패턴을 시각적으로 확인할 수 있다.

태스크 스케줄링 분석(Task Scheduling Analysis)은 실제 구현된 동작이 설계된 우선순위 아키텍처(Priority Architecture)와 일치하는지 검증하는 데 특히 유용하다. 높은 우선순위의 모터 제어 태스크(Motor-Control Task)는 일반적으로 실행 요청이 발생하면 신속하게 수행되어야 하며, 낮은 우선순위의 진단 또는 통신 태스크는 남은 프로세서 용량을 사용해야 한다. 트레이스 시각화(Trace Visualization)를 통해 지연된 활성화, 과도한 선점, 기아 상태(Starvation), 비정상적인 실행 버스트, 설계 의도와 일치하지 않는 우선순위 설정을 발견할 수 있다.

RTOS 객체가 트레이스에 포함되면 동기화 동작(Synchronization Behavior)을 훨씬 쉽게 이해할 수 있다. 세마포어나 큐를 기다리는 태스크는 오랫동안 비활성 상태로 보일 수 있지만, 트레이스를 통해 해당 블록 상태를 관련 커널 객체와 연결할 수 있다. 이후 다른 태스크나 인터럽트가 언제 해당 태스크를 해제하는 이벤트를 발생시켰는지 확인할 수 있다. 이러한 관계는 누락된 알림(Missed Notification), 큐 혼잡(Queue Congestion), 동기화 지연(Synchronization Delay), 잘못된 실행 순서 등을 진단하는 데 중요하다.

뮤텍스 분석(Mutex Analysis)을 사용하면 일반적인 CPU 사용률 측정만으로 설명하기 어려운 우선순위 역전(Priority Inversion)과 잠금 경합(Lock Contention)을 발견할 수 있다. 높은 우선순위 태스크가 필요한 뮤텍스를 낮은 우선순위 태스크가 소유하고 있기 때문에 블록될 수 있으며, 이러한 지연은 다른 실행 가능한 태스크와 상호작용하여 복잡한 스케줄링 동작을 만든다. 시각적 트레이스를 통해 뮤텍스 소유권, 블로킹 구간, 태스크 우선순위, 최종 해제 과정을 연결된 시간적 실행 순서로 분석할 수 있다.

인터럽트 동작(Interrupt Behavior)은 실시간 트레이싱(Real-Time Tracing)의 또 다른 중요한 분석 대상이다. 지나치게 긴 인터럽트 서비스 루틴, 인터럽트 폭주(Interrupt Storm), 잘못 설정된 인터럽트 우선순위는 응용 태스크가 올바르게 설계되어 있더라도 상당한 지연시간(Latency)을 발생시킬 수 있다. 인터럽트 실행과 태스크 스케줄링을 연관시켜 분석하면 제어 주기 지연이 스케줄러, 응용 태스크, 디바이스 드라이버(Device Driver), 반복적인 하드웨어 인터럽트 활동 중 어디에서 발생했는지를 판단할 수 있다.

Tracealyzer는 누적된 CPU 사용률 뒤에 숨겨진 시간적 구조(Temporal Structure)를 설명함으로써 FreeRTOS 런타임 통계(Runtime Statistics)를 보완할 수 있다. 두 태스크가 비슷한 총 프로세서 시간을 소비하더라도 하나는 짧고 규칙적인 주기로 실행되는 반면, 다른 하나는 불규칙한 연산 버스트(Computational Burst)를 발생시킬 수 있다. 런타임 통계가 CPU 시간을 얼마나 사용했는지를 보여준다면 트레이싱은 그 CPU 시간이 언제 사용되었으며 다른 태스크에 어떤 영향을 미쳤는지를 보여준다.

의미 있는 이벤트 사이의 관계를 이용하여 지연시간 측정(Latency Measurement)도 수행할 수 있다. 인터럽트 발생부터 관련 처리 태스크가 활성화되기까지의 시간, 큐 전송(Queue Send)부터 큐 수신(Queue Receive)까지의 시간, 태스크 릴리스(Task Release)부터 실제 실행까지의 시간 등을 조사할 수 있다. 이러한 측정은 실행 시간(Execution Time), 응답 시간(Response Time), 스케줄링 지연(Scheduling Delay)을 구분하는 데 도움을 주며, 제어 또는 통신 경로가 실시간 데드라인(Real-Time Deadline)을 만족하는지 평가할 때 중요하다.

사용자 정의 이벤트(User-Defined Event)를 이용하면 커널 활동을 넘어 응용 프로그램의 의미적 동작(Application Semantics)까지 트레이싱할 수 있다. 펌웨어는 센서 획득(Sensor Acquisition), 제어 주기 시작(Control-Cycle Start), 궤적 갱신(Trajectory Update), CAN 메시지 도착, 안전 상태 전환(Safety Transition), 액추에이터 명령 생성(Actuator Command Generation) 등의 이벤트를 기록할 수 있다. 이러한 이벤트를 스케줄러 및 인터럽트 활동과 함께 표시하면 로봇의 응용 수준 동작을 실제 RTOS 실행 순서와 직접 연결할 수 있다.

트레이스 계측은 일정한 오버헤드(Overhead)를 발생시키므로 트레이스 설정(Trace Configuration) 자체를 측정 설계(Measurement Design)의 일부로 고려해야 한다. 가능한 모든 이벤트를 기록하면 버퍼 공간(Buffer Space), 프로세서 시간, 통신 대역폭을 상당히 사용할 수 있다. 따라서 적절한 트레이스 범위(Trace Scope), 이벤트 집합(Event Set), 버퍼 크기(Buffer Size), 스트리밍 메커니즘(Streaming Mechanism)을 선택해야 한다. 엄격한 실시간 데드라인을 평가하는 경우 트레이싱 자체가 측정 대상의 타이밍을 변화시키는지도 검증해야 한다.

긴 트레이스(Long Trace)는 모든 이벤트를 수동으로 조사하기보다 체계적인 분석이 필요하다. 통계 뷰(Statistical View)는 기록 구간 전체에서 태스크 실행 시간, 응답 특성(Response Characteristics), 활성화 패턴(Activation Pattern), 기타 동작 특성을 요약할 수 있다. 개발자는 먼저 통계적으로 비정상적인 태스크나 시간 구간을 식별한 다음 해당 영역의 상세 타임라인으로 이동하여 정량적 선별(Quantitative Screening)과 이벤트 단위 근본 원인 분석(Root-Cause Analysis)을 결합할 수 있다.

로보틱스(Robotics)에서 시각적 트레이싱은 제어(Control), 센싱(Sensing), 통신(Communication), 안전 기능(Safety Function)이 서로 다른 주기로 동시에 실행되기 때문에 특히 효과적이다. 모터 루프(Motor Loop)는 높은 결정론적 주파수로 실행되는 반면 센서 처리와 네트워크 통신은 상대적으로 느린 주기로 동작할 수 있다. 하나의 통합된 타임라인을 통해 이러한 다중 주기 상호작용(Multi-Rate Interaction)을 확인하고 낮은 중요도의 작업 부하가 간헐적으로 제어 실행을 방해하거나 예상하지 못한 프로세서 시간을 소비하는지 판단할 수 있다.

따라서 트레이스 분석은 로봇이 유휴 상태일 때뿐만 아니라 실제 운용을 대표하는 작업 부하(Representative Workload)에서도 수행해야 한다. 가속, 회전, 높은 센서 트래픽(Dense Sensor Traffic), 통신 버스트, 장애 처리(Fault Handling), 복구 절차(Recovery Procedure)는 단순한 실험실 운용에서는 나타나지 않는 실행 패턴을 만들 수 있다. 유휴 상태(Idle), 정상 부하(Nominal Load), 최대 부하(Peak Load)의 트레이스를 비교하면 제어기가 실제 운용 조건에 접근할 때도 타이밍 여유(Timing Margin)가 안정적으로 유지되는지 확인할 수 있다.

Tracealyzer는 RTOS 디버깅 작업 흐름에서 기본적인 런타임 관찰(Runtime Observation)과 더욱 심층적인 성능 분석(Performance Analysis) 사이에 자연스럽게 위치한다. JTAG, SWD, GDB는 실행이 정지된 상태에서 상세한 상태 검사를 제공하고, FreeRTOS 런타임 통계는 실행 중 CPU 및 스택 자원(Stack Resource) 사용량을 정량화한다. 시각적 트레이싱은 여기에 시간적 차원(Temporal Dimension)을 추가하여 스케줄러, 인터럽트, 프로세스 간 통신(IPC), 동기화, 응용 이벤트 사이의 순서와 관계를 보여준다.

결과적으로 디버깅 작업 흐름은 문제가 존재한다는 사실을 발견하는 단계에서 정확히 언제, 왜 문제가 발생하는지를 이해하는 단계로 발전한다. 런타임 통계에서 특정 태스크의 과부하(Overload)를 발견했다면 Tracealyzer를 통해 그 원인이 과도한 실행 시간, 반복적인 활성화, 동기화 블로킹(Synchronization Blocking), 인터럽트 간섭(Interrupt Interference), 우선순위 상호작용(Priority Interaction) 중 무엇인지 확인할 수 있다. 이후 하드웨어 타임스탬프 측정(Hardware Timestamp Measurement)과 저수준 프로파일링(Low-Level Profiling)을 이용하여 중요한 실행 경로를 더욱 정밀하게 조사할 수 있다.

Chapter_09_RTOS_Debugging_and_Tracing의 구조에서 Tracealyzer를 이용한 시각적 RTOS 분석(Visual RTOS Analysis)은 자원 중심 모니터링(Resource-Oriented Monitoring)에서 시간 상관 기반 동작 진단(Time-Correlated Behavioral Diagnosis)으로 전환하는 단계에 해당한다. 태스크, 인터럽트, 커널 객체, 응용 이벤트를 하나의 시스템 수준 실행 그림(System-Level Execution Picture)으로 연결하며, 이후 커널 트레이싱(Kernel Tracing), 하드웨어 타임스탬프 기반 지터 측정(Jitter Measurement), 제어 루프 지연시간 분석(Control-Loop Latency Analysis), 동시성 디버깅(Concurrency Debugging), 프로파일링, 현장 근본 원인 분석(Field Root-Cause Analysis)을 수행하기 위한 기반을 제공한다.

## 09.04 LTTng and Babeltrace2: Linux RTOS Tracing [w/Code]

![](images/image4.png){width="7.268055555555556in" height="7.268055555555556in"}

LTTng(Linux Trace Toolkit next generation)은 실시간 스레드(Real-Time Thread), 인터럽트(Interrupt), 시스템 호출(System Call), 커널 스케줄링(Kernel Scheduling), 응용 이벤트(Application Event) 사이의 시간적 관계를 시스템 실행을 반복적으로 중단하지 않고 관찰해야 하는 Linux 시스템을 위한 저오버헤드 트레이싱(Low-Overhead Tracing)을 제공한다. PREEMPT_RT 및 Linux 기반 로봇 제어기에서 시스템 활동을 시간순으로 기록하여 일반적인 로그나 중단점 기반 디버깅(Breakpoint-Based Debugging)으로는 발견하기 어려운 지연 원인을 분석할 수 있다.

일반적인 디버깅과 달리 트레이싱(Tracing)은 시스템이 계속 동작하는 동안 이벤트(Event)를 기록하는 데 중점을 둔다. LTTng은 선택된 커널(Kernel) 및 사용자 공간(User Space)의 활동을 계측하고 타임스탬프가 포함된 이벤트 레코드(Timestamped Event Record)를 트레이스 버퍼(Trace Buffer)에 저장한다. 각 레코드는 무엇이 언제 발생했는지를 나타내며 추가적인 컨텍스트 정보(Context Information)를 포함한다. 이를 통해 스레드(Thread), 프로세스(Process), 인터럽트, 운영체제 서비스(Operating-System Service)에 걸친 실행 동작을 재구성할 수 있다.

LTTng은 일반적으로 커널 공간 트레이싱(Kernel-Space Tracing)과 사용자 공간 트레이싱(User-Space Tracing)으로 구분된다. 커널 트레이싱은 스케줄러 전환(Scheduler Transition), 시스템 호출, 인터럽트, 기타 커널 트레이스포인트(Kernel Tracepoint)와 같은 운영체제 활동을 관찰한다. 사용자 공간 트레이싱은 응용 프로그램이나 라이브러리에서 생성되는 이벤트를 기록한다. 두 계층을 결합하면 응용 수준의 제어 지연이 스케줄링, 디바이스 드라이버(Device Driver), 네트워크 처리, 메모리 활동 또는 다른 커널 수준 동작에서 발생했는지를 분석할 수 있다.

트레이싱 세션(Tracing Session)은 무엇을 기록하고 결과 데이터를 어디에 저장할지를 정의한다. 개발자는 세션을 생성하고 선택한 이벤트 클래스(Event Class)를 활성화한 다음 트레이싱을 시작하여 대상 작업 부하(Workload)를 재현하고, 트레이싱을 종료한 후 수집된 트레이스를 분석한다. 불필요한 활동까지 기록하면 트레이스 데이터의 양과 분석 복잡도가 증가하므로 선택적인 이벤트 설정(Selective Event Configuration)이 중요하다. 집중된 트레이스는 계측 및 저장 오버헤드를 줄이면서 더 명확한 분석 근거를 제공한다.

커널 스케줄러 이벤트(Kernel Scheduler Event)는 실시간 분석에서 가장 중요한 정보 중 하나이다. 스케줄러 트레이스포인트를 통해 스레드가 언제 실행 가능 상태(Runnable)가 되는지, 컨텍스트 전환(Context Switch)이 언제 발생하는지, 어떤 스레드가 선택되는지, 실시간 스레드가 CPU를 할당받기 전에 얼마나 기다리는지를 확인할 수 있다. 이를 통해 실제 연산 시간(Computation Time)과 스케줄링 지연(Scheduling Delay)을 분리하고 높은 우선순위 제어 스레드가 의도된 주기와 우선순위에 따라 실행되는지를 분석할 수 있다.

인터럽트 및 인터럽트 스레드(Interrupt Thread)의 동작 역시 PREEMPT_RT 시스템에서 중요하다. 하드웨어 이벤트(Hardware Event)는 인터럽트 처리와 커널 작업을 통해 직접 또는 간접적으로 응용 프로그램 실행을 지연시킬 수 있다. 인터럽트 활동과 스케줄러 이벤트를 함께 트레이싱하면 예상하지 못한 제어 루프 지연(Control-Loop Delay)이 인터럽트 버스트(Interrupt Burst), 긴 처리 경로, 경쟁하는 실시간 스레드 또는 다른 실행 간섭(Execution Interference)과 관련되어 있는지를 판단할 수 있다.

시스템 호출 트레이싱(System-Call Tracing)은 응용 프로그램과 Linux 커널 사이의 상호작용에 대한 가시성을 제공한다. 동기화(Synchronization), 타이머(Timer), 네트워킹(Networking), 파일 연산(File Operation), 메모리 관리(Memory Management)와 관련된 호출은 응용 코드만으로는 식별하기 어려운 지연을 발생시킬 수 있다. 시스템 호출의 진입 및 종료 이벤트를 스케줄러 활동과 연관시키면 지연이 응용 연산 내부, 커널 서비스를 기다리는 과정, 또는 스레드가 디스케줄(Descheduled)된 동안 발생했는지를 판단할 수 있다.

사용자 공간 트레이스포인트(User-Space Tracepoint)는 저수준 Linux 이벤트에 응용 프로그램의 의미(Application Meaning)를 추가한다. 로봇 응용 프로그램은 센서 수신(Sensor Reception), 제어 주기 활성화(Control-Cycle Activation), 위치추정 갱신(Localization Update), 궤적 생성(Trajectory Generation), 명령 전송(Command Transmission), 안전 상태 전환(Safety Transition) 등에 대한 트레이스 이벤트를 생성할 수 있다. 이러한 이벤트를 커널 트레이스와 동일한 타임라인에 배치하면 물리적 입력에서 소프트웨어 실행을 거쳐 액추에이터 명령 생성까지의 처리 경로를 추적할 수 있다.

트레이스 타임스탬프(Trace Timestamp)는 의미 있는 실시간 진단이 이벤트 순서와 시간 간격 측정에 의존하기 때문에 핵심적인 요소이다. 센서 도착(Sensor Arrival), 스레드 웨이크업(Thread Wakeup), 스케줄러 디스패치(Scheduler Dispatch), 제어 연산(Control Computation), 통신 전송(Communication Transmission), 액추에이터 명령(Actuator Command)의 흐름을 타임스탬프 이벤트로 표현할 수 있다. 이러한 타임스탬프 사이의 차이를 이용하면 응답시간(Response Time)과 지연시간(Latency)을 측정하고 제어 루프 지터(Control-Loop Jitter)를 증가시키는 변동성을 찾을 수 있다.

LTTng은 트레이싱 대상 시스템에 대한 간섭을 줄이기 위해 버퍼링(Buffering)을 사용한다. 이벤트는 트레이스 버퍼에 효율적으로 기록되고 이후에 소비되거나 저장되므로 즉시 사람이 읽을 수 있는 텍스트로 변환할 필요가 없다. 이는 터미널, 직렬 포트(Serial Port), 파일 시스템(File System)을 통한 동기식 로깅(Synchronous Logging)이 실시간 실행을 상당히 방해할 수 있기 때문에 중요하다. 바이너리 이벤트 기록(Binary Event Recording)은 빠른 데이터 수집과 상대적으로 느린 형식화 및 분석 작업을 분리한다.

그러나 버퍼 설정(Buffer Configuration)에는 공학적인 절충(Engineering Tradeoff)이 필요하다. 버퍼가 부족하면 높은 활동량에서 이벤트 손실(Event Loss)이 발생할 수 있고, 지나치게 큰 버퍼는 응용 프로그램에 필요한 메모리를 소비한다. 트레이스 지속시간과 활성화된 이벤트 밀도(Event Density)도 저장 공간 요구량에 영향을 준다. 따라서 실시간 실험에서는 이벤트 손실, 트레이스 오버헤드(Trace Overhead), 사용 가능한 메모리를 검증하여 측정 시스템 자체가 조사 대상의 동작을 크게 변화시키지 않도록 해야 한다.

Babeltrace2는 수집 이후 또는 수집 과정에서 트레이스 데이터를 읽고 처리함으로써 LTTng을 보완한다. LTTng은 일반적으로 공통 트레이스 형식(Common Trace Format, CTF)을 사용하여 트레이스를 생성하며, Babeltrace2는 이러한 이벤트 스트림(Event Stream)을 읽고 변환하고 필터링하며 표현하기 위한 프레임워크(Framework)를 제공한다. 이를 통해 트레이스 획득(Trace Acquisition)과 트레이스 해석(Trace Interpretation)을 분리하고 동일한 실행 이력을 명령줄 검사, 자동 처리, 상위 수준 분석 작업에 활용할 수 있다.

Babeltrace2는 대규모 트레이스에서 특정 문제와 관련된 이벤트만 추출해야 할 때 특히 유용하다. 모든 이벤트를 수동으로 검사하는 대신 특정 이벤트 유형(Event Type), 타임스탬프, 프로세스 또는 컨텍스트 필드(Context Field)를 중심으로 분석하고 반복적인 처리를 위한 파이프라인(Processing Pipeline)을 구성할 수 있다. 동일한 지연시간 측정이나 스케줄러 분석을 여러 실험 또는 소프트웨어 버전에 반복적으로 적용해야 할 때 이러한 방식이 효과적이다.

실제 분석에서는 사용자 공간의 제어 이벤트와 커널 스케줄링 정보를 서로 연관시킬 수 있다. 예를 들어 주기적인 로봇 제어 스레드가 간헐적으로 늦게 시작되는 경우 응용 트레이스포인트를 통해 예상된 제어 활동을 확인하고, 스케줄러 이벤트를 통해 스레드가 언제 실행 가능 상태가 되었으며 실제로 언제 실행되었는지를 확인할 수 있다. 여기에 인터럽트나 커널 이벤트를 결합하면 지연 시간 동안 발생한 경쟁 활동까지 파악하여 데드라인 이상(Deadline Anomaly)을 측정 가능한 원인들의 실행 순서로 변환할 수 있다.

이러한 기능은 다중 주기 로봇 작업 부하(Multi-Rate Robotic Workload)에서 특히 중요하다. 높은 주파수의 제어 스레드가 위치추정, 인지(Perception), 네트워킹, 로깅(Logging), ROS 2 통신, 시스템 관리 프로세스와 동일한 Linux 플랫폼에서 함께 실행될 수 있다. 평균 CPU 사용률이 적절하게 보이더라도 간헐적인 실행 간섭으로 허용할 수 없는 지연이 발생할 수 있다. 이벤트 트레이싱은 장기간 평균값에서는 사라지는 이러한 짧은 시간적 상호작용(Temporal Interaction)을 드러낸다.

따라서 트레이싱은 실제 시스템을 대표하는 부하(Representative System Load)에서 수행해야 한다. 네트워크 버스트(Network Burst), 센서 트래픽(Sensor Traffic), 저장장치 연산(Storage Operation), 높은 부하의 인지 작업, 장애 복구 절차(Fault-Recovery Procedure)는 스케줄러와 커널의 동작을 변화시킬 수 있다. 유휴 상태(Idle), 정상 운용(Nominal Operation), 최대 부하(Peak Load) 조건의 트레이스를 비교하면 전체 로봇 소프트웨어 스택이 동작할 때도 지연시간 분포(Latency Distribution)가 제한 범위 안에 유지되고 실시간 제어 경로에 충분한 타이밍 여유(Timing Margin)가 존재하는지 확인할 수 있다.

LTTng과 Babeltrace2는 JTAG, GDB, FreeRTOS 중심 도구와는 다른 디버깅 계층(Debugging Layer)에 위치한다. 하드웨어 디버깅(Hardware Debugging)은 시스템이 정지된 상태에서 상세한 검사를 제공하고, 런타임 통계(Runtime Statistics)는 자원 사용량을 요약하며, 시각적 RTOS 도구(Visual RTOS Tool)는 태스크 상호작용을 보여준다. Linux 트레이싱은 운영체제를 중단하지 않고 프로세스, 커널 스케줄링, 인터럽트, 시스템 호출, 사용자 응용 프로그램 전반으로 분석 범위를 확장한다.

수집된 트레이스는 자동화된 지연시간 및 지터 분석(Automated Latency and Jitter Analysis)의 입력 데이터로도 사용할 수 있다. 스크립트 또는 처리 파이프라인을 이용하여 선택한 이벤트 사이의 시간 간격을 계산하고, 최악 조건 샘플(Worst-Case Sample)을 식별하며, 분포를 생성하고, 서로 다른 소프트웨어 버전의 결과를 비교할 수 있다. 이를 통해 트레이싱은 대화형 디버깅 기법에서 반복 가능한 성능 검증 메커니즘(Performance-Validation Mechanism)으로 확장되며 시스템 통합 과정에서 실시간 동작을 지속적으로 검증하는 데 활용할 수 있다.

PREEMPT_RT를 사용하는 로봇 플랫폼에서 이러한 결합 방식은 응용 수준의 증상(Application-Level Symptom)과 운영체제 수준의 원인(Operating-System-Level Cause)을 연결하는 역할을 한다. 지연된 액추에이터 명령을 통신, 응용 프로그램 실행, 스케줄러 디스패치, 인터럽트 활동, 커널 서비스의 순서로 역추적할 수 있다. 따라서 Linux를 불투명한 실행 플랫폼으로 취급하는 대신 운영체제가 실시간 제어 경로에 어떻게 관여하는지를 시간적으로 연관된 형태로 분석할 수 있다.

Chapter_09_RTOS_Debugging_and_Tracing의 구조에서 LTTng과 Babeltrace2는 시각적 RTOS 분석(Visual RTOS Analysis)을 포괄적인 Linux 실행 트레이싱(Linux Execution Tracing)으로 확장한다. 이들은 이후의 커널 성능 분석(Kernel Performance Analysis), 하드웨어 타임스탬프 측정(Hardware Timestamp Measurement), 제어 루프 지연시간 분석(Control-Loop Latency Analysis), 동시성 디버깅(Concurrency Debugging), 프로파일링(Profiling), 현장 근본 원인 분석(Field Root-Cause Analysis)에 필요한 이벤트 및 타임스탬프 기반 구조를 제공하며, PREEMPT_RT 및 Linux 기반 로봇 시스템을 위한 중요한 진단 계층(Diagnostic Layer)을 형성한다.

## 09.05 ftrace and perf: Kernel-Level Performance Analysis [w/Code]

![](images/image5.png){width="7.268055555555556in" height="7.268055555555556in"}

ftrace는 Linux 커널(Linux Kernel)에 직접 내장된 트레이싱 프레임워크(Tracing Framework)로, 비교적 낮은 오버헤드(Low Overhead)로 실행 동작을 관찰할 수 있다. PREEMPT_RT 기반 로봇 제어기에서는 결정론적 제어(Deterministic Control)에 영향을 주는 스케줄러 활동(Scheduler Activity), 인터럽트 동작(Interrupt Behavior), 함수 실행(Function Execution), 웨이크업 지연시간(Wakeup Latency), 기타 커널 이벤트(Kernel Event)를 확인할 수 있다. 트레이싱이 커널 내부에서 수행되므로 응용 프로그램 로그(Application Log)만으로는 관찰할 수 없는 시간적 관계를 분석할 수 있다.

ftrace 인프라(Infrastructure)는 주로 Linux 트레이싱 파일 시스템(Tracing Filesystem)을 통해 접근하며, 일반적으로 tracefs를 통해 마운트된다. 개발자는 이 인터페이스에서 트레이서(Tracer)를 선택하고 특정 트레이스 이벤트(Trace Event)를 활성화하며 필터(Filter)를 적용하고 기록을 시작하거나 중지한 후 트레이스 버퍼(Trace Buffer)를 가져올 수 있다. 이러한 구조는 대화형 분석뿐만 아니라 성능 검증 과정에서 동일한 측정을 반복적으로 수행하는 자동화 스크립트(Automated Script)에도 유용하다.

가장 단순한 동작 방식 중 하나는 선택된 커널 트레이스포인트(Kernel Tracepoint)를 타임스탬프(Timestamp) 및 컨텍스트 정보(Context Information)와 함께 기록하는 이벤트 트레이싱(Event Tracing)이다. 스케줄러 이벤트(Scheduler Event), 인터럽트, 타이머(Timer), 워크 큐(Work Queue), 기타 커널 서브시스템(Kernel Subsystem)을 선택적으로 관찰할 수 있다. 모든 활동을 기록하기보다 의심되는 타이밍 문제와 관련된 이벤트만 제한적으로 추적하면 런타임 오버헤드와 분석해야 할 트레이스 데이터의 양을 모두 줄일 수 있다.

스케줄러 트레이싱(Scheduler Tracing)은 실시간 Linux(Real-Time Linux)에서 특히 중요하다. 스레드 웨이크업(Thread Wakeup)과 컨텍스트 전환(Context Switch) 같은 이벤트를 이용하면 제어 스레드(Control Thread)가 언제 실행 가능 상태(Runnable)가 되었는지, 실제로 언제 CPU를 할당받았는지, 이전에 어떤 태스크가 프로세서를 사용하고 있었는지, 언제 실행이 선점(Preemption)되었는지를 재구성할 수 있다. 웨이크업과 실제 실행 사이의 시간은 스케줄링 지연시간(Scheduling Latency)을 나타내며 주기적 제어 태스크가 예상보다 늦게 시작되는 경우 직접적인 분석 근거가 된다.

함수 트레이서(Function Tracer)는 스케줄러 이벤트 분석을 커널 실행 경로(Kernel Execution Path)까지 확장한다. 함수 트레이싱(Function Tracing)은 커널 함수의 진입을 기록하여 의심스러운 시간 구간에 어떤 함수가 실행되었는지를 확인할 수 있다. 함수 그래프 트레이싱(Function-Graph Tracing)은 여기에 호출 관계(Call Relationship)와 실행 시간(Execution Duration)을 추가하여 중첩된 커널 동작을 이해하기 쉽게 한다. 이를 통해 실시간 지연에 영향을 주는 비정상적으로 긴 드라이버 경로, 커널 서비스 또는 동기화 연산(Synchronization Operation)을 식별할 수 있다.

함수 트레이싱은 많은 데이터를 생성할 수 있으므로 필터링(Filtering)이 중요하다. 전체 커널을 추적하는 대신 특정 함수, 모듈(Module), 프로세스(Process), CPU 또는 이벤트에 분석 범위를 집중할 수 있다. 이는 높은 주파수로 동작하는 로봇 제어 시스템에서 특히 중요한데, 과도한 계측(Instrumentation)은 측정하려는 타이밍 자체에 영향을 줄 수 있기 때문이다. 일반적으로 전체 운영체제를 무제한으로 추적하는 것보다 명확한 가설에 기반하여 분석 범위를 좁힌 트레이스가 더 유용하다.

ftrace는 실시간 커널 분석에 유용한 지연시간 중심 메커니즘(Latency-Oriented Mechanism)도 제공한다. 커널 설정(Kernel Configuration)에 따라 인터럽트 비활성 구간(Interrupt-Disabled Section), 선점 비활성 구간(Preemption-Disabled Region), 스케줄링 지연, 웨이크업 동작 등을 조사하는 트레이서를 사용할 수 있다. 이러한 측정을 통해 응용 프로그램 자체의 연산 시간이 짧더라도 높은 우선순위의 실시간 스레드가 실행되지 못하도록 방해하는 커널 실행 영역을 식별할 수 있다.

인터럽트 분석(Interrupt Analysis)은 또 다른 중요한 진단 경로를 제공한다. 주기적인 제어 스레드는 예상된 실행 시점 주변에서 하드웨어 인터럽트(Hardware Interrupt), 스레드화 인터럽트(Threaded Interrupt), 소프트IRQ(Softirq), 지연된 커널 작업(Deferred Kernel Work)이 프로세서를 점유하면서 지터(Jitter)를 경험할 수 있다. 이러한 활동을 스케줄러 이벤트와 연관시키면 장애가 응용 프로그램 간 경쟁, 디바이스 드라이버(Device Driver), 네트워킹(Networking), 저장장치(Storage), 기타 커널 서브시스템 중 어디에서 발생했는지를 판단할 수 있다.

ftrace가 실행의 시간적 순서(Execution Chronology)를 중심으로 분석한다면 perf는 카운터(Counter), 샘플링(Sampling), 프로파일링(Profiling)을 중심으로 하는 보완적인 성능 분석 프레임워크(Performance-Analysis Framework)를 제공한다. Linux perf 서브시스템은 하드웨어 성능 모니터링 장치(Hardware Performance Monitoring Unit)와 소프트웨어 이벤트를 이용하여 CPU 사이클(CPU Cycle), 명령어(Instruction), 캐시 동작(Cache Behavior), 컨텍스트 전환, 페이지 폴트(Page Fault) 등의 실행 특성을 측정할 수 있다. 이를 통해 프로세서 자원이 어디에서 소비되고 연산 비용이 증가하는 이유가 무엇인지를 정량적으로 분석할 수 있다.

\`perf stat\` 작업 흐름(Workflow)은 집계된 성능 특성(Aggregate Performance Characteristic)을 분석할 때 유용하다. 모든 이벤트를 시간순으로 재구성하는 대신 특정 실행 구간에서 선택된 카운터 값을 요약한다. 사이클, 명령어, 컨텍스트 전환, 캐시 관련 이벤트, CPU 마이그레이션(CPU Migration) 등을 측정하면 기능적 동작에는 변화가 없어 보이더라도 소프트웨어 변경 이후 작업 부하의 실행 특성이 크게 변화했는지를 발견할 수 있다.

\`perf record\`를 이용한 샘플링은 실행이 발생하는 위치를 주기적으로 식별하여 또 다른 수준의 분석을 제공한다. 수집된 샘플은 이후 어떤 함수나 코드 경로(Code Path)가 상당한 프로세서 시간을 소비했는지 확인하는 데 사용할 수 있다. 모든 함수 호출을 기록하는 포괄적인 함수 트레이싱과 달리 통계적 샘플링(Statistical Sampling)은 모든 함수 호출을 저장할 필요가 없어 긴 작업 부하에서도 실용적일 수 있지만, 선택된 샘플링 주파수(Sampling Frequency)는 여전히 오버헤드와 분석 해상도에 영향을 준다.

호출 그래프 정보(Call-Graph Information)는 perf 프로파일링을 개별 함수에서 전체 실행 경로로 확장할 수 있다. 연산 핫스팟(Computational Hotspot)은 어떤 호출자가 해당 함수를 반복적으로 실행시켰는지 이해하지 못하면 그 의미를 정확히 판단하기 어렵다. 호출 그래프는 함수 사이의 관계를 보여주어 본질적으로 연산 비용이 높은 루틴과 다른 구성요소가 지나치게 자주 호출하여 비용이 증가한 루틴을 구분하는 데 도움을 준다. 이는 복잡한 Linux 로봇 소프트웨어 스택(Robotic Software Stack)을 최적화할 때 중요하다.

하드웨어 성능 카운터(Hardware Performance Counter)는 타이밍 트레이스(Timing Trace)만으로 설명하기 어려운 정보를 제공한다. 인지(Perception) 또는 제어 작업 부하는 캐시 미스(Cache Miss), 메모리 접근 동작(Memory-Access Behavior), 분기 동작(Branch Effect), 증가된 명령어 수 등으로 인해 더 긴 실행 시간을 요구할 수 있다. perf는 이러한 프로세서 아키텍처 수준의 특성과 소프트웨어 핫스팟(Software Hotspot)을 연관시켜 스케줄링 간섭(Scheduling Interference)과 비효율적인 연산 또는 불리한 프로세서 동작을 구분할 수 있도록 한다.

실시간 스레드를 분석할 때 CPU 친화도(CPU Affinity)와 마이그레이션도 고려해야 한다. 프로세서 코어 사이를 이동하는 스레드는 서로 다른 캐시 상태(Cache State)를 경험하고 다른 작업 부하와 경쟁할 수 있다. ftrace는 스케줄링 및 마이그레이션 동작을 보여주고 perf는 그와 관련된 연산 효과를 정량화할 수 있다. 두 도구를 함께 사용하면 PREEMPT_RT 시스템에서 사용되는 CPU 격리(CPU Isolation), IRQ 친화도(IRQ Affinity), 스레드 배치(Thread Placement) 전략을 분석할 수 있다.

따라서 ftrace와 perf의 관계는 경쟁적이라기보다 상호 보완적(Complementary)이다. ftrace는 주로 이벤트가 언제 발생했는지, 실행 순서가 어떻게 구성되었는지, 어떤 커널 경로가 지연을 발생시켰는지를 설명한다. 반면 perf는 프로세서 시간이 어디에서 소비되었으며 어떤 하드웨어 또는 소프트웨어 특성이 연산 비용에 영향을 주었는지를 설명한다. 시간적 트레이싱(Temporal Tracing)과 통계적 프로파일링(Statistical Profiling)을 결합하면 성능 문제에 대한 보다 완전한 설명을 얻을 수 있다.

예를 들어 ftrace를 통해 로봇 제어 스레드가 다른 커널 실행 경로가 CPU를 점유하기 때문에 간헐적으로 예상 시점보다 수백 마이크로초 늦게 실행된다는 사실을 발견할 수 있다. 이후 perf를 사용하여 해당 작업 부하를 프로파일링하면 집중적인 연산, 지나치게 높은 호출 빈도, 캐시 동작 또는 다른 자원 효과 때문에 해당 경로가 많은 시간을 소비하는지를 판단할 수 있다. 이를 통해 분석 과정은 단순히 지연을 발견하는 단계에서 지연의 연산적 원인(Computational Cause)을 설명하는 단계로 발전한다.

측정 오버헤드(Measurement Overhead)는 항상 고려해야 한다. 상세한 함수 트레이싱, 고주파 샘플링(High-Frequency Sampling), 광범위한 호출 그래프, 대규모 이벤트 집합은 시스템 동작에 영향을 줄 수 있다. 따라서 먼저 가벼운 측정(Lightweight Measurement)으로 시작하고 의심스러운 실행 영역을 좁힌 후 점진적으로 상세한 계측을 활성화해야 한다. 또한 트레이싱을 비활성화한 상태와 결과를 비교하여 진단 설정 자체가 실시간 동작을 실질적으로 변화시키지 않았는지 확인해야 한다.

로봇 시스템(Robotic System)은 Linux 프로세서에서 제어, 인지, 위치추정(Localization), 네트워킹, 미들웨어(Middleware), 로깅, 진단 기능을 동시에 실행하는 경우가 많기 때문에 이러한 계층적 접근(Layered Approach)의 이점을 크게 얻을 수 있다. 평균 CPU 사용률만으로는 짧은 실행 간섭이나 비용이 높은 커널 경로를 발견하기 어렵다. ftrace는 이들의 시간적 관계를 보여주고 perf는 연산 핫스팟과 프로세서 수준 동작을 식별하여 우선순위, CPU 친화도, 드라이버, 작업 부하 최적화를 위한 근거를 제공한다.

이러한 도구는 PREEMPT_RT 설정을 평가할 때 특히 유용하다. CPU 격리, IRQ 친화도, 실시간 우선순위(Real-Time Priority), 메모리 잠금(Memory Locking), 스레드 배치는 중요한 실행 경로를 간섭으로부터 보호하기 위한 것이다. ftrace와 perf를 이용하여 설정 변경 전후를 측정하면 이러한 변경이 실제로 웨이크업 지연, 컨텍스트 전환, CPU 마이그레이션, 연산 경합(Computational Contention), 기타 실시간 변동성(Real-Time Variability)의 원인을 감소시키는지 판단할 수 있다.

Chapter_09_RTOS_Debugging_and_Tracing의 구조에서 ftrace와 perf는 LTTng 및 Babeltrace2를 통해 도입된 Linux 트레이싱을 더욱 심층적인 수준으로 확장한다. LTTng은 커널과 사용자 공간 전체에 걸친 광범위한 시간 상관 이벤트 수집(Time-Correlated Event Collection)을 제공하며, ftrace는 커널 실행과 지연시간에 대한 집중적인 분석을 가능하게 한다. 여기에 perf가 카운터 기반 및 샘플링 기반 프로파일링을 추가함으로써 시스템 수준 관찰(System-Level Observation)에서 상세한 성능 진단(Performance Diagnosis)으로 이어지는 분석 흐름을 구성한다.

이러한 메커니즘을 함께 사용하면 이후의 하드웨어 타임스탬프 기반 지터 측정(Hardware Timestamp Jitter Measurement)과 로봇 제어 루프 지연시간 분석(Robot Control-Loop Latency Analysis)에 필요한 커널 수준의 근거(Kernel-Level Evidence)를 확보할 수 있다. 이를 통해 타이밍 장애가 스케줄링, 인터럽트, 커널 실행, CPU 배치(CPU Placement), 연산 핫스팟 중 어디에서 발생했는지를 판단하고, 관찰된 실시간 이상(Real-Time Anomaly)을 측정 가능한 실행 경로로 변환하여 최적화한 뒤 실제 로봇 작업 부하에서 다시 검증할 수 있다.

## 09.06 HW Timestamp-Based Jitter Measurement Automation [w/Code]

![](images/image6.png){width="7.268055555555556in" height="7.268055555555556in"}

하드웨어 타임스탬프 기반 지터 측정(Hardware Timestamp-Based Jitter Measurement)은 실시간 이벤트(Real-Time Event)의 실제 발생 시점이 의도된 스케줄에서 얼마나 벗어나는지를 고정밀로 측정하는 방법을 제공한다. 소프트웨어 클록(Software Clock), 로그(Log), 스케줄러 타임스탬프(Scheduler Timestamp)에만 의존하지 않고 하드웨어 경계(Hardware Boundary)에 가까운 위치에서 이벤트 시간을 포착한다. 이를 통해 소프트웨어 실행으로 발생하는 측정 불확실성을 줄이고 RTOS 또는 PREEMPT_RT 제어 시스템의 결정론적 동작(Deterministic Behavior)을 보다 객관적으로 검증할 수 있다.

지터(Jitter)는 단순한 평균 지연시간이 아니라 반복적으로 발생하는 이벤트의 타이밍 변동(Timing Variation)을 의미한다. 로봇 제어 루프(Robot Control Loop)가 1ms마다 실행되도록 설계되었다면 평균 주기가 약 1ms인지 확인하는 것만으로는 충분하지 않으며, 각각의 제어 주기(Control Cycle)가 목표값에서 얼마나 벗어나는지를 확인해야 한다. 하드웨어 타임스탬핑(Hardware Timestamping)을 사용하면 타이머, 캡처 주변장치(Capture Peripheral), 측정 아키텍처에 따라 마이크로초 또는 그보다 더 높은 수준의 정밀도로 이러한 편차를 측정할 수 있다.

하드웨어 타이머(Hardware Timer)는 대부분의 응용 소프트웨어 활동과 독립적으로 동작하는 안정적인 시간 기준(Time Reference)을 제공한다. 최신 MCU, SoC, 네트워크 제어기(Network Controller), 측정 장치는 특정 하드웨어 이벤트가 발생할 때 타임스탬프를 기록할 수 있는 프리러닝 카운터(Free-Running Counter) 또는 캡처 장치(Capture Unit)를 제공하는 경우가 많다. 이벤트 발생 후 소프트웨어 시간 함수를 호출할 때까지 기다릴 필요 없이 타임스탬프를 포착하므로 측정 불확실성을 크게 줄일 수 있다.

GPIO 토글링(GPIO Toggling)은 소프트웨어 타이밍을 외부 측정 하드웨어에 노출하는 가장 단순한 방법 중 하나이다. 제어 태스크(Control Task)는 진입, 종료 또는 중요한 실행 지점에서 출력 핀(Output Pin)을 전환할 수 있다. 오실로스코프(Oscilloscope), 로직 분석기(Logic Analyzer), 타이머 캡처 채널(Timer Capture Channel), FPGA는 이러한 물리적 신호 전환을 측정한다. 그 결과 제어 루프 주기, 실행시간(Execution Duration), 응답 지연(Response Delay), 주기 간 타이밍 변동을 독립적으로 측정할 수 있다.

내부 하드웨어 캡처(Internal Hardware Capture)를 사용하면 외부 실험 장비 없이도 유사한 측정을 수행할 수 있다. 타이머 입력 캡처 주변장치(Timer Input-Capture Peripheral)는 GPIO 에지(Edge) 또는 동기화 신호가 발생할 때 카운터 값을 래치(Latch)할 수 있다. DMA를 이용하면 프로세서 개입을 최소화하면서 캡처된 값을 메모리로 전송할 수 있다. 이러한 구조는 관찰자 효과(Observer Effect)를 줄이면서 장시간 지터를 측정할 수 있어 자동 회귀 시험(Automated Regression Test)과 임베디드 성능 모니터링(Embedded Performance Monitoring)에 적합하다.

측정 지점(Measurement Point)은 조사하려는 타이밍 특성에 따라 선택해야 한다. 태스크 활성화(Task Activation)를 타임스탬핑하면 릴리스 동작(Release Behavior)을 측정할 수 있고, 실제 태스크 실행 시점을 기록하면 스케줄링 효과(Scheduling Effect)를 확인할 수 있다. 연산, 통신 전송, 액추에이터 출력 주변에 추가 타임스탬프를 설정하면 스케줄링 지연(Scheduling Latency), 실행시간, 입출력 지연(I/O Delay)을 분리할 수 있다. 따라서 여러 측정 지점을 사용하면 하나의 지터 값이 아니라 전체 제어 경로(Control Path)의 타이밍을 세부적으로 분해할 수 있다.

주기적인 이벤트(Periodic Event)의 경우 각각의 측정된 타임스탬프를 이전 이벤트 또는 이상적인 기준 스케줄(Ideal Reference Schedule)과 비교할 수 있다. 주기 지터(Period Jitter)는 연속된 타임스탬프 간격의 변동으로 계산하며, 릴리스 지터(Release Jitter) 또는 위상 지터(Phase Jitter)는 예상된 절대 활성화 시점으로부터의 편차를 측정한다. 이들은 서로 다른 타이밍 특성을 의미하므로 혼용해서는 안 되며, 자동화 도구는 각각의 측정 지표에 사용된 이벤트와 기준을 명확하게 기록해야 한다.

유용한 측정 시스템은 평균값만 저장하는 대신 원시 타임스탬프 시퀀스(Raw Timestamp Sequence)를 보존해야 한다. 전체 데이터셋을 이용하면 최소값(Minimum), 최대값(Maximum), 평균(Mean), 표준편차(Standard Deviation), 백분위수(Percentile), 피크 투 피크 지터(Peak-to-Peak Jitter), 최악 조건 편차(Worst-Case Deviation)를 계산할 수 있다. 히스토그램(Histogram)과 누적 분포(Cumulative Distribution)를 이용하면 타이밍 변동이 좁고 안정적인 분포를 형성하는지, 또는 평균 성능은 양호하지만 실시간 데드라인을 위협하는 드문 롱테일 이벤트(Long-Tail Event)가 존재하는지를 확인할 수 있다.

로보틱스(Robotics)에서는 평균 성능보다 간헐적인 타이밍 이탈(Timing Excursion)이 더 중요할 수 있으므로 최악 조건 관측(Worst-Case Observation)이 특히 중요하다. 제어기는 수백만 주기 동안 정상적으로 실행되다가 인터럽트 버스트(Interrupt Burst), 스케줄러 상호작용(Scheduler Interaction), 메모리 이벤트(Memory Event), 통신 활동, 드라이버 동작으로 인해 한 번의 큰 지연을 경험할 수 있다. 장시간 자동 측정(Long-Duration Automated Measurement)은 이러한 희귀 이벤트를 포착할 가능성을 높이고 실제 운용 조건에서 타이밍 여유(Timing Margin)를 평가할 수 있는 근거를 제공한다.

자동화(Automation)는 하드웨어 타임스탬핑을 단순한 실험실 측정에서 반복 가능한 검증 프로세스(Repeatable Verification Process)로 전환한다. 시험 프로그램(Test Program)은 작업 부하를 설정하고 타임스탬프 수집을 시작한 뒤 정의된 시나리오를 실행하고, 수집을 종료한 후 샘플을 처리하여 허용 기준(Acceptance Criteria)과 자동으로 비교할 수 있다. 동일한 절차를 커널, 펌웨어, 드라이버, 컴파일러, 우선순위, 하드웨어 설정 변경 이후 반복함으로써 객관적인 성능 회귀(Performance Regression)를 탐지할 수 있다.

합격 및 불합격 기준(Pass/Fail Criteria)은 임의의 통계적 임계값이 아니라 시스템 요구사항(System Requirement)을 반영해야 한다. 예를 들어 1kHz 제어 루프는 명목상 1ms 주기(Nominal Period)와 함께 허용 가능한 편차 및 데드라인을 정의할 수 있다. 자동 분석은 관찰된 모든 주기가 허용된 타이밍 범위(Timing Envelope) 안에 유지되는지를 판단할 수 있다. 추가적인 경고 임계값(Warning Threshold)을 설정하면 실제 실시간 장애에 도달하기 전에 점진적인 성능 저하를 탐지할 수도 있다.

여러 장치 또는 프로세서에서 생성된 타임스탬프를 비교하려면 동기화(Synchronization)가 중요해진다. MCU, 엣지 컴퓨터(Edge Computer), 네트워크 인터페이스(Network Interface), 외부 측정 장비는 각각 별도의 클록(Clock)을 유지할 수 있다. 이러한 타임스탬프를 직접 비교하려면 공통 시간 기준(Common Time Base) 또는 클록 동기화 메커니즘(Clock Synchronization Mechanism)이 필요하다. 하드웨어 지원 동기화, PTP, 동기화 트리거 신호(Synchronized Trigger Signal), 공통 기준 클록(Common Reference Clock)을 이용하면 장치 경계를 넘는 종단 간 타이밍(End-to-End Timing)을 측정할 때 클록 오프셋(Clock Offset)과 드리프트(Drift)를 줄일 수 있다.

클록 품질(Clock Quality) 역시 측정 정확도에 영향을 준다. 타이머 해상도(Timer Resolution)는 관찰 가능한 최소 시간 간격을 결정하고, 오실레이터 안정성(Oscillator Stability)은 장시간 측정에 영향을 준다. 카운터 롤오버(Counter Rollover)를 올바르게 처리해야 하며, 타임스탬프 소스가 불변형(Invariant)이 아닌 경우 전력 관리(Power Management)에 따른 주파수 변화가 측정 가정을 무효화할 수 있다. 따라서 신뢰할 수 있는 자동화 프레임워크는 측정 샘플과 함께 타이머 주파수, 클록 소스, 해상도, 롤오버 동작, 동기화 상태를 기록해야 한다.

하드웨어 타임스탬핑은 소프트웨어 트레이싱(Software Tracing)과 연관시킬 때 특히 강력하다. 물리적 GPIO 전환은 데드라인 이상이 정확히 언제 발생했는지를 보여주고, ftrace, LTTng 또는 RTOS 트레이싱은 해당 시점에 프로세서가 무엇을 수행하고 있었는지를 설명할 수 있다. 하드웨어 타임스탬프를 스케줄러, 인터럽트, 드라이버, 응용 이벤트와 일치시키면 외부에서 검증된 실제 타이밍 동작을 내부 소프트웨어 실행 순서(Software Execution Sequence)와 연결할 수 있다.

이러한 상관 분석(Correlation Analysis)은 측정(Measurement)과 설명(Explanation)을 분리하는 데 도움을 준다. 하드웨어 타임스탬프는 실제 물리적 타이밍 요구사항이 충족되었는지를 판단하고, 소프트웨어 트레이스는 성공 또는 실패를 발생시킨 메커니즘을 식별한다. 예를 들어 외부 캡처를 통해 액추에이터 명령(Actuator Command)이 비정상적으로 늦게 발생했음을 발견하고, 커널 트레이싱을 통해 직전에 인터럽트, CPU 마이그레이션(CPU Migration), 경쟁 스레드(Competing Thread), 긴 커널 실행 경로가 제어 스레드를 지연시켰음을 확인할 수 있다.

중요한 관찰 지점에서 측정 오버헤드(Measurement Overhead)는 매우 작게 유지되어야 한다. 타임스탬프 출력, 문자열 형식화(String Formatting), 메모리 할당(Memory Allocation), 모든 샘플의 동기식 전송(Synchronous Transmission)은 추가적인 지터를 발생시킬 수 있다. 따라서 하드웨어 캡처, 직접 레지스터 연산(Direct Register Operation), 사전 할당 버퍼(Preallocated Buffer), DMA, 지연된 데이터 처리(Deferred Data Processing)를 사용하는 것이 바람직하다. 또한 측정 장치 자체의 지연과 변동성을 별도로 특성화하여 시스템 지터로 잘못 해석하지 않도록 해야 한다.

의미 있는 결과를 얻으려면 실제 운용을 대표하는 작업 부하(Representative Workload)를 선택해야 한다. 제어기가 유휴 상태일 때뿐만 아니라 실제 센서 트래픽, 네트워크 통신, 인지 작업 부하(Perception Workload), 저장장치 활동, 액추에이터 동작, 장애 복구(Fault Recovery)가 수행되는 동안에도 지터를 측정해야 한다. 시스템 부하를 단계적으로 증가시키면서 결과 분포를 비교하면 결정론적 타이밍이 어느 조건에서 저하되기 시작하는지와 어떤 운용 조건이 사용 가능한 타이밍 여유를 소비하는지를 확인할 수 있다.

로봇 플랫폼(Robotic Platform)에서는 자동화된 타임스탬프 시험(Automated Timestamp Test)을 하드웨어 인 더 루프(Hardware-in-the-Loop, HIL) 및 시스템 통합 환경(System-Integration Environment)에 포함할 수 있다. 모션 제어기(Motion Controller), MCU, PREEMPT_RT 엣지 컴퓨터, 통신 게이트웨이(Communication Gateway)가 정의된 타이밍 신호를 출력하면 시험 장비가 이를 자동으로 기록한다. 소프트웨어는 결과 지표를 펌웨어 및 시스템 버전과 함께 보관하여 전체 개발 과정에 걸친 실시간 성능 이력(Real-Time Performance History)을 구축할 수 있다.

Chapter_09_RTOS_Debugging_and_Tracing의 구조에서 하드웨어 타임스탬프 기반 지터 측정은 소프트웨어 트레이싱과 커널 수준 성능 분석(Kernel-Level Performance Analysis) 다음에 위치하며, 물리적 시스템 경계에 가까운 독립적인 시간 기준을 제공한다. JTAG와 런타임 도구(Runtime Tool)는 내부 상태를 보여주고, Tracealyzer와 LTTng은 실행 관계를 노출하며, ftrace와 perf는 커널 동작 및 연산 비용을 식별한다. 하드웨어 타임스탬핑은 이러한 내부 메커니즘이 최종적으로 요구되는 물리적 타이밍을 만족하는지를 검증한다.

결과적으로 자동화된 작업 흐름(Automated Workflow)은 디버깅에서 정량적인 실시간 검증(Quantitative Real-Time Validation)으로 이어지는 연결고리를 형성한다. 로봇 제어기가 단순히 빠르게 반응하는 것처럼 보인다고 판단하는 대신 실제 이벤트 주기, 지터 분포(Jitter Distribution), 최악 조건 편차, 데드라인 준수(Deadline Compliance)를 정의된 작업 부하에서 측정할 수 있다. 이러한 측정은 이후 제어 루프 지연시간 분석(Control-Loop Latency Analysis), 동시성 조사(Concurrency Investigation), 프로파일링(Profiling), 현장 근본 원인 분석(Field Root-Cause Analysis)의 기반이 되며 결정론적 로봇 제어(Deterministic Robotic Control)를 반복적으로 검증할 수 있도록 한다.

## 09.07 Robot Control Loop Latency Analysis Methodology

![](images/image7.png){width="7.268055555555556in" height="7.268055555555556in"}

로봇 제어 루프 지연시간 분석(Robot Control-Loop Latency Analysis)은 물리적 또는 논리적 입력 이벤트(Input Event)에서 이에 대응하는 액추에이터 명령(Actuator Command)이 생성되기까지 정보가 전달되는 데 걸리는 시간을 분석한다. CPU 사용률(CPU Utilization)이나 태스크 실행시간(Task Execution Time)만 측정하는 것과 달리 종단 간 지연시간(End-to-End Latency)은 센싱(Sensing), 인터럽트 처리, 스케줄링, 연산, 통신, 출력 단계를 모두 포함한다. 따라서 제어 태스크를 독립적인 소프트웨어 함수로 보는 것이 아니라 전체 인과 경로(Causal Path)를 분석해야 한다.

분석의 출발점은 제어 루프(Control Loop)의 타이밍 경계(Timing Boundary)를 명확하게 정의하는 것이다. 시작점은 센서 샘플링 에지(Sensor Sampling Edge), 하드웨어 인터럽트(Hardware Interrupt), 네트워크 프레임 도착(Network-Frame Arrival), 주기적 타이머 릴리스(Periodic Timer Release)가 될 수 있으며, 종료점은 명령 전송(Command Transmission), 모터 제어기 수신(Motor-Controller Reception), 실제 액추에이터 갱신(Physical Actuator Update)이 될 수 있다. 경계를 명확히 정의하지 않으면 서로 다른 도구의 측정값이 다른 지연 요소를 나타내어 신뢰성 있게 비교하기 어렵다.

종단 간 제어 지연시간은 개념적으로 센서 획득 지연(Sensor Acquisition Delay), 인터럽트 또는 입력 처리 지연(Input-Processing Delay), 태스크 웨이크업 및 스케줄링 지연(Task Wakeup and Scheduling Latency), 제어 연산 시간(Control Computation Time), 통신 지연(Communication Delay), 액추에이터 인터페이스 지연(Actuator-Interface Delay)으로 분해할 수 있다. 전체 지연시간이 과도하다는 사실만으로는 원인을 알 수 없으므로 중간 지점의 타임스탬프(Timestamp)를 측정하여 각 요소가 전체 타이밍 예산(Timing Budget)을 얼마나 소비하는지 파악해야 한다.

스케줄링 지연시간(Scheduling Latency)은 제어 태스크가 실행 준비 상태(Ready)가 된 시점부터 실제로 CPU를 할당받아 실행을 시작할 때까지의 시간이다. RTOS 또는 PREEMPT_RT 시스템에서는 높은 우선순위 작업, 인터럽트, 비선점 구간(Non-Preemptible Region), CPU 경합(CPU Contention), 동기화(Synchronization)로 인해 이 시간이 변동될 수 있다. Tracealyzer, LTTng, ftrace의 스케줄러 트레이스(Scheduler Trace)를 이용하면 이 구간을 확인하고 지연된 디스패치(Delayed Dispatch)와 과도한 제어 알고리즘 실행시간을 구분할 수 있다.

실행 지연시간(Execution Latency)은 태스크가 실행을 시작한 이후 제어 연산을 완료하는 데 필요한 시간을 의미한다. 여기에는 상태 추정(State Estimation), 필터링(Filtering), 궤적 처리(Trajectory Processing), 피드백 연산(Feedback Calculation), 안전 검사(Safety Check), 명령 생성(Command Generation)이 포함될 수 있다. 캐시 동작(Cache Behavior), 조건 분기, 데이터 의존 연산(Data-Dependent Computation), 선점(Preemption), 메모리 활동으로 주기별 변동이 발생할 수 있으므로 하나의 평균값이 아니라 분포(Distribution)로 측정해야 한다.

제어 처리가 여러 프로세서나 장치에 걸쳐 수행되면 통신 지연시간(Communication Latency)이 중요해진다. 명령은 엣지 컴퓨터(Edge Computer)에서 Ethernet, CAN, CAN FD, CANopen, EtherCAT, 공유 메모리(Shared Memory) 등의 전송 방식을 거쳐 하위 제어기(Lower-Level Controller)에 도달할 수 있다. 실제 통신 지연에는 순수 전송 시간뿐만 아니라 큐잉(Queueing), 프로토콜 처리(Protocol Processing), 드라이버 실행, 버스 중재(Bus Arbitration), 버퍼링(Buffering), 수신 태스크 스케줄링도 포함된다.

다중 주기 아키텍처(Multi-Rate Architecture)에서는 센싱, 인지(Perception), 계획(Planning), 제어(Control), 액추에이터 루프(Actuator Loop)가 서로 다른 주파수로 동작할 수 있으므로 특별한 주의가 필요하다. 카메라는 초당 수십 프레임의 데이터를 제공하고 엣지 수준 판단은 더 낮은 주기로 실행되는 반면, 모터 제어기는 수백 Hz 또는 kHz로 동작할 수 있다. 따라서 지연시간 분석에서는 갱신 주기(Update Rate)와 응답 지연시간(Response Latency)을 구분하고 어떤 데이터 샘플이 실제로 각각의 액추에이터 명령에 영향을 주었는지를 확인해야 한다.

정보 신선도(Age of Information)는 다중 주기 로봇 시스템에서 유용한 또 다른 개념이다. 제어 출력이 태스크 실행 이후 빠르게 생성되었더라도 센서 획득, 버퍼링, 인지 처리 또는 통신 지연 때문에 이미 오래된 센서 정보에 의존할 수 있다. 단순히 연산 시간만 측정하면 이러한 현상을 발견할 수 없다. 원본 센서 데이터에 연결된 타임스탬프를 추적하면 액추에이터 명령이 생성되는 시점에서 사용된 정보의 실제 신선도를 추정할 수 있다.

하드웨어 타임스탬프(Hardware Timestamp)는 소프트웨어 계측(Software Instrumentation)에 따른 불확실성을 줄이기 때문에 중요한 타이밍 경계에 대한 가장 강력한 기준을 제공한다. GPIO 전환, 타이머 캡처 장치(Timer Capture Unit), 네트워크 하드웨어 타임스탬프, FPGA 캡처 로직(Capture Logic), 동기화된 외부 측정 장비를 이용하여 물리적 인터페이스에 가까운 중요 이벤트를 기록할 수 있다. 이후 소프트웨어 트레이스를 이러한 측정값과 정렬하여 외부에서 검증된 타이밍 지점 사이에서 내부적으로 어떤 실행이 발생했는지를 설명할 수 있다.

완전한 분석에서는 지연시간(Latency)과 지터(Jitter)를 구분해야 한다. 지연시간은 정의된 시작 이벤트와 종료 이벤트 사이의 경과 시간을 의미하고, 지터는 반복되는 주기에서 해당 지연시간 또는 이벤트 타이밍이 얼마나 변화하는지를 나타낸다. 평균 지연시간이 허용 범위에 있더라도 간헐적으로 매우 큰 값이 발생할 수 있다. 따라서 단일 평균값보다 최소값, 최대값, 평균, 백분위수(Percentile), 분포, 최악 조건 관측(Worst-Case Observation)이 더 유용하다.

데드라인 분석(Deadline Analysis)은 측정된 지연시간에 시스템 요구사항(System Requirement)을 결합한다. 입력 이벤트가 발생한 후 정의된 시간 이내에 액추에이터 명령이 생성되어야 한다면 각각의 측정 경로를 해당 데드라인과 비교할 수 있다. 데드라인과 관찰된 지연시간 사이의 차이가 타이밍 여유(Timing Margin)가 된다. 작은 양의 여유를 가진 시스템은 기술적으로 요구사항을 만족하더라도 추가적인 센서, 통신, 진단 또는 연산 부하에 취약할 수 있다.

꼬리 지연시간(Tail Latency)은 드물게 발생하는 지연이 실시간 신뢰성(Real-Time Reliability)을 결정하는 경우가 많기 때문에 특별히 주의해야 한다. 99번째, 99.9번째 또는 더 높은 백분위수는 평균값에 숨겨진 동작을 보여줄 수 있지만, 안전 중요 요구사항(Safety-Critical Requirement)에서는 백분위수 기반 합격 기준만으로 충분하지 않고 직접적인 최악 조건 분석이 필요할 수 있다. 장시간 측정을 수행하면 간헐적으로 발생하는 인터럽트 폭주, 통신 버스트, 메모리 영향, 드라이버 이상, 스케줄러 상호작용을 포착할 가능성이 높아진다.

여러 장치에 걸친 측정에서는 공통된 시간 기준(Common Time Base)이 필요하다. MCU, Linux 엣지 컴퓨터, 네트워크 인터페이스, 모터 제어기에서 각각 생성된 타임스탬프를 비교할 때 클록 오프셋(Clock Offset)과 드리프트(Drift)가 실제 지연처럼 나타날 수 있다. PTP, 하드웨어 동기화 신호(Hardware Synchronization Signal), 공유 클록(Shared Clock), 특성이 검증된 타임스탬프 교환 방식을 이용하여 공통 시간 기준을 구성할 수 있다. 동기화 정확도는 분석하려는 지연시간 변동보다 충분히 높아야 한다.

관찰자 효과(Observer Effect) 역시 제어해야 한다. 중요한 실행 경로 내부에서 로그를 출력하거나 파일을 동기식으로 기록하고 메모리를 할당하거나 진단 메시지를 전송하면 측정하려는 지연시간 자체가 증가할 수 있다. 저오버헤드 트레이스포인트(Low-Overhead Tracepoint), 하드웨어 캡처, 사전 할당 버퍼(Preallocated Buffer), DMA, 지연 처리(Deferred Processing)를 사용하는 것이 바람직하다. 또한 계측을 줄이거나 비활성화한 상태에서 측정을 반복하여 진단 메커니즘이 시스템 타이밍을 실질적으로 변화시키는지 확인해야 한다.

실제 운용을 대표하는 작업 부하(Representative Workload)에서 시험하는 것이 필수적이다. 유휴 상태의 프로세서에서 측정한 지연시간은 실제 배치 환경을 제대로 나타내지 못하는 경우가 많다. 로봇 움직임, 센서 트래픽, 네트워크 버스트(Network Burst), 위치추정(Localization), 인지, 로깅(Logging), 저장장치 활동(Storage Activity), 진단, 장애 복구(Fault Recovery)가 처리 및 통신 자원을 놓고 경쟁할 수 있다. 따라서 유휴, 정상 부하(Nominal Load), 최대 부하(Peak Load), 비정상 시나리오에서 측정하여 실제 시스템의 지연시간 범위(Latency Envelope)를 확인해야 한다.

하드웨어 타임스탬프, 스케줄러 트레이스, 커널 트레이스(Kernel Trace), 프로파일링 데이터(Profiling Data)를 동일한 이벤트 시퀀스(Event Sequence)에 연결하면 근본 원인 분석(Root-Cause Analysis)이 가능해진다. 늦게 발생한 액추에이터 명령은 먼저 종단 간 지연시간 위반으로 관찰될 수 있다. 이후 ftrace 또는 LTTng을 통해 태스크 디스패치 지연이나 인터럽트 간섭(Interrupt Interference)을 확인하고, perf를 이용하여 간섭을 발생시킨 작업 부하에 연산 핫스팟(Computational Hotspot)이 존재하는지를 분석할 수 있다. 각각의 도구는 동일한 타이밍 문제의 서로 다른 부분을 설명한다.

자동화(Automation)는 이러한 분석 방법을 펌웨어와 시스템 버전 전체에서 반복 가능하게 만든다. 시험 프레임워크(Test Framework)는 미리 정의된 로봇 작업 부하를 실행하고 동기화된 타임스탬프와 트레이스를 수집하며, 각 지연시간 요소를 계산하고 분포를 생성하여 데드라인 위반을 탐지하고 검증된 기준선(Baseline)과 비교할 수 있다. RTOS 설정, PREEMPT_RT 커널, 우선순위, CPU 친화도(CPU Affinity), 드라이버, 통신 프로토콜, 제어 알고리즘의 변경 효과를 동일한 기준으로 평가할 수 있다.

계층형 로봇 시스템(Hierarchical Robotic System)에서는 모든 계층을 동일한 주기로 동작시키기보다 기능적 책임(Functional Responsibility)에 따라 지연시간 예산(Latency Budget)을 할당해야 한다. 상위 수준의 인지 및 판단 기능은 상대적으로 느리게 실행될 수 있는 반면, 하위 MCU 또는 모터 제어기 루프는 훨씬 빠르고 결정론적인 액추에이터 제어를 유지할 수 있다. 따라서 각 계층 내부의 지연시간과 계층 사이의 종단 간 전파 지연(End-to-End Propagation Delay)을 모두 검증하여 의도된 역할 분리가 유지되는지 확인해야 한다.

최종적인 목표는 측정된 모든 시간 간격을 단순히 최소화하는 것이 아니다. 가장 작은 평균 지연시간을 얻는 것보다 예측 가능성(Predictability), 제한된 최악 조건 동작(Bounded Worst-Case Behavior), 충분한 타이밍 여유를 확보하는 것이 일반적으로 더 중요하다. 평균값은 조금 느리더라도 변동 범위가 엄격하게 제한된 제어 경로가 평균은 빠르지만 간헐적으로 큰 지연이 발생하는 경로보다 더 적합할 수 있다. 따라서 최적화는 데드라인을 위협하거나 허용할 수 없는 변동성을 발생시키는 요소에 집중해야 한다.

Chapter_09_RTOS_Debugging_and_Tracing의 구조에서 로봇 제어 루프 지연시간 분석은 앞서 설명한 디버깅 및 측정 기술을 하나의 종단 간 방법론(End-to-End Methodology)으로 통합한다. 런타임 통계(Runtime Statistics)는 자원 사용량을 특성화하고, Tracealyzer와 LTTng은 시간적 실행 관계를 보여주며, ftrace와 perf는 커널 동작과 연산 비용을 분석하고, 하드웨어 타임스탬핑(Hardware Timestamping)은 물리적인 타이밍 근거를 제공한다. 이를 결합하면 제어 루프 타이밍을 측정 가능하고 세부적으로 분해할 수 있으며 반복적으로 검증 가능한 시스템 동작으로 전환할 수 있다.

## 09.08 Concurrency Bug Detection: ThreadSanitizer / Helgrind

![](images/image8.png){width="7.268055555555556in" height="7.268055555555556in"}

동시성 버그(Concurrency Bug)는 프로그램 논리 자체뿐만 아니라 실행 순서(Execution Ordering)에 따라 발생 여부가 달라지기 때문에 실시간 및 멀티스레드 소프트웨어(Multithreaded Software)에서 가장 진단하기 어려운 장애 중 하나이다. 시스템이 수천 번의 주기 동안 정상적으로 동작하다가 두 스레드(Thread)가 예상하지 못한 순서로 공유 상태(Shared State)에 접근하는 순간 실패할 수 있다. ThreadSanitizer와 Helgrind는 이러한 타이밍 의존적 동기화 결함(Timing-Sensitive Synchronization Defect)을 탐지하기 위한 동적 분석(Dynamic Analysis) 기법을 제공한다.

데이터 레이스(Data Race)는 여러 실행 컨텍스트(Execution Context)가 동일한 메모리 위치에 동시에 접근하고, 그중 하나 이상의 접근이 데이터를 변경하며, 이러한 접근이 적절하게 동기화되지 않았을 때 발생한다. 그 결과는 스케줄러 타이밍, 프로세서 부하, 인터럽트 활동, 컴파일러 최적화에 따라 달라질 수 있다. 이러한 결함은 상태 손상(Corrupted State), 일관되지 않은 센서 데이터, 잘못된 명령 또는 일반적인 디버깅 과정에서 실행 타이밍이 변경되면 사라지는 장애를 발생시킬 수 있다.

올바른 동기화(Correct Synchronization)는 동시 메모리 연산 사이에 적절한 실행 순서 관계(Ordering Relationship)를 형성한다. 뮤텍스(Mutex), 읽기-쓰기 잠금(Reader-Writer Lock), 세마포어(Semaphore), 조건 변수(Condition Variable), 원자적 연산(Atomic Operation) 등의 동기화 메커니즘을 이용하여 공유 자원에 대한 접근을 조정할 수 있다. 동적 동시성 분석(Dynamic Concurrency Analysis)은 이러한 동기화 연산과 메모리 접근을 함께 관찰하여 필요한 실행 순서 관계가 없거나 실제 프로그램 실행과 일치하지 않는 상황을 탐지한다.

일반적으로 TSan으로 줄여 부르는 스레드 새니타이저(ThreadSanitizer)는 주로 멀티스레드 프로그램의 데이터 레이스를 탐지하도록 설계된 컴파일러 지원 런타임 탐지기(Compiler-Assisted Runtime Detector)이다. 관련 메모리 접근과 동기화 연산을 실행 중에 감시할 수 있도록 새니타이저 계측(Sanitizer Instrumentation)을 포함하여 코드를 컴파일한다. 유효한 동기화 관계 없이 충돌하는 접근이 관찰되면 ThreadSanitizer는 관련 스레드와 코드 위치에 대한 정보와 함께 의심되는 레이스를 보고한다.

ThreadSanitizer의 강점은 레이스 보고서(Race Report)를 실제 소스 수준 실행(Source-Level Execution)과 연결할 수 있다는 점이다. 단순히 메모리 손상이 발생했다는 사실만 알려주는 것이 아니라 서로 충돌하는 읽기와 쓰기를 식별하고 각각의 접근에 도달한 경로를 보여주는 스택 트레이스(Stack Trace)를 제공할 수 있다. 이는 많은 작업 스레드, 통신 콜백(Communication Callback), 상태 추정기(State Estimator), 제어기, 백그라운드 서비스가 복잡한 데이터 구조를 공유하는 대규모 로보틱스 응용 프로그램에서 조사 범위를 크게 줄여준다.

ThreadSanitizer는 런타임 관찰(Runtime Observation)에 의존하므로 시험 과정에서 실제로 실행된 동작만 탐지할 수 있다. 실행되지 않은 오류 경로(Error Path)나 매우 드문 스케줄링 조합에 숨어 있는 동시성 결함은 발견되지 않을 수 있다. 따라서 시험 커버리지(Test Coverage)가 매우 중요하며 초기화, 정상 동작, 종료, 통신 손실, 재연결, 장애 복구, 모드 전환 등 스레드 상호작용 패턴을 변화시킬 가능성이 높은 조건을 스트레스 시험(Stress Test)에서 반복적으로 실행해야 한다.

계측(Instrumentation)은 정상 실행과 비교하여 상당한 오버헤드(Overhead)를 발생시킨다. ThreadSanitizer는 메모리 접근과 동기화 상태를 추적하므로 메모리 사용량과 실행시간이 증가한다. 따라서 새니타이저 빌드(Sanitizer Build)는 일반적으로 최종 실시간 배포 구성보다는 개발, 호스트 기반 시험(Host-Based Testing), 시뮬레이션 또는 통합 환경에서 사용한다. 계측된 빌드에서 얻은 타이밍 결과를 실제 실시간 성능을 대표하는 측정값으로 간주해서는 안 된다.

헬그라인드(Helgrind)는 Valgrind를 통해 동시성 분석을 제공하며 멀티스레드 프로그램의 동기화 오류(Synchronization Error)와 데이터 레이스 탐지에 중점을 둔다. Valgrind 동적 계측 환경(Dynamic Instrumentation Environment)에서 동작하기 때문에 ThreadSanitizer에서 사용하는 것과 동일한 컴파일러 계측 과정 없이 응용 프로그램을 분석할 수 있는 경우가 많다. Helgrind는 스레드 동작, 메모리 접근, 동기화 연산을 관찰하여 실행 컨텍스트 사이에서 잠재적으로 안전하지 않은 상호작용을 식별한다.

Helgrind는 특히 잘못된 잠금 동작(Locking Behavior)을 조사하는 데 유용할 수 있다. 충돌하는 메모리 접근뿐만 아니라 일관되지 않은 잠금 순서(Lock Ordering), 동기화 프리미티브(Synchronization Primitive)의 잘못된 사용, 예상된 뮤텍스 보호 없이 수행되는 연산 등을 동시성 분석을 통해 발견할 수 있다. 이러한 결함은 실제 데이터 손상이 발생하기 전에도 아키텍처 문제를 나타낼 수 있으므로 소유권(Ownership)과 동기화 규칙(Synchronization Discipline)에 대한 설계 가정을 검증하는 데 유용하다.

잠금 순서 분석(Lock-Order Analysis)은 일관되지 않은 잠금 획득 순서가 교착상태 위험(Deadlock Risk)을 발생시킬 수 있기 때문에 중요하다. 한 스레드가 뮤텍스 A를 획득한 후 뮤텍스 B를 획득하고 다른 실행 경로에서는 B를 먼저 획득한 다음 A를 획득한다면 두 스레드가 결국 서로를 무한정 기다릴 수 있다. 동적 분석은 실제로 관찰된 잠금 순서 관계를 확인하고 잠재적으로 위험한 패턴을 경고하여 드문 스케줄링 조건에서 시스템 정지(System Freeze)가 발생하기 전에 일관된 잠금 계층(Lock Hierarchy)을 구성하도록 지원한다.

모든 동시성 문제가 단순한 데이터 레이스인 것은 아니다. 교착상태(Deadlock), 라이브락(Livelock), 기아 상태(Starvation), 웨이크업 누락(Missed Wakeup), 잘못된 조건 변수 사용, 안전하지 않은 객체 수명(Object Lifetime), 원자성 위반(Atomicity Violation)도 유사한 증상을 발생시킬 수 있다. ThreadSanitizer와 Helgrind는 중요한 분석 근거를 제공하지만, 해당 보고서를 동시성 정확성의 완전한 증명으로 간주하기보다 아키텍처 이해, 스케줄러 트레이스, 동기화 객체 분석, 응용 프로그램 의미론(Application Semantics)과 함께 해석해야 한다.

사용자 정의 동기화(Custom Synchronization), 락프리 구조(Lock-Free Structure), 메모리 매핑 하드웨어(Memory-Mapped Hardware), 특수한 원자적 연산 또는 분석 도구가 완전히 이해하지 못하는 라이브러리를 사용할 경우 오탐(False Positive)이나 해석하기 어려운 보고서가 발생할 수 있다. 반대로 경고가 없다는 사실도 프로그램에 레이스가 없음을 증명하지는 않는다. 동적 도구는 실행된 경로만 분석하기 때문이다. 억제 파일(Suppression File)이나 제한된 범위의 주석(Annotation)이 필요할 수 있지만 경고를 억제하기 전에 반드시 기술적인 원인 조사가 선행되어야 한다.

로보틱스 소프트웨어(Robotics Software)는 여러 실행 컨텍스트가 지속적으로 상태를 교환하기 때문에 동시성 결함에 특히 노출되어 있다. 센서 획득 과정이 측정값을 갱신하는 동안 위치추정(Localization)이 이를 읽을 수 있고, 계획 모듈(Planning Module)이 궤적을 교체하는 동안 제어기가 이를 사용할 수 있으며, 통신 스레드가 동시에 명령이나 설정을 변경할 수도 있다. 명시적인 소유권 규칙이 없다면 단순해 보이는 공유 구조도 실제 프로세서 및 통신 부하에서 비결정적 동작(Nondeterministic Behavior)의 원인이 될 수 있다.

견고한 아키텍처(Robust Architecture)는 동적 분석을 시작하기 전부터 이러한 위험을 감소시킨다. 공유 변경 가능 상태(Shared Mutable State)는 최소화하고 소유권을 명확하게 정의하며 동기화 경계를 문서화해야 한다. 가능한 경우 메시지 전달(Message Passing) 또는 불변 스냅샷(Immutable Snapshot)을 사용하는 것이 바람직하다. 실시간 제어 경로에서는 불필요한 잠금 경합(Lock Contention)을 피하고 고주기 및 저주기 구성요소 사이의 데이터 교환에는 일관성과 블로킹 특성이 명확한 메커니즘을 사용해야 한다.

동시성 시험(Concurrency Testing)은 의도적으로 스케줄링 다양성(Scheduling Diversity)을 증가시켜야 한다. 서로 다른 프로세서 부하, 스레드 친화도(Thread Affinity), 메시지 전송률, 지연시간, 통신 패턴으로 시나리오를 반복하면 일반적인 기능 시험에서는 거의 나타나지 않는 인터리빙(Interleaving)을 노출할 수 있다. 시뮬레이션과 호스트 측 시험에서는 새니타이저 오버헤드를 허용할 수 있으므로 실제 실시간 지연시간 측정에는 적합하지 않은 장시간 스트레스 시험을 수행하는 데 특히 유용하다.

새니타이저가 레이스를 보고했을 때 목표는 단순히 해당 변수 주변에 뮤텍스를 추가하는 것이 아니다. 먼저 충돌하는 접근을 의도된 소유권 및 통신 모델(Communication Model)까지 추적해야 한다. 아키텍처 분석 없이 잠금을 추가하면 과도한 블로킹(Blocking), 우선순위 역전(Priority Inversion), 교착상태가 발생할 수 있다. 올바른 해결책은 소유권 이전, 데이터 복사, 원자적 연산 도입, 통신 구조 재설계 또는 실시간 상태와 비실시간 상태의 분리일 수 있다.

동시성 분석은 RTOS 및 Linux 트레이싱(Tracing)과도 연결해야 한다. ThreadSanitizer 또는 Helgrind는 의심스러운 공유 메모리 관계를 식별하고, Tracealyzer, LTTng 또는 ftrace는 관련 스레드가 시스템 실행 중 어떻게 스케줄링되고 동기화되었는지를 보여줄 수 있다. 이러한 조합은 메모리 정확성(Memory Correctness)과 시간적 동작(Temporal Behavior)을 연결하며 레이스가 제어 루프 지연시간, 예상하지 못한 블로킹 또는 우선순위 관련 문제까지 발생시키는 경우 특히 유용하다.

성능 분석(Performance Analysis)은 여전히 별개의 문제이다. 프로그램에서 데이터 레이스가 탐지되지 않더라도 과도한 잠금 경합이나 동기화 지연(Synchronization Delay)이 발생할 수 있다. 스케줄러 트레이싱과 프로파일링(Profiling)을 이용하면 기술적으로 올바른 잠금 방식이 높은 우선순위의 제어 스레드를 지나치게 오래 대기시키는지를 확인할 수 있다. 따라서 동시성 정확성(Concurrency Correctness)과 실시간 결정론(Real-Time Determinism)은 특히 동기화가 로봇 제어 경로에 직접 포함되는 경우 함께 검증해야 한다.

효과적인 검증 작업 흐름(Verification Workflow)은 최종 타이밍 검증 이전에 ThreadSanitizer와 Helgrind를 사용한다. 새니타이저가 활성화된 빌드에서 기능 및 스트레스 시나리오를 실행하여 레이스와 동기화 결함을 탐지한다. 발견된 문제를 수정한 후에는 무거운 계측 없이 소프트웨어를 다시 빌드하고 하드웨어 타임스탬프(Hardware Timestamp), ftrace, LTTng, perf 또는 RTOS 트레이싱을 사용하여 수정된 동기화 아키텍처가 지연시간과 지터 요구사항까지 충족하는지를 검증한다.

자동화(Automation)를 이용하면 동시성 분석을 지속적 시험(Continuous Testing)에 통합할 수 있다. 선택된 단위 시험(Unit Test), 통합 시험(Integration Test), 시뮬레이션 시나리오, 통신 스트레스 시험을 ThreadSanitizer 또는 Helgrind 환경에서 실행하고 탐지된 경고를 회귀 증거(Regression Evidence)로 사용할 수 있다. 분석 보고서를 소프트웨어 버전과 함께 보관하면 새롭게 발생한 레이스를 간헐적인 현장 장애로 나타난 이후가 아니라 해당 문제를 발생시킨 코드 변경 시점에 가깝게 발견할 수 있다.

Chapter_09_RTOS_Debugging_and_Tracing의 구조에서 ThreadSanitizer와 Helgrind는 기존의 타이밍 및 성능 문제 중심 디버깅 방법론을 동시성 정확성(Concurrent Correctness) 영역까지 확장한다. 앞에서 다룬 런타임 통계(Runtime Statistics), 트레이싱, 프로파일링, 하드웨어 타임스탬핑, 제어 루프 지연시간 분석은 시스템이 언제 실행되고 데드라인이 충족되는지를 설명한다. 동시성 분석은 동시에 실행되는 소프트웨어 구성요소가 공유 상태와 안전하게 상호작용하는지에 대한 추가적인 근거를 제공한다.

이러한 기법을 결합하면 로봇 시스템을 위한 보다 포괄적인 근본 원인 분석 방법론(Root-Cause Methodology)을 구성할 수 있다. 처음에는 무작위 상태 손상(Random State Corruption), 간헐적인 제어 이상(Control Anomaly), 원인을 알 수 없는 블로킹으로 보이는 장애도 메모리 접근, 동기화, 스케줄링, 타이밍 관점에서 함께 분석할 수 있다. 따라서 ThreadSanitizer와 Helgrind는 디버깅 과정이 프로파일링 자동화(Profiling Automation)와 현장 수준 근본 원인 분석(Field-Level Root-Cause Analysis)으로 진행되기 전에 소프트웨어 정확성과 결정론적 실시간 동작(Deterministic Real-Time Behavior)을 연결하는 중요한 역할을 수행한다.

## 09.09 Embedded Profiling: CPU, Memory, Power Simultaneous

![](images/image9.png){width="7.268055555555556in" height="7.268055555555556in"}

CPU, 메모리, 전력에 대한 임베디드 프로파일링(Embedded Profiling)은 세 가지 최적화 활동을 서로 독립적으로 수행하는 것이 아니라 동시에 측정해야 하는 시스템 수준의 문제로 다루어야 한다. 실시간 로봇은 CPU 사용률(CPU Utilization)이 적절하더라도 메모리 압박(Memory Pressure), 열 스로틀링(Thermal Throttling), 전력 소비 증가(Power Consumption)를 겪을 수 있다. 반대로 CPU 실행 시간을 줄이는 과정에서 메모리 트래픽이나 가속기 활동이 증가할 수도 있다. 따라서 프로파일링은 동일한 운용 조건에서 연산, 메모리, 타이밍, 전력, 열 상태, 워크로드 강도, 실행 시간을 서로 연관시켜야 한다.

CPU 프로파일링(CPU Profiling)은 실제로 프로세서 시간이 어디에서 소비되는지, 그리고 시스템이 현재 워크로드를 처리할 충분한 실행 능력을 갖추고 있는지를 확인한다. 측정에서는 애플리케이션 실행(Application Execution), 커널 동작(Kernel Activity), 인터럽트 처리(Interrupt Handling), 스케줄링 오버헤드(Scheduling Overhead), 드라이버 또는 I/O 처리를 구분해야 한다. 평균 CPU 사용률만으로는 짧은 지연시간 급증(Latency Spike)을 숨길 수 있기 때문에 충분하지 않다. 따라서 멀티코어 임베디드 시스템에서는 코어별 사용률, CPU affinity, 태스크 이동(Migration), 스케줄러 동작, 인터럽트 배치도 중요하다.

함수 수준 프로파일링(Function-Level Profiling)은 애플리케이션 내부에서 프로세서 시간을 실제로 많이 사용하는 계산 집중 지점(Computational Hotspot)을 찾는 데 도움을 준다. 샘플링 기반 방식(Sampling-Based Approach)은 모든 명령어에 계측을 삽입하지 않고도 빈번하게 실행되는 함수를 식별할 수 있으며, 호출 그래프 분석(Call-Graph Analysis)은 비용이 높은 실행 경로가 어떻게 도달되는지를 보여줄 수 있다. 임베디드 로봇에서는 인지 전처리(Perception Preprocessing), 센서 융합(Sensor Fusion), 위치 추정(Localization), 계획(Planning), 통신, 로깅, 디바이스 드라이버에서 병목이 발생할 수 있다. 최적화 방법을 선택하기 전에 실제 CPU 소비 원인을 찾는 것이 목적이다.

하드웨어 성능 카운터(Hardware Performance Counter)는 CPU 효율을 더욱 깊게 분석할 수 있도록 한다. 사이클(Cycle), 명령어(Instruction), 캐시 참조(Cache Reference), 캐시 미스(Cache Miss), 분기 동작(Branch Behavior), 정체 사이클(Stalled Cycle), 명령어당 사이클(Instructions-Per-Cycle) 등의 측정값은 워크로드가 연산 중심(Compute-Bound)인지 메모리 중심(Memory-Bound)인지를 판단하는 데 도움을 준다. 이러한 구분은 중요하다. 메모리 접근에 의해 실행이 제한되는 경우 프로세서 주파수를 높이거나 산술 연산을 최적화하더라도 효과가 작을 수 있기 때문이다. 따라서 하드웨어 카운터 정보는 관찰된 CPU 부하와 실제 실행 메커니즘을 연결하는 근거를 제공한다.

메모리 프로파일링(Memory Profiling)은 메모리 용량과 접근 동작을 모두 고려해야 한다. 영구 프로그램 데이터(Persistent Program Data), 태스크 스택, 큐, 모델 파라미터(Model Parameter), 버퍼, 캐시, 임시 할당(Temporary Allocation)은 메모리 사용량에 영향을 주며, 메모리 대역폭과 접근 지역성(Access Locality)은 실행 속도에 영향을 준다. 특히 피크 메모리 사용량(Peak Memory Usage)이 중요하다. 평균 사용량만으로는 일시적인 메모리 할당 증가를 숨길 수 있기 때문이다. 평균적인 여유 메모리가 충분한 시스템도 여러 워크로드가 동시에 실행되면서 추가 저장 공간을 요구하면 실패하거나 성능이 저하될 수 있다.

런타임 메모리 압박(Runtime Memory Pressure)은 메모리 할당 동작과 객체 수명(Object Lifetime)을 함께 평가해야 한다. 반복적인 동적 할당과 해제는 메모리 단편화(Fragmentation)를 발생시킬 수 있으며, 과도한 버퍼링은 처리량(Throughput)을 향상시키는 대신 메모리 용량과 지연시간을 증가시킬 수 있다. 제로 카피(Zero-Copy) 기법과 버퍼 재사용(Buffer Reuse)은 불필요한 데이터 이동과 할당을 줄일 수 있지만 아키텍처 복잡성을 증가시킬 수 있다. 따라서 적절한 해결책은 메모리 용량, 대역폭, 지연시간, 소프트웨어 소유권(Software Ownership) 사이의 측정된 관계에 따라 결정해야 한다.

전력 프로파일링(Power Profiling)은 임베디드 로봇이 제한된 에너지 및 열 범위(Energy and Thermal Envelope) 안에서 동작하기 때문에 또 다른 중요한 차원을 추가한다. 측정에서는 하나의 순간적인 값에 의존하지 않고 유휴(Idle), 일반(Normal), 피크(Peak), 지속(Sustained) 전력을 구분해야 한다. 임무 수준 효율성(Mission-Level Efficiency)을 평가할 때는 순간 전력보다 연산 또는 추론당 에너지(Energy per Operation or Inference)가 더욱 유용할 수도 있다. 짧은 시간 동안 높은 전력을 소비하는 워크로드는 지속 시간이 짧다면 허용될 수 있지만, 지속적인 높은 소비는 열적 한계를 만들고 장시간 성능을 감소시킬 수 있다.

열 동작(Thermal Behavior)은 온도가 프로세서 주파수에 영향을 주어 측정된 성능을 변화시킬 수 있기 때문에 CPU 및 메모리 활동과 연관시켜야 한다. 주파수 스케일링(Frequency Scaling), 열 스로틀링, 냉각 조건(Cooling Condition), 전력 정책(Power Policy), 워크로드 강도, 지속 실행 시간은 프로파일링 데이터와 함께 기록해야 한다. 그렇지 않으면 장시간 테스트에서 관찰된 성능 저하를 소프트웨어 변경의 결과로 잘못 판단할 수 있으며 실제 원인은 열 상태 또는 전력 관리 동작일 수 있다.

동시 프로파일링(Simultaneous Profiling)은 가능한 경우 CPU, 메모리, 전력, 열 측정값을 동일한 대표 워크로드(Representative Workload)에서 함께 수집하는 것을 의미한다. CPU 벤치마크와 메모리 벤치마크를 각각 실행한 후 유휴 상태에서 전력을 측정하는 방식은 실제 로봇의 동작을 설명하지 못한다. 인지, 위치 추정, 계획, 통신, 로깅, 디바이스 I/O, 안전 기능은 동시에 실행되는 경우가 많기 때문에 프로파일링 역시 이러한 상호작용을 재현해야 하며 각 서브시스템을 독립적으로 평가하는 데 그쳐서는 안 된다.

프로파일링 워크플로(Profiling Workflow)는 먼저 워크로드와 실제 시스템에 중요한 지표(Metric)를 정의하는 것에서 시작해야 한다. 측정 대상에는 CPU 사용률, 실행 시간, 지연시간, 지터, 컨텍스트 스위치(Context Switch), 인터럽트 응답, 메모리 사용량, 메모리 대역폭, 전력, 온도, 주파수, 데드라인 미스(Deadline Miss) 등이 포함될 수 있다. 이후 최적화 전에 통제된 기준 조건(Control Baseline Condition)에서 시스템을 측정해야 한다. 이를 통해 이후 변경 사항을 비교할 수 있는 증거 기반 기준선(Evidence-Based Baseline)을 확보할 수 있다.

일반적으로 먼저 낮은 오버헤드 측정(Low-Overhead Measurement)을 수행해야 한다. 계측 자체가 시스템 동작을 변경할 수 있기 때문이다. 이후 의심스러운 실행 경로나 특정 타이밍 구간에 대해 상세 트레이싱(Detailed Tracing)을 선택적으로 활성화할 수 있다. 실시간 시스템에서는 추가적인 트레이싱이 스케줄링, 캐시 동작, 메모리 트래픽, 전력 소비를 변화시킬 수 있으므로 측정 효과(Probe Effect)가 특히 중요하다. 상세한 증거를 수집한 이후에는 계측을 줄이거나 비활성화한 상태에서 동일한 워크로드를 다시 실행하여 관찰된 성능 특성이 실제 시스템을 대표하는지 확인해야 한다.

멀티코어 시스템에서는 시스템 수준과 코어별 수준 모두에서 프로파일링해야 한다. 네 개의 코어에서 평균 CPU 사용률이 낮더라도 하나의 코어만 과부하 상태이고 다른 코어는 거의 사용되지 않는 상황이 발생할 수 있다. 따라서 CPU affinity, 태스크 이동, 인터럽트 배치, 스케줄러 결정은 처리량과 지연시간 모두에 영향을 줄 수 있다. 로봇의 인지 파이프라인은 여러 코어에 작업을 분산하는 것이 유리할 수 있지만, 제어 관련 워크로드는 이동과 스케줄링 변동성을 줄이기 위해 신중하게 affinity를 설정해야 할 수 있다.

프로파일링 결과는 일반적인 추정이 아니라 측정 가능한 최적화 결정을 이끌어야 한다. CPU 연산이 병목이라면 알고리즘 최적화, 커널 최적화, 스케줄링 변경, 워크로드 감소가 도움이 될 수 있다. 메모리 트래픽이 지배적이라면 버퍼 재사용, 향상된 지역성(Locality), 제로 카피 경로, 데이터 이동 감소가 더욱 효과적일 수 있다. 전력 또는 열 동작이 지속 성능을 제한한다면 적응형 처리율(Adaptive Rate), 낮은 워크로드 강도, 전력 인지형 스케줄링(Power-Aware Scheduling), 냉각 성능 개선이 필요할 수 있다.

최적화 과정은 반복적으로 수행해야 한다. 개발자는 측정된 병목을 식별하고 관련 코드, 우선순위, affinity, 메모리 경로, 구성 또는 워크로드를 변경한 다음 동일한 시나리오를 다시 측정한다. 최적화는 목표 지표가 개선되는 동시에 지연시간, 메모리, 전력, 열 동작, 신뢰성 측면에서 허용할 수 없는 회귀(Regression)가 발생하지 않을 때만 성공한 것으로 판단해야 한다. 이를 통해 한 부분의 성능을 개선하면서 로봇 전체 성능 범위의 다른 부분을 악화시키는 국소 최적화(Local Optimization)를 방지할 수 있다.

회귀 테스트(Regression Testing)는 임베디드 최적화가 트레이드오프(Trade-Off)를 자주 발생시키기 때문에 특히 중요하다. 버퍼를 증가시키면 처리량이 향상될 수 있지만 메모리 사용량과 지연시간이 증가할 수 있다. CPU 사용량을 줄이는 과정에서 메모리 사용량이 증가할 수도 있으며, 공격적인 병렬화는 동기화 오버헤드(Synchronization Overhead)나 전력 소비를 증가시킬 수 있다. 따라서 승인된 기준 측정값을 보존하고 새로운 빌드와 비교해야 하며 실행 시간, 자원 사용량, 시작 동작, 지연시간 등의 변화는 회귀 증거로 관리해야 한다.

제품 수준 프로파일링(Production Profiling)은 개발 단계의 상세 계측보다 가벼운 메커니즘을 사용해야 한다. 지속적인 운용에서는 경량 카운터(Lightweight Counter), 선택적 진단(Targeted Diagnostics), 주기적인 상태 측정(Periodic Health Measurement), 필요에 따라 perf 또는 ftrace와 같은 도구를 사용할 수 있다. 상세 트레이스는 필요한 경우에만 활성화해야 한다. 저장 공간, 대역폭, CPU 오버헤드, 열 영향이 모두 제어되어야 하기 때문이다. 제품 수준 진단(Production Diagnostics)은 정상적인 로봇 동작을 유지하면서도 성능 저하를 조사하기에 충분한 문맥을 제공해야 한다.

생성된 프로파일링 데이터는 애플리케이션 로그(Application Log), 런타임 지표, 하드웨어 카운터, 트레이싱 정보, 워크로드 조건과 서로 연관시켜야 한다. 워크로드 문맥이 없는 CPU 병목은 잘못 해석될 수 있으며, 열 또는 스케줄링 정보가 없는 전력 급증 역시 원인을 파악하기 어렵다. 소프트웨어 버전, 워크로드 강도, 온도, 주파수, 전력 정책, 테스트 시간을 함께 기록하면 서로 다른 빌드와 운용 조건 사이에서 측정 결과를 재현하고 의미 있게 비교할 수 있다.

로봇 시스템에서 궁극적인 목표는 단순히 CPU 사용률을 최대화하거나 메모리 사용량을 최소화하는 것이 아니다. 충분한 메모리 여유(Memory Margin), 허용 가능한 전력 소비, 제어된 열 동작, 안정적인 실시간 동작을 유지하면서 예측 가능한 처리량과 지연시간을 확보하는 것이 목적이다. 따라서 프로파일링은 서로 경쟁하는 자원 사이의 균형을 결정하고 최적화가 하나의 고립된 지표가 아니라 전체 시스템을 개선했는지를 판단하는 데 필요한 근거를 제공한다.

완전한 임베디드 프로파일링 방법론(Embedded Profiling Methodology)은 결과적으로 측정(Measure), 이해(Understand), 최적화(Optimize), 검증(Verify), 반복(Repeat)의 지속적인 순환 구조를 따른다. CPU 프로파일링은 계산 병목과 스케줄링 동작을 설명하고, 메모리 프로파일링은 용량, 대역폭, 할당 제약을 드러내며, 전력 및 열 프로파일링은 지속적인 운용 한계를 보여준다. 이러한 측정을 실제 로봇의 동시 워크로드에서 동시에 수행하면 성능 엔지니어링(Performance Engineering)은 추정에 기반한 최적화가 아니라 데이터 기반 방식으로 전환되며, 예측 가능한 실시간 동작을 지원할 수 있다.

## 09.10 Field RTOS Failure Case Analysis and RCA

![](images/image10.png){width="7.268055555555556in" height="7.268055555555556in"}

현장 RTOS 장애 분석(Field RTOS Failure Analysis)은 실제 로봇에서 관찰되는 장애가 하나의 고립된 소프트웨어 결함으로 인해 발생하는 경우가 드물다는 사실을 인식하는 것에서 시작한다. 제어 주기 누락(Missed Control Cycle), 예기치 않은 리셋(Unexpected Reset), 통신 손실(Communication Loss), 액추에이터 정지(Actuator Stop), 일시적인 멈춤(Temporary Freeze)과 같은 현장 증상은 스케줄링(Scheduling), 인터럽트, 메모리, 동기화(Synchronization), 드라이버, 전력 조건, 워크로드 사이의 상호작용으로 발생할 수 있다. 따라서 근본 원인 분석(Root Cause Analysis, RCA)은 최초로 보이는 오류를 실제 원인으로 간주하기보다 전체 사건의 순서를 재구성해야 한다.

첫 번째 단계는 시스템을 수정하거나 재시작하여 유용한 정보를 제거하기 전에 원래의 장애 증거(Failure Evidence)를 보존하는 것이다. 가능한 경우 타임스탬프가 포함된 로그(Timestamped Log), 워치독 기록(Watchdog Record), 오류 레지스터(Fault Register), 태스크 상태(Task State), 스택 정보(Stack Information), 통신 상태(Communication Status), CPU 및 메모리 통계, 관련 센서 및 액추에이터 상태를 수집해야 한다. 목적은 장애 직전, 장애가 발생하는 동안, 장애 직후에 시스템이 무엇을 알고 있었는지를 확인하는 것이다. 증거가 보존되지 않으면 현장 디버깅은 불완전한 관찰을 바탕으로 한 추측으로 쉽게 변한다.

장애 분류(Failure Classification)는 시스템 수준의 관점을 유지하면서 조사 범위를 좁히는 데 도움을 준다. RTOS 장애는 타이밍 위반(Timing Violation), 태스크 기아(Task Starvation), 우선순위 역전(Priority Inversion), 교착 상태(Deadlock), 경쟁 조건(Race Condition), 스택 오버플로(Stack Overflow), 힙 고갈(Heap Exhaustion), 메모리 손상(Memory Corruption), 인터럽트 과부하(Interrupt Overload), 드라이버 오동작(Driver Malfunction), 통신 타임아웃(Communication Timeout), 워치독 리셋(Watchdog Reset), 예상하지 못한 상태 머신 전이(State-Machine Transition) 등으로 나타날 수 있다. 전압 변화, 온도, 진동, 전자기 간섭(Electromagnetic Interference), 네트워크 장애와 같은 물리적 조건도 소프트웨어에서 관찰되는 증상을 유발할 수 있다. 따라서 조사에서는 소프트웨어와 운용 조건을 모두 고려해야 한다.

실시간 로봇에서는 시스템이 수학적으로 올바른 결과를 생성하더라도 잘못된 시점에 생성하면 문제가 발생할 수 있기 때문에 타이밍 장애(Timing Failure)가 특히 중요하다. 주기적인 제어 태스크(Periodic Control Task)는 인터럽트 폭주(Interrupt Storm), CPU 경합(CPU Contention), 긴 임계 구역(Long Critical Section), 우선순위 간섭(Priority Interference), 캐시 또는 메모리 영향, 드라이버 실행, 예기치 않은 통신 처리로 인해 데드라인(Deadline)을 간헐적으로 놓칠 수 있다. 평균 실행 시간만으로는 이러한 장애를 설명하기에 충분하지 않다. 꼬리 지연시간(Tail Latency), 지터(Jitter), 최악 응답 시간(Worst-Case Response Time), 데드라인 이전에 남아 있는 타이밍 여유(Timing Margin)를 함께 조사해야 한다.

트레이스 및 타임스탬프 증거(Trace and Timestamp Evidence)는 장애 주변에서 발생한 시간적 순서를 재구성할 수 있도록 한다. RTOS 트레이스는 어떤 태스크가 실행 중이었는지, 어떤 태스크가 준비 또는 블록 상태였는지, 언제 인터럽트가 발생했는지, 언제 동기화 이벤트가 발생했는지를 보여줄 수 있다. 하드웨어 타임스탬프(Hardware Timestamp)는 소프트웨어 계측(Software Instrumentation)에 대한 의존성을 줄이면서 외부에서 관찰할 수 있는 정확한 타이밍을 제공할 수 있다. 이러한 정보를 서로 연관시키면 제어 응답 지연이 스케줄링 문제, 인터럽트 이벤트, 동기화 지연, 고비용 연산 중 어느 것에서 발생했는지를 구분할 수 있다.

동시성 장애(Concurrency Failure)는 이와 상호 보완적인 다른 관점에서 조사해야 한다. 경쟁 조건은 특정 태스크와 인터럽트 실행 순서에서만 상태 손상을 발생시킬 수 있기 때문에 장애가 무작위적으로 나타나는 것처럼 보일 수 있다. ThreadSanitizer 또는 Helgrind는 통제된 테스트 환경에서 의심스러운 동시 메모리 접근을 식별할 수 있으며, RTOS 트레이싱은 해당 결함이 실제로 관찰되도록 만든 스케줄링 순서를 보여줄 수 있다. 올바른 RCA는 단순히 진단 도구가 보고한 변수에 뮤텍스를 추가하는 것이 아니라 위반된 소유권(Ownership) 또는 동기화 모델(Synchronization Model)을 식별해야 한다.

메모리 관련 장애(Memory-Related Failure)는 용량(Capacity)과 무결성(Integrity)을 모두 고려하여 조사해야 한다. 스택 오버플로, 힙 고갈, 단편화(Fragmentation), 잘못된 포인터(Invalid Pointer), 해제 후 사용(Use-After-Free), 버퍼 오버런(Buffer Overrun), 메모리 손상은 최초 결함이 발생한 위치와 전혀 다른 곳에서 증상을 발생시킬 수 있다. 런타임 스택 하이워터 마크(Runtime Stack High-Water Mark), 힙 통계, 메모리 보호 메커니즘(Memory Protection Mechanism), 새니타이저(Sanitizer), 장애 기록, 디버거 검사는 서로 보완적인 증거를 제공할 수 있다. 높은 워크로드에서 장애가 발생했다는 이유만으로 현장 장애를 자동으로 메모리 부족으로 분류해서는 안 된다.

통신 장애(Communication Failure)는 RTOS 동작으로 연쇄적으로 전파될 수 있다. CAN, Ethernet 또는 다른 네트워크 메시지가 손실되면 타임아웃이 발생하고, 이로 인해 감독 태스크(Supervisory Task)가 활성화되거나 상태 머신이 변경되거나 액추에이터가 비활성화되거나 진단 트래픽이 증가할 수 있다. 따라서 실제로 관찰된 액추에이터 반응은 최초의 통신 이벤트보다 여러 계층 뒤에서 발생할 수 있다. RCA는 물리적 또는 네트워크 이벤트에서 드라이버, 인터럽트, RTOS 태스크, 통신 상태, 제어 로직을 거쳐 최종 물리 동작에 이르는 전체 전파 경로(Propagation Chain)를 추적해야 한다.

워치독 리셋(Watchdog Reset)은 워치독이 일반적으로 무엇인가가 실패했다는 사실은 알려주지만 왜 실패했는지는 설명하지 않기 때문에 특히 주의해서 조사해야 한다. 플랫폼이 허용하는 경우 리셋 원인(Reset Reason), 태스크 상태 정보, 오류 레지스터, 프로그램 카운터(Program Counter), 링크 레지스터(Link Register), 스택 프레임(Stack Frame), 영속 로그(Persistent Log), 최근 트레이스 정보를 보존해야 한다. 조사에서는 워치독이 교착 상태, 기아, 과도한 실행 시간, 인터럽트 잠금(Interrupt Lockup), 메모리 손상, 드라이버 장애, 전원 불안정 또는 다른 조건으로 인해 발생했는지를 판단해야 하며 워치독 리셋 자체를 근본 원인으로 간주해서는 안 된다.

최적화 설정(Optimization Setting)은 RTOS 장애가 나타나는 방식을 변화시킬 수 있다. 컴파일러 최적화(Compiler Optimization)는 타이밍, 명령어 순서, 레지스터 할당(Register Allocation), 메모리 배치(Memory Layout), 디버깅 중 변수의 가시성에 영향을 줄 수 있다. 따라서 디버그 빌드(Debug Build)에서 장애가 사라졌다고 해서 문제가 해결된 것은 아니다. 현장 RCA에서는 가능한 경우 제품 수준과 유사한 최적화 및 구성에서 장애를 재현해야 한다. 디버그 심볼(Debug Symbol)은 별도로 보존하여 제품 장애에서 발생한 주소를 해당 소스 위치와 실행 경로에 다시 매핑할 수 있도록 해야 한다.

현장 재현(Field Reproduction)은 장애가 사라지도록 즉시 시스템을 수정하는 것이 아니라 점진적으로 현실성을 높이는 방식으로 수행해야 한다. 먼저 보고된 운용 모드를 재현하고 이후 대표적인 센서 트래픽, 통신 부하, 로깅, 저장장치 활동, 열 조건, 비정상 이벤트를 추가할 수 있다. 장애 주입(Fault Injection)을 사용하여 메시지 손실, 응답 지연, 센서 오류, 태스크 과부하 또는 기타 통제된 장애를 의도적으로 발생시킬 수도 있다. 동일한 증상이 반복 가능하고 측정 가능한 조건에서 다시 발생한다면 장애 재현의 신뢰성이 더욱 높아진다.

RCA에서는 증상(Symptom), 직접 원인(Immediate Cause), 기여 요인(Contributing Factor), 근본 원인(Root Cause)을 구분해야 한다. 예를 들어 액추에이터가 정지하는 것은 증상이고, 제어 태스크가 장애 상태로 진입하는 것은 직접 원인일 수 있으며, 통신 타임아웃은 기여 요인일 수 있다. 반면 오래된 명령(Stale Command)이 컨트롤러에 도달할 수 있도록 허용한 아키텍처상의 가정은 더 깊은 근본 원인이 될 수 있다. 이러한 구분은 팀이 관찰된 동작을 설명하는 최초의 소프트웨어 조건에서 조사를 멈추지 않고 장애가 발생할 수 있도록 허용한 메커니즘까지 수정하도록 한다.

시정 조치(Corrective Action)는 식별된 결함뿐만 아니라 해당 결함이 발견되지 않은 상태로 남아 있을 수 있었던 조건까지 해결해야 한다. 소프트웨어 수정은 태스크 우선순위, 동기화, 버퍼 소유권, 타임아웃 처리, 상태 전이, 메모리 할당, 드라이버 동작, 오류 복구를 변경하는 형태가 될 수 있다. 그러나 시정 조치에는 적절한 회귀 테스트(Regression Test), 진단 증거, 모니터링, 스트레스 시나리오도 함께 포함해야 한다. 하나의 테스트에서 증상이 사라졌다고 해서 장애가 완전히 해결된 것은 아니다. 동일한 장애를 다시 발생시킬 수 있는 조건에서 인과 메커니즘(Causal Mechanism)을 검증해야 한다.

현장 증거(Field Evidence)는 지속적인 개발 및 검증 프로세스(Development and Verification Process)의 일부가 되어야 한다. 중요한 장애가 발생할 때마다 운용 조건, 소프트웨어 및 하드웨어 버전, 관찰된 증상, 증거, 인과 가설(Causal Hypothesis), 확인된 근본 원인, 시정 조치, 검증 결과, 남아 있는 가정을 포함하는 구조화된 기록을 생성할 수 있다. 이후 서로 다른 로봇 장치와 소프트웨어 버전에서 발생한 반복적인 장애를 비교할 수 있다. 이를 통해 현장 디버깅을 개별적인 사건 처리에서 축적되는 엔지니어링 지식 기반(Engineering Knowledge Base)으로 전환할 수 있다.

로봇 시스템에서 RCA는 궁극적으로 저수준 RTOS 동작과 물리적 결과(Physical Consequence)를 연결해야 한다. 스케줄러 지연(Scheduler Delay)은 제어 루프 데드라인 위반이 될 수 있고, 경쟁 조건은 잘못된 액추에이터 명령으로 이어질 수 있으며, 메모리 손상은 예기치 않은 리셋으로 이어질 수 있고, 통신 타임아웃은 제어된 정지(Controlled Stop)로 이어질 수 있다. 따라서 조사는 하나의 소프트웨어 모듈에 책임을 할당하는 데 그치지 않고 태스크, 인터럽트, IPC, 메모리, 드라이버, 통신, 제어, 안전 경계를 모두 넘어 수행되어야 한다.

전체 현장 분석 프로세스(Field Analysis Process)는 증거 기반 순환 구조(Evidence-Driven Loop)로 이해할 수 있다. 즉 장애를 감지하고, 증거를 보존하고, 타임라인을 재구성하고, 장애를 분류하고, 인과 관계를 식별하고, 조건을 재현하고, 근본 원인을 확인하고, 시정 조치를 구현한 다음, 대표적인 워크로드에서 결과를 검증하는 과정을 반복한다. 런타임 통계, Tracealyzer 방식의 트레이싱, 하드웨어 타임스탬프, 동시성 분석, 프로파일링, 장애 기록, 현장 로그는 각각 동일한 문제의 서로 다른 부분에 대한 답을 제공한다. 이러한 정보를 함께 활용하면 실제 배포된 로봇 시스템에서 신뢰성 높은 RTOS 동작을 확보할 수 있는 기반을 마련할 수 있다.
