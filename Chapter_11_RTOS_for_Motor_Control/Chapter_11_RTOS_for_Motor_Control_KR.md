**Volume 03 Real Time Operating Systems**

# 11. RTOS for Motor Control

## 11.01 Motor Control RTOS Requirements: Period, Jitter, Priority

![](images/image1.png){width="7.268055555555556in" height="7.268055555555556in"}

모터 제어(Motor Control)는 평균적인 성능이 아니라 결정론적인 타이밍을 제공해야 하는 대표적인 실시간 운영체제(RTOS) 적용 분야이다. 모터 제어 소프트웨어는 센싱(Sensing), 제어 연산(Control Computation), 액추에이터 갱신(Actuator Update)을 정의된 제어 주기(Control Period) 안에서 반복적으로 수행해야 한다. 따라서 핵심 요구사항에는 예측 가능한 태스크 주기(Task Period), 제한된 실행 지연(Execution Latency), 낮은 타이밍 지터(Timing Jitter), 적절한 태스크 우선순위(Task Priority), 신뢰성 있는 인터럽트 처리(Interrupt Handling)가 포함된다. 이러한 요구사항은 제어기가 변화하는 연산 및 시스템 부하에서도 안정적이고 정확한 모터 동작을 유지할 수 있는지를 결정한다.

제어 주기(Control Period)는 모터 제어 루프가 얼마나 자주 실행되는지를 정의한다. 주기가 T인 제어 루프에서는 다음 주기가 시작되기 전에 필요한 피드백(Feedback)을 획득하고, 제어 알고리즘(Control Algorithm)을 실행하며, 액추에이터(Actuator)를 갱신해야 한다. 따라서 데드라인(Deadline)을 놓치는 것은 일반적인 소프트웨어 지연보다 훨씬 심각하다. 이는 제어 시스템의 시간적 동작 자체를 변화시키기 때문이다. 필요한 주기는 모터의 특성, 기계적 동역학(Mechanical Dynamics), 제어기 구조, 센서 대역폭(Sensor Bandwidth), 애플리케이션에 따라 달라진다. 일반적으로 빠른 전류 제어 루프(Current Control Loop)는 상위 수준의 속도 또는 위치 루프보다 훨씬 짧은 주기를 요구한다.

주기(Period)만으로는 충분하지 않다. 동일한 명목 주파수(Nominal Frequency)를 갖는 두 개의 제어 루프라도 실행 시간이 변화하면 매우 다른 동작을 보일 수 있기 때문이다. 지터(Jitter)는 주기적 태스크(Periodic Task) 또는 인터럽트(Interrupt)의 예상 실행 시점과 실제 실행 시점 사이의 편차를 의미한다. 과도한 지터는 제어기가 경험하는 실제 샘플링 간격(Effective Sampling Interval)을 변화시키며, 위상 변화(Phase Variation), 제어 오차(Control Error), 토크 리플(Torque Ripple), 또는 불안정성(Instability)을 발생시킬 수 있다. 따라서 RTOS 모터 제어 설계에서는 명목 주기뿐만 아니라 허용 가능한 지터 한계(Jitter Bound)와 이를 측정하는 방법도 함께 정의해야 한다.

지연시간(Latency) 역시 제한된 범위를 갖는 값으로 다루어야 한다. 센서 변환(Sensor Conversion)이나 타이머 인터럽트(Timer Interrupt)와 같은 물리적 이벤트가 발생한 이후 이에 대응하는 제어 동작(Control Action)이 수행될 때까지의 시간은 모터 상태에 얼마나 빠르게 반응할 수 있는지를 결정한다. 지연시간의 원인에는 인터럽트 응답(Interrupt Response), 더 높은 우선순위 태스크의 실행, 컨텍스트 스위칭(Context Switching), 동기화 메커니즘(Synchronization Mechanism), 캐시 효과(Cache Effect), 메모리 접근(Memory Access), 통신 처리(Communication Processing) 등이 포함된다. 평균 지연시간이 낮더라도 간헐적으로 매우 긴 지연이 발생하는 시스템은 실시간 요구사항을 위반할 수 있다. 따라서 모터 제어 검증에서는 평균 타이밍보다 최악 조건 또는 높은 백분위수(High-Percentile) 동작을 중심으로 평가해야 한다.

태스크 우선순위(Task Priority)는 여러 태스크가 실행을 위해 경쟁할 때 어떤 활동이 프로세서 시간을 먼저 확보하는지를 결정한다. 모터 제어 태스크는 일반적으로 로깅(Logging), 진단(Diagnostics), 사용자 인터페이스(User Interface), 백그라운드 데이터 처리(Background Data Processing)와 같은 비핵심 활동보다 높은 우선순위를 가져야 한다. 그러나 우선순위 설계는 단순히 모든 제어 관련 태스크에 가장 높은 우선순위를 부여하는 방식으로 이루어져서는 안 된다. 지나치게 많은 고우선순위 작업은 낮은 우선순위 기능의 실행을 방해할 수 있으며, 잘못 설계된 동기화는 우선순위 역전(Priority Inversion)을 발생시킬 수 있다. 따라서 우선순위 구조는 제어 데드라인, 실행 주기, 그리고 중요도(Criticality)를 종합적으로 반영해야 한다.

타이머 인터럽트(Timer Interrupt)는 주기적인 모터 제어를 위한 중요한 시간 기준(Time Reference)을 제공한다. 하드웨어 타이머(Hardware Timer)를 사용하면 일정한 간격으로 제어 시퀀스를 트리거할 수 있으므로 소프트웨어 지연에 대한 의존성을 줄이고 시간적 결정성(Temporal Determinism)을 향상시킬 수 있다. 인터럽트 핸들러(Interrupt Handler)는 짧고 예측 가능하게 유지해야 하며, 더 큰 연산은 아키텍처가 허용하는 경우 적절한 우선순위의 RTOS 태스크로 전달할 수 있다. 특히 고성능 제어 루프에서는 PWM 생성(PWM Generation), ADC 샘플링(ADC Sampling), 인터럽트 실행, 제어기 연산(Control Computation), PWM 갱신(PWM Update) 사이의 시간 관계를 독립적인 소프트웨어 기능이 아니라 하나의 결정론적인 체인으로 설계해야 한다.

모터 제어 시스템은 서로 다른 타이밍 요구사항을 갖는 여러 개의 중첩 루프(Nested Loop)를 포함하는 경우가 많다. 전류 루프(Current Loop)는 높은 주파수로 동작할 수 있는 반면, 속도 루프(Speed Loop)와 위치 루프(Position Loop)는 더 느리게 동작할 수 있다. 이는 각 루프가 서로 다른 주기와 우선순위를 갖는 다중 주기 구조(Multi-Rate Architecture)로 자연스럽게 이어진다. 빠른 루프는 짧은 데드라인 안에 완료되어야 하며 느린 작업에 의해 방해받아서는 안 된다. 따라서 RTOS 스케줄러(RTOS Scheduler)는 제어 아키텍처의 일부가 된다. 태스크 주기, 우선순위, 인터럽트 동작, CPU 사용률(CPU Utilization)이 함께 각 제어 루프가 필요한 시간 제약 안에서 실행될 수 있는지를 결정하기 때문이다.

PWM 타이밍(PWM Timing)과 제어 루프 실행(Control Loop Execution)의 관계는 디지털 모터 제어에서 특히 중요하다. PWM은 언제 스위칭 명령(Switching Command)이 적용되는지를 결정하고, ADC 샘플링은 언제 모터 전류 또는 다른 피드백 신호를 관측하는지를 결정한다. 샘플링과 액추에이션(Actuation)이 시간적으로 조정되지 않으면 제어기가 일관되지 않은 측정값을 사용하거나 추가적인 타이밍 변동을 발생시킬 수 있다. 따라서 결정론적인 설계에서는 타이머 이벤트(Timer Event), ADC 획득(ADC Acquisition), 제어 연산, PWM 갱신이 각 제어 주기에서 반복 가능한 시간적 순서를 갖도록 구성해야 한다.

CPU 사용률(CPU Utilization)은 타이밍 요구사항과 함께 평가해야 한다. 모터 제어 태스크의 명목 실행 시간이 충분해 보이더라도 추가적인 통신, 진단, 인터럽트 처리 또는 AI 관련 작업이 남아 있는 타이밍 여유를 소비할 수 있다. 따라서 실시간 설계에서는 최악 실행 시간(Worst-Case Execution Time)과 데드라인 사이에 충분한 여유를 확보해야 한다. 시스템이 복잡해질수록 실제 실행 시간 분포와 최악 조건의 동작을 대표적인 최대 부하 환경에서 측정해야 하며, 단순한 명목 벤치마크(Nominal Benchmark)에만 의존해서는 안 된다.

동기화 메커니즘(Synchronization Mechanism) 역시 모터 제어의 결정성에 영향을 준다. 뮤텍스(Mutex), 세마포어(Semaphore), 큐(Queue), 공유 메모리(Shared Memory)는 블로킹(Blocking)과 스케줄링 의존성을 발생시킬 수 있다. 높은 우선순위의 제어 태스크가 낮은 우선순위의 태스크를 기다리게 되면 특히 우선순위 역전이 발생하는 상황에서 예상하지 못한 지연이 발생할 수 있다. 따라서 중요한 제어 경로에서는 불필요한 블로킹을 최소화하고 적절한 동기화 전략을 사용해야 한다. 가능한 경우 시간에 민감한 데이터 교환은 통신 자체가 제어 루프의 시간적 보장을 훼손하지 않도록 제한적이고 예측 가능한 메커니즘을 사용해야 한다.

RTOS는 시간에 민감한 모터 기능과 비실시간 처리를 분리해야 한다. 텔레메트리(Telemetry), 로깅, 설정(Configuration), 네트워크 통신(Network Communication), 진단 기능은 중요하지만 결정론적인 제어 경로를 방해해서는 안 된다. 이는 하나의 MCU가 모터 제어 계층을 수행하고 상위 프로세서가 내비게이션(Navigation), 인지(Perception), AI 추론(AI Inference)을 수행하는 로봇에서 더욱 중요하다. 모터 제어기는 상위 수준의 연산에서 가변적인 지연이나 일시적인 과부하가 발생하더라도 자체적인 실시간 보장을 유지해야 한다.

검증(Verification)은 스케줄러만이 아니라 전체 타이밍 체인을 측정해야 한다. 하드웨어 타임스탬프(Hardware Timestamp), GPIO 계측(GPIO Instrumentation), 트레이스 기능(Trace Facility), 타이머 카운터(Timer Counter), RTOS 트레이싱(RTOS Tracing)을 사용하면 실제 주기, 실행 시간, 인터럽트 지연, 지터를 확인할 수 있다. 측정은 최대 통신 트래픽, 동시 인터럽트, CPU 부하, 진단 활동 등을 포함하는 현실적인 최악 조건에서 수행해야 한다. 목표는 정상적인 조건에서 제어기가 동작한다는 것을 보여주는 것이 아니라, 관측된 타이밍이 정의된 한계 안에 지속적으로 유지된다는 것을 입증하는 것이다.

실제 로봇 시스템에서는 이러한 요구사항이 상위 수준의 모션 인텔리전스(Motion Intelligence)와 하위 수준의 결정론적 액추에이션(Deterministic Actuation) 사이의 명확한 경계를 형성한다. 상위 수준 제어기는 비교적 낮은 주기로 속도, 토크 또는 위치 명령을 생성할 수 있는 반면, 모터 제어 RTOS는 훨씬 높은 주기와 더 결정론적인 방식으로 액추에이터 루프를 실행할 수 있다. 이러한 분리를 통해 인지, 계획, AI 작업이 가변적인 연산 시간을 허용하면서도 모터 안정성에 직접적인 영향을 주지 않도록 할 수 있다. 따라서 모터 제어 계층은 로봇의 물리적 동작을 위한 실시간 실행 기반(Real-Time Execution Foundation)으로 기능한다.

최종적인 설계 원칙은 모터 제어 RTOS의 선택이 기능의 개수보다는 시간적 보장(Temporal Guarantee)을 중심으로 이루어져야 한다는 것이다. 주기, 데드라인, 지터, 지연시간, 우선순위, 인터럽트 응답, 동기화, CPU 사용률, 측정 능력은 서로 연관된 요소로 함께 고려해야 한다. FreeRTOS, Zephyr 또는 다른 RTOS도 해당 구성과 하드웨어 플랫폼이 요구되는 결정성을 제공한다면 적합할 수 있다. 궁극적으로 적절한 아키텍처는 모든 핵심 제어 주기가 요구되는 데드라인 안에서 예측 가능하게 완료된다는 사실을 측정과 분석을 통해 입증할 수 있는 구조이다.

## 11.02 FOC (Field Oriented Control) Real-Time Implementation [w/Code]

![](images/image2.png){width="7.268055555555556in" height="7.268055555555556in"}

![](images/image3.png){width="7.268055555555556in" height="7.268055555555556in"}

필드 지향 제어(Field Oriented Control, FOC)는 모터의 3상 전기적 동작을 회전 기준 좌표계(Rotating Reference Frame)의 성분으로 변환하여 보다 독립적으로 제어하는 실시간 모터 제어 방법이다. FOC는 상전류(Phase Current)를 직접 제어하는 대신 전자기 자속(Magnetic Flux)과 토크(Torque) 생성에 각각 관련된 전류를 분리한다. 이를 통해 AC 모터의 효율과 동적 성능을 유지하면서도 분리 여자 DC 모터(Separately Excited DC Motor)와 유사한 방식으로 모터 토크와 자속을 제어할 수 있다.

일반적인 FOC 구현은 상전류(Phase Current)와 회전자 위치(Rotor Position) 정보의 획득에서 시작한다. 전류 센서(Current Sensor)는 모터의 상전류를 측정하고, 엔코더(Encoder), 리졸버(Resolver), 홀 센서(Hall Sensor) 또는 센서리스 추정(Sensorless Estimation) 방법을 통해 전기적 회전자 각도(Electrical Rotor Angle)를 얻는다. 이러한 신호는 좌표 변환(Coordinate Transformation)의 정확도가 전류 측정과 회전자 위치 사이의 관계에 의존하기 때문에 정밀하게 제어된 시점에 샘플링되어야 한다. 따라서 RTOS 구현에서는 ADC 트리거(ADC Trigger), 센서 데이터 획득(Sensor Acquisition), 각도 처리(Angle Processing), 제어 연산(Control Computation)을 결정론적인 실시간 시퀀스로 구성해야 한다.

측정된 3상 전류는 먼저 Clarke 변환(Clarke Transformation)을 사용하여 정지 2축 좌표계(Stationary Two-Axis Coordinate System)로 변환된다. 그 결과로 얻어진 α축과 β축 전류는 3상 전류 벡터(Current Vector)를 2차원 정지 기준 좌표계에서 표현한다. 이후 제어기는 추정된 전기적 회전자 각도에 따라 이 벡터를 회전시키는 Park 변환(Park Transformation)을 적용한다. 그 결과가 d축 및 q축 전류 표현이다. d축 성분은 주로 자기 자속과 관련되고, q축 성분은 주로 토크 생성과 관련된다.

d축과 q축 전류를 분리하는 것은 FOC의 가장 중요한 장점 가운데 하나이다. 많은 영구자석 모터(Permanent Magnet Motor)에서는 d축 전류를 적절한 기준값 주변으로 조절하면서 필요한 토크에 따라 q축 전류를 제어함으로써 원하는 운전 상태를 달성할 수 있다. 외부 속도 또는 위치 제어기(Outer Speed or Position Controller)는 토크와 관련된 전류 기준값(Current Reference)을 생성하고, 내부 전류 제어기(Inner Current Controller)는 측정된 전류를 기준값에 빠르게 수렴시킨다. 이러한 중첩 구조(Nested Structure)는 빠른 토크 응답과 정밀한 모션 제어가 필요한 서보 시스템에 FOC가 특히 적합하도록 만든다.

