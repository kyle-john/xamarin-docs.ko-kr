---
title: Xamarin.ios에서 네이티브 맵 앱을 시작 합니다.
description: 각 플랫폼의 네이티브 맵 앱은 xamarin.ios 시작 관리자 클래스를 통해 Xamarin.ios 응용 프로그램에서 시작할 수 있습니다.
ms.prod: xamarin
ms.assetid: 5CF7CD67-3F20-4D80-B99E-D35A5FD1019A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/30/2019
ms.openlocfilehash: 54776d28bb75b152a6402e4d531d1baa4f724cba
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426326"
---
# <a name="launch-the-native-map-app-from-xamarinforms"></a>Xamarin.ios에서 네이티브 맵 앱을 시작 합니다.

[![샘플 다운로드](~/media/shared/download.png) 샘플 다운로드](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

각 플랫폼의 네이티브 맵 앱은 xamarin.ios `Launcher` 클래스를 통해 Xamarin.ios 응용 프로그램에서 시작할 수 있습니다. 이 클래스를 사용 하면 응용 프로그램에서 사용자 지정 URI 체계를 통해 다른 앱을 열 수 있습니다. `OpenAsync` 메서드를 사용 하 여 시작 관리자 기능을 호출 하 여 열려는 사용자 지정 URL 체계를 나타내는 `string` 또는 `Uri` 인수를 전달할 수 있습니다. Xamarin.ios에 대 한 자세한 내용은 [xamarin.ios](~/essentials/index.md?context=xamarin/xamarin-forms)를 참조 하세요.

> [!NOTE]
> Xamarin.ios `Launcher` 클래스를 사용 하는 대신 `Map` 클래스를 사용 합니다. 자세한 내용은 [xamarin.ios: Map](~/essentials/maps.md?context=xamarin/xamarin-forms)을 참조 하십시오.

각 플랫폼의 maps 앱은 고유한 사용자 지정 URI 체계를 사용 합니다. IOS의 maps URI 체계에 대 한 자세한 내용은 developer.apple.com의 [지도 링크](https://developer.apple.com/library/archive/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html) 를 참조 하세요. Android의 maps URI 체계에 대 한 자세한 내용은 developers.android.com의 Android 용 [Maps 개발자 가이드](https://developer.android.com/guide/components/intents-common.html#Maps) 및 [Google maps 의도](https://developers.google.com/maps/documentation/urls/android-intents) 항목을 참조 하세요. UWP (유니버설 Windows 플랫폼)의 maps URI 체계에 대 한 자세한 내용은 [Windows maps 앱 시작](/windows/uwp/launch-resume/launch-maps-app)을 참조 하세요.

## <a name="launch-the-map-app-at-a-specific-location"></a>특정 위치에서 맵 앱 시작

각 맵 앱에 대 한 사용자 지정 URI 체계에 적절 한 쿼리 매개 변수를 추가 하 여 네이티브 맵 앱의 위치를 열 수 있습니다.

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    // https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html
    await Launcher.OpenAsync("http://maps.apple.com/?q=394+Pacific+Ave+San+Francisco+CA");
}
else if (Device.RuntimePlatform == Device.Android)
{
    // open the maps app directly
    await Launcher.OpenAsync("geo:0,0?q=394+Pacific+Ave+San+Francisco+CA");
}
else if (Device.RuntimePlatform == Device.UWP)
{
    await Launcher.OpenAsync("bingmaps:?where=394 Pacific Ave San Francisco CA");
}
```

이 예제 코드는 각 플랫폼에서 네이티브 맵 앱이 시작 되 고, 지정 된 위치를 나타내는 핀 가운데에 지도를 표시 합니다.

[![IOS 및 Android의 네이티브 맵 앱 스크린샷](native-map-app-images/location.png "네이티브 맵 앱")](native-map-app-images/location-large.png#lightbox "네이티브 맵 앱")

## <a name="launch-the-map-app-with-directions"></a>방향으로 맵 앱 시작

각 맵 앱에 대 한 사용자 지정 URI 체계에 적절 한 쿼리 매개 변수를 추가 하 여 기본 맵 앱을 시작할 수 있습니다.

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    // https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html
    await Launcher.OpenAsync("http://maps.apple.com/?daddr=San+Francisco,+CA&saddr=cupertino");
}
else if (Device.RuntimePlatform == Device.Android)
{
    // opens the 'task chooser' so the user can pick Maps, Chrome or other mapping app
    await Launcher.OpenAsync("http://maps.google.com/?daddr=San+Francisco,+CA&saddr=Mountain+View");
}
else if (Device.RuntimePlatform == Device.UWP)
{
    await Launcher.OpenAsync("bingmaps:?rtp=adr.394 Pacific Ave San Francisco CA~adr.One Microsoft Way Redmond WA 98052");
}
```

이 예제 코드는 각 플랫폼에서 네이티브 맵 앱이 시작 되 고 지정 된 위치 간의 경로를 중심으로 하는 지도를 생성 합니다.

[![IOS 및 Android의 네이티브 맵 앱 경로 스크린샷](native-map-app-images/directions.png "네이티브 맵 앱 지침")](native-map-app-images/directions-large.png#lightbox "네이티브 맵 앱 지침")

## <a name="related-links"></a>관련 링크

- [Maps 샘플](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin.Essentials](~/essentials/index.md?context=xamarin/xamarin-forms)
- [맵 링크](https://developer.apple.com/library/archive/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html)
- [Maps 개발자 가이드](https://developer.android.com/guide/components/intents-common.html#Maps)
- [Android 용 Google Maps 의도](https://developers.google.com/maps/documentation/)
- [Windows Maps 앱을 시작 합니다.](/windows/uwp/launch-resume/launch-maps-app)
