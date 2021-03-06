---
title: 3부. XAML 태그 확장
description: XAML 태그 확장 속성을 개체 또는 다른 원본에서 직접 참조 되는 값을 설정할 수 있도록 XAML에서 중요 한 기능을 구성 합니다.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F4A37564-B18B-42FF-B841-9A1949895AB6
author: davidbritch
ms.author: dabritch
ms.date: 03/27/2018
ms.openlocfilehash: 89e2026ff16a9614234d6ee4bfa4df620cf58b56
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305902"
---
# <a name="part-3-xaml-markup-extensions"></a>3부. XAML 태그 확장

[![샘플 다운로드](~/media/shared/download.png) 샘플 다운로드](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_XAML 태그 확장은 다른 소스에서 간접적으로 참조 되는 개체 또는 값으로 속성을 설정할 수 있도록 하는 XAML의 중요 한 기능을 구성 합니다. XAML 태그 확장은 개체를 공유 하 고 응용 프로그램 전체에서 사용 되는 상수를 참조 하는 데 특히 중요 하지만 데이터 바인딩에서 가장 큰 유틸리티를 찾을 수 있습니다._

## <a name="xaml-markup-extensions"></a>XAML 태그 확장

일반적으로 XAML 문자열, 숫자, 열거형 멤버를 백그라운드에서 값으로 변환 되는 문자열 등의 명시적 값으로 개체의 속성을 설정 하려면 사용 합니다.

그러나 경우에 따라 속성 대신 다른 곳에 정의 되는 값을 참조 해야 합니다 또는 런타임 시 코드에 의해 거의 처리에 필요할 수 있습니다는. 이러한 목적을 위해 XAML *태그 확장* 을 사용할 수 있습니다.

이러한 XAML 태그 확장 XML의 확장 않습니다. XAML은 완전히 올바른 XML입니다. 이러한 클래스는 `IMarkupExtension`을 구현 하는 클래스의 코드에서 지원 되기 때문에 "확장" 이라고 합니다. 사용자 고유의 사용자 지정 태그 확장을 작성할 수 있습니다.

대부분의 경우에서 XAML 태그 확장은 XAML 파일에 즉시 인식할 수 있는 중괄호를 구분 하는 특성 설정으로 표시 되므로: {및}, 있지만 태그 확장 태그에서 기본 요소로 표시 하는 경우가 있습니다.

## <a name="shared-resources"></a>공유 리소스

일부 XAML 페이지에는 동일한 값으로 설정 하는 속성을 사용 하 여 여러 뷰가 포함 됩니다. 예를 들어 이러한 `Button` 개체에 대 한 대부분의 속성 설정은 동일 합니다.

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

        <Button Text="Do that!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

        <Button Text="Do the other thing!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

    </StackLayout>
</ContentPage>
```

이러한 속성 중 하나를 변경 해야 합니다 하는 경우에 세 번 보다는 한 번만 변경 내용을 확인 하는 것이 좋습니다. 코드 인 경우 가능성이 사용 상수 및 정적 읽기 전용 개체 일관적이 고 쉽게 수정할 수 이러한 값을 유지할 수 있습니다.

XAML에서 널리 사용 되는 솔루션 중 하나는 *리소스 사전*에 이러한 값 또는 개체를 저장 하는 것입니다. `VisualElement` 클래스는 `string` 형식의 키와 `object`형식의 값이 있는 사전 인 `ResourceDictionary`형식의 `Resources` 라는 속성을 정의 합니다. 이 사전에 개체를 넣으면 모든 XAML 내에서 해당 개체를 태그로 참조할 수 있습니다.

페이지에서 리소스 사전을 사용 하려면 `Resources` 속성-요소 태그 쌍을 포함 합니다. 이러한 페이지의 맨 위에 있는 가장 유용 합니다.

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>

    </ContentPage.Resources>
    ...
</ContentPage>
```

또한 `ResourceDictionary` 태그를 명시적으로 포함 해야 합니다.

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

이제 리소스 사전에 개체 및 다양 한 형식의 값을 추가할 수 있습니다. 이러한 형식은 인스턴스화할 수 있어야 합니다. 추상 클래스, 예를 들어 수 없습니다. 이러한 형식에 매개 변수가 없는 public 생성자가 있어야 합니다. 각 항목에는 `x:Key` 특성으로 지정 된 사전 키가 필요 합니다. 다음은 그 예입니다.

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

이러한 두 항목은 `LayoutOptions`구조체 형식의 값 이며, 각 항목에는 고유한 키와 하나 또는 두 개의 속성이 설정 되어 있습니다. 코드 및 태그에서 `LayoutOptions`의 정적 필드를 사용 하는 것이 훨씬 더 일반적 이지만 여기서는 속성을 설정 하는 것이 더 편리 합니다.

이제 이러한 단추의 `HorizontalOptions` 및 `VerticalOptions` 속성을 이러한 리소스에 설정 하 고 `StaticResource` XAML 태그 확장을 사용 하 여 작업을 수행 해야 합니다.

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="3"
        Rotation="-15"
        TextColor="Red"
        FontSize="24" />
```

`StaticResource` 태그 확장은 항상 중괄호로 구분 되며 사전 키를 포함 합니다.

이름 `StaticResource`은 Xamarin. Forms에서 지 원하는 `DynamicResource`와 구별 합니다. `DynamicResource`는 런타임에 변경 될 수 있는 값과 연결 된 사전 키에 대 한 것이 고, `StaticResource`는 페이지의 요소가 생성 될 때 사전에 있는 요소에 한 번만 액세스 하는 것입니다.

`BorderWidth` 속성의 경우 사전에 double을 저장 해야 합니다. XAML은 `x:Double` 및 `x:Int32`와 같은 일반적인 데이터 형식에 대 한 태그를 편리 하 게 정의 합니다.

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

        <x:Double x:Key="borderWidth">
            3
        </x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

세 줄에 추가할 필요가 없습니다. 이 회전 각도 대 한이 사전 항목을 한 줄 위로 사용:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

         <x:Double x:Key="borderWidth">
            3
         </x:Double>

        <x:Double x:Key="rotationAngle">-15</x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

이러한 두 리소스는 `LayoutOptions` 값과 동일한 방식으로 참조할 수 있습니다.

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="24" />
```

`Color`형식의 리소스에 대해 이러한 형식의 특성을 직접 할당할 때 사용 하는 것과 동일한 문자열 표현을 사용할 수 있습니다. 형식 변환기는 리소스를 만들 때 호출 됩니다. `Color`형식의 리소스는 다음과 같습니다.

```xaml
<Color x:Key="textColor">Red</Color>
```

프로그램에서 `FontSize` 속성을 `Large`와 같은 `NamedSize` 열거의 멤버로 설정 하는 경우가 많습니다. `FontSizeConverter` 클래스는 백그라운드에서 작동 하 여 `Device.GetNamedSized` 메서드를 사용 하 여 플랫폼 종속 값으로 변환 합니다. 그러나 font-size 리소스를 정의할 때 여기에 `x:Double` 형식으로 표시 된 숫자 값을 사용 하는 것이 더 적합 합니다.

```xaml
<x:Double x:Key="fontSize">24</x:Double>
```

이제 `Text`를 제외한 모든 속성은 리소스 설정으로 정의 됩니다.

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="{StaticResource textColor}"
        FontSize="{StaticResource fontSize}" />
```

리소스 사전 내에서 `OnPlatform`를 사용 하 여 플랫폼에 대해 다른 값을 정의할 수 있습니다. 다음은 `OnPlatform` 개체가 다른 텍스트 색의 리소스 사전에 포함 될 수 있는 방법입니다.

```xaml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
    <On Platform="UWP" Value="#80FF80" />
</OnPlatform>
```

`OnPlatform`는 `x:Key` 특성을 가져옵니다 .이 특성은 제네릭 클래스 이기 때문에 사전에 있는 개체이 고 `x:TypeArguments` 특성입니다. `iOS`, `Android`및 `UWP` 특성은 개체가 초기화 될 때 `Color` 값으로 변환 됩니다.

다음은 6 개 공유 값에 액세스 하는 세 개의 단추를 사용 하 여 최종 완료 XAML 파일이입니다.

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />

            <x:Double x:Key="borderWidth">3</x:Double>

            <x:Double x:Key="rotationAngle">-15</x:Double>

            <OnPlatform x:Key="textColor"
                        x:TypeArguments="Color">
                <On Platform="iOS" Value="Red" />
                <On Platform="Android" Value="Aqua" />
                <On Platform="UWP" Value="#80FF80" />
            </OnPlatform>

            <x:Double x:Key="fontSize">24</x:Double>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do that!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do the other thing!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

    </StackLayout>
</ContentPage>
```

스크린샷은 일관 된 스타일 및 플랫폼별 스타일 지정을 확인합니다.

[![스타일이 지정 된 컨트롤](xaml-markup-extensions-images/sharedresources.png)](xaml-markup-extensions-images/sharedresources-large.png#lightbox)

페이지 맨 위에 `Resources` 컬렉션을 정의 하는 것이 가장 일반적 이지만 `Resources` 속성은 `VisualElement`에 의해 정의 되 고 페이지의 다른 요소에 `Resources` 컬렉션을 포함할 수 있다는 점에 유의 하세요. 예를 들어이 예제에서 `StackLayout`에 하나를 추가 해 봅니다.

```xaml
<StackLayout>
    <StackLayout.Resources>
        <ResourceDictionary>
            <Color x:Key="textColor">Blue</Color>
        </ResourceDictionary>
    </StackLayout.Resources>
    ...
</StackLayout>
```

단추의 텍스트 색이 파란색 이제 알 수 있습니다. 기본적으로 XAML 파서는 `StaticResource` 태그 확장을 발견할 때마다 시각적 트리를 검색 하 고 해당 키를 포함 하는 첫 번째 `ResourceDictionary`를 사용 합니다.

리소스 사전에 저장 되는 가장 일반적인 개체 형식 중 하나는 속성 설정의 컬렉션을 정의 하는 Xamarin.ios `Style`입니다. 스타일은 문서 [스타일](~/xamarin-forms/user-interface/styles/index.md)에 설명 되어 있습니다.

때로는 XAML을 처음 접하는 개발자가 `Label` 또는 `Button` 같은 시각적 요소를 `ResourceDictionary`에 배치할 수 있는지 여부를 궁금해 합니다. 물론 가능 하다 고 하는 동안 말을 것 하지 않습니다. `ResourceDictionary` 목적은 개체를 공유 하는 것입니다. 시각적 요소를 공유할 수 없습니다. 동일한 인스턴스는 단일 페이지에 두 번 나타날 수 없습니다.

## <a name="the-xstatic-markup-extension"></a>X:static 태그 확장명

이름의 유사성에도 불구 하 고 `x:Static`와 `StaticResource`은 매우 다릅니다. `StaticResource`는 `x:Static`에서 다음 중 하나에 액세스 하는 동안 리소스 사전에서 개체를 반환 합니다.

- 공용 정적 필드
- 공용 정적 속성
- 공용 상수 필드
- 열거형 멤버입니다.

`StaticResource` 태그 확장은 리소스 사전을 정의 하는 XAML 구현에서 지원 되며, `x:Static`는 `x` 접두사가 표시 될 때 XAML의 내장 부분입니다.

`x:Static` 정적 필드 및 열거형 멤버를 명시적으로 참조할 수 있는 방법을 보여 주는 몇 가지 예제는 다음과 같습니다.

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="{x:Static LayoutOptions.Start}"
       HorizontalTextAlignment="{x:Static TextAlignment.Center}"
       TextColor="{x:Static Color.Aqua}" />
```

지금까지 매우 인상적인 아닙니다. 그러나 `x:Static` 태그 확장은 사용자 고유의 코드에서 정적 필드 또는 속성을 참조할 수도 있습니다. 예를 들어 응용 프로그램 전체의 여러 페이지에서 사용할 수 있는 몇 가지 정적 필드를 포함 하는 `AppConstants` 클래스는 다음과 같습니다.

```csharp
using System;
using Xamarin.Forms;

namespace XamlSamples
{
    static class AppConstants
    {
        public static readonly Thickness PagePadding;

        public static readonly Font TitleFont;

        public static readonly Color BackgroundColor = Color.Aqua;

        public static readonly Color ForegroundColor = Color.Brown;

        static AppConstants()
        {
            switch (Device.RuntimePlatform)
            {
                case Device.iOS:
                    PagePadding = new Thickness(5, 20, 5, 0);
                    TitleFont = Font.SystemFontOfSize(35, FontAttributes.Bold);
                    break;

                case Device.Android:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(40, FontAttributes.Bold);
                    break;

                case Device.UWP:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(50, FontAttributes.Bold);
                    break;
            }
        }
    }
}
```

XAML 파일에서이 클래스의 정적 필드를 참조 하려면이 파일이 위치 하는 XAML 파일 내에서 표시 하는 방법이 사용 해야 합니다. XML 네임 스페이스 선언을 사용 하 여이 작업을 수행 합니다.

표준 Xamarin.Forms XAML 템플릿의 일부로 생성 된 XAML 파일에 두 개의 XML 네임 스페이스 선언을 포함 회수: Xamarin.Forms 클래스 및 태그와 특성에 XAML 내장 함수를 참조 하는 것에 대 한 다른 액세스 하기 위한 하나:

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

다른 클래스에 접근하려면 XML 네임 스페이스 선언이 필요합니다. 각 추가 XML 네임 스페이스 선언은 새 접두사를 정의합니다. `AppConstants`와 같이 공유 응용 프로그램 .NET Standard 라이브러리에 로컬인 클래스에 액세스 하기 위해 XAML 프로그래머는 종종 접두사 `local`사용 합니다. 네임 스페이스 선언은 .NET 네임 스페이스 이름이 라고도 하는 CLR (공용 언어 런타임) 네임 스페이스 이름 ( C# `namespace` 정의 또는 `using` 지시문에 표시 되는 이름)을 나타내야 합니다.

```csharp
xmlns:local="clr-namespace:XamlSamples"
```

.NET Standard 라이브러리를 참조 하는 모든 어셈블리에서.NET 네임 스페이스에 대 한 XML 네임 스페이스 선언을 정의할 수도 있습니다. 예를 들어, 다음은 **netstandard.library** 어셈블리에 있는 표준 .net `System` 네임 스페이스에 대 한 `sys` 접두사입니다. 이는 다른 어셈블리 이므로 어셈블리 이름 (이 경우 **netstandard.library**)도 지정 해야 합니다.

```csharp
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

키워드 `clr-namespace` 뒤에 콜론과 .NET 네임 스페이스 이름, 세미콜론, 키워드 `assembly`, 등호 및 어셈블리 이름이 차례로 나옵니다.

예, 콜론이 `clr-namespace` 뒤에 오는 경우에는 `assembly`뒤에 등호가 나옵니다. 구문은 이러한 방식으로 의도적으로 정의 되었습니다. 대부분의 XML 네임 스페이스 선언은 `http`와 같은 URI 체계 이름을 시작 하는 URI (항상 뒤에 콜론이 옴)를 참조 합니다. 이 문자열의 `clr-namespace` 부분은 해당 규칙을 모방 하기 위한 것입니다.

이러한 네임 스페이스 선언은 모두 **StaticConstantsPage** 샘플에 포함 되어 있습니다. `BoxView` 차원은 `Math.PI` 및 `Math.E`로 설정 되지만 100의 비율로 조정 됩니다.

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             x:Class="XamlSamples.StaticConstantsPage"
             Title="Static Constants Page"
             Padding="{x:Static local:AppConstants.PagePadding}">

    <StackLayout>
       <Label Text="Hello, XAML!"
              TextColor="{x:Static local:AppConstants.BackgroundColor}"
              BackgroundColor="{x:Static local:AppConstants.ForegroundColor}"
              Font="{x:Static local:AppConstants.TitleFont}"
              HorizontalOptions="Center" />

      <BoxView WidthRequest="{x:Static sys:Math.PI}"
               HeightRequest="{x:Static sys:Math.E}"
               Color="{x:Static local:AppConstants.ForegroundColor}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="100" />
    </StackLayout>
</ContentPage>
```

화면에 상대적인 결과 `BoxView`의 크기는 플랫폼에 따라 다릅니다.

[x:Static 태그 확장을 사용 하는 ![컨트롤](xaml-markup-extensions-images/staticconstants.png)](xaml-markup-extensions-images/staticconstants-large.png#lightbox)

## <a name="other-standard-markup-extensions"></a>다른 표준 태그 확장

여러 가지 태그 확장은 XAML 및 Xamarin.Forms XAML 파일에서 지원 합니다. 이 중 몇 가지 자주 사용 되지 않지만 필요할 때 필수적인:

- 속성에 `null` 값이 아닌 값이 기본적으로 있지만 `null`으로 설정 하려는 경우이 값을 `{x:Null}` 태그 확장으로 설정 합니다.
- 속성이 `Type`형식인 경우 태그 확장 `{x:Type someClass}`를 사용 하 여 `Type` 개체에 할당할 수 있습니다.
- `x:Array` 태그 확장을 사용 하 여 XAML에서 배열을 정의할 수 있습니다. 이 태그 확장에는 배열의 요소 형식을 나타내는 `Type` 라는 필수 특성이 있습니다.
- `Binding` 태그 확장은 4 부에서 설명 합니다 [. 데이터 바인딩 기본 사항](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)입니다.
- `RelativeSource` 태그 확장은 [상대 바인딩에서](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)설명 합니다.

## <a name="the-constraintexpression-markup-extension"></a>ConstraintExpression 태그 확장

태그 확장 속성을 가질 수 있지만 같은 XML 특성이 설정 되지 않습니다. 태그 확장에서 속성 설정을 쉼표로 구분 하 여 및 하지 인용 부호 중괄호 내에 표시 합니다.

이는 `RelativeLayout` 클래스와 함께 사용 되는 `ConstraintExpression`이라는 Xamarin.ios 태그 확장을 사용 하 여 설명할 수 있습니다. 상수 또는 부모 또는 다른 명명 된 보기에 상대적인 위치 또는 자식 뷰 크기를 지정할 수 있습니다. `ConstraintExpression` 구문을 사용 하 여 다른 뷰의 속성에 `Factor` 시간을 추가 하 고 `Constant`를 사용 하 여 뷰의 위치나 크기를 설정할 수 있습니다. 항목 보다 더 복잡 한 코드가 필요합니다.

예를 들면 다음과 같습니다.

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.RelativeLayoutPage"
             Title="RelativeLayout Page">

    <RelativeLayout>

        <!-- Upper left -->
        <BoxView Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Upper right -->
        <BoxView Color="Green"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Lower left -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />
        <!-- Lower right -->
        <BoxView Color="Yellow"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />

        <!-- Centered and 1/3 width and height of parent -->
        <BoxView x:Name="oneThird"
                 Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"  />

        <!-- 1/3 width and height of previous -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=X}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Y}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Height,
                                            Factor=0.33}"  />
    </RelativeLayout>
</ContentPage>
```

아마도 이 샘플에서 얻어야 할 가장 중요한 배움은 태그 확장의 구문입니다. 따옴표는 태그 확장의 중괄호 내에 올 수 없습니다. XAML 파일에 태그 확장을 입력할 때 속성의 값을 자연스럽게 따옴표로 묶고 싶을 것입니다. 유혹을 이겨내십시오!

실행 중인 프로그램이 다음과 같습니다.

[제약 조건을 사용 하 여 상대 레이아웃 ![](xaml-markup-extensions-images/relativelayout.png)](xaml-markup-extensions-images/relativelayout-large.png#lightbox)

## <a name="summary"></a>요약

문서에 표시된 XAML 태그 확장은 XAML 파일에 대한 중요한 지원을 제공합니다. 그러나 가장 중요 한 XAML 태그 확장은이 시리즈의 다음 부분인 4 부에서 설명 하는 `Binding`됩니다 [. 데이터 바인딩 기본 사항](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)입니다.

## <a name="related-links"></a>관련 링크

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [1 부. XAML 시작](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [2 부. 필수 XAML 구문](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [4 부. 데이터 바인딩 기본 사항](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [5 부. MVVM에 데이터 바인딩](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
