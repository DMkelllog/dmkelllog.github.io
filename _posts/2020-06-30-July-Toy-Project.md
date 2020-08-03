---
title:  "7월 이달의 프로젝트"
excerpt: "연구실 책상 Time Checker 만들기 with 아두이노"

categories:
  - ToyProject
tags:
  - Arduino
---

예전부터 아두이노가 하고 싶었다.  

하지만 학교 다니며 다룰 기회가 없었고 혼자서 해볼 생각도 못했다.  

드디어 토이 프로젝트로 아두이노를 다룰 기회가 생겼다.  

# 연구실 책상 Time Checker 만들기 with 아두이노  
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

##### 결과

<회로도>

<실제 제작 사진>

<코드 - 아두이노>

```c
int distance;
int triggerPin = 8;
int echoPin = 7;
int arr[6];
int idx = 0; // idx for arr, 0~5
int count = 0; // count every 10 seconds
int save_idx = 0; //save
int output = 0; // output 0: not sitted, 1: sitted
int distance2;
int triggerPin2 = 9;
int echoPin2 = 6;
int stay = 1;

void setup()
{Serial.begin(9600);
  pinMode(triggerPin,OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(triggerPin2,OUTPUT);
  pinMode(echoPin2, INPUT);}

void loop()
{ digitalWrite(triggerPin, HIGH);  
  delayMicroseconds(10);
  digitalWrite(triggerPin,LOW);
  distance = pulseIn(echoPin, HIGH)/58; 
  // pulse return time -> distance(cm)
  
  digitalWrite(triggerPin2, HIGH);  
  delayMicroseconds(10);
  digitalWrite(triggerPin2, LOW);  
  distance2 = pulseIn(echoPin2, HIGH)/58;
  stay = 1;
  if (distance> 63 && distance < 70){//floor detected
    if (distance2 > 40 && distance2 < 100){
      stay = 0;}
    
    if (distance2 > 500){
      stay = 0;
    }}
  else if (distance>19 && distance < 27){//chair detected
    stay = 0;
    }
  else {
    stay = 1;}
  arr[idx] = stay;

  if (idx == 5){
    for (idx=0; idx<=5; idx++){
      count += arr[idx];}
    if (count>2){     // threshold:2 if larger, record=1
      output = 1;}
    else{
      output = 0;
    }
    Serial.println(output);
    idx = 0;
    count = 0;
    output = 0;
      
    if (save_idx == 4){  //save csv file every 4 minutes
      //Serial.println("SAVEWORKBOOK");
      save_idx = 0;}
    else{
      save_idx ++; }}
  else{
    idx++;}
  delay(9964.5);} // to calibrate 10 seconds

```



<코드 - 파이썬>

```python
import serial
import datetime
import csv
import matplotlib.pyplot as plt

s = serial.Serial('COM4', baudrate = 9600)

def log_record(sit):
    with open('log3.csv', 'a', newline='') as csvfile:
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
    plt.plot(range(len(sit_arr)),sit_arr)
    # x_ticks = range(180, 1440+180, 180)
    # plt.xticks(x_ticks, [6,9,12,15,18,21,24,3])
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
            # print every 5 minutes
            sit_list = smoothing(sit_list)
            total_sit = sum(sit_list)
            standup = num_stand(sit_list)
            print( '%d시 %d분 sit time: %d stand up: %d' % (hour_now, minute_now, total_sit, standup))
            if minute_now == 0:
                # plot on every hour
                plot_sit(sit_list)
                # reinitiate at 3 am
                if hour_now == 3 and minute_now == 0:
                    # calculate 
                    if sum(sit_list) == 0:
                        pass
                    else:
                        print('daily logging')
                        come_idx = sit_list.index(1)
                        rev = list(reversed(sit_list))
                        leave_idx = rev.index(1)
                        come_time = time_list[come_idx]
                        leave_time = time_list[-leave_idx-1]

                        # record log 
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





