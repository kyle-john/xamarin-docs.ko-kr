---
title: IOS 특정 형식 추가
description: 이 문서에서는 Xamarin.ios 사용자 지정 렌더러를 사용 하지 않고 iOS 관련 모양을 설정 하는 방법을 설명 합니다.
ms.prod: xamarin
ms.assetid: CE50E207-D092-4D88-8439-1B51F178E7ED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2016
ms.openlocfilehash: 24d86c54ea4b346e1c165b28c6b62f5a98390d64
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79306418"
---
# <a name="adding-ios-specific-formatting"></a>IOS 특정 형식 추가

IOS 관련 형식을 설정 하는 한 가지 방법은 컨트롤에 대 한 [사용자 지정 렌더러](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) 를 만들고 각 플랫폼에 대 한 플랫폼별 스타일과 색을 설정 하는 것입니다.

Xamarin.ios iOS 앱의 모양을 제어 하는 다른 옵션은 다음과 같습니다.

- Info.plist에서 표시 옵션을 구성 하는 중 [ **입니다.** ](#info-plist)
- [`UIAppearance` API](#uiappearance) 를 통해 컨트롤 스타일 설정

이러한 대안은 아래에 설명 되어 있습니다.

<a name="info-plist"/>

## <a name="customizing-infoplist"></a>Info.plist 사용자 지정

**Info.plist** 파일을 사용 하 여 상태 표시줄을 표시할지 여부와 같은 iOS 응용 프로그램의 렌더링 일부 측면을 구성할 수 있습니다.

예를 들어 [Todo 샘플](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo) 은 다음 코드를 사용 하 여 모든 플랫폼의 탐색 모음 색과 텍스트 색을 설정 합니다.

```csharp
var nav = new NavigationPage (new TodoListPage ());
nav.BarBackgroundColor = Color.FromHex("91CA47");
nav.BarTextColor = Color.White;
```

결과는 아래 화면 조각에 표시 됩니다. 상태 표시줄 항목이 검게 표시 됩니다 (플랫폼별 기능 이므로 Xamarin.ios 내에서 설정할 수 없음).

![](theme-images/status-default-sml.png "iOS Theming")

또한 상태 표시줄은 iOS 프로젝트에서 직접 수행할 수 있는 것이 좋습니다. **Info.plist** 에 다음 항목을 추가 하 여 상태 표시줄을 흰색으로 강제 적용 합니다.

![](theme-images/info-plist.png "iOS Info.plist Entries")

또는 다음을 포함 하도록 해당 **info.plist** 파일을 직접 편집 합니다.

```xml
<key>UIStatusBarStyle</key>
<string>UIStatusBarStyleLightContent</string>
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
```

이제 앱이 실행 되 면 탐색 모음이 녹색 이며 텍스트는 흰색 (Xamarin.ios 서식으로 인해)이 *고,* 상태 표시줄 텍스트는 iOS 관련 구성 덕분에도 흰색입니다.

![](theme-images/status-white-sml.png "iOS Theming")

<a name="uiappearance"/>

## <a name="uiappearance-api"></a>UIAppearance API

[`UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) 를 사용 하 여 [사용자 지정 렌더러](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)를 만들지 *않고도* 많은 iOS 컨트롤의 시각적 속성을 설정할 수 있습니다.

**AppDelegate.cs** `FinishedLaunching` 메서드에 단일 코드 줄을 추가 하면 `Appearance` 속성을 사용 하 여 지정 된 형식의 모든 컨트롤에 스타일을 지정할 수 있습니다. 다음 코드에는 탭 표시줄과 스위치 컨트롤의 전역 스타일을 지정 하는 두 가지 예가 포함 되어 있습니다.

IOS 프로젝트의 **AppDelegate.cs**

```csharp
public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
  // tab bar
    UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
  // switch
    UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
    // required Xamarin.Forms code
    Forms.Init ();
    LoadApplication (new App ());
    return base.FinishedLaunching (app, options);
}
```

### <a name="uitabbar"></a>UITabBar

기본적으로 [`TabbedPage`](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md) 의 선택 된 탭 표시줄 아이콘
는 파란색입니다.

![](theme-images/tabbar-default.png "Default iOS Tab Bar Icon in TabbedPage")

이 동작을 변경 하려면 `UITabBar.Appearance` 속성을 설정 합니다.

```csharp
UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

이렇게 하면 선택 된 탭이 녹색이 됩니다.

![](theme-images/tabbar-custom.png "Green iOS Tab Bar Icon in TabbedPage")

이 API를 사용 하면 매우 작은 코드를 사용 하 여 iOS에서 Xamarin.ios `TabbedPage`의 모양을 사용자 지정할 수 있습니다. 사용자 지정 렌더러를 사용 하 여 탭에 대 한 특정 글꼴을 설정 하는 방법에 대 한 자세한 내용은 [탭 사용자 지정 조리법](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs) 을 참조 하세요.

### <a name="uiswitch"></a>UISwitch

`Switch` 컨트롤은 쉽게 스타일을 지정할 수 있는 또 다른 예입니다.

```csharp
UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

이 두 화면 캡처는 왼쪽에 기본 `UISwitch` 컨트롤을 표시 하 고 [Todo 샘플](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)의 오른쪽에 사용자 지정 된 버전 (`Appearance`설정)을 표시 합니다.

![](theme-images/switch-default.png "기본 UISwitch 색") ![](theme-images/switch-custom.png "사용자 지정 된 UISwitch 색")

### <a name="other-controls"></a>기타 컨트롤

많은 iOS 사용자 인터페이스 컨트롤은 [`UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)를 사용 하 여 기본 색 및 기타 특성을 설정할 수 있습니다.

## <a name="related-links"></a>관련 링크

- [UIAppearance](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)
- [탭 사용자 지정](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs)
