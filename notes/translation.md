# Learning Depth Video from Monocular Video using Direct Method  

## Abstract 

>The ability to predict depth from a single image - using recent advances in CNNs - is of increasing interest to the vision community. Unsupervised strategies to learning are particularly appealing as they can utilize much larger and varied monocular video datasets during learning with- out the need for ground truth depth or stereo.   

- single image(CNN)로부터 depth를 예측하는 것은 vision community에서 관심이 증가하고 있는 주제
- **Unsupervised(비지도) 학습 전략** 은 특히 매력적이다. 보다 크고 다양한 monocular video의 ground truth depth(사물의 실제 위치) or stereo(입체) <u>없는</u> datasets를 활용할 수 있기 때문에.
  
> In previous works, separate pose and depth CNN predictors had to be determined such that their joint outputs minimized the photometric error.  

- ?? 이전의 연구들에서는, CNN predictor의 pose(위치?)와 depth를 분리하는 것이 결정되어져야만 했기에, 그들을 합친 결과는(?) photometric(노출에 의한?) 에러를 줄이는 것이었다.   

>Inspired by recent advances in direct visual odometry (DVO), we argue that the depth CNN predictor can be learned without a pose CNN predictor.  

- 최근 direct visual odometry(DVO/직접적인 시각 주행 거리 측정?)에 영감을 받아, **우리는 depth CNN predictor가 pose CNN predictor 없이 학습될 수 있단 사실을 논해왔다.**

> Further, we demonstrate empirically that incorporation of a differentiable implementation of DVO, along with a novel depth normalization strategy - substantially improves per- formance over state of the art that use monocular videos for training.

- **더욱이, 우리는 incorporation of a differentiable implementation of DVO(DVO의 판별 가능한 구현의 결합)을 경험에 의하여 논증한다, 새로운 depth normalization(표준화) 전략을 따라서.** 
- 이 방법은 trainig에 monocular video를 사용하는 기술의 급을 넘어, 상당히 성능을 향상시킬 수 있다.  

 
##  1. Introduction 
> Depth prediction from a single image using CNNs has had a surge of interest in recent years [7, 21, 20]. Recently, unsupervised methods that rely solely on monocular video for training (without depth or stereo groundtruth) have captured the attention of the community. Of particular note in this regard is the work of Zhou et al. [31] who proposed a strategy that learned separate pose and depth CNN predictors by minimizing the photometric consistency across monocular video datasets during training. Although achieving impressive results this strategy falls noticeably behind those that have been trained using rectified stereo image pairs [13, 19]. These rectified stereo methods have shown comparable accuracy to supervised methods [26, 7, 23] over datasets where only sparse depth annotation is available. However, the assumption of using calibrated binocular image pairs excludes itself from utilizing monocular video which is easier to obtain and richer in variability. This performance gap between stereo [13, 19] and monocular [31] learning strategies is of central focus in this paper.

- depth prediction from single image using CNN 요즘 핫함.
- 최근 monocular video에만 의존한 비지도 학습이 주목받는 추세다.
- ?? Zhou : **a strategy that learned separate pose and depth CNN predictors by minimizing the photometric consistency across monocular video datasets during training.**
- 좋은 결과에 도달했음에도 불구하고, **rectified stereo image pairs** 때문에 뭍혔다.
- These rectified stereo methods have shown comparable accuracy to supervised methods [26, 7, 23] over datasets where only sparse depth annotation is available.
- 그러나 이런 calibrated binocular image pairs에서의 가정은 (더 가변성이 풍부하고 획득하기도 쉬운) monocular video를 활용할 수 없게 한다. 
- **stereo와 monocular learning strategy 사이의 성능 갭은 이 논문의 중점이다.** 

