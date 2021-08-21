# 이미지 최적화 방법론

웹 화면에 렌더링을 빠르게 하기 위해선, 이미지의 리소스 최적화가 반드시 필요하다. 이미지를 최적화 하는 방법으로 4가지의 방법론을 소개하고자 한다.

- 최적화된 이미지 `포맷`을 사용하라.

- `<img>`에 `width, height` 값을 선언해 `Reflow`를 방지하라.

- `CSS Sprite` 기법을 사용하라.

- `lazy loading`을 활용하라.



<br>

---

<br>

## 이미지 포맷
웹 브라우저에 최적화된 이미지를 찾기 위해 AVIF, WebP, JPEG를 비교한 사례에 대해서 가져왔다.

<br>

### JPEG, WebP, AVIF

**JPEG**는 'Joint Photographic Experts Group' 의 약자이며 확장자는 '.jpg' 또는 '.jpeg' 이다. 
JPEG는 손실이 많은 압축 디지털 이미지를 만드는 데 사용할 수 있는 압축 방법이다. 크기와 품질 사이에서 절충하기 위해 압축 정도를 선택할 수 있다.

**WebP**는 구글이 2010년에 발표한 포맷으로 파일 크기를 줄이기 위해 손실 없는 압축과 무손실 압축을 모두 사용하고 있다. 웹사이트의 트래픽 감소 및 로딩 속도 단축을 겨냥한 것으로, 주로 사진 이미지 압축 효과가 높은 것으로 알려졌다. 

**AVIF**는 AOMedia에서 개발한 이미지 포맷으로 손실 압축과 비 손실 압축을 전부 지원하기 때문에 WebP처럼 GIF, PNG, JPEG 등의 상용 이미지 포맷을 대체할 수 있다. 또한 애니메이션 기능이 있어 움짤로 쓸 수 있고, 압축 효율이 뛰어나다는 점에서 WebP를 닮았다.

<br>

### JPEG vs WebP vs AVIF
이미지 포맷 비교 테스트는 가장 인기 있는 이미지 500개를 선택하여 원본 형식(PNG/JPEG), WebP 및 최종 AVIF출력 형식으로 진행하였다.

결과는 다음과 같다.

- 93% 경우 AVIF 이미지가 WebP 이미지보다 작았다.
- 평균적으로 AVIF 이미지는 WebP 이미지보다 47% 작았다.
- WebP 이미지는 99%의 경우 최적화된 PNG/JPEG 이미지보다 작았다.


한가지 구체적인 예를 살펴보면 원본 이미지는 다음과 같다.

![Racing car](./css/images/compareimg.jpeg)


| Format | Size |
| :------: | :----: |
| JPEG | 101Kb |
| WebP | 66kb |
| AVIF | 33kb |


이러한 결과를 통해 다음과 같은 제안을 할 수 있다.

**브라우저가 AVIF를 지원하면 AVIF를 사용하고, 그렇지 않은 경우 WebP, 그렇지 않은 경우 PNG 또는 JPEG의 사용이다.**



<br>


### HTML에서 사용법

```html
<picture>
    <source srcset="supercar.avif" type="image/avif">
    <source srcset="supercar.webp" type="image/webp">
    <img src="supercar.jpeg" alt="Fast red car"/>
</picture>
```

> 다음과 같이 `picture` 태그와 `source`태그, `img`태그를 이용하여 avif파일 및 webp파일을 지원하지 않는 경우에 jpeg파일을 사용하게 할 수 있다.

<br>

---

<br>


## 이미지 고정값

### Reflow
어떠한 액션이나 이벤트에 의해 DOM요소의 크기나 위치 등을 변경하면 해당 노드의 하위 노드와 상위의 노드들을 포함하여 Layout 단계를 다시 수행하게 된다. 변경하려는 특정 요소의 위치와 크기뿐만 아니라 연관된 요소들의 위치와 크기도 재계산을 하기 때문에 브라우저의 퍼포먼스를 저하시키는 요인이다.

### Reflow를 발생시키는 원인(치수가 없는 이미지) 해결법

