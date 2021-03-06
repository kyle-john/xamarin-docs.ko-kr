---
title: DataPages 시작
description: 이 문서에서는 Xamarin.Forms DataPages를 사용 하 여 간단한 데이터 기반 페이지를 구축을 시작 하는 방법에 설명 합니다.
ms.prod: xamarin
ms.assetid: 6416E5FA-6384-4298-BAA1-A89381E47210
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 1f7917784ea66c31979b87f43639a7d03756692c
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725597"
---
# <a name="getting-started-with-datapages"></a>DataPages 시작

[![샘플 다운로드](~/media/shared/download.png) 샘플 다운로드](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)

![](~/media/shared/preview.png "This API is currently in preview")

> [!IMPORTANT]
> DataPages를 렌더링 하려면 Xamarin.ios 테마 참조가 필요 합니다. 여기에는 프로젝트에 [xamarin.ios](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) . a s e. m a s e. a s e. m a s [e.](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) [Xamarin.Forms.Theme.Dark](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/)

DataPages 미리 보기를 사용 하 여 간단한 데이터 기반 페이지를 작성 합니다. 시작 하려면 다음 단계를 수행 합니다. 이 데모에서는 미리 보기에서 하드 코드 된 스타일 ("이벤트")를 작성 하는 코드에서 특정 JSON 형식 에서만 작동 합니다.

