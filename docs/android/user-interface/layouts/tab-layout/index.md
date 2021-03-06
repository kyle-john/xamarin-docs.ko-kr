---
title: 탭 레이아웃
description: Android의 탭 레이아웃 개요
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/08/2017
ms.openlocfilehash: 4ca4200d0f9036ed76e20e3a1840303e7bb3b7e3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028784"
---
# <a name="tabbed-layouts"></a>탭 레이아웃

## <a name="overview"></a>개요

탭은 간단 하 고 유용성으로 모바일 응용 프로그램에서 널리 사용 되는 사용자 인터페이스 패턴입니다. 응용 프로그램의 다양 한 화면을 탐색 하는 일관적이 고 간단한 방법을 제공 합니다. Android에는 탭 인터페이스를 위한 몇 가지 API가 있습니다. 

- **ActionBar** &ndash;은 일관 된 탐색 및 뷰 전환 인터페이스를 제공 하는 목표로 Android 3.0 (api 수준 11)에 도입 된 새로운 API 집합의 일부입니다. [Android 지원 라이브러리 v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)를 사용 하 여 android 2.2 (API 수준 8)로 다시 이식 되었습니다. 

- **PagerTabStrip** &ndash; `ViewPager`의 현재, 다음 및 이전 페이지를 나타냅니다. `ViewPager`는 [Android 지원 라이브러리 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)를 통해서만 사용할 수 있습니다.
     `PagerTabStrip`에 대 한 자세한 내용은 [Viewpager](~/android/user-interface/controls/view-pager/index.md)를 참조 하세요.

- **도구 모음** &ndash; `Toolbar`은 `ActionBar`을 대체 하는 더 빠르고 유연한 작업 모음 구성 요소입니다. `Toolbar`는 android 5.0 롤리팝 이상에서 사용할 수 있으며 [Android 지원 라이브러리 v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet 패키지를 통해 이전 버전의 android 에서도 사용할 수 있습니다. 
    `Toolbar`은 현재 Android 앱에서 사용 하는 권장 되는 작업 모음 구성 요소입니다.
    자세한 내용은 [도구 모음](~/android/user-interface/controls/tool-bar/index.md)을 참조 하세요. 

## <a name="related-links"></a>관련 링크

- [재질 디자인-탭](https://material.io/guidelines/components/tabs.html)- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [Android 지원 라이브러리 v7 AppCompat NuGet 패키지](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [v7 appcompat 라이브러리](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