치수가 없는 이미지들은 Reflow를 발생시켜 퍼포먼스를 저하시키기 때문에 이를 해결하기 위해 이미지 및 비디오 요소에 `width`와 `height`속성을 항상 포함하거나 또는 CSS를 사용하여 필요한 공간 `aspect-ratio`를 잡는다. 이 방법을 사용하면 이미지가 로드되는 동안 브라우저가 문서의 공간을 올바르게 할당할 수 있다. 

```html
<img src="puppy.jpg" width="640" height="360" alt="Puppy with balloons">
```

> 다음의 방식으로 reflow와 re-layout을 최소화할 수 있다.

<br>

반응형 웹 디자인의 경우 width와 height를 생략하고 CSS를 사용하여 이미지 크기를 조정하기 시작하였는데 

```css
img {
  width: 100%; /* or max-width: 100%; */
  height: auto;
}
```

이 접근 방식의 단점은 다운로드가 시작되고 브라우저가 크기를 결정할 수 있는 경우에만 이미지를 위한 공간을 할당할 수 있다는 점이다. 이미지가 로드되어 각 이미지가 화면에 나타나면 reflow 되어 텍스트가 갑자기 화면 아래로 튀어나가는 등의 문제가 발생하였는데 이것을 방지하기 위해 `aspect-ratio`를 사용한다.

이 `aspect-ratio`속성을 사용하면 복잡한 계산 없이 간단하게 속성으로 레이아웃 이동 방지를 할 수 있다.

<br>

---

<br>


## Css Sprite
페이지의 첫 로딩 속도를 줄여주는 여러 방법 중에서도 서버로의 요청 횟수를 최소화 하는 것은 최적화 요소중에서도 중요하며, 이 중 CSS sprites는 실제 적용하기에 아주 손쉬운 방법중 하나이다.

웹페이지에 필수적으로 자주 사용되는 아이콘, 버튼 같은 이미지들을 쓸 때마다 여러 이미지들을 불러오는 것이 아니라, 한 이미지 파일로 통합한 후 배경 이미지로 만들어 놓고 `postiion`값으로 각각의 이미지를 불러오는 것이 `css sprite`기법 이다.

이러한 방식으로 이미지를 불러오게 되면 트래픽이 절약되고 HTTP요청 횟수를 줄여 빠른 웹 브라우징을 할 수 있게 된다.

### CSS에서의 사용법

```css
div#sprite { background:url(/images/sprite.png) no-repeat; } //한 이미지를 불러옴
 
// position으로 각 이미지를 불러옴
div#sprite > .first { background-position:0 0; }
div#sprite > .second { background-position:0 -15px; }
div#sprite > .third { background-position:0 -30px; }
```

> 다음과 같이 한 이미지를 불러와서 `background-position`값을 조절하여 원하는 이미지를 가져오는 것이 CSS Sprite 기법이다. 



<br>

---

<br>


## Lazy loading

<br>

---

<br>


## 참고 사이트

- [WebP는 정말 JPG보다 뛰어날까? 최신 이미지 파일 3종 비교](https://techit.kr/view/?no=20200701213803)

- [Is WebP really better than JPEG?](https://siipo.la/blog/is-webp-really-better-than-jpeg)

- [A practical comparison of image formats for the web. JPEG vs WebP vs AVIF.](https://fronius.me/articles/2020-10-14-comparing-image-formats-jpg-webp-avif.html)

- [WebP and AVIF: Image File Formats You Need to Know About](https://creativepro.com/webp-and-avif-file-formats-you-need-to-know-about/)

- [Load Your Images Faster with the Next-Gen AVIF Format](https://pixboost.com/blog/next-gen-avif-format/)

- [Google 엔지니어가 말하는 "웹 사이트의 이미지 가져오기"를 최적화하는 방법](https://nerowang.tistory.com/995)

- [새로운 웹페이지 성능 측정 지표 CLS(Cumulative Layout Shift)](https://wit.nts-corp.com/2020/12/28/6240)

- [CSS Sprite(스프라이트) 기법이란?](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=goodleedw&logNo=221470292886)

- [CSS sprites 기법](https://dev.eyegood.co.kr/entry/HTML5-CSS3-CSS-sprites-%EA%B8%B0%EB%B2%95)