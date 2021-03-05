# simple_object_recognition

## 단순한 물체 인식 구현 프로젝트

### 기본 개념
* Thresholding 
   * object를 구분하기 위해 이미지의 픽셀 값을 가장 단순한 형태인 0과 1의 값으로 변환하는 과정이다.
   * 어떤 기준 (임계값=Threshold)으로 0/1 (배경/물체)로 나눌지 정한다.

* Erosion, Dilation, Opening, Closing (Morphological)
   * Thresholding 과정에서 배경과 물체가 제대로 구분이 되지 않는 문제가 발생한다.
   * 이 경우, 노이즈를 제거하거나 object 내부의 구멍을 메우기 위해 확장하거나 축소한다.
   * 형태학적(Morphological)인 연산이며 Erosion은 흰색 오브젝트의 외곽 픽셀을 0(검은색)으로 만들어 침식시키고,
   * Dilation은 흰색 object의 외곽 픽셀 주변에 1(흰색)으로 추가하여 확장한다.
   * Opening은 Erosion후 Dilation하여 이미지상의 노이즈를 제거, Closing은 Dilation후 Erotion하여 object의 구멍을 메꾼다.
 
![image](https://user-images.githubusercontent.com/57992071/110110953-e99efb00-7df2-11eb-9d1f-a7f9cf922fcb.png) Erosion, Dilation
![image](https://user-images.githubusercontent.com/57992071/110110978-ef94dc00-7df2-11eb-8762-e162360ad331.png) Opening, Closing

* Count Features
  * 한 이미지에 여러 개의 object가 존재할 경우 object 별로 측정 및 분류를 하기위해 ID를 부여하는 작업이다.
  * 현재 탐색중인 픽셀의 주변에 ID가 부여된 픽셀이 없다면 새로운 오브젝트로 판정하고 새 ID를 픽셀에 부여한다.
  * 현재 탐색중인 픽셀의 주변에 ID가 부여된 픽셀이 있을 때, 현재 탐색중인 픽셀과 어떻게 인접해 있는 가에 따라 이웃 픽셀의 ID를 가져오거나, ID를 재배치한다.

* Measurement
  * 오브젝트를 구분하기 위해 필요한 각종 값(넓이, 최대 길이, 둘레 등)을 측정하는 작업이다.

* Classification
  * 기존에 미리 분류해둔 오브젝트의 측정 값을 사용해서 분류되지 않은 오브젝트를 분류하는 작업이다.

---

### 실험 과정

#### 0. 촬영 환경 세팅
* 학교에서 진행했던 프로젝트이며, 같은 위치에서 찍기 위해 상자에 구멍을 뚫어 환경을 만들었다.
* 초반 물체 선택에 있어서 아무거나 골랐지만, 추후 흰 배경에 검은 물체 위주로 선택하였다.

![image](https://user-images.githubusercontent.com/57992071/110107952-eb66bf80-7dee-11eb-966c-5434c58de73c.png)


#### 1. 분류하고자 하는 물체 사진 촬영 & 이미지 불러오기 : 많을 수록 구분하기 좋다.

![image](https://user-images.githubusercontent.com/57992071/110105531-da687f00-7deb-11eb-98be-4d2a90da9c1e.png)

#### 2. Threshold & Closing, Opening 적용
* 내부에서 Grayscale 이미지로 변환된 다음, Minimum Fuzziness Method로 Threshold를 처리
* Closing & Opening을 적용해서 구멍을 메우고, 오브젝트 주변의 노이즈를 제거한다.

![image](https://user-images.githubusercontent.com/57992071/110105654-01bf4c00-7dec-11eb-8ce8-0e4c3fdd23bb.png)

#### 3. Contour Features & Measurement
* Contours를 통해 이미지의 물체 테두리를 탐색하고 각 오브젝트마다 Area, Perimeter, Formfactor 값을 측정한다.

![image](https://user-images.githubusercontent.com/57992071/110106042-83af7500-7dec-11eb-8b02-19b2ef275dcf.png)

#### 4. Training Data Load & Plot
* 측정된 값을 통해 오브젝트를 비교할 척도를 결정한다.
* 이 데이터에서는 Perimeter와 Formfactor를 통한 오브젝트의 분류가 가장 적합하다.
* 각 오브젝트 당 3개씩으로 분포를 확인하여도 잘 분류됨을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/57992071/110106127-a2157080-7dec-11eb-9740-046323accc3c.png)

#### 5. Classification & Results
* 기존에 분류되어 있던 오브젝트의 성분 값의 평균을 구하고 현재 분류하려는 오브젝트의 성분 값과의 거리를 계산하여, 가장 가까운 거리를 가진 쪽으로 분류한다.
* 최종적으로, 다른 이미지를 추가하였을 때에도 오브젝트를 잘 구분할 수 있다.

![image](https://user-images.githubusercontent.com/57992071/110106224-c07b6c00-7dec-11eb-83bd-55e31fdc9817.png)

![image](https://user-images.githubusercontent.com/57992071/110106435-06d0cb00-7ded-11eb-8706-86ee03dea121.png)

![image](https://user-images.githubusercontent.com/57992071/110106461-0df7d900-7ded-11eb-9cd2-7fb240c626ef.png)

## 부족한 부분
* 실시간 영상처리
  * 웹캠을 통해 영상 값을 실시간으로 적용하지 못한 점이 아쉬운 부분이다.
* 환경 세팅
  *	상자에 구멍을 뚫어 오브젝트의 영상을 촬영하는 방식은 물체를 자유롭게 바꾸는 것이 불가능하였다. 그래서 실시간 영상 촬영이 불가하였기에 프로젝트를 하는데 미흡한 점이다.
