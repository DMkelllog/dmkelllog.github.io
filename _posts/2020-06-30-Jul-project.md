---

title:  "7월 이달의 프로젝트"
excerpt: "연구실 책상 Time Checker 만들기 with 아두이노"

categories:
  - ToyProject
tags:
  - Arduino
  - Monthly
---

### 연구실 책상 Time Checker 만들기 with 아두이노  

예전부터 아두이노가 하고 싶었다.  
하지만 학교 다니며 다룰 기회가 없었고 혼자서 해볼 생각도 못했다.  
드디어 토이 프로젝트로 아두이노를 다룰 기회가 생겼다.  

##### 기간: 7/1-7/31
##### 요약: 
* 연구실에 앉아있는 시간을 아두이노를 이용하여 측정/기록하고 시각화  


##### 목표: 
 * 연구 시간을 기록하여 연구 의욕을 고취하며 통계적인 인사이트를 얻는다.
 * 아두이노를 이용한 간단한 IoT 시스템을 구축한다.
![컨셉](https://github.com/DMkelllog/dmkelllog.github.io/blob/master/assets/images/July%20plan%201.png?raw=true)


##### 세부 사항:
 * 구성: 아두이노 센서, 보드, 제어 프로그램, 시각화 프로그램  
 * 진행 단계:
   1. 아두이노 기초 공부
   2. 아두이노 설계
   3. 부품 구입 및 조립
   4. 제어 프로그램 구축
   5. 측정/기록 정상 작동 확인
   6. 통계 분석 및 시각화 프로그램 구축  
   
* 구상도
![구상도](https://github.com/DMkelllog/dmkelllog.github.io/blob/master/assets/images/July%20plan%202.png?raw=true)
* 예상 어려움  
  * 컴퓨터의 전원을 껐을 때 측정 지속 여부
  * 부재중일 때, 주변의 다른 사람/물체가 오측정

-----------------------------------------------------------

##### 결과

* 아두이노

![arduino circuit](https://github.com/DMkelllog/dmkelllog.github.io/blob/master/assets/images/%EC%95%84%EB%91%90%EC%9D%B4%EB%85%B8%20%ED%9A%8C%EB%A1%9C%EB%8F%841.PNG?raw=true)    

시뮬레이터 덕분에 아두이노 구성에는 큰 어려움이 없었다.
아두이노 프로그래밍도 학부 때 배운 걸음마 수준의 C언어로 충분했다.
문제는 2가지였다.

* 효과적인 착석 탐지 
* 기록, 전처리, 시각화

##### 착석 탐지

주어진 센서는 초음파 센서
처음 계획은 초음파 센서 하나만 쓰려고 했지만 거리 측정의 신뢰도가 높지 않아 두개를 사용하였다.
초음파 센서는 옷처럼 표면이 고르지 않을 경우 정확한 측정을 하지 못하고 500cm이상과 같은 거리를 출력한다.
주센서 하나는 책상 밑에서 바닥을 향하고 보조센서는 정면을 향하도록 부착하였다.

두 센서를 이용한 착석 탐지 알고리즘

1. 주센서가 바닥을 탐지하고(60<distance1<70) 보조센서가 상체를 탐지하지 못한다(40<distance2) .
2. 주센서가 의자를 탐지한다.(20<distance1<30)

여러 상황에서 값을 바꿔가며 경험적으로 최적의 값을 얻었다. 
그 결과 다양한 자세에서 착석을 탐지할 수 있고 다양한 부재중 상황에서 미착석을 탐지할 수 있었다.
측정 주기는 10초, 기록 주기는 1분이며 1분동안 6번 중 3번이상 착석으로 측정되면 착석으로 기록하였다.

##### 기록, 전처리, 시각화

기록되는 데이터를 PC로 불러오기 위해 아두이노와 함께 가장 많이 쓰이는 PLX-DAQ를 사용하였다.
이는 엑셀의 VBA 기반의 프로그램으로 아두이노에서 출력하는 값을 엑셀에 값이 기록한다.
하지만 값을 복사하여 다른 엑셀에서 가공을 하는 과정에서 충돌이 생겼다.
PLX-DAQ도 매크로를 사용하고 매크로는 동시에 실행이 되지 않기 때문에 사용을 포기하였다.

파이썬에도 시리얼 신호를 기록할 수 있다는 것을 알고 익숙한 파이썬으로 방향을 틀었다.
신호 불러오기, 기록, 전처리, 가공, 시각화, 모두 익숙한 작업이어서 프로그래밍은 어렵지 않았지만
다른 파이썬 작업 중 메모리 오버플로우 등의 문제가 발생할 때는 같이 종료해야 한다는 단점이 있다.



(코드 - 아두이노)  

```c
int distance;
int triggerPin = 8;
int echoPin = 7;
int arr[6];
int idx = 0; // idx for arr, 0~5
int count = 0; // count every 10 seconds
int output = 0; // output 0: not sitted, 1: sitted
int distance2;
int triggerPin2 = 9;
int echoPin2 = 6;
int stay = 1;

void setup()
{
  Serial.begin(9600);
  pinMode(triggerPin,OUTPUT);
  pinMode(echoPin, INPUT);

  pinMode(triggerPin2,OUTPUT);
  pinMode(echoPin2, INPUT);
  
}

void loop()
{
  digitalWrite(triggerPin, HIGH);  
  delayMicroseconds(10);
  digitalWrite(triggerPin,LOW);
  distance = pulseIn(echoPin, HIGH)/ 58;
  
  digitalWrite(triggerPin2, HIGH);  
  delayMicroseconds(10);
  digitalWrite(triggerPin2, LOW);  
  distance2 = pulseIn(echoPin2, HIGH)/ 58;
  stay = 1;
  //바닥
  if (distance> 60 && distance < 70){
    if (distance2 > 40 && distance2 < 100){
      stay = 0;}
    if (distance2 > 500){
      stay = 0;
    }}
  //의자
  else if (distance>20 && distance < 30){
    stay = 0;
    }
  else {
    stay = 1;}
  arr[idx] = stay;
  
  // threshold:2 if larger, output=1
  if (idx == 5){
    for (idx=0; idx<=5; idx++){
      count += arr[idx];
      }
    if (count>2){     
      output = 1;
      }
    else{
      output = 0;
    }
    //Serial.print("DATA,DATE,TIME,");
    Serial.println(output);
    idx = 0;
    count = 0;
    output = 0;}
  else{
    idx++;}
  //Serial.print(distance);
  //Serial.print(",");
  //Serial.print(distance2);
  //Serial.print(",");
  //Serial.println(stay);
  delay(9964.5); // to calibrate 10 seconds
}

```



(코드 - 파이썬)

```python
import serial
import datetime
import csv
import matplotlib.pyplot as plt

s = serial.Serial('COM4', baudrate = 9600)

def log_record(sit):
    with open('log.csv', 'a', newline='') as csvfile:
        fieldnames = ['date', 'time', 'sit']
        writer = csv.DictWriter(csvfile, fieldnames=fieldnames)
        writer.writerow({'date': datetime.datetime.today().date(),
                         'time': datetime.datetime.today().time().replace(microsecond=0),
                         'sit': sit})
        
def smoothing(arr):
    for i in range(1,len(arr)-1):
        if arr[i] != arr[i-1] and arr[i] != arr[i+1]:
            arr[i] = arr[i-1]
    return arr

def num_stand(arr):
    cnt = 0
    for i in range(0,len(arr)-1):
        if arr[i] == 1 and arr[i+1] == 0:
            cnt += 1
    return cnt

def plot_sit(sit_arr):
    list_15 = []
    length = len(sit_arr) // 15
    for i in range(length):
        sum15 = sum(sit_arr[15*i:15*(i+1)])
        if sum15 >= 8:
            sit = 1
        else:
            sit = 0
        list_15.append(sit)
    list_15 = np.array(list_15).reshape(1,-1) 
    plt.imshow(list_15, cmap='Blues')
    plt.show()

sit_list = []
time_list = []
while True:
    if s.readable():
        res = s.readline()
        sit = int(res.decode()[:len(res)-1])
        log_record(sit)
        sit_list.append(sit)
        minute_now = datetime.datetime.now().timetuple()[4]
        hour_now = datetime.datetime.now().timetuple()[3]
        time_list.append([hour_now, minute_now])
        
        if minute_now % 5 == 0:
            # 매 5분
            sit_list = smoothing(sit_list)
            total_sit = sum(sit_list)
            standup = num_stand(sit_list)
            print( '%d시 %d분 sit time: %d stand up: %d' % (hour_now, minute_now, total_sit, standup))
            if minute_now == 0:
                # 매 정각 계산
                plot_sit(sit_list)
                # 새벽 3시에 초기화
                if hour_now == 3 and minute_now == 0:
                    # 출퇴근 시간
                    if sum(sit_list) == 0:
                        pass
                    else:
                        print('daily logging')
                        come_idx = sit_list.index(1)
                        rev = list(reversed(sit_list))
                        leave_idx = rev.index(1)
                        come_time = time_list[come_idx]
                        leave_time = time_list[-leave_idx-1]

                        # log 기록
                        with open('daily_log.csv', 'a', newline='') as csvfile:
                            fieldnames = ['date','출근','퇴근','Sit time', 'Stand up']
                            writer = csv.DictWriter(csvfile, fieldnames=fieldnames)
                            writer.writerow({'date':datetime.datetime.today().date(),
                                             '출근': datetime.time(hour=come_time[0], minute=come_time[1]),
                                             '퇴근': datetime.time(hour=leave_time[0], minute=leave_time[1]),
                                             'Sit time': total_sit, 'Stand up': standup})                                 
                    # list 초기화
                    sit_list = []
                    time_list = []
```