> In this paper, we attempt to close this gap by drawing inspiration from recent advances in direct visual odometry. Specifically, we note that the major difference between stereo and monocular strategies stems from: (i) unknown camera pose between frames, and (ii) ambiguity in scale. Existing methods [28, 31] for learning depth from monocular video address these differences only partially by adding an extra CNN pose prediction module. In this paper we argue that these previous strategies do not adequately address the scale ambiguity issue - causing divergence during train-ing. Further, we advocate that the incorporation of an additional CNN pose prediction module is unnecessary. Instead we argue that one could employ a differentiable and deterministic objective for pose prediction which is now commonly employed within the SLAM community for direct visual odometry [10, 8, 6, 2].

- DVO에서의 최근 발전으로부터 영감을 받아, 위에서 말한 gap 줄이고자 했다.
- stereo와 monocular 전략의 차이점은 이로부터 기인했다.
  - (1) unknown camera pose between frames
  - (2) ambiguity in scale
- 현존하는 learning depth (by monocular video) 방법들은 CNN pose predictor를 추가함으로서 부분적으로만 이러한 차이점들을 다룬다.
- 이 논문에서 우리는 위와 같은 이전의 방법들이 ambiguity in scale 측면에서 적절치 않았음을 말할거다. (트레이닝 중에 divergence(분기?)를 야기함.)
- 더욱이, 우리는 별도의 CNN pose prediction는 불필요하다고 생각한다.
- ?? 대신에 우리는 (DVO를 위한 SLAM에 주로 쓰이는) pose prediction을 위한 **differentiable and deterministic objective를** 사용할 수 있다고 제안한다.

> Contributions: We make the following contributions. First, we characterize theoretically and demonstrate empirically why scale ambiguity in current monocular methods is problematic. Specifically, the problem lies in the scale sensitivity of the depth regularization terms employed during training. Inspired by related work in direct visual odometry [10] we propose a simple normalization strategy that circumvents many of these problems and leads to noticeably improved performance over Zhou et al. [31] (see Fig. 1 for qualitative comparison).

Contribution (1)
- **why scale ambiguity in current monocular methods is problematic** 지에 관하여
  - characterizing theoretically (이론적으로 특징화)
  - demonstrating empirically (경험적으로 논증)
- ?? 문제들은 (training 동안 사용되는) the scale sensitivity of the depth regularization terms 때문이었다.
- DVO에서 영감을 받아, 우리는 더 간단한 표준화 전략을 제안한다. 이 전략은 많은 문제들을 피해갈 수 있고, 엄청난 성능 개선 또한 시킬 수 있다. 

> Second, we suggest that learning an additional pose predicting CNN (which we shall refer to herein as the Pose-CNN) is not the most effective strategy for estimating a depth predicting CNN from monocular video. The Pose-CNN employed by Zhou et al. does not fully exploit the relation between camera pose and depth prediction, and ignores the fact that pose estimation from depth is a well studied problem with known geometric properties and well performing algorithms. We instead propose the incorporation of a Direct Visual Odometry (DVO) [27] pose predictor into our framework as: (i) it requires no learnable parameters, (ii) establishes a direct relationship between the input dense depth map and the output pose prediction, and (iii) it is derived from the same image reconstruction loss used for minimizing our entire unsupervised training pipeline. To incorporate DVO into end-to-end training, we propose a differentiable implementation of the DVO (DDVO module), so that the back-propagation signals reaching the camera pose predictor can be propagated to the depth estimator.

Contribution (2)
- **별도의 pose predicting CNN을 추가하는 것이 depth predicting CNN from monocular video에서 가장 효율적인 방법이 아니었음을 배웠다.**
- Pose-CNN(Zhou꺼)의 문제점
  - camera pose와 depth prediction의 관계를 충분히 활용하지 않음
  - ?? depth로 pose estimation을 하는 것이 알고리즘을 잘 수행한다는 것과 기하학적인 프로퍼티들이 알려져있다는 것을 배운 문제라는 것을 무시한다.
- 아래와 같은 이유로, **우리는 대신 우리의 framework로 DVO와의 결합을** 제안한다.
  - 학습 가능한 parameter를 필요로 하지 않는다.
  - input dense depth map과 output pose prediction 사이의 직접적인 관계를 설립한다.
  - ?? same image reconstruction loss used for minimizing our entire unsupervised training pipeline 에서부터 야기한다.