[![](get-started-images/demo-sml.png "DataPages Sample Application")](get-started-images/demo.png#lightbox "DataPages Sample Application")

## <a name="1-add-nuget-packages"></a>1. NuGet 패키지 추가

이러한 NuGet 패키지를 Xamarin.ios .NET Standard 라이브러리 및 응용 프로그램 프로젝트에 추가 합니다.

- Xamarin.Forms.Pages
- Xamarin.Forms.Theme.Base
- 테마 구현 NuGet (예: Xamarin.Forms.Theme.Light)

## <a name="2-add-theme-reference"></a>2. 테마 참조 추가

**앱 .xaml** 파일에서 테마에 대 한 사용자 지정 `xmlns:mytheme` 추가 하 고 테마가 응용 프로그램의 리소스 사전에 병합 되도록 합니다.

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
  xmlns:mytheme="clr-namespace:Xamarin.Forms.Themes;assembly=Xamarin.Forms.Theme.Light"
  x:Class="DataPagesDemo.App">
    <Application.Resources>
        <ResourceDictionary MergedWith="mytheme:LightThemeResources" />
    </Application.Resources>
</Application>
```

> [!IMPORTANT]
> IOS `AppDelegate` 및 Android `MainActivity`에 몇 가지 상용구 코드를 추가 하 여 테마 어셈블리를 로드 하는 단계를 수행 해야 합니다 [(아래 참조)](#loadtheme) . 향후 미리 보기 릴리스에서 개선 됩니다.

## <a name="3-add-a-xaml-page"></a>3. XAML 페이지 추가

Xamarin.ios 응용 프로그램에 새 XAML 페이지를 추가 하 고 `ContentPage`에서 `Xamarin.Forms.Pages.ListDataPage`로 *기본 클래스를 변경* 합니다. 여기에 C# 및 XAML을 모두에서 수행 될 수 있습니다.

**C#파일과**

```csharp
public partial class SessionDataPage : Xamarin.Forms.Pages.ListDataPage // was ContentPage
{
  public SessionDataPage ()
  {
    InitializeComponent ();
  }
}
```

**XAML 파일**

루트 요소를 변경 하는 것 외에도 `xmlns:p`에 대 한 사용자 지정 네임 스페이스를 추가 해야 `<p:ListDataPage>`.

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage">

    <ContentPage.Content></ContentPage.Content>

</p:ListDataPage>
```

**응용 프로그램 하위 클래스**

`MainPage` `App` 클래스 생성자를 변경 하 여 새 `SessionDataPage`를 포함 하는 `NavigationPage`으로 설정 합니다. 탐색 페이지를 사용 *해야* 합니다.

```csharp
MainPage = new NavigationPage (new SessionDataPage ());
```

## <a name="3-add-the-datasource"></a>3. DataSource를 추가 합니다.

`Content` 요소를 삭제 하 고 해당 요소를 `p:ListDataPage.DataSource`로 바꿔서 페이지를 데이터로 채웁니다. 원격 Json 아래 예제에서는 데이터 파일 URL에서 로드 되 고 있습니다.

> [!NOTE]
> 미리 보기에는 데이터 원본에 대 한 렌더링 힌트를 제공 하는 `StyleClass` 특성이 *필요* 합니다. `StyleClass="Events"`는 미리 보기에 미리 정의 된 레이아웃을 나타내며 사용 중인 JSON 데이터 원본과 일치 하도록 *하드 코딩* 된 스타일을 포함 합니다.

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<p:ListDataPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:p="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
             x:Class="DataPagesDemo.SessionDataPage"
             Title="Sessions" StyleClass="Events">

    <p:ListDataPage.DataSource>
        <p:JsonDataSource Source="http://demo3143189.mockable.io/sessions" />
    </p:ListDataPage.DataSource>

</p:ListDataPage>
```

**JSON 데이터**

데모 원본의 JSON 데이터 예제는 다음과 같습니다.

```json
[{
  "end": "2016-04-27T18:00:00Z",
  "start": "2016-04-27T17:15:00Z",
  "abstract": "The new Apple TV has been released, and YOU can be one of the first developers to write apps for it. To make things even better, you can build these apps in C#! This session will introduce the basics of how to create a tvOS app with Xamarin, including: differences between tvOS and iOS APIs, TV user interface best practices, responding to user input, as well as the capabilities and limitations of building apps for a television. Grab some popcorn—this is going to be good!",
  "title": "As Seen On TV … Bringing C# to the Living Room",
  "presenter": "Matthew Soucoup",
  "biography": "Matthew is a Xamarin MVP and Certified Xamarin Developer from Madison, WI. He founded his company Code Mill Technologies and started the Madison Mobile .Net Developers Group.  Matt regularly speaks on .Net and Xamarin development at user groups, code camps and conferences throughout the Midwest. Matt gardens hot peppers, rides bikes, and loves Wisconsin micro-brews and cheese.",
  "image": "http://i.imgur.com/ASj60DP.jpg",
  "avatar": "http://i.imgur.com/ASj60DP.jpg",
  "room": "Crick"
}]
```

## <a name="4-run"></a>4. 실행!

위의 단계를 작업 데이터 페이지에서 발생 해야 합니다.

[![](get-started-images/demo-sml.png "DataPages Sample Application")](get-started-images/demo.png#lightbox "DataPages Sample Application")

이는 미리 작성 된 스타일 **"이벤트"** 가 밝은 테마 NuGet 패키지에 있고 데이터 원본과 일치 하는 스타일이 정의 되어 있기 때문에 작동 합니다 (예: "title", "image", "프레 젠 터").

"Events" `StyleClass`는 Xamarin.ios에 정의 된 사용자 지정 `CardView` 컨트롤을 사용 하 여 `ListDataPage` 컨트롤을 표시 하도록 빌드됩니다. `CardView` 컨트롤에는 `ImageSource`, `Text`및 `Detail`의 세 가지 속성이 있습니다. 테마는 datasource의 세 필드 (JSON 파일)에서 표시에 대 한 이러한 속성에 바인딩할 하드 코딩 됩니다.

## <a name="5-customize"></a>5. 사용자 지정

상속 된 스타일 템플릿을 지정 하 고 데이터 원본 바인딩을 사용 하 여 재정의할 수 있습니다. 아래 XAML은 새 `ListItemControl` 및 `{p:DataSourceBinding}` 구문을 사용 하 여 각 행에 대 한 사용자 지정 템플릿을 **선언 합니다.**

```xaml
<p:ListDataPage.DefaultItemTemplate>
    <DataTemplate>
        <ViewCell>
            <p:ListItemControl
                Title="{p:DataSourceBinding title}"
                Detail="{p:DataSourceBinding room}"
                ImageSource="{p:DataSourceBinding image}"
                DataSource="{Binding Value}"
                HeightRequest="90"
            >
            </p:ListItemControl>
        </ViewCell>
    </DataTemplate>
</p:ListDataPage.DefaultItemTemplate>
```

이 코드는 `DataTemplate`를 제공 하 여 `StyleClass`를 재정의 하 고 대신 `ListItemControl`에 기본 레이아웃을 사용 합니다.

[![](get-started-images/custom-sml.png "DataPages Sample Application")](get-started-images/custom.png#lightbox "DataPages Sample Application")

XAML을 선호 C# 하는 개발자는 데이터 소스 바인딩만 만들 수 있습니다 (`using Xamarin.Forms.Pages;` 문을 포함 해야).

```csharp
SetBinding (TitleProperty, new DataSourceBinding ("title"));
```

테마를 처음부터 만드는 데 더 많은 작업이 필요 하지만 이후 미리 보기 릴리스에서는이 작업을 보다 쉽게 수행할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

<a name="loadtheme" />

## <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>파일이 나 어셈블리 'Xamarin.Forms.Theme.Light' 또는 해당 종속성 중 하나를 로드할 수 없습니다.

미리 보기 릴리스에서 테마 못할 런타임에 로드할 수 있습니다. 이 오류를 해결 하려면 관련 프로젝트에 아래 표시 된 코드를 추가 합니다.

**iOS**

**AppDelegate.cs** 에 다음 줄을 추가 `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

**MainActivity.cs** 에 다음 줄을 추가 `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```

## <a name="related-links"></a>관련 링크

- [DataPagesDemo 샘플](https://github.com/xamarin/xamarin-forms-samples/tree/master/Pages/DataPagesDemo)
