---
layout: post
current: post
cover: assets/images/mlagents/Unity-Machine-Learning-Agents.jpg
navigation: True
title: 2. Play Tutorial
date: 2022-10-24 18:00:00 +0900
tags: [ml-agents]
class: post-template
subclass: 'post'
author: YeonSeok
---
{% include ml-agents-table-of-contents.html %}

### 작업을 시작하기에 앞서서 정상적으로 기능이 실행되고 학습되는지, 튜토리얼을 실행해서 테스트 하는것을 목표로 합니다. 

<br>
앞서 ML-Agents의 깃을 다운로드 받은 경로에서 
- ./Project/Assets/ML-Agents/Examples/3DBall
- ./Project/Assets/ML-Agents/Examples/SharedAssets
두 폴더를 현재 프로젝트로 복사해 옵니다. 

3D Ball 데모 씬을 로드한 후에 플레이 버튼을 눌러보면 공이 아래로 떨어지지 않게 중십을 잡고 있는 모습을 볼 수 있습니다. 
<img src="/assets/images/mlagents/K-001.png" style="margin-left: false; margin-right: auto; display: block;"/>

3DBall 프리팹 -> Agent GameObject -> Behavior Parameter Script-> Model 속성에 학습된 모델(3DBall-NNModel)이 적용되어 있어서 위와 같이 에이전트가 행동을 하게 됩니다. 
<img src="/assets/images/mlagents/behaviorParameters.png" style="margin-left: false; margin-right: auto; display: block;"/>


먄약, Model이 할당되지 않는다면 ml-agents 및 사용자 입력에 따라 학습을 하게 됩니다. 

현재는 ml-agents가 실행되어 있지 않기 때문에 사용자입력 만 적용됩니다. 
<img src="/assets/images/mlagents/logic.png" style="margin-left: false; margin-right: auto; display: block;"/>

Cmd 창을 열고 프로젝트 경로로 이동한 후, 아래의 명령어를 입력하여 ml-agents 를 실행합니다. 

```
mlagents-learn config/ppo/3DBall.yaml --run-id=first
```

<img src="/assets/images/mlagents/mlagent-run.png" style="margin-left: false; margin-right: auto; display: block;"/>

###### ml-agents 2.0.1 버전에서는 과거의 --train --load 같은 파라메터가 사라졌습니다. 
###### 훈련하기 위해서는 그냥 --run-id 만 입력하면 되고, 이어하려면 --resume 파라메터만 추가하면 됩니다. 
```
--run-id="훈련할이름"
--resume(이어하기)
```

```
INFO [environment.py:204] Listening on port 5004. Start training by pressing the Play button in the Unity Editor.
```

이 메시지가 나오 후에 Unity에서 Play 버튼을 누르면 훈련을 시작하게 됩니다.

<img src="/assets/images/mlagents/result.png" style="margin-left: false; margin-right: auto; display: block;"/>

훈련 종료후 결과는 ./reusult/run-id 폴더에 생성됩니다.

.onnx 파일을 agent 프리팹에서 변경한 후 플레이해서 정상적으로 훈련되었는지 확인해 봅니다. 
  
