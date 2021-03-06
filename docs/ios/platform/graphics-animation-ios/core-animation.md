---
title: Xamarin.ios의 핵심 애니메이션
description: 이 문서에서는 핵심 애니메이션 프레임 워크를 검사 하 여 UIKit에서 고성능, 유체 애니메이션을 사용 하도록 설정 하는 방법 뿐만 아니라 하위 수준 애니메이션 컨트롤에 대해이를 직접 사용 하는 방법을 보여 줍니다.
ms.prod: xamarin
ms.assetid: D4744147-FACB-415B-8155-3A6B3C35E527
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 60bab56440fc7227e14d31875a8b6108cd1a86f3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032487"
---
# <a name="core-animation-in-xamarinios"></a>Xamarin.ios의 핵심 애니메이션

_이 문서에서는 핵심 애니메이션 프레임 워크를 검사 하 여 UIKit에서 고성능, 유체 애니메이션을 사용 하도록 설정 하는 방법 뿐만 아니라 하위 수준 애니메이션 컨트롤에 대해이를 직접 사용 하는 방법을 보여 줍니다._

iOS에는 응용 프로그램의 보기에 대 한 애니메이션 지원을 제공 하기 위한 [*핵심 애니메이션이*](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html) 포함 되어 있습니다.
테이블 스크롤 및 여러 뷰 사이를 살짝 밀기와 같은 iOS의 모든 초소형 애니메이션은 내부적으로 핵심 애니메이션을 사용 하기 때문에 수행 합니다.

핵심 애니메이션 및 핵심 그래픽 프레임 워크를 함께 사용 하 여 멋진 애니메이션 2D 그래픽을 만들 수 있습니다. 실제로 핵심 애니메이션은 3D 공간에서 2D 그래픽을 변환 하 여 놀라운 시네마 환경을 만들 수도 있습니다. 그러나 진정한 3D 그래픽을 만들려면 OpenGL ES와 같은 항목을 사용 하거나, MonoGame와 같은 API로 전환 해야 합니다. 하지만 3D는이 문서의 범위를 벗어났습니다.

<a name="Using_Core_Animation" />

## <a name="core-animation"></a>핵심 애니메이션

iOS에서는 핵심 애니메이션 프레임 워크를 사용 하 여 뷰 간 전환, 슬라이딩 메뉴 및 스크롤 효과와 같은 애니메이션 효과를 만듭니다. 애니메이션을 사용 하는 방법에는 두 가지가 있습니다.