전류 제어 루프(Current Control Loop)는 일반적으로 FOC 알고리즘에서 가장 시간에 민감한 부분이다. 각 제어 주기마다 제어기는 동기화된 전류 측정값을 획득하고, 전기적 각도를 결정하며, 좌표 변환을 수행하고, 전류 오차를 계산하고, d축 및 q축 제어기를 실행한 후 전압 명령(Voltage Command)을 생성한다. 이러한 모든 연산은 정의된 제어 주기(Control Period) 안에 완료되어야 한다. 연산이 너무 늦게 완료되면 결과적인 전압 명령이 PWM 주기의 적절하지 않은 시점에 적용될 수 있으며, 이는 실질적인 제어 지연을 증가시키고 전류 제어 성능과 모터 안정성을 저하시킬 수 있다.

d-q 전류 루프의 비례-적분 제어기(Proportional-Integral Controller, PI Controller)는 전류 오차를 전압 명령으로 변환한다. PI 제어기의 게인은 모터의 전기적 파라미터(Electrical Parameters), 샘플링 주파수(Sampling Frequency), 인버터 특성(Inverter Characteristics), 원하는 동적 응답(Dynamic Response)을 고려하여 결정해야 한다. 제어기 출력은 일반적으로 DC 버스 전압(DC-Bus Voltage)과 인버터의 변조 범위(Modulation Range)에 의해 제한된다. 따라서 안티 와인드업(Anti-Windup) 동작이 중요하다. 지속적인 포화(Saturation)가 발생할 경우 적분항(Integral Term)이 과도하게 누적될 수 있으며, 이후 시스템이 다시 제어 가능한 영역으로 돌아왔을 때 느린 회복이나 불안정성을 발생시킬 수 있기 때문이다.

d-q 회전 좌표계에서 전압 명령을 계산한 후 역 Park 변환(Inverse Park Transformation)을 사용하여 이를 정지 α-β 좌표계로 다시 변환한다. 이후 변조 단계(Modulation Stage)는 요청된 전압 벡터(Voltage Vector)를 인버터 스위칭 명령(Inverter Switching Command)으로 변환하며, 일반적으로 공간 벡터 PWM(Space Vector PWM, SVPWM) 또는 다른 PWM 방식을 사용한다. 최종 PWM 듀티 사이클(PWM Duty Cycle)은 전력단 스위치(Power-Stage Switch)가 모터의 각 상에 전압을 인가하는 방식을 결정한다. 따라서 ADC 샘플링, FOC 연산, PWM 갱신 사이의 시간 관계는 전체 실시간 구현에서 핵심적인 요소이다.

실시간 실행은 오프라인 수학적 구현(Offline Mathematical Implementation)과 다른 요구사항을 가진다. FOC 알고리즘은 엄격하게 제한된 실행 시간 안에서 주기적으로 실행되어야 하며, 연속된 실행 사이의 변동 역시 작아야 한다. 과도한 지터(Jitter)는 실질적인 샘플링 간격(Effective Sampling Interval)을 변화시키고 전류 제어 루프에 위상 오차(Phase Error)를 발생시킬 수 있다. 인터럽트 지연(Interrupt Latency), RTOS 스케줄링(Scheduling), 캐시 동작(Cache Behavior), 통신 활동(Communication Activity), 경쟁 태스크(Competing Task)는 모두 타이밍 변동에 영향을 줄 수 있다. 따라서 모터 제어 태스크에는 적절한 실시간 우선순위(Real-Time Priority)를 부여하고 불필요한 블로킹(Blocking)과 비핵심 처리를 방지해야 한다.

실제 RTOS 아키텍처에서는 일반적으로 고주파 FOC 루프와 더 느린 제어 및 시스템 관리 기능을 분리한다. 전류 루프(Current Loop)는 가장 높은 제어 주파수에서 동작하고, 속도 제어(Speed Control), 위치 제어(Position Control), 진단(Diagnostics), 통신(Communication), 로깅(Logging), 설정(Configuration) 등의 태스크는 더 낮은 주기에서 동작할 수 있다. 하드웨어 타이머(Hardware Timer)와 PWM 주변장치(PWM Peripheral)는 주요 시간 기준을 제공하고, RTOS는 태스크 실행과 지원 기능을 관리한다. 이러한 다중 주기 구조(Multi-Rate Architecture)는 느린 소프트웨어 기능이 가장 중요한 모터 제어 경로의 결정론적인 실행을 불필요하게 방해하는 것을 방지한다.

센서와 액추에이터의 동기화(Sensor and Actuator Synchronization) 역시 중요하다. 전류 샘플링은 PWM 스위칭 주기에서 잘 정의된 시점에 수행되어야 하며, 이를 통해 스위칭 과도현상(Switching Transient)과 측정 잡음(Measurement Noise)을 제어할 수 있다. 샘플링된 전류값은 알려진 전기적 회전자 각도에 대응해야 하며, 계산된 전압 명령은 예측 가능한 PWM 갱신 시점에 PWM 하드웨어로 전달되어야 한다. 이러한 타이밍 체인에서 작은 오차가 발생하더라도 추가적인 위상 지연이나 전류 왜곡(Current Distortion)으로 나타날 수 있다. 따라서 FOC 성능은 수학적 제어기뿐만 아니라 전체 센싱, 연산, 액추에이션 파이프라인의 시간적 정확도에 의해 결정된다.

FOC 구현은 비정상적인 운전 조건(Abnormal Operating Condition)도 고려해야 한다. 잘못된 센서 측정, 과전류(Overcurrent), DC 버스 과전압 또는 저전압(DC-Bus Overvoltage or Undervoltage), 회전자 위치 정보 손실(Loss of Rotor-Position Information), 인버터 고장(Inverter Fault), 연산 타이밍 실패(Computational Timing Failure)는 계속해서 모터를 운전하는 것을 위험하게 만들 수 있다. 실시간 제어 계층(Real-Time Control Layer)은 중요한 고장을 제한된 시간 안에 감지하고 모터를 적절한 안전 상태(Safe State)로 전환해야 한다. 고장 처리는 진단이나 통신 처리가 보호 응답(Protective Response)을 지연시키지 않도록 설계해야 한다. 이는 FOC 구현과 RTOS 스케줄링, 하드웨어 보호(Hardware Protection), 전체 모터 시스템 안전성 사이에 직접적인 관계를 형성한다.

검증(Verification)은 제어 품질(Control Quality)과 시간적 동작(Temporal Behavior)을 함께 평가해야 한다. 전류 추종 오차(Current-Tracking Error), 토크 응답(Torque Response), 속도 제어(Speed Regulation), 정상 상태 리플(Steady-State Ripple), 과도 응답(Transient Response), 운전점 안정성(Operating-Point Stability)을 제어 루프 주기, 실행 시간, 인터럽트 지연, 지터와 함께 측정해야 한다. 테스트에는 높은 CPU 부하, 통신 활동, 센서 외란(Sensor Disturbance), 급격한 토크 변화, 운전 한계 조건(Boundary Operating Condition)을 포함해야 한다. 하드웨어 타임스탬프(Hardware Timestamp), 오실로스코프 측정(Oscilloscope Measurement), RTOS 트레이싱(RTOS Tracing), GPIO 계측(GPIO Instrumentation)을 통해 실제 제어 주기가 정의된 시간 한계 안에 유지되는지를 확인할 수 있다.

로봇에서 FOC 계층은 상위 수준 모션 제어(Motion Control) 아래에서 결정론적인 기반을 형성한다. 내비게이션(Navigation)이나 모션 계획(Motion Planning) 시스템은 비교적 낮은 주기로 속도, 토크 또는 위치 명령을 생성할 수 있지만, 모터 제어기는 훨씬 높은 주기로 전류와 모터 토크를 지속적으로 조절한다. 이러한 분리를 통해 로봇의 상위 프로세서는 인지(Perception), 계획(Planning), AI 추론(AI Inference), 통신(Communication)을 수행하면서도 인버터의 각 스위칭 동작에 필요한 정밀한 타이밍을 직접 결정하지 않아도 된다. 따라서 FOC 제어기는 상위 수준의 모션 의도를 안정적이고 정밀하게 제어된 물리적 액추에이션으로 변환한다.

전체 실시간 FOC 설계는 결과적으로 센싱(Sensing), 좌표 변환(Coordinate Transformation), 전류 조절(Current Regulation), 전압 생성(Voltage Generation), 변조(Modulation), 액추에이터 실행(Actuator Execution)이 통합된 하나의 체인으로 이해할 수 있다. 그 효과는 제어 이론(Control Theory)의 정확성과 결정론적인 소프트웨어 실행에 모두 의존한다. 잘 설계된 구현은 동기화된 ADC 샘플링과 PWM 동작, 제한된 연산 시간, 낮은 스케줄링 지터, 적절한 태스크 우선순위, 강건한 고장 처리(Robust Fault Handling), 충분한 연산 여유(Computational Margin)를 유지한다. 따라서 RTOS는 단순히 FOC 코드를 실행하는 환경이 아니라, 수학적으로 설계된 제어 전략이 실제 모터에서 안정적으로 구현될 수 있는지를 결정하는 시간적 아키텍처(Temporal Architecture)의 일부이다.

## 11.03 EtherCAT Master Multi-Axis Synchronous Control [w/Code]

![](images/image4.png){width="7.268055555555556in" height="7.268055555555556in"}

EtherCAT 마스터(EtherCAT Master)는 여러 서보 드라이브(Servo Drive), 모터 컨트롤러(Motor Controller), I/O 장치를 높은 수준의 예측 가능한 타이밍으로 조정하기 위해 설계된 실시간 통신 및 제어 아키텍처이다. 다축 로봇(Multi-Axis Robot)에서는 마스터가 정의된 통신 주기 안에서 여러 축에 모션 명령(Motion Command)을 분배하고 각 축의 피드백(Feedback)을 수집해야 한다. 일반적인 네트워크 통신과 달리 목표는 단순히 높은 대역폭(Bandwidth)을 확보하는 것이 아니라 결정론적인 주기 시간(Cycle Time), 제한된 지연시간(Bounded Latency), 정밀한 동기화(Synchronization), 모든 축에서 일관된 명령 적용을 보장하는 것이다. 이러한 특성 때문에 EtherCAT은 협조 로봇 동작(Coordinated Robot Motion)과 서보 제어(Servo Control)에 특히 적합하다.

EtherCAT 시스템은 일반적으로 하나의 마스터(Master)와 논리적인 통신 토폴로지(Communication Topology)로 구성된 여러 슬레이브(Slave) 장치로 이루어진다. 마스터는 목표 위치(Target Position), 속도(Velocity), 토크(Torque), 제어 워드(Control Word)와 같은 명령을 포함하는 주기적 프로세스 데이터 프레임(Cyclic Process-Data Frame)을 생성하고, 각 슬레이브는 프레임이 장치를 통과하는 과정에서 자신의 프로세스 데이터를 삽입하거나 추출한다. 이러한 처리 방식은 각 장치가 전체 프레임을 독립적으로 수신하고 다시 전송해야 하는 필요성을 줄인다. 그 결과 통신 오버헤드(Communication Overhead)를 낮게 유지하면서도 많은 축이 매우 엄격하게 제어되는 실시간 주기 안에서 데이터를 교환할 수 있다.

EtherCAT 마스터는 1ms 또는 애플리케이션에 맞게 정의된 다른 주기와 같은 사전에 정의된 통신 주기(Communication Period)에 따라 동작한다. 각 주기에서 마스터는 다음 명령을 준비하고, 프로세스 데이터 프레임을 전송하며, 갱신된 피드백을 수신하고, 해당 정보를 제어 애플리케이션에서 사용할 수 있도록 한다. 필요한 주기 시간은 축의 수, 제어기 연산(Control Computation), 네트워크 토폴로지, 장치 응답 시간(Device Response Time), 모션 제어 요구사항에 따라 결정된다. 마스터는 다음 주기가 시작되기 전에 자신의 처리를 완료해야 하며, 이를 통해 명령과 피드백의 타이밍을 예측 가능한 상태로 유지할 수 있다.

다축 동기화(Multi-Axis Synchronization)는 로봇 시스템에서 EtherCAT을 사용하는 가장 중요한 이유 가운데 하나이다. 개별 서보 드라이브는 각각의 로컬 클록(Local Clock)을 가지고 있을 수 있지만, 협조 동작(Coordinated Motion)을 위해서는 모든 축의 제어 동작이 공통의 시간 기준(Common Temporal Reference)에 대응해야 한다. EtherCAT Distributed Clocks는 장치의 타이밍을 매우 높은 정밀도로 동기화할 수 있는 메커니즘을 제공한다. 마스터는 이러한 공통 시간 기반을 이용하여 여러 축의 샘플링(Sampling), 명령 실행(Command Execution), 위치 갱신(Position Update)을 조정할 수 있으며, 이를 통해 모션 왜곡(Motion Distortion)으로 나타날 수 있는 축 간 상대적인 타이밍 오차를 줄일 수 있다.

Distributed Clock 동기화는 여러 관절이 하나의 궤적(Trajectory)에 따라 동시에 움직여야 할 때 특히 중요하다. 각 축이 서로 약간 다른 시간에 명령을 적용한다면 수치적으로는 동일한 명령을 사용하더라도 로봇은 동기화 오차(Synchronization Error)를 경험할 수 있다. 동기화된 클록을 사용하면 제어기는 모든 축에 대해 공통의 실행 시점(Common Execution Point)을 설정할 수 있다. 이는 궤적의 일관성(Trajectory Consistency), 협조적인 가감속(Coordinated Acceleration and Deceleration), 다축 모션의 반복 정밀도(Repeatability)를 향상시킨다. 따라서 타이밍 동기화는 단순한 통신 기능이 아니라 모션 제어 아키텍처의 일부가 된다.

프로세스 데이터 모델(Process-Data Model)은 로봇의 실시간 제어 요구사항에 따라 설계해야 한다. 주기적 데이터에는 일반적으로 실제 위치(Actual Position), 실제 속도(Actual Velocity), 실제 토크(Actual Torque), 목표 위치, 목표 속도, 제어 상태(Control Status), 고장 정보(Fault Information)와 같은 시간에 민감한 값이 포함된다. 중요도가 낮은 설정(Configuration)이나 진단(Diagnostics) 정보는 결정론적인 제어 경로를 불필요하게 점유하지 않도록 별도로 처리할 수 있다. 따라서 잘 설계된 마스터는 주기적인 실시간 프로세스 데이터와 더 느린 파라미터 관리(Parameter Management) 및 진단 작업을 구분한다.

EtherCAT 마스터는 또한 통신 타이밍을 로봇 제어기의 제어 루프 아키텍처(Control-Loop Architecture)와 통합해야 한다. 상위 수준의 모션 플래너(Motion Planner)는 비교적 낮은 주기로 궤적을 생성할 수 있지만, EtherCAT 마스터는 훨씬 높은 주기로 갱신된 설정값(Setpoint)을 서보 드라이브에 분배할 수 있다. 이후 서보 드라이브는 전류(Current), 속도(Velocity), 위치(Position) 제어를 더욱 빠른 주기로 자체적으로 수행한다. 이 구조는 궤적 생성, EtherCAT 통신, 로컬 서보 제어가 서로 다른 주파수에서 동작하면서도 시간적으로 동기화되는 계층적 다중 주기 구조(Hierarchical Multi-Rate Structure)를 형성한다.

