---

title : mediapipe-model-maker retraining시 문제해결

  

date : 2024-08-21 13:00:00 +09:00

  

categories : [Python, mediapipe]

  

tags : [tips]

  

---

  

mediapipe-model-maker retraining시 문제해결

    Received incompatible tensor with shape (5,) when attempting to restore variable with shape (3,) and name dense_1/bias:0.

이전에 작업한 checkpoint가 남아있어서 발생. 해당 폴더 제거시 정상 작동
