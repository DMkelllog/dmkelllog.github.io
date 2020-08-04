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

##### 결과

* 회로도  

![arduino circuit](https://github.com/DMkelllog/dmkelllog.github.io/blob/master/assets/images/%EC%95%84%EB%91%90%EC%9D%B4%EB%85%B8%20%ED%9A%8C%EB%A1%9C%EB%8F%841.PNG?raw=true)    



(코드 - 아두이노)  



<details>
<summary>I could use some help...</summary>
<p>

```c#

    public class Order
{
    public int OrderId { get; set; }
    public int CustomerId { get; set; }

    public List<int> Products { get; set; }
}
\```

</p>
</details>
```





<details>
<summary>I could use some help...</summary>
<p>

```c#
public class Order
{
    public int OrderId { get; set; }
    public int CustomerId { get; set; }

    public List<int> Products { get; set; }
}
\```

</p>
</details> 
```