따라서 EtherCAT 마스터가 실행되는 호스트 프로세서(Host Processor)에서는 실시간 스케줄링(Real-Time Scheduling)이 필수적이다. 통신 스레드(Communication Thread)는 적절한 실시간 우선순위(Real-Time Priority)를 가져야 하며 불필요한 블로킹(Blocking), 동적 메모리 할당(Dynamic Memory Allocation), 예측하기 어려운 백그라운드 처리를 피해야 한다. 마스터가 실시간 Linux 플랫폼(Real-Time Linux Platform)에서 구현되는 경우에는 CPU Affinity, 인터럽트 처리(Interrupt Handling), 메모리 잠금(Memory Locking), 실시간 스케줄링 정책(Real-Time Scheduling Policy)이 필요할 수 있다. 목표는 다른 로봇 소프트웨어가 동시에 실행되는 상황에서도 네트워크 송수신이 예측 가능한 시간 범위 안에서 수행되도록 하는 것이다.

통신 주기는 센서 데이터 획득(Sensor Acquisition) 및 액추에이터 갱신(Actuator Update)과도 조정되어야 한다. 마스터는 모든 드라이브에서 실제 위치와 상태 데이터를 수신하고, 모션 제어 계층에서 이러한 값을 처리한 다음, 동일한 주기 또는 다음 주기 안에서 새로운 명령을 전송할 수 있다. 이 과정에서 추가적인 지연이 발생하면 실질적인 제어 루프 지연시간이 증가한다. 따라서 아키텍처에서는 피드백이 언제 사용 가능해지는지, 새로운 명령이 언제 계산되는지, 각 서보가 언제 해당 명령을 적용하는지를 명확하게 정의해야 한다. 이러한 전체 체인에서 결정론적인 타이밍을 확보하는 것은 안정적이고 정밀한 다축 제어를 위해 필수적이다.

고장 처리(Fault Handling)는 독립적인 진단 기능으로 취급하지 말고 EtherCAT 마스터에 통합해야 한다. 통신 손실(Communication Loss), 슬레이브 장치 고장(Slave Device Fault), 잘못된 프로세스 데이터(Invalid Process Data), 동기화 오류(Synchronization Error), 드라이브 고장(Drive Fault), 통신 주기 누락(Missed Communication Cycle)은 모두 협조적인 로봇 동작에 영향을 줄 수 있다. 마스터는 중요한 고장을 제한된 시간 안에 감지하고 제어 감속(Controlled Deceleration), 토크 비활성화(Torque Disable), 사전에 정의된 안전 상태(Safe State) 전환과 같은 적절한 대응을 시작해야 한다. 대응 동작은 시스템 안전 요구사항에 따라 우선순위를 가져야 하며, 로깅이나 진단 활동이 중요한 보호 동작을 지연시켜서는 안 된다.

성능 검증(Performance Verification)은 네트워크 동작과 모션 제어 동작을 모두 평가해야 한다. 통신 주기 시간, 전송 지연시간(Transmission Latency), 동기화 오차, 누락된 주기, 지터(Jitter), CPU 사용률(CPU Utilization), 패킷 처리 시간(Packet Processing Time)을 현실적인 최대 부하 조건에서 측정해야 한다. 동시에 다축 위치 오차(Multi-Axis Position Error), 속도 일관성(Velocity Consistency), 궤적 추종(Trajectory Tracking), 정착 동작(Settling Behavior), 동기화 정확도(Synchronization Accuracy)도 평가해야 한다. 하드웨어 타임스탬프(Hardware Timestamp), 실시간 트레이싱(Real-Time Tracing), 네트워크 진단(Network Diagnostics), 드라이브 피드백을 활용하면 통신 시스템이 정의된 시간 한계 안에서 동작하는지를 입증할 수 있다.

로봇 아키텍처에서 EtherCAT은 일반적으로 상위 수준 제어기와 개별 서보 드라이브 사이의 결정론적인 계층(Deterministic Layer)에 위치한다. 내비게이션(Navigation), 계획(Planning), AI 추론(AI Inference), 감독 소프트웨어(Supervisory Software)는 Linux 또는 엣지 컴퓨터(Edge Computer)에서 실행되면서 모션 목표(Motion Objective)를 생성할 수 있고, 실시간 제어기는 이러한 목표를 동기화된 축 명령(Synchronized Axis Command)으로 변환한다. EtherCAT은 이러한 명령과 피드백 값을 예측 가능한 타이밍으로 전달하며, 각각의 모터 컨트롤러는 자신의 빠른 로컬 제어 루프를 실행한다. 이러한 분리를 통해 상위 수준 연산의 유연성을 유지하면서도 하위 수준의 협조적인 액추에이션을 저해하지 않을 수 있다.

전체 다축 EtherCAT 아키텍처는 궤적 생성(Trajectory Generation), 실시간 스케줄링, 주기적 프로세스 데이터 교환(Cyclic Process-Data Exchange), Distributed Clock 동기화, 로컬 서보 제어(Local Servo Control), 물리적 모션(Physical Motion)을 연결하는 동기화된 체인으로 이해할 수 있다. EtherCAT의 가치는 단순히 많은 모터와 통신할 수 있다는 데 있는 것이 아니라, 서로 독립적인 여러 액추에이터가 하나의 협조적인 시스템처럼 동작하도록 만들 수 있다는 데 있다. 강건한 구현은 결정론적인 마스터 스케줄링, 동기화된 장치 클록, 제한된 통신 지연시간, 적절한 프로세스 데이터 설계, 고장 처리, 정량적인 타이밍 검증을 결합하여 고성능 로봇 모션 제어를 위한 신뢰성 있는 기반을 제공한다.

## 11.04 Current / Speed / Position Loop Real-Time Layer Design

![](images/image5.png){width="7.268055555555556in" height="7.268055555555556in"}

로봇 모터 제어 시스템은 일반적으로 서로 다른 속도에서 동작하는 위치 제어(Position Control), 속도 제어(Speed Control), 전류 제어(Current Control)의 중첩된 실시간 제어 루프(Nested Real-Time Control Loop) 계층으로 구성된다. 위치 루프는 원하는 모션을 정의하고, 속도 루프는 모션 오차를 토크 관련 기준값으로 변환하며, 전류 루프는 가장 빠른 액추에이터 수준의 조절을 수행한다. 이러한 계층 구조를 통해 각 제어기는 물리적 동역학에 적합한 주기로 동작하면서도 기준값과 피드백의 명확한 흐름을 유지할 수 있다. 이러한 구조는 RTOS 기반 모터 제어기에서 특히 중요하다.

위치 루프(Position Loop)는 기계적인 위치 변화가 전기적 전류 변화보다 상대적으로 느리기 때문에 일반적으로 가장 낮은 제어 주기에서 동작한다. 위치 명령(Position Command)과 측정된 위치를 비교하여 다음 제어 계층을 위한 속도 기준값(Speed Reference)을 생성한다. 애플리케이션에 따라 위치 제어기는 관절 위치(Joint Position), 휠 위치(Wheel Position), 조향각(Steering Angle) 또는 기타 기계적 상태(Mechanical State)를 제어할 수 있다. 위치 루프의 실행 주기가 전류 루프의 높은 주파수와 동일할 필요는 없지만, 생성되는 속도 기준값이 일관되게 갱신되도록 실행 타이밍은 예측 가능해야 한다.

속도 루프(Speed Loop)는 중간 속도에서 동작하며 속도 오차를 토크 또는 전류 기준값(Torque or Current Reference)으로 변환한다. 위치 제어기에서 전달된 속도 기준값을 받아 측정된 모터 또는 휠 속도와 비교한다. 일반적으로 PI 제어기(PI Controller)를 사용하여 필요한 토크 관련 명령을 생성하며, 이 명령은 필드 지향 제어(Field Oriented Control, FOC) 구현에서는 q축 전류 기준값(q-Axis Current Reference)이 될 수 있다. 속도 루프는 위치 루프보다 빠르고 전류 루프보다 느리게 동작하므로, 그 주기와 우선순위는 전체 다중 주기 제어 아키텍처(Multi-Rate Control Architecture)의 일부로 결정해야 한다.

전류 루프(Current Loop)는 전기적 모터 동역학이 기계적 위치나 속도보다 훨씬 빠르게 변화하기 때문에 가장 빠르고 시간적으로 가장 중요한 제어 계층이다. FOC 시스템에서는 전류 제어기가 상전류 피드백(Phase-Current Feedback)을 처리하고, 좌표 변환(Coordinate Transformation)을 수행하며, d축 및 q축 전류를 조절하고, PWM 변조를 위한 전압 명령(Voltage Command)을 생성한다. 모터와 인버터에 따라 수 kHz 이상의 주기로 실행될 수 있다. 전류 루프의 실행 주기, 지연시간(Latency), 지터(Jitter)는 토크 응답(Torque Response), 전류 추종(Current Tracking), 액추에이터 전체의 안정성에 직접적인 영향을 준다.

세 개의 루프는 서로 독립적인 제어기로 보아서는 안 된다. 이들은 각각의 느린 계층이 바로 아래의 빠른 계층에 기준값을 제공하는 중첩된 계층 구조를 형성한다. 위치 제어는 속도 기준값을 생성하고, 속도 제어는 토크 또는 전류 기준값을 생성하며, 전류 제어는 모터를 직접 구동하는 전압 또는 PWM 명령을 생성한다. 피드백은 해당 측정값을 통해 상위 계층으로 전달된다. 이러한 계층 구조는 기계적 목표와 전기적 액추에이션을 분리하면서도 전체 시스템이 하나의 폐루프 제어기(Closed-Loop Controller)처럼 동작하도록 한다.

각 제어 루프의 주기가 서로 다른 이유는 각각의 물리적 변수가 서로 다른 동적 대역폭(Dynamic Bandwidth)을 갖기 때문이다. 모든 제어기를 동일한 매우 높은 주파수로 실행하면 프로세서 자원을 낭비할 수 있는 반면, 전류 루프를 지나치게 느리게 실행하면 모터 제어 성능이 저하될 수 있다. 따라서 다중 주기 설계(Multi-Rate Design)는 물리적 응답과 제어 중요도에 따라 연산 자원을 할당한다. 예를 들어 위치 루프는 수십 또는 수백 Hz, 속도 루프는 수백 Hz에서 수 kHz, 전류 루프는 수 kHz 이상의 주기로 동작할 수 있으며, 실제 값은 시스템 요구사항에 따라 결정된다.

RTOS 스케줄러(RTOS Scheduler)는 이러한 계층 구조를 위한 시간적 기반을 제공한다. 가장 빠른 전류 제어 태스크(Current-Control Task)는 일반적으로 가장 높은 실시간 우선순위(Real-Time Priority)를 가지며, 속도 및 위치 태스크는 점차 낮은 우선순위를 갖는다. 통신(Communication), 진단(Diagnostics), 로깅(Logging), 설정(Configuration), 사용자 인터페이스(User Interface) 기능은 일반적으로 제어 태스크보다 낮은 우선순위에서 실행되어야 한다. 높은 우선순위의 제어 태스크가 실행 가능 상태가 되면 스케줄러는 불필요한 간섭 없이 해당 태스크가 실행될 수 있도록 해야 한다. 이러한 우선순위 구조는 가장 시간에 민감한 제어 기능이 필요한 시점에 프로세서 시간을 확보하도록 한다.

하드웨어 타이머(Hardware Timer)와 PWM 주변장치(PWM Peripheral)는 가장 빠른 제어 계층을 위한 정밀한 타이밍 기준을 제공한다. 타이머 이벤트는 ADC 샘플링을 트리거하고, 전류 제어 연산을 시작하거나 이를 알리며, 이후 PWM 갱신을 조정할 수 있다. 샘플링, 연산, 액추에이션 사이의 시간 관계는 모든 제어 주기에서 반복 가능하게 유지되어야 한다. 샘플링이 너무 늦게 수행되거나 PWM 갱신이 지연되면 실질적인 제어 루프 지연시간이 변화한다. 따라서 RTOS는 소프트웨어 지연을 이용하여 정밀한 주변장치 타이밍을 대체하기보다는 하드웨어 타이밍과 협력해야 한다.

전류, 속도, 위치 계층 사이의 경계는 로봇 제어기의 유용한 인터페이스도 정의한다. 위치 계층은 위치 기준값과 측정 위치를 교환하고, 속도 계층은 속도 기준값과 측정 속도를 교환하며, 전류 계층은 토크 또는 전류 기준값과 전기적 피드백(Electrical Feedback)을 교환한다. 이러한 인터페이스를 사용하면 전체 제어 계층 구조를 유지하면서 각 계층을 독립적으로 개발하고 테스트할 수 있다. 또한 필요한 경우 서로 다른 기능을 서로 다른 프로세서에 배치할 수도 있으며, 이 경우에도 통신 지연시간과 동기화가 요구되는 한계 안에 있어야 한다.

제어 주파수가 높아질수록 실시간 제약은 더욱 엄격해진다. 전류 루프는 충분한 여유를 확보한 상태에서 실행 시간이 제어 주기보다 항상 짧게 유지되도록 제한된 실행 시간(Bounded Execution Time)을 가져야 한다. 속도와 위치 루프는 상대적으로 완화된 타이밍 요구사항을 갖지만, 이들 역시 주기적이고 예측 가능한 방식으로 실행되어야 한다. 과도한 스케줄링 지터(Scheduling Jitter), 블로킹 동기화(Blocking Synchronization), 동적 메모리 할당(Dynamic Memory Allocation), 긴 인터럽트 핸들러(Long Interrupt Handler)는 제어 계층 전체에 타이밍 변동을 전달할 수 있다. 따라서 핵심 제어 경로에서는 블로킹을 최소화하고 진단 및 통신 기능을 시간에 민감한 실행 경로로부터 분리해야 한다.

다중 주기 아키텍처(Multi-Rate Architecture)는 모터 제어와 상위 수준 로봇 소프트웨어 사이에도 자연스러운 관계를 형성한다. 내비게이션(Navigation), 궤적 계획(Trajectory Planning), 모션 제어(Motion Control) 시스템은 상대적으로 느린 속도로 위치 또는 속도 명령을 생성할 수 있는 반면, 임베디드 제어기는 더 빠른 속도 및 전류 루프를 지속적으로 실행한다. 따라서 엣지 컴퓨터(Edge Computer)나 상위 수준 프로세서가 모터의 모든 전기적 제어 주기를 직접 제어할 필요는 없다. 상위 계층은 모션 의도(Motion Intent)를 제공하고, 결정론적인 모터 제어 계층이 이를 지속적으로 조절되는 물리적 액추에이션(Physical Actuation)으로 변환한다.

검증(Verification)은 개별 제어기만 측정하는 것이 아니라 전체 계층 구조를 평가해야 한다. 위치 정확도(Position Accuracy), 속도 제어(Speed Regulation), 전류 추종(Current Tracking), 토크 응답(Torque Response), 과도 응답(Transient Behavior), 정상 상태 안정성(Steady-State Stability)을 루프 주기, 실행 시간, 지연시간, 지터와 함께 평가해야 한다. 테스트에는 높은 프로세서 부하, 통신 활동, 급격한 기준값 변화, 센서 외란(Sensor Disturbance), 경계 운전 조건(Boundary Operating Condition)을 포함해야 한다. 목표는 각 제어 계층이 요구되는 타이밍을 유지하고, 느린 소프트웨어 기능에서 발생하는 외란이 가장 빠른 액추에이터 루프의 결정론적인 동작을 저해하지 않는다는 것을 확인하는 것이다.

