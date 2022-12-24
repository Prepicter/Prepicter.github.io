---
date : 2022-12-24 21:00:00 +09:00
categories : [ML]
tags : [object-detection, yolo]
---
# Maple Rune Solver

22년도 여름방학 프로젝트

Object Detection 공부 용으로 시작

목표는 룬 해방시 생기는 화살표를 정확하게 맞추는것
 

**공부 목적. 악의적인 목적으로 사용하지 않음**

## 초기 설계

**CNN** 만으로 구현을 시도하였음
 - 게임 화면에서 룬 영역을 추출
 - 룬 영역을 일정 간격으로 4개를 슬라이싱
 - 각 이미지를 CNN으로 분류

**CNN 분류기**는 기본 500장의 라벨링된 데이터에 색조와 방향을 조정하고, 
노이즈를 추가하여 데이터를 증식시켰고, 약 1500장의 이미지를 학습데이터로 사용하였다.

**추출된 룬 영역** 

![룬 영역](https://user-images.githubusercontent.com/42761686/209433667-57811e95-a002-431c-9843-9f687d90ef25.png)

**이상적인 슬라이싱**

![correct](https://user-images.githubusercontent.com/42761686/209433695-838e927a-6044-44b5-afe6-8305559aeffa.png)

**문제가 되는 경우**

![error 1](https://user-images.githubusercontent.com/42761686/209433738-6d1f09e7-b02e-449b-933b-47601608b617.png)  	     ![error2](https://user-images.githubusercontent.com/42761686/209433751-96d8b5a2-ab40-44da-aacb-c8e1000117e2.png)




**발생한 문제점**

 - 룬은 랜덤한 간격을 가진다 -> 넉넉하게 슬라이싱해도 서로 섞이는 문제 발생
 - 위의 문제점으로 화살표의 위치가 들쭉날쭉이라 분류의 정확도가 감소

 

## 초기 설계 개선

어짜피 이미지가 섞인다면 차라리 전체 룬 영역 이미지에서 CNN의 방식처럼 일정 간격(stride)으로 
조금씩 이동하면서 각각 분류시킨후 분포에 따라 분류를 시키기

**조금씩 이동**

![part_0](https://user-images.githubusercontent.com/42761686/209433883-675ec463-1df7-47e3-8b2e-d57ce68014c3.png) ![part_3](https://user-images.githubusercontent.com/42761686/209433969-26365ec9-1b90-4066-8ad3-def43f61c375.png) ![part_5](https://user-images.githubusercontent.com/42761686/209434000-e92be8ec-2ae0-412a-aaa5-2a12904a6c13.png) ![part_11](https://user-images.githubusercontent.com/42761686/209434079-cf2754de-a5ed-4bdb-a146-436eeddd811f.png)

**분포**
![분포](https://user-images.githubusercontent.com/42761686/209434459-1947a600-33ff-4bbf-9e9c-73a083e630d3.png)
참고로 left right top top 이였다.

**결과**

 - 시도는 좋았으나, 잘려진 부분의 이미지를 잘 분류하지는 못했다.
 - 이전보단 결과가 좋긴한데,  그래도 분류에 오류 발생

## Object Detection 사용

이전 방식으로 계속 하다간 끝이 안날것 같아 변경
Yolo v4를 사용하였다.

**변경된 설계**
1. 룬 영역 추출 까지는 동일. 이미지 사이즈를 416x416으로 확장
2. 이미지를 통째로 Yolo에 넣기
3. 결과 출력

**416 사이즈로 뽑기**

![resized_1-min](https://user-images.githubusercontent.com/42761686/209434282-6e5fb520-63a7-4fa1-b3ba-4047141216b2.png)

**출력**

![1_rect-min](https://user-images.githubusercontent.com/42761686/209434261-ca9821e3-ed1a-47bd-8072-d5ebc00cb9a6.png)


**결과**

 - 룬 자체는 잘 찾았다. 다만 방향을 제대로 분류하진 못했다.
 - Yolo가 방향성도 고려하여 학습을 하나?
 - 학습 데이터(약 1500장)가 적어서 그럴지도

## 분류기 추가

어짜피 룬 자체는 잘 찾으니, 찾은 룬 이미지를 그대로 분류기에 넣어버리기.
이미지 사이즈가 다르니, 학습데이터를 조정후 다시 학습시켰다.

**분류기에 넣기**

 ![1_1](https://user-images.githubusercontent.com/42761686/209434393-c6867d01-c7be-48d7-8d53-328e0e38a0fa.png)  

**출력**

    [5.5755044e-17 2.5297098e-11 1.0000000e+00 2.5603323e-13] 
    right


**결과**

 - 잘 찾아내고 분류도 잘함 
 - 실제 게임에서 약 500번 정도 룬 이미지를 수집하고, 돌려봤는데 놀랍게도 실패는 없더라.

## 끝내면서

 - Yolo는 ROI를 찾으면서 동시에 분류를 하여 속도가 빠르다.
 - 근데 여기서는 분류기를 추가로 넣었으니 의미가 없지 않나.
 - 차라리 Masked R-CNN으로 만들었다면, 추가로 분류기를 안 넣어도 잘 찾지 않았을까?

> 코드는 추후 github에 업로드