- 뷰 기반 애니메이션 뿐만 아니라 컨트롤러 간의 애니메이션 전환을 포함 하는 [UIKit를 통해](#Using_UIKit_Animation)
- 더 세밀 하 게 제어할 수 있도록 하는 [핵심 애니메이션을 통해](#Using_Core_Animation)직접 계층을 사용할 수 있습니다.

<a name="Using_UIKit_Animation" />

## <a name="using-uikit-animation"></a>UIKit 애니메이션 사용

UIKit는 응용 프로그램에 애니메이션을 쉽게 추가할 수 있도록 하는 몇 가지 기능을 제공 합니다. 내부적으로 핵심 애니메이션을 사용 하지만 뷰 및 컨트롤러 에서만 작동 하도록이를 추상화 합니다.

이 섹션에서는 다음을 비롯 한 UIKit 애니메이션 기능에 대해 설명 합니다.

- 컨트롤러 간 전환
- 뷰 간 전환
- 속성 애니메이션 보기

### <a name="view-controller-transitions"></a>뷰 컨트롤러 전환

 `UIViewController`은 `PresentViewController` 메서드를 통해 뷰 컨트롤러 간을 전환할 수 있는 기본 제공 지원을 제공 합니다. `PresentViewController`를 사용 하는 경우 두 번째 컨트롤러에 대 한 전환은 선택적으로 애니메이션을 적용할 수 있습니다.

예를 들어 첫 번째 `PresentViewController` 컨트롤러의 단추를 터치 하 여 두 번째 컨트롤러를 표시 하는 두 개의 컨트롤러를 사용 하는 응용 프로그램이 있다고 가정 합니다. 두 번째 컨트롤러를 표시 하는 데 사용 되는 전환 애니메이션을 제어 하려면 아래와 같이 [`ModalTransitionStyle`](xref:UIKit.UIModalTransitionStyle) 속성을 설정 하기만 하면 됩니다.

```csharp
SecondViewController vc2 = new SecondViewController {
  ModalTransitionStyle = UIModalTransitionStyle.PartialCurl
};
```

이 경우 다음을 포함 하 여 여러 다른 항목을 사용할 수 있지만 `PartialCurl` 애니메이션이 사용 됩니다.

- `CoverVertical`-화면 아래쪽에서 위쪽으로 슬라이드
- `CrossDissolve` – 이전 보기는 새 보기가 페이드 인 & 페이드 아웃 됩니다.
- `FlipHorizontal`-가로 오른쪽에서 왼쪽으로 대칭 이동 합니다. 해제에서는 전환이 왼쪽에서 오른쪽으로 대칭 이동 됩니다.

전환에 애니메이션 효과를 주려면 `PresentViewController`에 `true`를 두 번째 인수로 전달 합니다.

```csharp
PresentViewController (vc2, true, null);
```

다음 스크린샷은 `PartialCurl` 사례에 대 한 전환을 표시 하는 모습을 보여 줍니다.

 ![](core-animation-images/06-view-transitions.png "This screenshot shows the PartialCurl transition")

### <a name="view-transitions"></a>전환 보기

UIKit는 컨트롤러 간의 전환 외에도 보기 간에 애니메이션 효과를 적용 하 여 뷰 간을 서로 교환할 수 있도록 지원 합니다.

예를 들어 `UIImageView`있는 컨트롤러를 사용 하는 경우 이미지를 누르면 두 번째 `UIImageView`표시 됩니다. 두 번째 이미지 뷰로 전환 하기 위해 이미지 뷰의 슈퍼 뷰에 애니메이션 효과를 주려면 `UIView.Transition`를 호출 하는 것 만큼 간단 하 고 아래와 같이 `toView` 및 `fromView`를 전달 합니다.

```csharp
UIView.Transition (
  fromView: view1,
  toView: view2,
  duration: 2,
  options: UIViewAnimationOptions.TransitionFlipFromTop |
    UIViewAnimationOptions.CurveEaseInOut,
  completion: () => { Console.WriteLine ("transition complete"); });
```

또한 `UIView.Transition`는 애니메이션 실행 시간을 제어 하는 `duration` 매개 변수를 사용 하 고 사용 하는 애니메이션 및 감속/가속 함수와 같은 항목을 지정 하는 [`options`](xref:UIKit.UIViewAnimationOptions) 를 사용 합니다. 또한 애니메이션이 완료 될 때 호출 되는 완료 처리기를 지정할 수 있습니다.

아래 스크린샷은 `TransitionFlipFromTop` 사용 될 때 이미지 뷰 간의 애니메이션 전환을 보여줍니다.

 ![](core-animation-images/07-animated-transition.png "This screenshot shows the animated transition between the image views when TransitionFlipFromTop is used")

### <a name="view-property-animations"></a>속성 애니메이션 보기

UIKit는 다음을 포함 하 여 `UIView` 클래스에서 다양 한 속성에 대 한 애니메이션 효과를 무료로 지원 합니다.

- 프레임
- 범위
- 가운데 맞춤
- 알파
- 변형
- Color

이러한 애니메이션은 정적 `UIView.Animate` 메서드에 전달 된 `NSAction` 대리자의 속성 변경 내용을 지정 하 여 암시적으로 발생 합니다. 예를 들어 다음 코드는 `UIImageView`중심점에 애니메이션을 적용 합니다.

```csharp
pt = imgView.Center;

UIView.Animate (
  duration: 2, 
  delay: 0, 
  options: UIViewAnimationOptions.CurveEaseInOut | 
    UIViewAnimationOptions.Autoreverse,
  animation: () => {
    imgView.Center = new CGPoint (View.Bounds.GetMaxX () 
      - imgView.Frame.Width / 2, pt.Y);},
  completion: () => {
    imgView.Center = pt; }
);
```

그러면 다음과 같이 이미지가 화면 위쪽에서 앞뒤로 애니메이션 효과를 줍니다.

 ![](core-animation-images/08-animate-center.png "An image animating back and forth across the top of the screen as the output")

`Transition` 메서드와 마찬가지로 `Animate`는 감속/가속 함수와 함께 기간을 설정할 수 있습니다. 또한이 예제에서는 애니메이션을 값에서 초기 값으로 다시 애니메이션 효과를 적용 하는 `UIViewAnimationOptions.Autoreverse` 옵션을 사용 했습니다. 그러나이 코드는 완료 처리기에서 `Center`를 초기 값으로 다시 설정 합니다. 애니메이션은 시간이 지남에 따라 속성 값을 보간 속성의 실제 모델 값은 항상 설정 된 최종 값입니다. 이 예제에서 값은 슈퍼 뷰의 오른쪽 근처에 있는 지점입니다. `Center`를 초기 지점으로 설정 하지 않으면 (설정 `Autoreverse`로 인해 애니메이션이 완료 됨) 아래와 같이 애니메이션이 완료 된 후 이미지가 오른쪽으로 다시 맞춰집니다.

 ![](core-animation-images/09-animation-complete.png "Without setting the Center to the initial point, the image would snap back to the right side after the animation completes")

## <a name="using-core-animation"></a>핵심 애니메이션 사용

 `UIView` 애니메이션은 많은 기능을 허용 하므로 구현 용이성 때문에 가능 하면 사용 해야 합니다. 앞서 언급 했 듯이 UIView 애니메이션은 핵심 애니메이션 프레임 워크를 사용 합니다. 그러나 뷰를 사용 하 여 애니메이션 효과를 낼 수 없는 추가 속성에 애니메이션을 적용 하거나 비선형 경로를 따라 보간 하는 등의 `UIView` 애니메이션으로는 일부 작업을 수행할 수 없습니다. 더 세밀 하 게 제어 해야 하는 경우에도 핵심 애니메이션을 직접 사용할 수 있습니다.

### <a name="layers"></a>레이어에

핵심 애니메이션을 사용할 때 애니메이션은 `CALayer`형식의 *레이어*를 통해 발생 합니다. 계층은 뷰 계층 구조가 있는 것과 매우 유사 하 게 계층 계층 구조가 있음을 나타내는 뷰와 개념적으로 유사 합니다. 실제로 뷰는 사용자 상호 작용에 대 한 지원을 추가 하는 뷰를 사용 하 여 다시 표시 합니다. 뷰의 `Layer` 속성을 통해 뷰의 계층에 액세스할 수 있습니다. 실제로 `UIView`의 `Draw` 메서드에 사용 되는 컨텍스트는 실제로 계층에서 만들어집니다. 내부적으로 `UIView`를 지 원하는 계층에는 `Draw`를 호출 하는 뷰 자체에 대 한 대리자가 설정 되어 있습니다. 따라서 `UIView`로 그리면 실제로 해당 계층으로 그리기를 합니다.

레이어 애니메이션은 암시적 이거나 명시적 일 수 있습니다. 암시적 애니메이션은 선언적입니다. 변경 해야 하는 계층 속성을 선언 하기만 하면 애니메이션은 작동 합니다. 반면에 명시적 애니메이션은 레이어에 추가 된 애니메이션 클래스를 통해 생성 됩니다. 명시적 애니메이션은 애니메이션 생성 방법을 추가로 제어할 수 있습니다. 다음 섹션에서는 암시적 및 명시적 애니메이션에 대해 자세히 설명 합니다.

### <a name="implicit-animations"></a>암시적 애니메이션

계층의 속성에 애니메이션 효과를 주는 한 가지 방법은 암시적 애니메이션을 통하는 것입니다. `UIView` 애니메이션은 암시적 애니메이션을 만듭니다. 그러나 계층에 대해서도 직접 암시적 애니메이션을 만들 수 있습니다.

예를 들어 다음 코드는 이미지에서 계층의 `Contents`를 설정 하 고, 테두리 너비와 색을 설정 하 고, 계층을 뷰 계층의 하위 계층으로 추가 합니다.

```csharp
public override void ViewDidLoad ()
{
  base.ViewDidLoad ();

  layer = new CALayer ();
  layer.Bounds = new CGRect (0, 0, 50, 50);
  layer.Position = new CGPoint (50, 50);
  layer.Contents = UIImage.FromFile ("monkey2.png").CGImage;
  layer.ContentsGravity = CALayer.GravityResize;
  layer.BorderWidth = 1.5f;
  layer.BorderColor = UIColor.Green.CGColor;

  View.Layer.AddSublayer (layer);
}
```

계층에 대 한 암시적 애니메이션을 추가 하려면 단순히 `CATransaction`의 속성 변경 내용을 래핑합니다. 이렇게 하면 아래와 같이 `BorderWidth` 및 `BorderColor`와 같이 뷰 애니메이션으로 애니메이션 효과 되지 않는 속성에 애니메이션을 적용할 수 있습니다.

```csharp
public override void ViewDidAppear (bool animated)
{
  base.ViewDidAppear (animated);

  CATransaction.Begin ();
  CATransaction.AnimationDuration = 10;
  layer.Position = new CGPoint (50, 400);
  layer.BorderWidth = 5.0f;
  layer.BorderColor = UIColor.Red.CGColor;
  CATransaction.Commit ();
}
```

또한이 코드는 계층의 `Position`에 애니메이션 효과를 적용 합니다. 계층의 앵커 포인트는 슈퍼 계층 좌표의 왼쪽 위에서 측정 된 위치입니다. 계층의 앵커 지점은 계층 좌표계 내의 정규화 된 점입니다.

다음 그림은 위치 및 앵커 지점을 보여 줍니다.

 ![](core-animation-images/10-postion-anchorpt.png "This figure shows the position and anchor point")

예제를 실행 하면 `Position``BorderWidth` 및 `BorderColor` 다음 스크린샷에 표시 된 것 처럼 애니메이션 효과가 적용 됩니다.

 ![](core-animation-images/11-implicit-animation.png "When the example is run, the Position, BorderWidth and BorderColor animate as shown")

### <a name="explicit-animations"></a>명시적 애니메이션

암시적 애니메이션 외에도 핵심 애니메이션에는 계층에 명시적으로 추가 되는 애니메이션을 캡슐화 할 수 있는 `CAAnimation`에서 상속 되는 다양 한 클래스가 포함 되어 있습니다. 애니메이션의 시작 값을 수정 하 고, 애니메이션을 그룹화 하 고, 비선형 경로를 허용 하기 위해 키 프레임을 지정 하는 등 애니메이션을 보다 세부적으로 제어할 수 있습니다.

다음 코드에서는 앞에 표시 된 계층에 대 한 `CAKeyframeAnimation` (암시적 애니메이션 섹션)를 사용 하는 명시적 애니메이션의 예를 보여 줍니다.

```csharp
public override void ViewDidAppear (bool animated)
{
  base.ViewDidAppear (animated);
  
  // get the initial value to start the animation from
  CGPoint fromPt = layer.Position;
  
  /* set the position to coincide with the final animation value
  to prevent it from snapping back to the starting position
  after the animation completes*/
  layer.Position = new CGPoint (200, 300);
  
  // create a path for the animation to follow
  CGPath path = new CGPath ();
  path.AddLines (new CGPoint[] { fromPt, new CGPoint (50, 300), new CGPoint (200, 50), new CGPoint (200, 300) });
  
  // create a keyframe animation for the position using the path
  CAKeyFrameAnimation animPosition = (CAKeyFrameAnimation)CAKeyFrameAnimation.FromKeyPath ("position");
  animPosition.Path = path;
  animPosition.Duration = 2;
  
  // add the animation to the layer.
  /* the "position" key is used to overwrite the implicit animation created
  when the layer positino is set above*/
  layer.AddAnimation (animPosition, "position");
}
```

이 코드는 키 프레임 애니메이션을 정의 하는 데 사용 되는 경로를 만들어 계층의 `Position`를 변경 합니다. 계층의 `Position`는 애니메이션에서 `Position`의 최종 값으로 설정 됩니다. 이를 사용 하지 않으면 애니메이션은 표시 값만 변경 하 고 실제 모델 값은 변경 하지 않기 때문에 애니메이션 이전에 `Position`로 갑자기 돌아갑니다. 모델 값을 애니메이션에서 최종 값으로 설정 하면 레이어가 애니메이션의 끝 부분에 그대로 유지 됩니다.

다음 스크린샷에서는 지정 된 경로를 통해 애니메이션 효과를 주는 이미지를 포함 하는 계층을 보여 줍니다.

 ![](core-animation-images/12-explicit-animation.png "This screenshot shows the layer containing the image animating through the specified path")

## <a name="summary"></a>요약

이 문서에서는 *핵심 애니메이션* 프레임 워크를 통해 제공 되는 애니메이션 기능을 살펴보았습니다. UIKit에서 애니메이션을 구동 하는 방법과 하위 수준 애니메이션 컨트롤에 대해 직접 사용할 수 있는 방법을 모두 보여 주는 핵심 애니메이션을 검사 했습니다.

## <a name="related-links"></a>관련 링크

- [핵심 애니메이션 샘플](https://docs.microsoft.com/samples/xamarin/ios-samples/graphicsandanimation)
- [핵심 그래픽](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [그래픽 및 애니메이션 연습](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [핵심 애니메이션](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