- DVO를 end-to-end training으로 결합하기 위하여, 우리는 differentiable implementation of the DVO를 제안한다.
- 그럼으로서, camera pose predictor로 도달한 back-propagation 신호들이 depth estimator로 전달되어질 수 있다.

> Finally, since DVO is a second order gradient descent based method, a good initialization point can lead to better solution. So instead of starting our DDVO module from the identity pose, we propose a mixed training procedure - use a pretrained Pose-CNN to provide pose initialization for DDVO during training. We demonstrate empirically that this hybrid method provides better performance compared to training with Pose-CNN or DDVO alone, and achieves comparable results to Gordard et al. [13], which is the state of the art method trained with calibrated binocular pairs on KITTI dataset [12].

Contribution (3)
- since DVO is a second order gradient descent based method, a good initialization point can lead to better solution.
- 그래서 DDVO 모듈을 동일한 위치에서 시작하는 것 대신에, 우리는 복합적인 트레이닝 과정을 제안한다. 그것은 바로, **(트레이닝 중에 DDVO를 위한 pose initialization을 제공하기 위하여) 미리 학습된 Pose-CNN을 사용하는 것.**
- **hybrid method의 장점**
  - Pose-CNN or DDVO를 하나씩만 training하는 것과 비교하여 나은 성능을 제공한다는 것.
  - Gordard꺼 보다, 나은 결과에 도달함. (which is the state of the art method trained with calibrated binocular pairs on KITTI dataset)
  
## 2. Learning depth estimation from videos

### 2.1. Scale ambiguity

### 2.2. Modeling pose predictor

## 3. Differentiable direct visual odometry

### 3.1. Direct visual odometry

### 3.2. Differientiable implementation

## 4. Training loss

## 5. Experiments

### 5.1. Training configurations

### 5.2. Results on KITTI

### 5.3. Results on Make3D

## 6. Discussion

> We have found that, as in monocular SLAM algorithms [10], scale ambiguity has to be taken into account when training with monocular videos. As shown in Fig. 3 this was a missing point in previous approaches training from monocular videos. Additionally, we have shown that Direct Visual Odometry can be used to estimate the relative camera pose between frames instead of learning a predictor with a CNN (Pose-CNN). This strategy needs less parameters to learn and potentially less images to train. We plan to investigate the number of images needed in the future. Finally, we have found that DDVO and Pose-CNN pose prediction modules can be improved with a hybrid architecture of Pose-CNN initialization and DDVO pose refinement giving the best results.
The current major bottleneck for our approach is that we’re not modeling the non-rigidity of the world. As shown in Fig. 6, our current method does not perform well for articulated objects like bikers and pedestrians. Possible future work for this is to incorporate techniques from non-rigid SfM e.g. [18] to the pipeline.

- monocular SLAM 알고리즘 에서, scale ambiguity 는 monocular video로 training을 할 때 고려되어져 왔다.
- 근데 Fig 3에서 보는 것과 같이, 이건 잘못된 관점이다.
- 추가로, 우리는 DVO가 Pose-CNN 대신에 the relative camera pose between frames가 사용되어질 수 있다고 보였다.
- 이런 전략은 학습 시키는 데에 보다 적은 매개변수들을 필요로 하며, 훈련하는 데에도 더 적은 이미지를 필요로 한다.
- 결과적으로, DDVO와 Pose-CNN의 pose predction modules은 향상되어질 수 있다, Pose-CNN의 hybrid architecture와 DDVO pose refinement로.
- 우리 접근의 가장 큰 애로사항은 세상의 non-rigidity를 모델링하지 않고 있다는 것이다. 
- Fig 6.에서 보여지듯, 우리의 현재 방법은 자전거를 타는 사람이나 보행자처럼, articulated objects에서는 잘 작동하지 않는다. 
- 앞으로 작업해야 할만한 일은, incorporating techniques from non-rigid SfM e.g. to the pipeline.
