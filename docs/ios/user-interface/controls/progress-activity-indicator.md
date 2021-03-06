---
title: Xamarin.ios의 진행률 및 작업 표시기
description: 이 문서에서는 Xamarin.ios에서 진행률 및 작업 표시기를 사용 하는 방법을 설명 합니다. 프로그래밍 방식으로 및 스토리 보드를 사용 하 여이를 사용 하는 방법을 설명 합니다.
ms.prod: xamarin
ms.assetid: 7AA887E4-51F7-4867-82C5-A8D2EA48AE07
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/11/2017
ms.openlocfilehash: 76e1ee54a5e1b729fdcb0b0a2c1f278703b2b4d6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021964"
---
# <a name="progress-and-activity-indicators-in-xamarinios"></a>Xamarin.ios의 진행률 및 작업 표시기

앱에서 데이터 로드 또는 처리와 같은 장기 실행 작업을 수행 해야 할 수 있으며,이 지연으로 인해 UI 업데이트가 지연 될 수 있습니다. 이 시간 동안에는 항상 진행률 표시기를 사용 하 여 시스템에서 작업을 수행 하는 데 사용 중인 사용자를 reassure 합니다. 이렇게 하면 앱이 요청에 대해 작업 하 고 있는 사용자 정의 컨트롤을 제공 하 고, 입력을 기다리지 않으며, 대기 해야 하는 시간을 정확 하 게 자세히 설명 하는 방법을 제공할 수 있습니다.

iOS는 앱에서 이러한 진행률 표시를 제공 하는 두 가지 주요 방법인 활동 표시기 (특정 _네트워크_ 활동 표시기 포함) 및 진행률 표시줄을 제공 합니다.

## <a name="activity-indicator"></a>작업 표시기

앱에서 긴 프로세스를 실행 하는 경우 작업 표시기가 표시 되어야 하지만 작업에 필요한 정확한 시간을 알 수 없습니다.

Apple에는 활동 표시기를 사용 하기 위한 다음과 같은 제안이 있습니다.

- **가능 하면 언제 든 지 진행률** 표시줄을 사용 합니다. 작업 표시기는 실행 중인 프로세스에 대 한 피드백을 사용자에 게 제공 하지 않으며, 길이가 아는 경우 (예: 파일에서 다운로드할 바이트 수) 항상 진행률 표시줄을 사용 합니다.
- **표시기를 애니메이션으로 유지** -사용자가 고정 된 작업 표시기를 지연 된 앱에 연결 하므로 표시 되는 동안 항상 표시기가 애니메이션 되도록 해야 합니다.
- **처리 중인 작업에 대해 설명** 합니다. 즉, 작업 표시기를 단독으로 표시 하는 것 만으로는 사용자가 대기 중인 프로세스에 대해 알고 있어야 합니다. 작업을 명확 하 게 정의 하는 의미 있는 레이블 (일반적으로 단일 전체 문장)을 포함 합니다.

### <a name="implementing-an-activity-indicator"></a>활동 표시기 구현

활동 표시기는 [`UIActivityIndictorView`](xref:UIKit.UIActivityIndicatorView) 클래스를 통해 구현 되어 `UIActivity` 발생 함을 나타냅니다.

### <a name="activity-indicators-and-storyboards"></a>활동 표시기 및 스토리 보드

IOS Designer를 사용 하 여 UI를 만드는 경우 도구 상자에서 활동 표시기를 레이아웃에 추가할 수 있습니다. Properties Pad에서 다음 속성을 조정할 수 있습니다.

![Properties Pad](progress-activity-indicator-images/progress-indicator1.png)

### <a name="managing-activity-indicator-behavior"></a>활동 표시기 동작 관리

`StartAnimating()` 및 `StopAnimating()` 메서드를 사용 하 여 작업 표시기 애니메이션을 시작 하 고 중지할 수 있습니다.