잘 설계된 전류-속도-위치(Current-Speed-Position) 아키텍처는 제어 이론(Control Theory), 다중 주기 스케줄링(Multi-Rate Scheduling), 하드웨어 타이밍, 피드백 처리(Feedback Processing), RTOS 실행을 하나의 실시간 시스템으로 통합한다. 위치 제어는 로봇이 어디로 이동해야 하는지를 결정하고, 속도 제어는 얼마나 빠르게 이동해야 하는지를 결정하며, 전류 제어는 모터가 필요한 물리적 힘을 어떻게 생성할지를 결정한다. RTOS는 이러한 계층에 예측 가능한 실행 환경을 제공하고, 하드웨어 타이머, PWM, 센싱(Sensing)은 정밀한 시간적 동기화를 유지한다. 이러한 분리 구조는 안정적이고 정밀한 액추에이션이 필요한 AMR, 매니퓰레이터(Manipulator), 사족보행 로봇(Quadruped), 휴머노이드(Humanoid) 및 기타 로봇 시스템을 위한 확장 가능한 기반을 제공한다.

## 11.05 RTOS Interrupt-Based PWM Control Loop [w/Code]

![](images/image6.png){width="7.268055555555556in" height="7.268055555555556in"}

![](images/image7.png){width="7.268055555555556in" height="7.268055555555556in"}

인터럽트 기반 PWM 제어(Interrupt-Based PWM Control)는 모터 제어 알고리즘을 정확하고 반복 가능한 시간 간격으로 실행하기 위한 핵심적인 실시간 기법이다. 일반적인 태스크가 제어 루프의 실행 시점을 결정하도록 하는 대신, 하드웨어 타이머(Hardware Timer) 또는 PWM 주변장치(PWM Peripheral)가 사전에 정의된 PWM 주기의 특정 시점에서 인터럽트(Interrupt)를 발생시킨다. 이 인터럽트는 센싱(Sensing), 제어 연산(Control Computation), 액추에이터 갱신(Actuator Update)을 위한 결정론적인 타이밍 기준을 제공한다. RTOS 기반 모터 제어기에서는 이러한 방식이 하드웨어 수준의 타이밍과 소프트웨어 스케줄링(Software Scheduling)을 연결하며, 고속 폐루프 제어(Closed-Loop Control)를 위한 신뢰성 있는 기반을 제공한다.

일반적인 제어 주기는 PWM 타이머가 사전에 정의된 비교 이벤트(Compare Event) 또는 갱신 이벤트(Update Event)에 도달하면서 시작된다. 이 하드웨어 이벤트가 인터럽트를 발생시키고, 인터럽트 서비스 루틴(Interrupt Service Routine, ISR)이 필요한 피드백 획득을 수행하거나 시작한다. 마이크로컨트롤러 구조에 따라 ADC 변환(ADC Conversion)이 타이머에 의해 이미 트리거되어 ISR이 완료된 측정값을 처리할 수도 있다. 이후 제어 알고리즘이 다음 액추에이터 명령을 계산하고, 결과적인 PWM 듀티값(PWM Duty Value)이 적절한 타이머 레지스터(Timer Register)로 전달된다. 이 시퀀스는 최소한의 변동으로 모든 제어 주기마다 반복된다.

PWM 생성(PWM Generation)과 제어 루프 타이밍(Control-Loop Timing)의 관계는 PWM이 액추에이터 명령 수단인 동시에 정밀한 시간 기준(Time Base)이기 때문에 특히 중요하다. PWM 캐리어(PWM Carrier)는 스위칭 동작(Switching Action)이 언제 발생하는지를 결정하고, 동기화된 ADC 샘플링(ADC Sampling)은 전기적 피드백이 언제 관측되는지를 결정한다. 잘 설계된 시스템은 PWM 파형, ADC 샘플링 시점, 제어 연산, 듀티 사이클 갱신 사이에 고정된 시간 관계를 설정한다. 이를 통해 불필요한 위상 변동(Phase Variation)을 방지하고 실질적인 샘플링 간격을 예측 가능하게 만든다. 이러한 동기화는 안정적인 전류 조절(Current Regulation)과 반복 가능한 모터 동작에 필수적이다.

ISR은 짧고 결정론적인 실행 경로(Deterministic Execution Path)로 설계해야 한다. ISR의 주요 책임은 하드웨어 이벤트에 신속하게 응답하고, 중요한 데이터를 획득하거나 검증하며, 시간에 민감한 제어 동작을 시작하는 것이다. 긴 연산, 로깅(Logging), 통신 처리(Communication Processing), 메모리 할당(Memory Allocation), 기타 비핵심 작업은 일반적으로 ISR 내부에 직접 배치해서는 안 된다. 제어 연산이 충분히 복잡한 경우 ISR은 RTOS 태스크에 알림을 전달하거나 해당 태스크를 실행 가능 상태로 전환할 수 있다. 적절한 구조는 요구되는 제어 주파수, 프로세서 성능, 측정된 인터럽트 지연시간에 따라 결정된다.

매우 빠른 모터 제어 루프에서는 전체 제어 알고리즘을 인터럽트 컨텍스트(Interrupt Context)에서 직접 실행하면 스케줄링 오버헤드를 줄이고 매우 예측 가능한 타이밍을 제공할 수 있다. 이 방식은 연산량이 제어 가능한 범위에 있고 PWM 주기 안에서 안정적으로 완료될 수 있는 소형 FOC 전류 루프(FOC Current Loop)에 적합하다. 그러나 ISR 실행 시간은 제한된 범위 안에 있어야 한다. 지나치게 긴 인터럽트는 다른 인터럽트와 RTOS 동작을 지연시킬 수 있기 때문이다. 따라서 설계에서는 명확한 최악 실행 시간(Worst-Case Execution Time) 예산을 설정하고 전체 인터럽트 경로가 다음 중요한 하드웨어 이벤트 전에 완료되는지를 검증해야 한다.

또 다른 구조에서는 ISR을 주로 RTOS 태스크를 트리거하는 용도로 사용한다. 타이머 인터럽트는 최소한의 처리만 수행한 후 RTOS 알림(Notification), 세마포어(Semaphore), 이벤트(Event) 또는 유사한 메커니즘을 통해 높은 우선순위의 제어 태스크에 신호를 전달한다. 스케줄러는 즉시 해당 제어 태스크를 실행 가능 상태로 만들고, 태스크는 더 큰 연산을 수행한 후 PWM 출력을 갱신한다. 이러한 구조는 하드웨어 이벤트 처리와 애플리케이션 수준 제어 로직을 명확하게 분리할 수 있지만 추가적인 스케줄링 지연시간을 발생시킨다. 따라서 이 추가적인 소프트웨어 계층의 지연시간과 지터가 제어 주기와 호환되는지를 반드시 측정해야 한다.

PWM 레지스터 갱신(PWM Register Update)은 하드웨어의 동작 특성을 세심하게 고려해야 한다. 많은 모터 제어용 타이머는 프리로드(Preload) 또는 섀도 레지스터(Shadow Register)를 제공하여 새로운 듀티 사이클 값이 현재 활성화된 PWM 주기의 중간에 적용되지 않도록 한다. 대신 계산된 값은 버퍼 레지스터(Buffered Register)에 기록되고 정의된 타이머 갱신 이벤트에서 활성 비교 레지스터(Active Compare Register)로 전달될 수 있다. 이를 통해 부분적으로 갱신된 듀티 사이클을 방지하고 각 제어 주기 사이에 명확한 경계를 형성할 수 있다. 이러한 하드웨어 지원 동기화는 소프트웨어 타이밍에만 의존하는 것보다 유리하며, CPU 실행 시간이 변하더라도 주변장치가 갱신 관계를 보장할 수 있기 때문이다.

ADC 트리거링(ADC Triggering) 역시 PWM 파형과 동기화되어야 한다. 스위칭 인버터(Switching Inverter)에서는 전기적 잡음과 과도 전류가 발생하기 때문에 특정 샘플링 시점이 다른 시점보다 적합하지 않을 수 있다. 따라서 타이머가 PWM 주기에서 신중하게 선택된 시점에 ADC를 트리거하도록 구성할 수 있으며, 이를 통해 보다 대표적인 상전류 측정값을 획득할 수 있다. 이후 획득된 데이터는 다음 적절한 PWM 갱신 전에 FOC 알고리즘에서 처리될 수 있다. 이러한 전체 과정은 연속된 제어 주기에서 일관되게 유지되어야 하는 결정론적인 샘플-연산-갱신 파이프라인(Sample--Compute--Update Pipeline)으로 볼 수 있다.

RTOS 우선순위 설계(Priority Design)는 인터럽트 기반 제어가 다른 소프트웨어와 어떻게 상호작용하는지를 결정한다. 모터 제어 인터럽트는 일반적으로 요구되는 지연시간 안에서 응답할 수 있도록 충분히 높은 하드웨어 우선순위(Hardware Priority)를 가져야 하며, 지연 실행(Deferred Execution)을 사용하는 경우 해당 제어 태스크에도 높은 RTOS 우선순위를 부여해야 한다. 통신, 진단, 로깅, 설정, 사용자 인터페이스와 같은 낮은 우선순위 기능은 제어 루프에 필요한 시간 여유를 소비해서는 안 된다. 또한 인터럽트 중첩(Interrupt Nesting)과 임계 구역(Critical Section)에도 주의해야 한다. 인터럽트를 일시적으로 비활성화하거나 잠금을 유지하면 모터 제어 경로에 예상하지 못한 지연이 발생할 수 있기 때문이다.

인터럽트 기반 PWM 제어는 고장 조건(Failure Condition)도 고려해야 한다. 과전류(Overcurrent), 인버터 고장(Inverter Fault), DC 버스 이상(DC-Bus Abnormality), 센서 고장(Sensor Failure) 또는 기타 위험한 조건에서는 PWM 출력을 즉시 비활성화해야 할 수 있다. 많은 모터 제어용 타이머 주변장치는 브레이크 입력(Break Input)이나 고장 처리 메커니즘(Fault Mechanism)을 제공하여 정상적인 소프트웨어 실행을 기다리지 않고 PWM 출력을 사전에 정의된 안전 상태(Safe State)로 전환할 수 있다. 이후 소프트웨어 고장 처리는 이벤트를 기록하고 시스템 상태를 갱신하며 복구 또는 종료 절차를 조정할 수 있다. 하드웨어 보호와 RTOS 수준의 고장 처리를 결합하면 모든 고장을 소프트웨어 태스크만으로 감지하고 대응하는 것보다 강력한 보장을 제공할 수 있다.

전체 제어 주기의 타이밍 예산(Timing Budget)에는 인터럽트 지연시간, ADC 획득, 데이터 준비, 제어 연산, PWM 레지스터 갱신, 그리고 필요한 컨텍스트 스위칭(Context Switching) 또는 동기화 오버헤드를 포함해야 한다. PWM 주기가 T라면 트리거 이벤트가 발생한 시점부터 필요한 액추에이터 갱신까지의 전체 시간이 충분한 여유를 두고 T보다 짧게 유지되어야 한다. 이 여유는 서로 다른 운전 조건에서 실행 시간이 증가할 수 있기 때문에 중요하다. 정상 동작에서 간신히 데드라인을 만족하는 설계는 통신 트래픽, 진단, 인터럽트, 캐시 효과 또는 기타 연산 부하가 증가하면 실패할 수 있다.

따라서 검증(Verification)은 의도된 스케줄이 실제로 실행되고 있다고 가정하지 말고 실제 하드웨어 타이밍을 측정해야 한다. GPIO 계측(GPIO Instrumentation)을 사용하면 오실로스코프를 통해 제어 연산의 시작과 종료를 확인할 수 있으며, 하드웨어 타이머와 트레이스 시스템(Trace System)은 인터럽트 지연시간, 실행 시간, 주기 변동을 측정할 수 있다. 테스트는 현실적인 최대 CPU 부하와 통신 활동에서 수행해야 하며, 급격한 모터 과도상태와 경계 운전 조건도 포함해야 한다. 중요한 측정 항목에는 제어 루프 주기, ISR 실행 시간, 스케줄링 지연시간, PWM 갱신 타이밍, 지터, 누락된 제어 주기, 그리고 그에 따른 모터 제어 성능이 포함된다.

로봇 아키텍처에서 인터럽트 기반 PWM 제어는 RTOS 또는 MCU 제어 환경과 물리적 액추에이터를 연결하는 가장 낮은 수준의 결정론적 소프트웨어 계층을 형성한다. 상위 수준의 위치 및 속도 제어기는 더 느린 주기로 기준값을 생성할 수 있지만, 빠른 전류 제어 계층은 모든 PWM 관련 제어 이벤트에 응답한다. 이러한 분리를 통해 로봇의 상위 소프트웨어는 모션 계획(Motion Planning), 통신, 인지(Perception), AI 처리를 수행하면서도 모터 인버터의 정밀한 스위칭 타이밍을 직접 제어할 필요가 없어진다. 인터럽트 기반 계층은 이러한 상위 수준 기준값을 안정적인 전기적 액추에이션으로 지속적으로 변환한다.

결과적으로 이 아키텍처는 하드웨어 타이머의 정밀성, 동기화된 ADC 획득, 결정론적인 제어 연산, 버퍼 기반 PWM 갱신, RTOS 스케줄링, 하드웨어 고장 보호를 하나의 폐루프 실행 모델로 통합한다. 핵심 목표는 단순히 PWM 신호를 생성하는 것이 아니라 모든 제어 주기가 예측 가능한 시점에 시작되고, 연산되며, 액추에이터를 갱신하도록 보장하는 것이다. 인터럽트 경로가 짧고, 제어 연산이 제한된 실행 시간을 가지며, 동기화가 유지되고, 충분한 CPU 여유가 확보된다면 시스템은 낮은 지터와 안정적인 실시간 동작을 달성할 수 있다. 이러한 이유로 인터럽트 기반 PWM 제어는 고성능 로봇 모터 제어기의 핵심 구현 패턴 가운데 하나이다.

## 11.06 Motor Control Error Detection and Safe Stop Logic [w/Code]

![](images/image8.png){width="7.268055555555556in" height="7.268055555555556in"}

모터 제어 오류 감지 및 안전 정지 로직(Motor-Control Error Detection and Safe-Stop Logic)은 비정상적인 액추에이터 동작이 제어되지 않는 움직임이나 장비 손상으로 발전하는 것을 방지하는 보호 계층(Protection Layer)을 제공한다. 제어기는 정상적인 제어 루프(Control Loop)가 실행되는 동안 전기적, 기계적, 통신 및 소프트웨어 상태를 지속적으로 감시한다. 일반적으로 감시되는 조건에는 과전류(Overcurrent), 과열(Overtemperature), 비정상적인 버스 전압(Bus Voltage), 엔코더 고장(Encoder Fault), 스톨(Stall), 명령 추종 오류(Command-Tracking Error), 통신 손실(Communication Loss), 워치독 이벤트(Watchdog Event) 등이 포함된다. 목적은 단순히 오류를 감지하는 것이 아니라 적절한 보호 대응을 선택하고 실행할 수 있을 만큼 충분히 이른 시점에 오류를 감지하는 것이다.

오류 감지(Error Detection)는 직접적인 임계값 감시(Threshold Monitoring)와 개연성 및 일관성 검사(Plausibility and Consistency Check)를 결합해야 한다. 전류(Current), 전압(Voltage), 온도(Temperature), 엔코더 위치(Encoder Position), 속도(Velocity), 명령값(Command Value)은 물리적으로 의미 있는 범위 안에 있어야 한다. 수치적으로 유효한 신호라도 다른 측정값이나 예상되는 모터 동작과 일치하지 않을 수 있다. 예를 들어 엔코더가 계속해서 값을 출력하더라도 측정된 위치가 명령된 움직임과 일치하지 않을 수 있다. 따라서 제어기는 정상 동작을 판단하기 전에 개별 신호의 한계값과 신호 사이의 관계를 모두 평가해야 한다.

