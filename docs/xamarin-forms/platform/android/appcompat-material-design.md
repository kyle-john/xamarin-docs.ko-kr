---
title: AppCompat 및 재질 디자인 추가
description: 이 문서에서는 AppCompat 및 재질 디자인을 사용 하도록 기존 Xamarin. Forms Android 앱을 변환 하는 방법을 설명 합니다.
ms.prod: xamarin
ms.assetid: 045FBCDF-4D45-48BB-9911-BD3938C87D58
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2017
ms.openlocfilehash: 36c5733c347e3493b5ed423c52766c7e33fbdb3d
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728332"
---
# <a name="adding-appcompat-and-material-design"></a>AppCompat 및 재질 디자인 추가

_이러한 단계를 수행 하 여 기존 Xamarin.ios Android 앱에서 AppCompat 및 재질 디자인을 사용 하도록 변환 합니다._

<!-- source https://gist.github.com/jassmith/a3b2a543f99126782936
https://blog.xamarin.com/material-design-for-your-xamarin-forms-android-apps/ -->

## <a name="overview"></a>개요

이 지침에서는 AppCompat 라이브러리를 사용 하도록 기존 Xamarin.ios Android 응용 프로그램을 업데이트 하 고 Xamarin.ios 앱의 Android 버전에서 재질 디자인을 사용 하도록 설정 하는 방법을 설명 합니다.

### <a name="1-update-xamarinforms"></a>1. Xamarin 양식을 업데이트 합니다.

솔루션이 Xamarin.ios 2.0 이상 버전을 사용 하 고 있는지 확인 합니다. 필요한 경우 Xamarin.ios NuGet 패키지를 2.0으로 업데이트 합니다.

### <a name="2-check-android-version"></a>2. Android 버전 확인

Android 프로젝트의 대상 프레임 워크가 Android 6.0 (Marshmallow) 인지 확인 합니다. **Android 프로젝트 > 옵션 > 빌드 > 일반** 설정을 확인 하 여 corrent 프레임 워크를 선택 했는지 확인 합니다.

 ![](appcompat-images/target-android-6-sml.png "Android General Build Configuration")

### <a name="3-add-new-themes-to-support-material-design"></a>3. 지원 자료 디자인에 새 테마 추가

Android 프로젝트에서 다음 세 개의 파일을 만들고 아래 내용을 붙여넣습니다. Google은 지정 된에 대 한 대체 색 구성표를 선택 하는 데 도움이 되는 [스타일 안내선](https://www.google.com/design/spec/style/color.html#color-color-palette) 및 [색상표 생성기](https://www.materialpalette.com/) 를 제공 합니다.

**Resources/values/colors.xml**

```xml
<resources>
  <color name="primary">#2196F3</color>
  <color name="primaryDark">#1976D2</color>
  <color name="accent">#FFC107</color>
  <color name="window_background">#F5F5F5</color>
</resources>
```

**Resources/values/style.xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
  </style>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light.NoActionBar">
    <item name="colorPrimary">@color/primary</item>
    <item name="colorPrimaryDark">@color/primaryDark</item>
    <item name="colorAccent">@color/accent</item>
    <item name="android:windowBackground">@color/window_background</item>
    <item name="windowActionModeOverlay">true</item>
  </style>
</resources>
```

Android 롤리팝 이상에서 실행 될 때 특정 속성을 적용 하려면 **v21** 폴더에 추가 스타일을 포함 해야 합니다.

**Resources/values-v21/style.xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
    <!--If you are using MasterDetailPage you will want to set these, else you can leave them out-->
    <!--<item name="android:windowDrawsSystemBarBackgrounds">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>-->
  </style>
</resources>
```

### <a name="4-update-androidmanifestxml"></a>4. 업데이트 AndroidManifest .xml

이 새 테마 정보를 사용 하려면 `android:theme="@style/MyTheme"`을 추가 하 여 **Androidmanifest** 파일에 테마를 설정 합니다 (나머지 XML은 그대로 유지).

**Properties/AndroidManifest.xml**

```xml
...
<application android:label="AppName" android:icon="@drawable/icon"
  android:theme="@style/MyTheme">
...
```

### <a name="5-provide-toolbar-and-tab-layouts"></a>5. 도구 모음 및 탭 레이아웃 제공

[ **리소스/레이아웃** ] 디렉터리에 있는 **tabbar. Axml** 및 **Toolbar xml** 파일을 만들고 아래 내용을 붙여넣습니다.

**Resources/layout/Tabbar.axml**

```xml
<android.support.design.widget.TabLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/sliding_tabs"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:tabIndicatorColor="@android:color/white"
    app:tabGravity="fill"
    app:tabMode="fixed" />
```

탭의 몇 가지 속성은 탭의 무게를 포함 하 여 `fill` 하 고 `fixed`모드로 설정 되었습니다.
탭이 많은 경우이를 보다 자세히 알아보려면 Android [TabLayout 설명서](https://developer.android.com/reference/android/support/design/widget/TabLayout.html) 를 통해 스크롤할 수 있도록 전환 하는 것이 좋습니다.

**Resources/layout/Toolbar.axml**

```xml
<android.support.v7.widget.Toolbar
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
    app:layout_scrollFlags="scroll|enterAlways" />
```

이러한 파일에서는 응용 프로그램에 따라 달라질 수 있는 도구 모음에 대 한 특정 테마를 만듭니다.
자세한 내용은 [Hello 도구 모음](https://blog.xamarin.com/android-tips-hello-toolbar-goodbye-action-bar/) 블로그 게시물을 참조 하세요.

### <a name="6-update-the-mainactivity"></a>6. `MainActivity` 업데이트

기존 Xamarin.ios 앱에서 **MainActivity.cs** 클래스는 `FormsApplicationActivity`에서 상속 됩니다. 새 기능을 사용 하려면 `FormsAppCompatActivity`으로 바꾸어야 합니다.

**MainActivity.cs**

```csharp
public class MainActivity : FormsAppCompatActivity  // was FormsApplicationActivity
```

마지막으로, 다음과 같이 `OnCreate` 메서드의 5 단계에서 새 레이아웃을 "연결" 합니다.

```csharp
protected override void OnCreate(Bundle bundle)
{
  // set the layout resources first
  FormsAppCompatActivity.ToolbarResource = Resource.Layout.Toolbar;
  FormsAppCompatActivity.TabLayoutResource = Resource.Layout.Tabbar;

  // then call base.OnCreate and the Xamarin.Forms methods
  base.OnCreate(bundle);
  Forms.Init(this, bundle);
  LoadApplication(new App());
}
```
