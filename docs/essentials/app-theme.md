---
title: 'Xamarin.Essentials: 앱 테마'
description: 이 문서에서는 실행 중인 앱에 대해 어떤 테마 스타일이 필요한지 정보를 제공하는 Xamarin.Essentials의 요청된 앱 테마 API에 대해 설명합니다.
ms.assetid: F6F6D496-A8A9-4B9A-AF1A-370D937E5073
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: e31cae6ff639dbe261599a7cf78ae31fc09318b3
ms.sourcegitcommit: c83b55f60ece20e9163b3e587130250fdf113a16
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79190323"
---
# <a name="xamarinessentials-app-theme"></a>Xamarin.Essentials: 앱 테마

**RequestedTheme** API는 [`AppInfo`](app-information.md) 클래스의 일부이며 시스템에서 실행 중인 애플리케이션에 대해 요청된 테마에 관한 정보를 제공합니다.

## <a name="get-started"></a>시작

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-requestedtheme"></a>RequestedTheme 사용

클래스에서 Xamarin.Essentials에 대한 참조를 추가합니다.

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-theme-information"></a>테마 정보 가져오기

요청된 애플리케이션 테마는 다음 API를 사용하여 검색할 수 있습니다.

```csharp
AppTheme appTheme = AppInfo.RequestedTheme;

```

그러면 시스템에서 애플리케이션에 대한 현재 요청된 테마를 제공합니다. 반환 값은 다음 중 하나입니다.

* Unspecified
* 밝게
* 어둡게

지정되지 않음은 운영 체제에 요청할 특정 사용자 인터페이스 스타일이 없을 때 반환됩니다. 그 예는 13.0 이전 버전의 iOS를 실행하는 디바이스입니다.


## <a name="platform-implementation-specifics"></a>플랫폼 구현 관련 정보

# <a name="android"></a>[Android](#tab/android)

Android는 구성 모드를 사용하여 사용자가 요청하는 테마 유형을 지정합니다. Android의 버전에 따라 사용자가 이를 변경할 수 있고 또는 배터리 절약 모드가 활성화된 경우 변경됩니다.

자세한 내용은 [어두운 테마에 대한 공식 Android 설명서](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme)에서 확인할 수 있습니다.


# <a name="ios"></a>[iOS](#tab/ios)

13.0 이전 버전의 iOS에서는 항상 지정되지 않음이 반환됩니다. 


# <a name="uwp"></a>[UWP](#tab/uwp)

기본적으로 앱은 Windows 설정에서 사용자가 설정한 테마(**설정 > 개인 설정 > 색> 기본 앱 모드 선택**)를 사용하여 실행됩니다. 사용자 기본값을 재정의하고 사용할 테마를 지정하도록 앱의 RequestedTheme 속성을 설정할 수 있습니다.

[UWP 요청된 테마 설명서](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.requestedtheme)에서 자세한 내용을 알아볼 수 있습니다.

--------------

## <a name="api"></a>API

- [AppInfo 소스 코드](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [AppInfo API 문서](xref:Xamarin.Essentials.AppInfo)
