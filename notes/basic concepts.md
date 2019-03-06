# 기본 용어 및 개념 정리

[TOC]

------------

## Pose

![img](https://cdn-images-1.medium.com/max/1600/1*4OAVRUx3tuKnXEo8tl9xtQ.png)

[사진 출처](https://becominghuman.ai/human-pose-matching-on-mobile-a-fun-application-using-human-pose-estimation-part-1-intro-93c5cbe3a096)

>  The combination of *position* and *orientation* is referred to as the **pose** of an object, even though this concept is sometimes used only to describe the orientation. *Exterior orientation* and *translation* are also used as synonyms of pose.
>
> [위키백과](https://en.wikipedia.org/wiki/Pose_(computer_vision))



----------

## Scale

![img](https://t1.daumcdn.net/cfile/tistory/265F7F33536CC2AD26)

[사진 출처](https://darkpgmr.tistory.com/137)

범위.

이미지에서 물체의 특징을 계산할 때, 어떤 scale에서 보느냐가 결과를 좌우할 수 있다. 예를 들면, 위 사진과 같은데, 위 사진의 녹색 박스가 scale일 경우에는 해당 선은 직선/완만한 곡선이라고 판단할 것이고, scale이 빨간색 박스일 경우는 명확한 코너로 인식 되어질 것이기 때문이다.

------------

## Stereo vs Monocular

### Stereo

이거 두개 참고해서 쓰기 !

https://avisingh599.github.io/vision/visual-odometry-full/

https://darkpgmr.tistory.com/137

### Monocular



### 참고 자료

[Visual Odmetry from scratch - A tutorial for beginners](https://avisingh599.github.io/vision/visual-odometry-full/)

--------

## Direct Visual Odometry (DVO)



### Odometry

> **Odometry** is the use of data from [motion sensors](https://en.wikipedia.org/wiki/Motion_sensor) to estimate change in position over time. It is used in [robotics](https://en.wikipedia.org/wiki/Robotics) by some legged or wheeled [robots](https://en.wikipedia.org/wiki/Robot) to estimate their position relative to a starting location. 
>
> [위키백과](https://en.wikipedia.org/wiki/Odometry)

주행 거리 측정. (자동차 등의) 시간에 따른 위치 변화를 motion sensor등으로 측정하여, 시작점으로 부터의 위치를 측정한다. 



### Visual Odometry

> **visual odometry** is the process of determining the position and orientation of a robot by analyzing the associated camera images.
>
> [위키백과](https://en.wikipedia.org/wiki/Visual_odometry)

이미지나 영상등을 분석하여, odometry(object의 상대적인 pose)를 알아내는 것.



Visual Odometry는 camera setup, method 종류 등에 따라서 타입을 나눌 수 있다. 

- camera setup 
  - monocular (single camera)
  - stereo (two camera in stereo setup)
- method
  - feature based method 
    - extract image feature points and tracking them in the image sequence.
  - direct method 
    - uses pixel intensity in the image sequence directly as visual input.
  - hybrid

