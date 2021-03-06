# 강의 

### 1. transformer self-attention 계산

* QK 연산후 분산이 늘어나므로 √K차원으로 나눠준다. 분산이 크면 softmax의 결과가 하나로 쏠리게 된다.

* ...

### 2. MHA

* attention head를 여러번 반복해서 concat하는 방식. -> 실제 구현에서는 Q,K,V차원을 head의 수만큼 나눠서 계산 후 linear 변환을 거치는 것으로 한다.

* GPU사용량이 높고, 계산은 한번이라 빠르다.(RNN처럼 반복안하므로)

* Add & Norm 부분  
  MHA 다음에 해당하는 부분이다.  
  Add 는 residual 을 뜻한다.  
  Norm 은 layer normalization이다.  
  
  * Affine transformation  
    여기서는 최적화된 평균, 분산을 찾도록 사용한다. y = 2x + 3
  
  * batch norm  
    한 단어(row)별 대상으로 Affine transformation
  
  * layer norm   
    단어들의 차원별을 대상으로 Affine transformation
    
  * batch, layer norm 전에 평균0, 분산1로 만드는 (z score?) 작업을 한다.

* Feed forward 는 fully connected layer를 뜻한다.

* MHA구조도 위로 쌓을 수 있다. num_layers.

### 3. positional encoding

* I go home 이나 home go I 이나 같은 단어의 인풋의 결과는 똑같을 것이다. 순서가 고려되지 않는 것이다.

* 벡터에 순서정보를 남기는 방법으로써 사용된다.

* embedding vector에 값을 추가한다. (encoder, decoder 둘다사용)

* 더하는 상수값은 sin cos ???

### 4. learning rate

* 처음에는 가파른 지점에 있을 수 있으므로 lr을 작게,  
  그 후 완만한 지점에서는 lr를 크게한다.  
  최저점에 도달할 수 록 lr를 다시 줄인다.

### 5. Decoder  

* 첫번째 sublayer -> masked MHA
  * 디코더로 오는 input을 encode한다. (= hidden state를 구하는 과정과 같은 개념)
  
  * query 벡터를 만든다 (여기는 self-attetion으로 계산)  
  
  * masked MHA  
    행렬상에서 현재 단어보다 뒤에 있는 단어에 해당하는 부분을 0으로 만든다.(구현시 softmax넣기전에 실행)  
    그 후 다시 합이 1이 되도록 normalization

* 두번째 sublayer -> MHA
  query는 이전 layer에서 가져오고 key, value 벡터는 마지막 인코더에서 가져온 후 계산한다.

* 세번째 sublayer -> Feed Forward
  
# 하이퍼파라미터

* 한정된 자원에서의 방법.

* random search가 grid search에 비해 좋다. 
  random search???

* 제일 중요한 파라미터는 learning rate
  그 다음 batch size

* 한번에 하는 그래디언트가 더 낫다.
* random seed 마다도 결과가 다르다. 운영체제, 하드웨어 차이도 고려.
* validation 횟수??

* 순서
1. lr스케쥴 고정후 train
2. 초반부터(early stage) 좋은 결과의 파라미터들이 보통 좋다.
3. training 러닝 커브를 보고 어느 정도 문제점을 예측한다. lr 우선
4. validation 러닝 커브도 함께 본다.

* 비슷한 모델들의 레퍼런스 참고. 

* 기본모델 선택
  * 많이 사용된것, 다양하게 사용된것, 너무 최신말고 검증된 것, 수정하기 용이한 것이 좋다.
    벤치만큼 안나온다.