`StopAnimating()` 호출 된 후 작업 표시기가 사라지게 하려면 `HidesWhenStopped` 속성을 `true`로 설정 합니다. 이는 기본적으로 `true`로 설정 됩니다. 언제 든 지 `IsAnimating` 속성을 확인 하 여 활동 표시기가 회전 하는 애니메이션을 실행 하 고 있는지 확인할 수 있습니다. 

### <a name="managing-activity-indicator-appearances"></a>작업 표시기 모양 관리

작업 표시기를 인스턴스화할 때 `UIActivityIndicatorViewStyle` 열거형을 매개 변수로 전달할 수 있습니다. 이를 사용 하 여 비주얼 스타일을 `Gray`, `White`또는 `WhiteLarge`로 설정할 수 있습니다. 예를 들면 다음과 같습니다.

```csharp
activitySpinner = new UIActivityIndicatorView(UIActivityIndicatorViewStyle.WhiteLarge);
```

`Color` 속성을 설정 하 여 `UIActivityIndicatorViewStyle`에서 제공 하는 색을 재정의할 수 있습니다.

## <a name="progress-bar"></a>Progress Bar

진행률 표시줄은 시간이 많이 걸리는 작업의 상태 및 길이를 나타내기 위해 색으로 채워진 선으로 나타납니다. 작업의 길이가 알거나 계산할 수 있는 경우에는 항상 진행률 표시줄을 사용 해야 합니다.

Apple에는 진행률 표시줄을 사용 하기 위한 다음과 같은 제안이 있습니다.

- **정확히 보고 진행률** -진행률 표시줄은 작업을 완료 하는 데 필요한 시간을 정확 하 게 표시 해야 합니다. 앱을 사용 중으로 표시 하는 시간을 조작할 수 없습니다.
- **잘 정의 된 기간에 사용** -진행률 표시줄은 시간이 오래 걸리는 작업을 표시 하는 것 뿐만 아니라 완료 된 작업의 양과 남은 시간을 보여 줍니다.

### <a name="implementing-an-progress-bar"></a>진행률 표시줄 구현

진행률 표시줄은를 인스턴스화하여 생성 됩니다 [`UIProgressView`](xref:UIKit.UIProgressView)

### <a name="progress-bars-and-storyboards"></a>진행률 표시줄 및 Storyboard

IOS Designer를 사용 하는 경우 UI에 진행률 표시줄을 추가할 수도 있습니다. **도구 상자** 에서 **진행률 보기** 를 검색 하 여 보기로 끌어 옵니다.

속성 패드에서 다음 속성을 조정할 수 있습니다.

![Properties Pad](progress-activity-indicator-images/progress-indicator3.png)

### <a name="managing-progress-bar-behavior"></a>진행률 표시줄 동작 관리

`Progress` 속성을 사용 하 여 표시줄의 진행률을 처음으로 설정할 수 있습니다.

```csharp
ProgressBar.Progress = 0f;
```

`SetProgress` 메서드를 사용 하 여 진행률을 조정 하 고 변경 내용을 애니메이션에 적용 하거나 적용 하지 않으려면 부울 선언을 전달할 수 있습니다.

```csharp
ProgressBar.SetProgress(1.0f, true);
```

진행률 표시줄 사용에 대 한 자세한 내용은 [보고 진행률](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/networking/download_progress) 조리법 및 [UICatalog tvOS 샘플](https://docs.microsoft.com/samples/xamarin/ios-samples/tvos-uicatalog)을 참조 하세요.

### <a name="managing-progress-bar-appearance"></a>진행률 표시줄 모양 관리

작업 표시기와 마찬가지로 진행률 표시줄을 인스턴스화할 때 `UIProgressViewStyle` 열거형을 매개 변수로 전달할 수 있습니다.

진행률 및 트랙 이미지와 색조 색은 다음 속성을 사용 하 여 조정할 수 있습니다.

```csharp
progressBar = new UIProgressView(UIProgressViewStyle.Default)
            {
                ProgressImage = UIImage.FromBundle("TrackImage"),
                ProgressTintColor = UIColor.Cyan,
                TrackImage = UIImage.FromBundle("TrackImage"),
                TrackTintColor = UIColor.Magenta
            }; 
```