오류 감지 경로는 감시 대상 조건의 심각도에 적합한 타이밍으로 동작해야 한다. 과전류나 인버터 이상(Inverter Abnormality)과 같은 빠른 전기적 고장은 일반적인 RTOS 태스크가 처리할 때까지 기다리기에는 너무 느릴 수 있으므로 하드웨어 수준의 보호가 필요할 수 있다. 반면 온도 상승, 명령 추종 편차(Command-Tracking Deviation), 통신 타임아웃(Communication Timeout)과 같은 느린 조건은 주기적인 소프트웨어 감시를 통해 평가할 수 있다. 이를 통해 하드웨어 보호는 즉각적인 위험을 처리하고 펌웨어 감시는 보다 광범위한 시스템 상태 인식, 진단 및 제어된 대응을 수행하는 계층적 오류 감지 구조를 형성한다.

비정상 상태가 감지되면 제어기는 오류를 심각도와 복구 요구사항에 따라 분류해야 한다. 복구 가능한 오류(Recoverable Fault)는 성능을 낮추거나 통신을 재시도하거나 원인이 사라진 후 준비 상태(Ready State)로 복귀하는 것을 허용할 수 있다. 래치형 오류(Latched Fault)는 동작 재개 전에 명시적인 확인(Acknowledgement)이 필요할 수 있다. 안전상 중요한 오류(Safety-Critical Fault)는 보호 상태로 전환하도록 강제하고 정상적인 제어가 계속되는 것을 방지해야 한다. 이러한 분류를 통해 모든 비정상 상태를 동일하게 처리하지 않고 실제 위험도에 맞는 대응을 선택할 수 있다.

안전 정지 로직(Safe-Stop Logic)은 단순히 모터 출력을 0으로 설정하는 하나의 명령이 아니라 상태 전이(State Transition)로 구현해야 한다. 감지된 조건에 따라 제어기는 Running 상태에서 Stopping 상태를 거쳐 안전 상태(Safe State)로 전환할 수 있다. 즉각적인 전원 차단이 필요하지 않은 경우 제어된 정지(Controlled Stop)를 통해 속도나 토크를 점진적으로 감소시킬 수 있으며, 보호 정지(Protective Stop)는 위험한 액추에이터 출력을 빠르게 제거할 수 있다. 심각한 고장의 경우 토크를 비활성화하거나 전력단(Power Stage)을 즉시 차단할 수 있다. 적절한 대응 방식은 오류의 종류, 로봇 동역학(Robot Dynamics), 저장 에너지(Stored Energy), 주변 위험요소에 따라 결정되어야 한다.

명령된 정지(Commanded Stop)와 확인된 안전 상태(Confirmed Safe State)를 구분하는 것은 매우 중요하다. 0 속도 또는 0 토크 명령을 전송했다고 해서 모터가 실제로 정지했다는 것을 의미하지 않는다. 제어기는 속도, 토크, 브레이크 상태(Brake Status), 드라이브 상태(Drive State), 액추에이터 응답(Actuator Response)과 같은 피드백을 관찰하여 요구되는 상태가 실제로 달성되었는지를 확인해야 한다. 예상되는 상태 전이가 정의된 타임아웃 안에 발생하지 않는다면 시스템은 보다 강력한 보호 동작으로 전환해야 한다. 따라서 안전성은 명령이 실행되었을 것이라는 가정이 아니라 피드백과 상태 확인에 기반해야 한다.

안전 정지 시퀀스(Safe-Stop Sequence)는 로봇의 물리적 동작도 고려해야 한다. 갑작스러운 토크 제거가 항상 가장 안전한 대응은 아니다. 이동 로봇은 계속해서 굴러갈 수 있고, 매니퓰레이터(Manipulator)는 페이로드(Payload)를 지지하지 못하게 될 수 있으며, 실외 플랫폼은 지형이나 차량 및 보행자 등의 위험에 계속 노출될 수 있다. 따라서 제어기는 속도, 가속도, 제동 능력(Braking Capability), 액추에이터 상태, 환경 제약(Environmental Constraint)을 이용하여 적절한 정지 프로파일(Stopping Profile)을 선택해야 한다. 안전 상태는 단순히 명령이 비활성화된 소프트웨어 상태가 아니라 물리적으로 안정되고 제어 가능한 상태를 의미한다.

하드웨어와 펌웨어는 보호 경로(Protection Path)에서 서로 협력해야 한다. 인버터 브레이크 입력(Inverter Break Input), 비상 정지 입력(Emergency-Stop Input), 워치독, 전류 보호(Current Protection), 전력단 차단(Power-Stage Inhibit)과 같은 하드웨어 메커니즘은 정상적인 소프트웨어 실행과 독립적으로 즉각적인 보호를 제공할 수 있다. 펌웨어는 이후 오류 분류, 상태 전이, 이벤트 기록(Event Recording), 통신 및 복구 결정을 관리한다. 이러한 분리는 다중 방어 계층(Defense in Depth)을 형성한다. 하드웨어는 즉각적인 물리적 위험을 제한하고 RTOS 기반 소프트웨어는 시스템 수준의 상태 인식을 유지하면서 이후의 안전 대응을 조정한다.

RTOS 아키텍처는 안전 관련 감지와 대응이 비핵심 작업에 의해 지연되지 않도록 해야 한다. 오류 감시 기능에는 적절한 우선순위를 부여해야 하며, 중요한 보호 동작이 장시간 실행되는 통신, 로깅 또는 진단 작업에 의존해서는 안 된다. 긴급한 하드웨어 고장을 처리하는 인터럽트 핸들러(Interrupt Handler)는 짧고 결정론적으로 유지해야 하며, 보다 큰 오류 관리 작업은 높은 우선순위의 안전 태스크(Safety Task)에서 실행할 수 있다. 워치독 감시(Watchdog Supervision)는 소프트웨어 고장, 태스크 기아(Task Starvation), 제어기 응답 없음(Controller Unresponsiveness)을 감지하기 위한 또 하나의 독립적인 메커니즘을 제공할 수 있다.

상위 수준 명령이 CAN, CANopen, EtherCAT 또는 다른 제어 인터페이스를 통해 전달되는 경우 통신 감시는 특히 중요하다. 모터 제어기는 명령 소스가 사라졌음에도 오래된 모션 명령(Stale Motion Command)을 계속 사용해서는 안 된다. 메시지 검증(Message Validation)은 식별자, 범위, 시퀀스 정보(Sequence Information), 무결성 필드(Integrity Field)를 검사할 수 있으며, 타임아웃 감시는 명령이 예상된 주기 안에 도착하고 있는지를 판단한다. 통신이 유효하지 않거나 사용할 수 없게 되면 제어기는 마지막 명령을 계속 유효한 것으로 간주하는 것이 아니라 사전에 정의된 안전 정책에 따라 상태를 전환해야 한다.

명령 추종 오류(Command-Tracking Error)는 또 다른 중요한 보호 메커니즘이다. 제어기는 명령된 모션과 실제 액추에이터 동작을 비교하고 그 차이가 허용 가능한 범위 안에 유지되는지를 판단해야 한다. 지속적인 위치 오차(Persistent Position Error), 과도한 속도 편차(Excessive Velocity Deviation), 예상하지 못한 토크 동작 또는 응답 실패는 기계적 장애물, 액추에이터 열화(Actuator Degradation), 센서 문제 또는 통신 고장을 나타낼 수 있다. 검출 임계값은 정상적인 과도 동작을 고려하여 설정해야 하므로 정상적인 가속이나 부하 변화가 불필요한 정지를 발생시키지 않아야 한다. 동시에 지속적인 편차는 적절한 보호 대응을 발생시켜야 한다.

오류 처리는 Detect, React, Record, Notify라는 결정론적인 순서를 따르는 것이 바람직하다. Detection 단계에서는 비정상 상태를 식별하고 해당 오류 코드(Fault Code)와 심각도를 결정한다. Reaction 단계에서는 제어 감속, 토크 제거, 전력단 차단 또는 안전 상태 전환과 같은 필요한 보호 동작을 수행한다. Record 단계에서는 안전 대응을 지연시키지 않는 범위에서 원인, 타임스탬프(Timestamp), 운전 모드(Operating Mode), 관련 측정값을 저장한다. Notify 단계에서는 통신이 가능한 경우 상위 수준 소프트웨어나 운영자에게 상태를 전달한다. 안전 동작은 항상 로깅과 알림보다 우선되어야 한다.

복구(Recovery)는 일반적인 제어 재개보다 더 엄격해야 한다. 오류가 발생한 후에는 먼저 원인이 사라졌는지 확인하고 관련 안전 입력, 센서, 액추에이터, 통신 경로, 운전 조건이 모두 정상인지 검증해야 한다. 오류 플래그를 제거했다고 해서 모터가 자동으로 다시 시작되어서는 안 된다. 아키텍처에 따라 복구에는 명시적인 확인, 제어된 재초기화(Controlled Reinitialization), 새로운 활성화 조건(Enable Condition), 명시적인 재시작 명령(Restart Command)이 필요할 수 있다. 보호 정지 후 대기 중이던 모션 명령이 자동으로 재개되어서는 안 된다. 그렇게 하면 원래의 위험한 조건이 다시 발생할 수 있기 때문이다.

검증 및 진단(Verification and Diagnostics)은 모든 중요한 오류 조건이 요구된 시간 안에 의도된 대응으로 이어지는지를 입증해야 한다. 오류 주입(Fault Injection)을 사용하여 센서 고장, 통신 손실, 워치독 이벤트, 엔코더 오류, 과전류 조건, 명령 편차, 실행 지연 등을 시뮬레이션할 수 있다. 측정해야 할 대응 특성에는 감지 지연시간(Detection Latency), 대응 지연시간(Reaction Latency), 액추에이터 정지 동작, 안전 상태 확인, 재시작 차단(Restart Inhibition)이 포함된다. 이벤트 로그와 오류 카운터(Fault Counter)는 실시간 보호 경로를 방해하지 않는 범위에서 근본 원인 분석(Root-Cause Analysis)을 지원해야 한다. 최종적인 목표는 제어기가 단순히 오류를 감지하는 것을 넘어, 오류를 안전하고 검증 가능한 물리적 동작으로 안정적으로 변환한다는 것을 입증하는 것이다.

## 11.07 Robot Joint Servo Controller MCU RTOS Case [w/Code]

![](images/image9.png){width="7.268055555555556in" height="7.268055555555556in"}

MCU와 RTOS를 기반으로 하는 로봇 관절 서보 제어기(Robot Joint Servo Controller)는 상위 수준의 로봇 모션 명령과 물리적인 모터 사이에서 결정론적인 액추에이터 계층(Deterministic Actuator Layer)을 제공한다. 제어기는 상위 수준 제어기(Upper-Level Controller)로부터 위치, 속도 또는 토크 기준값을 수신하고 관절에 필요한 더 빠른 피드백 루프(Feedback Loop)를 실행한다. 주요 기능에는 센서 획득(Sensor Acquisition), 전류 조절(Current Regulation), 속도 및 위치 제어, PWM 생성(PWM Generation), 통신, 진단(Diagnostics), 보호(Protection)가 포함된다. RTOS는 모터 제어 경로에 필요한 엄격한 타이밍을 유지하면서 이러한 기능을 조정한다.

MCU는 충분한 연산 성능뿐만 아니라 실시간 모터 제어 주변장치(Real-Time Motor-Control Peripheral)를 지원할 수 있도록 선택해야 한다. 일반적인 하드웨어 자원에는 고해상도 타이머(High-Resolution Timer), PWM 생성기(PWM Generator), 동기화된 ADC 채널(Synchronized ADC Channel), 엔코더 인터페이스(Encoder Interface), 통신 주변장치(Communication Peripheral), DMA, 워치독(Watchdog), 하드웨어 고장 입력(Hardware Fault Input)이 포함된다. 부동소수점(Floating-Point) 또는 DSP 기능은 FOC 연산과 필터링을 가속할 수 있다. 목표는 단순히 높은 프로세서 속도를 확보하는 것이 아니라 센싱, 연산, 통신, 액추에이터 갱신을 예측 가능한 타이밍으로 동기화할 수 있는 하드웨어 아키텍처를 구성하는 것이다.

가장 빠른 제어 계층은 일반적으로 필드 지향 제어(Field Oriented Control, FOC)를 사용하여 구현되는 전류 제어 루프(Current-Control Loop)이다. PWM 타이머 이벤트(PWM Timer Event)가 주요 타이밍 기준을 제공하며, ADC 샘플링은 인버터 스위칭 파형(Inverter Switching Waveform)과 동기화된다. 제어기는 상전류(Phase Current)를 측정하고 전기적 회전자 각도(Electrical Rotor Angle)를 결정하며 Clarke 및 Park 변환(Clarke and Park Transformation)을 수행한 후 d축과 q축 전류를 조절하고 전압 명령(Voltage Command)을 계산한다. 이후 역변환(Inverse Transformation)과 PWM 변조(PWM Modulation)를 통해 인버터에 적용되는 듀티 사이클(Duty Cycle)을 생성한다. 이 전체 과정은 엄격하게 제한된 제어 주기 안에서 완료되어야 한다.

속도 루프(Speed Loop)와 위치 루프(Position Loop)는 전류 루프보다 느린 주기로 상위에서 동작한다. 위치 제어기는 명령된 관절 위치와 측정된 관절 위치를 비교하여 속도 기준값(Velocity Reference)을 생성하고, 속도 제어기는 속도 오차를 토크 또는 전류 기준값(Torque or Current Reference)으로 변환한다. 이후 전류 루프가 해당 명령을 실현하기 위해 필요한 전기적 구동력(Electrical Effort)을 생성한다. 이러한 중첩된 다중 주기 아키텍처(Nested Multi-Rate Architecture)는 관절의 서로 다른 기계적 및 전기적 동역학을 반영하며, 각 제어 기능을 필요한 대역폭에 맞는 주기로만 실행하여 불필요한 프로세서 부하를 방지한다.

RTOS 스케줄러(RTOS Scheduler)는 이러한 제어 계층을 명확한 우선순위 계층(Priority Hierarchy)에 매핑한다. 고주파 전류 제어 기능에는 가장 높은 제어 우선순위가 부여되고, 그 다음으로 속도 및 위치 제어가 배치된다. 통신, 상태 관리(State Management), 진단, 로깅(Logging), 백그라운드 기능은 더 낮은 우선순위에서 실행된다. 일부 구현에서는 전류 루프를 타이머 인터럽트(Timer Interrupt)에서 직접 실행하고, 다른 구현에서는 짧은 인터럽트 서비스 루틴(Interrupt Service Routine, ISR)이 높은 우선순위의 제어 태스크(Control Task)를 활성화하도록 구성한다. 어느 방식이든 평균 실행 성능보다 제한된 지연시간(Bounded Latency)과 낮은 지터(Low Jitter)가 더욱 중요하다.

대표적인 실행 주기(Execution Cycle)는 PWM 타이머가 동기화된 ADC 획득을 트리거하면서 시작된다. 필요한 전류 샘플을 사용할 수 있게 되면 MCU는 전류 제어 알고리즘을 실행하고 새롭게 계산된 듀티 사이클을 PWM 섀도 레지스터(PWM Shadow Register)에 기록한다. 이 값은 정의된 타이머 갱신 경계(Timer Update Boundary)에서 활성화되어 PWM 주기 중간에 값이 변경되는 것을 방지한다. 동시에 더 느린 RTOS 태스크는 각각의 주기에 따라 속도 제어, 위치 제어, 통신, 진단, 상태 감시(State Supervision)를 실행한다. 따라서 하드웨어 타이밍과 RTOS 스케줄링은 액추에이터의 시간 흐름을 서로 경쟁적으로 제어하는 것이 아니라 상호 협력하여 관리한다.

관절 위치 피드백(Joint Position Feedback)은 증분형 엔코더(Incremental Encoder), 절대형 엔코더(Absolute Encoder), 리졸버(Resolver), 홀 센서(Hall Sensor) 또는 기타 적절한 센싱 장치를 통해 획득할 수 있다. 제어기는 원시 측정값(Raw Measurement)을 위치와 속도 정보로 변환하고 피드백 루프에서 사용하기 전에 유효성 검사(Validity Check)를 수행한다. 신호 필터링(Signal Filtering)은 잡음 감소와 추가적인 위상 지연(Phase Delay) 사이의 균형을 고려해야 한다. 서보 성능은 피드백 품질에 크게 의존하므로 센서 획득, 타임스탬핑(Timestamping), 각도 처리(Angle Processing), 제어 연산 사이에는 알려진 시간적 관계가 유지되어야 하며, 이를 서로 독립적인 소프트웨어 동작으로 취급해서는 안 된다.

통신(Communication)은 관절 제어기를 로봇의 상위 제어 계층과 연결한다. CAN, CANopen, EtherCAT 또는 기타 결정론적인 인터페이스(Deterministic Interface)를 통해 목표 위치, 속도, 토크, 운전 모드(Operating Mode), 상태(Status), 진단 정보를 전달할 수 있다. 통신 태스크는 수신된 명령의 유효성을 검사하고 메시지 타임아웃(Message Timeout)을 감시하여 오래된 명령(Stale Command)이 무기한 실행되지 않도록 해야 한다. 네트워크 처리는 가장 빠른 모터 제어 경로와 분리되어야 하며, 이를 통해 일시적인 통신 부하나 상위 수준 연산의 변동이 전류 루프 타이밍을 직접적으로 방해하지 않도록 해야 한다.

서보 펌웨어(Servo Firmware)는 초기화(Initialization), 준비(Ready), 활성화(Enabled), 실행(Running), 정지(Stopping), 고장(Fault), 복구(Recovery)와 같은 명시적인 상태 머신(State Machine)을 중심으로 구성할 수 있다. 상태 전이는 PWM 출력을 언제 활성화할 수 있는지, 제어기의 적분항(Control Integrator)을 언제 초기화하는지, 기준값이 언제 유효해지는지, 모터를 언제 비활성화해야 하는지를 정의한다. 이를 통해 제어 동작이 여러 태스크에 분산된 조건문에 의존하는 것을 방지할 수 있다. RTOS 태스크, 인터럽트 핸들러, 통신 인터페이스, 보호 기능은 모두 동일하게 관리되는 운전 상태(Operating State)를 참조할 수 있다.

보호(Protection)는 추가적인 진단 기능이 아니라 관절 제어기의 핵심적인 구성 요소이다. MCU는 상전류, DC 버스 전압(DC-Bus Voltage), 온도, 엔코더 유효성(Encoder Validity), 명령 추종(Command Tracking), 통신 상태(Communication Health), 워치독 상태, 인버터 고장(Inverter Fault)을 감시해야 한다. 빠른 전기적 고장은 정상적인 소프트웨어 실행을 기다리지 않고 하드웨어 브레이크(Hardware Break) 또는 전력단 차단(Power-Stage Inhibit) 메커니즘을 활성화할 수 있다. 이후 펌웨어가 이벤트를 기록하고, 고장을 분류하며, 필요한 상태 전이를 수행하고, 가능한 경우 해당 상태를 상위 제어기에 전달한다.

안전 정지 동작(Safe-Stop Behavior)은 로봇 관절의 기계적 기능을 고려해야 한다. 일부 전기적 고장에서는 즉각적인 토크 제거가 적절할 수 있지만, 페이로드를 지지하는 중력 부하 매니퓰레이터 관절(Gravity-Loaded Manipulator Joint)에서는 위험할 수 있다. 다른 고장에서는 드라이브를 비활성화하거나 기계식 브레이크(Mechanical Brake)를 적용하기 전에 제어된 감속(Controlled Deceleration)을 수행할 수 있다. 따라서 제어기는 제어 정지(Controlled Stop), 보호 정지(Protective Stop), 즉각적인 하드웨어 종료(Immediate Hardware Shutdown)를 구분해야 한다. 시스템이 정지 시퀀스의 완료를 보고하기 전에 피드백을 통해 관절이 의도된 안전 상태에 도달했는지를 확인해야 한다.

MCU의 메모리와 연산 능력에는 제한이 있으므로 자원 관리(Resource Management)가 중요하다. 중요한 RTOS 객체에는 정적 할당(Static Allocation)을 우선적으로 사용할 수 있으며, 결정론적인 제어 경로에서는 동적 할당(Dynamic Allocation)을 최소화해야 한다. 인터럽트와 태스크 사이에서 공유되는 데이터에는 실행 시간이 제한된 동기화(Bounded Synchronization)가 필요하며, 대용량 로깅 또는 통신 버퍼가 제어 기능에 필요한 메모리를 소비해서는 안 된다. DMA를 사용하면 ADC 또는 통신 전송에 필요한 CPU 오버헤드를 줄일 수 있지만, 최악 실행 동작(Worst-Case Execution Behavior)을 결정할 때 DMA의 타이밍과 공유 메모리 상호작용도 함께 고려해야 한다.

검증(Verification)은 제어기를 하나의 완전한 실시간 액추에이터 시스템(Real-Time Actuator System)으로 측정해야 한다. 주요 측정 항목에는 전류 루프 주기(Current-Loop Period), ISR 지연시간(ISR Latency), 실행 시간(Execution Time), 스케줄링 지터(Scheduling Jitter), PWM 갱신 타이밍(PWM Update Timing), 통신 지연시간(Communication Latency), 위치 오차(Position Error), 속도 추종(Velocity Tracking), 전류 추종(Current Tracking), 고장 대응 시간(Fault-Response Time)이 포함된다. 테스트는 높은 통신 및 CPU 부하와 급격한 모션 변화 조건에서도 반복해야 한다. GPIO 계측(GPIO Instrumentation), 타이머 캡처(Timer Capture), RTOS 트레이싱(RTOS Tracing), 드라이브 로그(Drive Log), 오실로스코프 측정(Oscilloscope Measurement)을 통해 실제 하드웨어가 의도된 타이밍 아키텍처를 따르는지를 입증할 수 있다.

이러한 MCU-RTOS 서보 아키텍처(MCU-RTOS Servo Architecture)는 로봇 지능(Robot Intelligence)과 결정론적인 관절 실행(Deterministic Joint Execution) 사이에 명확한 경계를 형성한다. Linux, 엣지 컴퓨터(Edge Computer), 모션 제어 컴퓨터(Motion-Control Computer)는 궤적(Trajectory)과 협조 다축 목표(Coordinated Multi-Axis Objective)를 생성할 수 있으며, 각각의 관절 제어기는 자체적으로 빠른 전기적 및 기계적 피드백 루프를 유지한다. 상위 수준 소프트웨어에서 가변적인 지연시간이 발생하더라도 로컬 제어기는 정밀한 타이밍, 액추에이터 보호, 즉각적인 고장 대응을 담당한다. 그 결과 상위 수준 연산은 모션 의도(Motion Intent)를 정의하고 MCU 기반 실시간 서보 제어기가 이를 안정적이고 정밀하며 안전한 물리적 관절 동작으로 변환하는 확장 가능한 분산 아키텍처(Scalable Distributed Architecture)가 형성된다.

## 11.08 BLDC / PMSM / Linear Motor RTOS Control Comparison

![](images/image10.png){width="7.268055555555556in" height="7.268055555555556in"}

BLDC, PMSM, 선형 모터(Linear Motor)는 모두 RTOS 기반 로봇 제어 시스템에 통합할 수 있지만, 각각의 전기적 정류(Electrical Commutation), 피드백 요구사항(Feedback Requirements), 제어 알고리즘(Control Algorithm), 기계적 출력(Mechanical Output)의 차이로 인해 서로 다른 실시간 설계 우선순위를 갖는다. RTOS 자체가 모터의 전자기적 동작(Electromagnetic Behavior)을 근본적으로 변화시키는 것은 아니다. 대신 센싱(Sensing), 제어 연산(Control Computation), PWM 생성(PWM Generation), 통신, 진단(Diagnostics), 보호(Protection)를 결정론적으로 실행할 수 있는 환경을 제공한다. 따라서 적절한 아키텍처는 각 모터가 전기적 명령을 물리적 운동으로 변환하는 방식과 그 과정이 얼마나 정밀하게 제어되어야 하는지에 따라 결정된다.

브러시리스 DC 모터(Brushless DC Motor, BLDC)는 일반적으로 3상 인버터(Three-Phase Inverter)를 통해 전자 정류(Electronic Commutation) 방식으로 구동된다. 비교적 단순한 구현에서는 홀 센서(Hall Sensor) 또는 추정된 회전자 위치(Rotor Position) 정보를 기반으로 6단계 정류(Six-Step Commutation) 또는 사다리꼴 정류(Trapezoidal Commutation)를 사용한다. 제어기는 활성화할 상 조합(Phase Combination)을 결정하고 적절한 데드 타임(Dead Time)을 포함한 상보형 PWM 신호(Complementary PWM Signal)를 생성한다. 정류 이벤트는 올바른 회전자 위치에서 발생해야 하므로 MCU는 위치 정보를 처리하고 제한된 지연시간(Bounded Latency) 안에서 인버터 상태를 갱신해야 한다. RTOS는 빠른 정류 동작을 느린 통신 및 진단 기능과 분리하면서 이러한 동작을 조정할 수 있다.

BLDC 제어에서도 더 부드러운 토크와 낮은 음향 또는 기계적 리플(Ripple)이 필요한 경우 정현파 제어(Sinusoidal Control) 또는 필드 지향 제어(Field Oriented Control, FOC)를 사용할 수 있다. 이 경우 소프트웨어 아키텍처는 점차 PMSM 제어와 유사해진다. 상전류 획득(Phase-Current Acquisition), 전기적 각도 추정(Electrical-Angle Estimation), Clarke 및 Park 변환(Clarke and Park Transformation), 전류 조절(Current Regulation), 역변환(Inverse Transformation), PWM 변조(PWM Modulation)가 모두 고주파 제어 루프 안에서 실행될 수 있다. 따라서 BLDC와 PMSM 제어의 차이는 모터 구조뿐만 아니라 선택된 정류 및 제어 전략에도 영향을 받는다.

영구자석 동기 모터(Permanent Magnet Synchronous Motor, PMSM)는 일반적으로 정현파 역기전력(Sinusoidal Back-EMF)과 고성능 FOC를 사용하는 방식과 연관된다. RTOS 구현에서는 상전류 샘플링(Phase-Current Sampling), 회전자 각도 획득(Rotor-Angle Acquisition), 전류 제어 연산(Current-Control Computation), PWM 갱신 사이의 정밀한 동기화를 유지해야 한다. d축 및 q축 전류는 자기 자속(Magnetic Flux)과 토크를 제어하기 위해 독립적으로 조절된다. 이러한 방식은 기본적인 6단계 BLDC 제어보다 많은 수학적 연산을 필요로 하지만, DSP 또는 부동소수점(Floating-Point)을 지원하는 최신 MCU에서는 결정론적인 인터럽트 기반 제어 주기 안에서 알고리즘을 효율적으로 실행할 수 있다.

PMSM 제어는 부드러운 토크, 낮은 리플, 높은 효율, 정확한 동적 응답(Dynamic Response)이 요구되는 로봇 관절 및 서보 액추에이터(Servo Actuator)에 특히 적합하다. 일반적인 중첩 아키텍처(Nested Architecture)에서는 FOC 전류 루프(Current Loop)가 가장 높은 주파수에서 동작하고, 속도 루프(Velocity Loop)는 중간 주파수에서, 위치 루프(Position Loop)는 더 낮은 주파수에서 동작한다. 하드웨어 타이머(Hardware Timer)는 ADC 샘플링과 PWM 생성을 동기화하며, RTOS 우선순위는 통신, 로깅(Logging), 파라미터 관리(Parameter Management), 기타 느린 소프트웨어 작업으로부터 전류 제어 경로를 보호한다. 이러한 구조는 로봇 관절 제어기에 사용되는 계층적 서보 제어 구조(Hierarchical Servo-Control Structure)와 밀접하게 대응한다.

선형 모터(Linear Motor)는 전자기력이 회전 운동이 아니라 직접적인 직선 운동(Translational Motion)을 생성한다는 점에서 기계적으로 차이가 있다. 그러나 기본적인 전자기 제어 원리는 특히 영구자석 동기 선형 모터(Permanent-Magnet Synchronous Linear Motor)의 경우 회전형 PMSM과 유사할 수 있다. 회전 토크와 각도 위치를 제어하는 대신 선형 힘(Linear Force), 속도, 위치를 제어한다. 따라서 피드백 장치로 회전형 엔코더 대신 선형 엔코더(Linear Encoder)를 사용할 수 있지만, 인버터, 상전류 센싱, FOC 연산, PWM 생성은 개념적으로 유사한 구조를 유지할 수 있다.

선형 모터의 직접 구동 특성(Direct-Drive Characteristic)은 기계적 제어 문제를 크게 변화시킨다. 회전 모터는 일반적으로 기어박스(Gearbox) 또는 감속기(Reducer)를 통해 관절을 구동하는 반면, 선형 모터는 이동 스테이지(Moving Stage)에 직접 힘을 가할 수 있다. 기계적 백래시(Backlash)와 전달계 컴플라이언스(Transmission Compliance)는 감소할 수 있지만 외란(Disturbance)과 제어 오차가 부하에 더욱 직접적으로 작용할 수 있다. 따라서 고해상도 위치 피드백(High-Resolution Position Feedback), 정확한 힘 조절(Force Regulation), 낮은 제어 루프 지터(Control-Loop Jitter)가 특히 중요해진다. RTOS는 직접 구동의 장점이 소프트웨어 타이밍 변동에 의해 저하되지 않도록 결정론적인 센서-액추에이터 타이밍을 유지해야 한다.

실시간 스케줄링(Real-Time Scheduling)의 관점에서 세 가지 모터 유형 모두 다중 주기 제어 아키텍처(Multi-Rate Control Architecture)의 이점을 얻을 수 있다. 가장 빠른 전기적 루프(Electrical Loop)에 가장 높은 우선순위를 부여하고, 그 다음으로 속도 및 위치 루프를 배치하며, 통신과 진단은 더 낮은 우선순위에서 실행해야 한다. 그러나 가장 빠른 루프의 연산 요구량은 제어 전략에 따라 달라진다. 6단계 BLDC 제어기는 비교적 단순한 정류 및 PWM 연산만 필요할 수 있지만, FOC 기반 BLDC, PMSM, 동기식 선형 모터(Synchronous Linear Motor) 제어기는 좌표 변환, PI 전류 제어기(PI Current Regulator), 변조(Modulation), 정밀한 전기적 각도 처리를 필요로 한다.

센서 요구사항(Sensor Requirements) 역시 RTOS 아키텍처에 영향을 준다. 홀 센서를 사용하는 BLDC 시스템은 상대적으로 낮은 해상도의 회전자 위치 정보로 동작할 수 있는 반면, 고성능 PMSM 시스템은 일반적으로 엔코더(Encoder), 리졸버(Resolver), 또는 고급 센서리스 추정기(Advanced Sensorless Estimator)를 사용한다. 선형 서보 시스템은 정밀한 직선 위치 제어를 위해 고해상도 선형 엔코더가 필요할 수 있다. 센서 유형과 관계없이 데이터 획득은 해당 제어 주기와 타임스탬프(Timestamp) 또는 동기화되어야 한다. 또한 손상된 피드백이 액추에이터 명령으로 전달되기 전에 잘못된 측정값, 비정상적인 상태 전이, 신호 손실, 오래된 데이터(Stale Data)를 감지해야 한다.

PWM 생성은 세 가지 모터에서 공통적으로 사용되는 결정론적인 기반이다. BLDC 6단계 제어에서는 정확한 상 정류(Phase Commutation)와 상보형 스위칭(Complementary Switching)이 필요하며, FOC 기반 시스템에서는 전압 벡터 명령(Voltage-Vector Command)에서 계산된 PWM 듀티 사이클을 동기화하여 갱신해야 한다. 상보형 출력(Complementary Output), 데드 타임 삽입(Dead-Time Insertion), 섀도 레지스터(Shadow Register), 동기화된 ADC 트리거(Synchronized ADC Trigger), 하드웨어 브레이크 입력(Hardware Break Input)과 같은 고급 타이머 기능은 가변적인 소프트웨어 타이밍에 대한 의존성을 줄인다. 이러한 하드웨어 기능을 통해 RTOS와 MCU 주변장치가 협력하여 정밀한 샘플-연산-갱신(Sample-Compute-Update) 동작과 즉각적인 보호를 유지할 수 있다.

모든 모터 유형에는 고장 처리(Fault Handling)가 필요하지만 기계적 결과는 서로 다를 수 있다. 과전류(Overcurrent), 인버터 고장(Inverter Fault), 비정상적인 DC 버스 전압(DC-Bus Voltage), 과도한 온도, 센서 고장, 통신 타임아웃(Communication Timeout), 제어 루프 타이밍 위반(Control-Loop Timing Violation)을 감시해야 한다. 하드웨어 보호는 위험한 전기적 상태에 즉시 대응해야 하며, 펌웨어는 고장을 분류하고 제어 정지(Controlled Stop), 상태 전이(State Transition), 로깅, 복구(Recovery)를 관리한다. 선형 축(Linear Axis)에서는 제동이나 제어된 힘 감소가 필요할 수 있으며, 로봇 관절에서는 제어되지 않은 부하 움직임을 방지하기 위해 토크 관리 또는 기계식 브레이크(Mechanical Brake)가 필요할 수 있다.

통신 요구사항(Communication Requirements)은 모터 기술 자체보다는 로봇 아키텍처에 의해 크게 결정된다. CAN, CANopen, EtherCAT 또는 기타 결정론적인 인터페이스를 통해 위치, 속도, 토크 또는 힘 기준값(Force Reference)을 로컬 제어기로 전달할 수 있다. MCU는 상위 수준 명령의 타이밍에 일반적인 변동이 발생하더라도 자체적인 빠른 로컬 제어 루프를 유지해야 한다. 이를 통해 BLDC 휠 드라이브(Wheel Drive), PMSM 로봇 관절, 선형 위치 결정 축(Linear Positioning Axis)은 내비게이션(Navigation), 궤적 생성(Trajectory Generation), 다축 협조 제어(Multi-Axis Coordination), AI 연산이 훨씬 느린 주기로 실행되는 상황에서도 전기적 안정성을 유지할 수 있다.

따라서 이러한 비교는 RTOS 설계가 단순히 모터의 명칭에 따라 결정되는 것이 아니라 제어 방식(Control Method)과 애플리케이션 동역학(Application Dynamics)을 따라야 한다는 것을 보여준다. 기본적인 BLDC 정류는 비교적 가벼운 실시간 제어 경로를 사용할 수 있지만, 고성능 BLDC와 PMSM 드라이브는 일반적으로 FOC 기반 아키텍처로 수렴한다. 선형 모터는 동일한 전기 제어 구조의 상당 부분을 재사용할 수 있지만 직선 운동 피드백과 직접적인 힘 중심의 기계 제어가 필요하다. 모든 경우에서 결정론적인 타이밍, 동기화된 센싱과 PWM, 제한된 지연시간, 우선순위 격리(Priority Isolation), 고장 보호, 타이밍 검증(Timing Verification)은 공통적인 RTOS 기반을 형성한다.

따라서 확장 가능한 로봇 플랫폼(Scalable Robotic Platform)은 여러 액추에이터 기술에서 공통 MCU-RTOS 소프트웨어 아키텍처를 사용하면서 모터별 센싱, 정류, 파라미터화(Parameterization), 기계 제어 구성요소만 교체할 수 있다. 공통 서비스에는 스케줄링(Scheduling), 통신, 상태 관리(State Management), 진단, 워치독 감시(Watchdog Supervision), PWM 인프라(PWM Infrastructure), 안전 처리(Safety Handling)가 포함될 수 있다. 이후 모터별 모듈이 BLDC 정류, PMSM FOC 또는 선형 힘 및 위치 제어를 구현한다. 이러한 분리는 아키텍처의 파편화(Architectural Fragmentation)를 줄이면서 각 액추에이터에 필요한 특화된 실시간 동작을 유지하여, 하나의 결정론적인 제어 프레임워크가 휠, 로봇 관절, 정밀 선형 메커니즘을 모두 지원할 수 있도록 한다.

## 11.09 Control Loop Jitter Impact Analysis and Improvement

![](images/image11.png){width="7.268055555555556in" height="7.268055555555556in"}

제어 루프 지터(Control-Loop Jitter)는 실제 제어 주기의 타이밍이 의도된 주기적 스케줄을 중심으로 변동하는 현상이다. 모터 제어 루프가 고정된 주기 T를 기준으로 설계되었다면 이상적인 실행 시점은 정확히 T의 정수배에서 발생하지만, 실제 실행은 약간 빠르거나 늦게 시작되거나 완료될 수 있다. 이러한 편차는 실질적인 샘플링 및 액추에이션 간격(Sampling and Actuation Interval)을 변화시킨다. 따라서 RTOS 모터 제어기에서 지터는 단순한 스케줄링 통계가 아니라 피드백 제어 알고리즘(Feedback-Control Algorithm)이 사용하는 시간적 가정에 직접적인 영향을 미치는 요소이다.

지터는 여러 가지 타이밍 관점에서 설명할 수 있다. 주기 지터(Period Jitter)는 연속된 제어 주기 사이의 간격 변화를 의미하며, 릴리스 또는 위상 지터(Release or Phase Jitter)는 이상적인 예정 활성화 시점으로부터의 편차를 나타낸다. 실행 시간 변동(Execution-Time Variation)과 액추에이터 갱신 지터(Actuator-Update Jitter) 역시 물리적 플랜트(Physical Plant)에서 관찰되는 최종적인 타이밍 불확실성에 영향을 줄 수 있다. 평균 루프 주파수만 측정하면 이러한 영향을 발견하지 못할 수 있다. 제어기의 평균 주파수가 정확히 1 kHz이더라도 개별 주기는 공칭 1 ms 주기를 중심으로 상당히 변동할 수 있다.

지터의 영향은 특히 고대역폭 전류 및 서보 루프(High-Bandwidth Current and Servo Loop)에서 중요해진다. 제어기 파라미터는 일반적으로 일정한 샘플링 주기(Sampling Period)를 가정하여 설계되기 때문이다. 실제 주기가 변동하면 센서 측정값이 일정하지 않은 시점에서 처리되고 제어 출력도 가변적인 지연시간으로 적용된다. 이는 폐루프(Closed Loop)에 실질적인 위상 변동(Phase Variation)을 발생시킨다. 작은 지터는 눈에 띄는 성능 저하를 일으키지 않을 수 있지만 변동이 증가하면 안정성 여유(Stability Margin)가 감소하고 추종 오차가 증가하며 토크 또는 속도 리플이 발생하고 과도 응답(Transient Behavior)의 반복성이 저하될 수 있다.

필드 지향 제어(Field Oriented Control, FOC)에서 타이밍 변동은 전류 샘플링(Current Sampling), 회전자 각도 정보(Rotor-Angle Information), 좌표 변환(Coordinate Transformation), 제어 연산(Control Computation), PWM 갱신 사이의 관계에 영향을 준다. 상전류 값은 알려진 전기적 각도(Electrical Angle)와 예측 가능한 PWM 위치에 대응해야 한다. 연산 또는 갱신 타이밍이 변동하면 계산된 전압 벡터(Voltage Vector)가 예상보다 늦게 적용될 수 있다. 이러한 현상은 수학적인 FOC 방정식 자체가 정확하더라도 추가적인 위상 지연(Phase Delay), 전류 왜곡(Current Distortion), 토크 리플(Torque Ripple), 고속 영역 성능 저하로 나타날 수 있다.

지터는 실시간 실행 체인(Real-Time Execution Chain)의 다양한 부분에서 발생할 수 있다. 인터럽트 버스트(Interrupt Burst)는 태스크 활성화를 지연시킬 수 있고, 경쟁 태스크(Competing Task)는 스케줄러 간섭(Scheduler Interference)을 발생시킬 수 있으며, 긴 인터럽트 서비스 루틴(Interrupt Service Routine, ISR)은 제어 처리를 지연시킬 수 있다. 또한 동기화 프리미티브(Synchronization Primitive)는 블로킹(Blocking)을 발생시킬 수 있다. 메모리 이벤트, 캐시 효과(Cache Effect), DMA 경합(DMA Contention), 디바이스 드라이버(Device Driver), 네트워크 활동, 백그라운드 처리 역시 간헐적인 타이밍 편차를 발생시킬 수 있다. 따라서 지터 분석은 모든 타이밍 변동의 원인을 제어 알고리즘 자체로 가정하지 않고 전체 시스템을 대상으로 수행해야 한다.

과도한 지터가 발생할 경우 가장 먼저 검토해야 하는 영역 중 하나가 우선순위 구성(Priority Configuration)이다. 가장 빠르고 시간적으로 중요한 모터 제어 기능에는 일반적으로 속도 제어, 위치 제어, 통신, 진단, 로깅보다 높은 우선순위를 부여해야 한다. 그러나 높은 우선순위를 설정하는 것만으로 결정론적인 실행이 보장되는 것은 아니다. 인터럽트 우선순위(Interrupt Priority), 임계 구역(Critical Section), 우선순위 역전(Priority Inversion), 블로킹 동기화(Blocking Synchronization), 커널 활동(Kernel Activity)이 여전히 제어 경로를 지연시킬 수 있다. 따라서 우선순위 설계는 전체 스케줄링 및 인터럽트 아키텍처와 함께 평가해야 한다.

하드웨어 트리거 실행(Hardware-Triggered Execution)은 가변적인 소프트웨어 스케줄링에 대한 의존성을 크게 줄일 수 있다. PWM 타이머는 기본적인 제어 시간 기준(Control Time Base)을 설정하고 정의된 시점에서 ADC 샘플링을 트리거하며 제어 연산을 시작하는 인터럽트를 생성할 수 있다. 이후 PWM 섀도 레지스터(PWM Shadow Register)는 새로운 듀티 값을 알려진 갱신 경계(Update Boundary)에서 적용할 수 있다. 이를 통해 동기화된 샘플-연산-갱신 파이프라인(Sample-Compute-Update Pipeline)이 형성된다. 하드웨어 타이밍이 연산 시간의 변동을 완전히 제거하지는 못하지만 안정적인 기준 시점을 제공하고 소프트웨어 기반 주기 지연으로 인해 발생하는 타이밍 불확실성을 줄일 수 있다.

제어 경로에서는 예측하기 어려운 실행 시간을 갖는 작업도 최소화해야 한다. 동적 메모리 할당(Dynamic Memory Allocation), 블로킹 입출력(Blocking I/O), 콘솔 출력(Console Output), 파일 연산(File Operation), 대규모 로깅 활동, 불필요한 뮤텍스 획득(Mutex Acquisition)은 고주파 루프 외부에 배치해야 한다. 버퍼와 RTOS 객체는 사전 할당(Preallocation)할 수 있으며, 중요한 데이터 교환에는 필요한 경우 실행 시간이 제한된 방식 또는 락프리 메커니즘(Lock-Free Mechanism)을 사용할 수 있다. 인터럽트 핸들러는 짧게 유지하고 중요하지 않은 작업은 낮은 우선순위의 태스크로 연기해야 한다. 목표는 일반적인 타이밍 변동뿐만 아니라 드물게 발생하는 롱테일 지연시간(Long-Tail Latency) 이벤트도 줄이는 것이다.

멀티코어 프로세서(Multicore Processor)에서는 CPU 배치(CPU Placement)와 인터럽트 라우팅(Interrupt Routing)을 추가적인 개선 방법으로 활용할 수 있다. 실시간 제어 스레드(Real-Time Control Thread)를 전용 또는 격리된 CPU에 할당하고 중요하지 않은 프로세스는 다른 CPU에서 실행할 수 있다. IRQ 어피니티(IRQ Affinity)를 설정하면 관련 없는 디바이스 인터럽트가 제어 코어를 반복적으로 방해하는 것을 방지할 수 있다. CPU 이동(CPU Migration)이 캐시 또는 스케줄러 간섭을 발생시키는 경우 이를 최소화해야 한다. 또한 주파수 스케일링(Frequency Scaling)과 전력 관리(Power Management)가 타이밍 변동을 발생시키는 경우 해당 동작도 제어해야 한다. 최적화에서는 프로세서, 스케줄러, 인터럽트, 메모리, 입출력을 하나의 타이밍 시스템(Timing System)으로 고려해야 한다.

지터 측정(Jitter Measurement)은 태스크 설정으로부터 추정된 가정이 아니라 실제 타임스탬프(Actual Timestamp)를 기반으로 수행해야 한다. 하드웨어 타이머(Hardware Timer), 타이머 캡처 주변장치(Timer Capture Peripheral), GPIO 토글과 오실로스코프 또는 로직 분석기(Logic Analyzer), 저오버헤드 트레이싱(Low-Overhead Tracing)을 이용하면 제어 이벤트가 실제로 언제 발생하는지를 확인할 수 있다. 유용한 측정 지점에는 태스크 릴리스(Task Release), 실행 시작, 연산 완료, 통신 이벤트, 액추에이터 출력이 포함된다. 하드웨어 타임스탬핑(Hardware Timestamping)은 소프트웨어 로깅 오버헤드의 영향을 적게 받으면서 하드웨어 경계에 가까운 위치에서 타임스탬프를 획득할 수 있기 때문에 특히 유용하다.

유용한 분석에서는 최소값, 최대값, 평균값만 보고하는 것이 아니라 지터의 분포(Distribution)를 함께 조사해야 한다. 표준편차(Standard Deviation), RMS 변동(RMS Variation), 피크 대 피크 지터(Peak-to-Peak Jitter), 99% 및 99.9%와 같은 백분위수(Percentile), 최악 편차(Worst-Case Deviation)는 각각 시간적 동작의 서로 다른 특성을 보여준다. 히스토그램(Histogram)과 누적 분포 함수(Cumulative Distribution Function, CDF)를 사용하면 대부분의 주기는 좁은 범위에 집중되어 있지만 드문 이벤트가 큰 지연을 발생시키는지를 확인할 수 있다. 이러한 드문 이벤트는 평균 성능이 우수하더라도 단 한 번의 극단적인 주기가 데드라인을 위반할 수 있기 때문에 실시간 검증에서 특히 중요하다.

따라서 장시간 테스트(Long-Duration Testing)가 필수적이다. 인터럽트 버스트, 스케줄러 상호작용(Scheduler Interaction), 메모리 이벤트, 통신 활동, 드라이버 동작은 낮은 빈도로 발생할 수 있으므로 짧은 측정에서는 실제보다 지나치게 낙관적인 결과가 나타날 수 있다. 센서, 네트워킹, 저장장치, 진단, 기타 로봇 기능이 활성화된 상태에서 유휴(Idle), 정상(Nominal), 현실적인 최대 부하(Realistic Peak Workload) 조건을 대상으로 제어기를 테스트해야 한다. 측정 주기 수를 증가시키면 드문 타이밍 편차를 포착할 확률이 높아지고 사용 가능한 실시간 여유(Real-Time Margin)에 대한 더욱 강력한 근거를 확보할 수 있다.

트레이싱(Tracing)은 측정된 타이밍 이상 현상과 그 소프트웨어 원인을 연결하는 데 도움을 준다. RTOS 트레이싱 또는 ftrace, perf, LTTng와 같은 Linux 도구를 사용하면 지터 이벤트가 발생한 시점 주변의 스케줄러 활동, 인터럽트, 커널 작업, 동기화, 경쟁 스레드(Competing Thread)를 확인할 수 있다. 하드웨어 타임스탬프는 물리적인 타이밍 편차가 언제 발생했는지를 알려주고, 소프트웨어 트레이싱은 그 현상이 왜 발생했는지를 설명하는 데 도움을 준다. 이러한 두 가지 관점을 연계하면 지터 감소 작업을 시행착오 방식의 튜닝이 아니라 증거 기반 근본 원인 분석(Evidence-Based Root-Cause Analysis)으로 전환할 수 있다.

개선 작업은 측정-분석-변경-검증(Measure-Analyze-Change-Verify)을 반복하는 과정으로 수행해야 한다. 주요 원인을 식별한 후 태스크 우선순위를 변경하거나 ISR을 단축하고, 블로킹 연산을 제거하거나 CPU 또는 IRQ 어피니티를 수정하고, 메모리를 사전 할당하거나 드라이버 동작을 조정하고, 통신 처리를 격리할 수 있다. 이후 동일한 워크로드와 측정 방법을 사용하여 다시 검증해야 한다. 변경 사항은 측정된 지터 분포와 최악 조건 동작이 개선되고 시스템의 다른 영역에 새로운 데드라인, 안전 또는 자원 문제를 발생시키지 않는 경우에만 적용해야 한다.

최종 목표는 일반적으로 현실적으로 달성하기 어려운 제로 지터(Zero Jitter)가 아니라 제어기의 안정성, 정확도, 데드라인 요구사항과 양립할 수 있는 제한된 지터(Bounded Jitter)를 확보하는 것이다. 견고한 모터 제어 아키텍처는 결정론적인 하드웨어 타이밍을 구축하고, 우선순위와 격리(Isolation)를 통해 중요 태스크를 보호하며, 예측하기 어려운 실행 경로를 최소화하고, 충분한 CPU 여유(CPU Margin)를 유지하며, 현실적인 최악 부하 조건에서 타이밍을 검증한다. 이를 통해 제어 루프 지터는 로봇 소프트웨어 플랫폼이 발전함에 따라 예산화하고, 분석하고, 감소시키며, 지속적으로 회귀 테스트(Regression Test)할 수 있는 측정 가능한 엔지니어링 파라미터가 된다.

## 11.10 Humanoid Multi-Joint Synchronous Control RTOS Case

![](images/image12.png){width="7.268055555555556in" height="7.268055555555556in"}

휴머노이드 로봇(Humanoid Robot)은 다리, 몸통, 팔, 손, 목 및 기타 메커니즘에 분산된 다수의 구동 관절(Actuated Joint)을 결정론적으로 협조 제어해야 한다. 단일 서보 축과 달리 전신 운동(Whole-Body Motion)은 여러 관절이 제어된 시간적 관계를 유지하면서 명령된 위치, 속도 또는 토크에 도달하는 것에 의존한다. 따라서 RTOS 아키텍처는 결정론적인 로컬 서보 실행(Local Servo Execution)을 유지하는 동시에 분산 액추에이터 네트워크(Distributed Actuator Network) 전체의 동기화를 보장하여 로봇이 하나의 협조된 기계 시스템으로 동작하도록 해야 한다.

제어 아키텍처(Control Architecture)는 계층적으로 구성할 수 있다. 상위 수준 컴퓨터(High-Level Computer)는 인지(Perception), 상태 추정(State Estimation), 궤적 생성(Trajectory Generation), 보행 계획(Gait Planning), 역기구학(Inverse Kinematics), 전신 제어(Whole-Body Control)를 수행하고, 실시간 제어기(Real-Time Controller)는 이러한 목표를 동기화된 관절 명령으로 변환한다. 이후 개별 관절 제어기는 빠른 전기적 및 기계적 피드백 루프를 실행한다. 이러한 분리를 통해 계산 복잡도가 높은 계획 기능은 중간 수준의 주기로 동작하면서 전류, 속도, 위치 제어는 각 액추에이터가 요구하는 결정론적인 주기로 로컬에서 계속 실행될 수 있다.

전신 제어(Whole-Body Control)는 하나의 협조된 집합으로 해석될 때 의미를 갖는 기준값(Reference)을 생성한다. 예를 들어 보행 중에는 엉덩이, 무릎, 발목, 몸통, 팔의 움직임이 동일하게 의도된 로봇 상태와 제어 시점에 대응해야 한다. 일부 관절에는 하나의 제어 프레임(Control Frame)에 속한 명령을 적용하고 다른 관절에는 다음 프레임의 명령을 적용하면 계획된 신체 구성이 왜곡될 수 있다. 따라서 명령에는 사이클 식별자(Cycle Identifier), 시퀀스 번호(Sequence Number), 타임스탬프(Timestamp) 또는 이에 상응하는 시간 기준을 포함하여 분산 제어기가 모든 관절 목표를 올바른 동기화 프레임(Synchronization Frame)과 연결할 수 있도록 해야 한다.

EtherCAT과 같은 결정론적 필드버스(Deterministic Fieldbus)는 실시간 마스터(Real-Time Master)와 분산 관절 드라이브(Distributed Joint Drive) 사이의 통신 백본(Communication Backbone)을 제공할 수 있다. 주기적 프로세스 데이터(Cyclic Process Data)는 정의된 통신 주기 안에서 관절 위치, 속도, 토크, 모드, 상태 정보를 전송한다. 분산 클록(Distributed Clocks)은 참여 장치 사이에 공통 시간 기준을 설정하여 액추에이터 간 상대적인 타이밍 오차를 줄일 수 있다. 목표는 단순히 네트워크 지연시간을 줄이는 것이 아니라 예측 가능한 명령 전달과 다수 관절 사이에서 정밀하게 정렬된 액추에이션(Actuation)을 확보하는 것이다.

각 관절에서는 MCU-RTOS 서보 제어기(MCU-RTOS Servo Controller)가 중앙 컴퓨터와 독립적으로 가장 빠른 제어 기능을 유지할 수 있다. 일반적인 PMSM 액추에이터는 하드웨어 타이머(Hardware Timer)와 우선순위가 지정된 RTOS 태스크를 사용하여 동기화된 전류 샘플링(Current Sampling), FOC 연산, PWM 생성, 속도 제어, 위치 제어를 실행한다. 전류 루프(Current Loop)는 수 kHz 이상의 주기로 실행될 수 있으며, 속도 및 위치 루프는 이보다 느리게 동작한다. 이러한 로컬 계층 구조(Local Hierarchy)는 필드버스 타이밍과 상위 수준 연산이 모든 모터의 전기적 제어 타이밍을 직접 결정하는 것을 방지한다.

따라서 전체 휴머노이드 시스템에는 여러 개의 중첩된 시간 척도(Nested Time Scale)가 존재한다. 전기적 전류 제어(Electrical Current Control)는 가장 빠른 계층을 구성하고, 그 위에서 로컬 속도 및 위치 제어가 동작하며, 네트워크 동기화(Network Synchronization)가 또 다른 주기적 계층을 형성하고, 전신 제어는 더 느린 주기로 협조된 목표를 생성한다. 인지, 계획, AI 기능은 이보다 더 느린 주기로 동작할 수 있다. 아키텍처에서는 이러한 주기와 계층 사이의 데이터 전환(Data Transition)을 명확하게 정의하여 느린 연산이 빠른 서보 계층에 제어되지 않은 타이밍 변동을 발생시키지 않으면서 모션을 유도할 수 있도록 해야 한다.

RTOS 스케줄링 우선순위(RTOS Scheduling Priority)는 물리적인 타이밍 중요도(Physical Timing Criticality)를 따라야 한다. 전류 제어와 동기화된 PWM 갱신에는 가장 높은 로컬 우선순위를 부여하고, 그 다음으로 속도 및 위치 제어를 배치한다. 네트워크 프로세스 데이터 처리(Network Process-Data Handling)와 명령 유효성 검사(Command Validation)는 관련 데드라인 이전에 완료되어야 하며, 진단, 파라미터 관리(Parameter Management), 텔레메트리(Telemetry), 로깅은 더 낮은 우선순위에서 실행한다. 중앙 실시간 컴퓨터에서도 주기적 네트워크 마스터(Cyclic Network Master)와 전신 제어 경로를 비실시간 시각화, 저장장치, 사용자 인터페이스, AI 워크로드로부터 보호해야 한다.

동기화(Synchronization)는 휴머노이드의 모든 연산이 정확히 동일한 순간에 실행되어야 한다는 것을 의미하지 않는다. 대신 각 계층은 알려진 시간적 관계(Known Temporal Relationship)와 제한된 오차(Bounded Error)를 가져야 한다. 관절 드라이브는 로컬 제어 출력을 독립적으로 계산할 수 있지만 새롭게 수신된 기준값은 공통 동기화 경계(Shared Synchronization Boundary)에서 활성화할 수 있다. 센서 측정값 역시 알려진 샘플링 시점에 대응해야 한다. 이를 통해 중앙 제어기는 서로 크게 다르고 알려지지 않은 시점에서 수집된 관절 정보를 결합하는 대신 일관된 로봇 상태(Coherent Robot State)를 기반으로 연산할 수 있다.

피드백 동기화(Feedback Synchronization)는 특히 균형 제어(Balance Control)와 보행(Locomotion)에서 중요하다. 관절 엔코더(Joint Encoder), 관성 측정 장치(Inertial Measurement Unit, IMU), 힘 또는 토크 센서(Force or Torque Sensor), 발 접촉 정보(Foot-Contact Information)는 함께 휴머노이드의 물리적 상태를 나타낸다. 이러한 측정값의 타임스탬프가 일관되지 않거나 데이터 사이의 시간 차이가 지나치게 크면 상태 추정기는 실제로 존재한 적이 없는 신체 구성을 표현할 수 있다. 따라서 타임스탬핑(Timestamping), 동기화된 획득(Synchronized Acquisition), 제한된 통신 지연시간(Bounded Communication Latency), 데이터 최신성 검사(Freshness Checking)는 선택적인 네트워크 기능이 아니라 제어 아키텍처의 필수 구성요소가 된다.

지터(Jitter)는 로컬 및 분산 계층 모두에서 제어되어야 한다. 로컬 제어 루프 지터(Local Control-Loop Jitter)는 개별 서보의 안정성과 추종 성능에 영향을 주며, 네트워크 주기 또는 동기화 지터(Network-Cycle or Synchronization Jitter)는 관절 사이의 상대적인 타이밍 오차를 발생시킨다. 하나의 축에서 발생하는 작은 오차는 독립적으로 보면 중요하지 않을 수 있지만 여러 관절이 발 접촉(Foot Contact), 질량 중심 운동(Center-of-Mass Motion), 조작 정밀도(Manipulation Accuracy)를 유지하기 위해 협력하는 경우 중요한 문제가 될 수 있다. 따라서 타이밍 분석에서는 개별 루프 지터, 네트워크 주기 변동, 클록 동기화 오차(Clock Synchronization Error), 종단 간 명령-액추에이션 지연시간(End-to-End Command-to-Actuation Latency)을 측정해야 한다.

휴머노이드의 신체는 기계적으로 결합되어 있기 때문에 고장 관리(Fault Management)는 더욱 복잡해진다. 하나의 액추에이터 고장은 여러 다른 관절의 부하와 안정성 요구사항을 변화시킬 수 있다. 각각의 로컬 드라이브는 전기적 고장과 센서 고장을 신속하게 감지해야 하며, 중앙 제어기는 이러한 고장이 전신에 미치는 영향을 평가해야 한다. 고장의 심각도와 로봇 상태에 따라 토크 제한(Torque Limitation), 제어된 자세 전환(Controlled Posture Transition), 모션 정지(Motion Freezing), 지지력 재분배(Support Redistribution), 제어 정지(Controlled Stop), 브레이크 작동(Brake Activation), 즉각적인 드라이브 차단(Immediate Drive Inhibition) 등을 수행할 수 있다.

통신 고장(Communication Failure) 역시 협조된 동작이 필요하다. 관절 제어기는 오래된 기준값을 무기한 계속 실행하는 대신 타임스탬프, 시퀀스 모니터링(Sequence Monitoring), 타임아웃 감시(Timeout Supervision)를 사용하여 오래된 명령(Stale Command)을 거부해야 한다. 그러나 하나의 관절만 독립적으로 비활성화하면 로봇의 안정성을 저하시킬 수 있다. 따라서 로컬 제어기는 사전에 정의된 안전 동작(Safe Behavior)을 수행하면서 상태를 감독 계층(Supervisory Layer)에 보고해야 하며, 감독 계층은 시스템 수준의 대응을 협조한다. 안전 설계는 빠른 로컬 자율성(Local Autonomy)과 전역적으로 일관된 휴머노이드 동작(Globally Consistent Humanoid Behavior)을 결합해야 한다.

검증(Verification)은 각각의 서보만 독립적으로 시험하는 것이 아니라 실제적인 전신 워크로드(Whole-Body Workload)에서 동기화를 평가해야 한다. 측정 항목에는 관절 루프 주기(Joint-Loop Period), PWM 타이밍, 네트워크 주기 지연시간(Network-Cycle Latency), 분산 클록 정렬(Distributed-Clock Alignment), 명령 데이터의 경과 시간(Command Age), 액추에이터 갱신 스큐(Actuator Update Skew), 추종 오차(Tracking Error), 고장 대응 시간(Fault-Response Time)이 포함되어야 한다. 보행, 정지 상태 균형 유지, 빠른 자세 변화, 양팔 동시 운동, 높은 통신 부하 조건은 유용한 스트레스 시험(Stress Test)을 제공한다. 드물게 발생하는 스케줄러, 네트워크, 인터럽트, 동기화 이상을 발견하기 위해 장시간 테스트(Long-Duration Test)도 필요하다.

하드웨어 타임스탬프(Hardware Timestamp), GPIO 계측(GPIO Instrumentation), EtherCAT 진단(EtherCAT Diagnostics), RTOS 트레이싱(RTOS Tracing), 고해상도 로깅(High-Resolution Logging)을 사용하면 제어 계층 전체의 이벤트를 서로 연관시킬 수 있다. 엔지니어는 의도된 전신 명령 시점과 네트워크 전송, 드라이브 수신, 로컬 연산, PWM 활성화, 실제 관절 응답을 비교할 수 있다. 이러한 종단 간 관점(End-to-End View)은 CPU 사용률만 확인하는 것보다 더 많은 정보를 제공한다. 시스템에 상당한 프로세서 여유가 있더라도 스케줄링, 통신 또는 제대로 정렬되지 않은 갱신 경계(Update Boundary) 때문에 동기화 오류가 발생할 수 있기 때문이다.

결과적으로 이러한 아키텍처는 상위 수준 지능(High-Level Intelligence)이 협조된 전신 운동을 정의하고 결정론적인 RTOS와 필드버스 계층(Fieldbus Layer)이 정밀한 물리적 실행을 보장하는 분산 실시간 제어 시스템(Distributed Real-Time Control System)을 형성한다. 중앙 연산 시스템이 모든 PWM 에지를 생성할 필요는 없으며, 로컬 관절 제어기가 전체 보행이나 조작 작업을 이해할 필요도 없다. 동기화된 명령(Synchronized Command), 공유 타이밍(Shared Timing), 로컬 다중 주기 서보 루프(Local Multi-Rate Servo Loop), 제한된 지터(Bounded Jitter), 협조된 고장 처리(Coordinated Fault Handling), 종단 간 검증을 결합함으로써 휴머노이드는 개별 모터 제어에서 안정적이고 정밀한 다관절 물리 동작(Multi-Joint Physical Behavior)으로 확장될 수 있다.
