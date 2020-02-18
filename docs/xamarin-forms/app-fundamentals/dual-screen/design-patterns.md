---
title: Xamarin.Forms 이중 화면 디자인 패턴
description: 이 가이드에서는 Xamarin.Forms가 이중 화면 디바이스에 최적화된 다양한 디자인 패턴을 지원하는 방법을 설명합니다.
ms.prod: xamarin
ms.assetid: 3176d792-6dba-4e00-b463-497c58678ee9
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: c739b32cb45c27bbbc166f898dee964a7cbbbce6
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145527"
---
# <a name="xamarinforms-dual-screen-design-patterns"></a>Xamarin.Forms 이중 화면 디자인 패턴

![](~/media/shared/preview.png "This API is currently pre-release")

[![샘플 다운로드](~/media/shared/download.png) 샘플 다운로드](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/UserInterface/DualScreenDemos)

이 가이드에서는 이중 화면 디바이스를 위한 권장 디자인 패턴을 소개하고 멋지고 유용한 사용자 환경을 제공하는 인터페이스를 만드는 데 도움이 되는 코드와 샘플을 제공합니다.

## <a name="extended-canvas-pattern"></a>확장된 캔버스 패턴

확장된 캔버스 패턴에서는 두 화면을 하나의 큰 캔버스로 처리하여 지도, 이미지, 스프레드시트 또는 분산을 활용해 최대 공간을 사용하는 기타 콘텐츠를 표시합니다.

![](design-patterns-images/extended-canvas-sample.png "Extended canvas sample")

```xaml
<ContentPage
    xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples"
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:d="http://xamarin.com/schemas/2014/forms/design"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    x:Class="Xamarin.Duo.Forms.Samples.ExtendCanvas">
    <ContentPage.Content>
        <Grid>
            <WebView x:Name="webView" HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
            <SearchBar x:Name="searchBar" Placeholder="Find a place..." BackgroundColor="DarkGray" Opacity="0.8" HorizontalOptions="FillAndExpand" VerticalOptions="Start" />
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

이 예제에서는 `Grid` 및 내부 콘텐츠가 확장되어 단일 화면에 표시되든 두 화면에 걸쳐 표시되든 사용 가능한 화면을 모두 사용합니다.

## <a name="master-detail-pattern"></a>마스터-세부 패턴

마스터-세부 패턴의 경우 마스터 보기(일반적으로 왼쪽의 목록)에서는 사용자가 선택하는 콘텐츠를 제공하고, 선택하면 해당 항목의 세부 정보가 오른쪽에 표시됩니다.

![](design-patterns-images/master-detail-sample.png "Master detail sample")

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage
    xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples"
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen"
    x:Class="Xamarin.Duo.Forms.Samples.MasterDetail">
    <dualScreen:TwoPaneView MinWideModeWidth="4000" MinTallModeHeight="4000">
        <dualScreen:TwoPaneView.Pane1>
            <local:Master x:Name="masterPage"></local:Master>
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <local:Details x:Name="detailsPage"></local:Details>
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

이 예제에서는 `TwoPaneView`를 사용하여 한 창에서는 목록을 설정하고 다른 창에서는 세부 정보 보기를 설정할 수 있습니다.

## <a name="two-page-pattern"></a>두 페이지 패턴

두 페이지 패턴은 문서 리더, 메모 또는 아트보드와 같은 2단 레이아웃에 적합한 콘텐츠를 위한 것입니다.

![](design-patterns-images/two-page-sample.png "Two page sample")

```xaml
<Grid x:Name="layout">
    <CollectionView x:Name="cv" BackgroundColor="LightGray">
        <CollectionView.ItemsLayout>
            <GridItemsLayout
                SnapPointsAlignment="Start"
                SnapPointsType="MandatorySingle"
                Orientation="Horizontal"
                HorizontalItemSpacing="{Binding Source={x:Reference mainPage}, Path=HingeWidth}" />
        </CollectionView.ItemsLayout>
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Frame BackgroundColor="LightGray" Padding="0" Margin="0"
                        WidthRequest="{Binding Source={x:Reference mainPage}, Path=ContentWidth}"
                        HeightRequest="{Binding Source={x:Reference mainPage}, Path=ContentHeight}">
                    <Frame Margin="20" BackgroundColor="White">
                        <Label FontSize="Large" Text="{Binding .}" VerticalTextAlignment="Center" HorizontalTextAlignment="Center" HorizontalOptions="Center" VerticalOptions="Center"></Label>
                    </Frame>
                </Frame>
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
</Grid>
```

힌지 너비로 분할되는 그리드 레이아웃을 사용하는 [`CollectionView`](xref:Xamarin.Forms.CollectionView)는 이 이중 화면 환경을 제공하는 데 적합한 방법입니다.

## <a name="dual-view-pattern"></a>이중 보기 패턴

이중 보기는 “두 페이지” 보기처럼 보일 수 있지만 내용과 사용자 시나리오에서 차이가 있습니다. 이 패턴에서는 예를 들어 문서 또는 사진을 편집하거나, 여러 식당 메뉴를 비교하거나, 코드 파일의 병합 충돌을 비교하기 위해 콘텐츠를 나란히 비교합니다.

![](design-patterns-images/dual-view-sample.png "Dual view sample")

```xaml
<ContentPage
    xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples"
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen"
    x:Class="Xamarin.Duo.Forms.Samples.DualViewListPage">
    <dualScreen:TwoPaneView>
        <dualScreen:TwoPaneView.Pane1>
            <CollectionView SelectionMode="Single" x:Name="mapList">
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <Grid Padding="10,5,10,5">
                            <Frame Visual="Material" BorderColor="LightGray">
                                <StackLayout Padding="5">
                                    <Label FontSize="Title" Text="{Binding Title}"></Label>
                                </StackLayout>
                            </Frame>
                        </Grid>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <local:DualViewMap x:Name="mapPage"></local:DualViewMap>
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

## <a name="companion-pattern"></a>보조 패턴

보조 패턴에서는 그리기 앱, 게임 또는 미디어 편집의 경우에서처럼 두 번째 화면을 사용하여 기본 보기와 관련된 두 번째 수준의 콘텐츠를 제공하는 방법을 보여 줍니다.

![](design-patterns-images/companion-pane-sample.png "Companion pane sample")

```xaml
<ContentPage
    xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples" xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:dualscreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen"
    x:Name="mainPage"
    x:Class="Xamarin.Duo.Forms.Samples.CompanionPane"
    BackgroundColor="LightGray"
    Visual="Material">
    <dualscreen:TwoPaneView x:Name="twoPaneView" MinWideModeWidth="4000" MinTallModeHeight="4000">
        <dualscreen:TwoPaneView.Pane1>
            <CarouselView x:Name="cv" BackgroundColor="LightGray" IsScrollAnimated="False" >
                <CarouselView.ItemTemplate>
                    <DataTemplate>
                        <Frame BackgroundColor="LightGray" Padding="0" Margin="0"
                           WidthRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane1.Width}"
                           HeightRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane1.Height}">
                            <Frame Margin="20" BackgroundColor="White">
                                <Label FontSize="Large" Text="{Binding ., StringFormat='Slide Content {0}'}" VerticalTextAlignment="Center" HorizontalTextAlignment="Center" HorizontalOptions="Center" VerticalOptions="Center"></Label>
                            </Frame>
                        </Frame>
                    </DataTemplate>
                </CarouselView.ItemTemplate>
            </CarouselView>
        </dualscreen:TwoPaneView.Pane1>
        <dualscreen:TwoPaneView.Pane2>
            <CollectionView x:Name="indicators"
            SelectionMode="Single"
            Margin="20, 20, 20, 20"
            BackgroundColor="LightGray"
            WidthRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane2.Width}"
            ItemsSource="{Binding Source={x:Reference cv}, Path=ItemsSource}">
                <CollectionView.Resources>
                    <ResourceDictionary>
                        <Style TargetType="Frame">
                            <Setter Property="VisualStateManager.VisualStateGroups">
                                <VisualStateGroupList>
                                    <VisualStateGroup x:Name="CommonStates">
                                        <VisualState x:Name="Normal">
                                            <VisualState.Setters>
                                                <Setter Property="Padding" Value="0"></Setter>
                                            </VisualState.Setters>
                                        </VisualState>
                                        <VisualState x:Name="Selected">
                                            <VisualState.Setters>
                                                <Setter Property="BorderColor" Value="Green" />
                                                <Setter Property="Padding" Value="1"></Setter>
                                            </VisualState.Setters>
                                        </VisualState>
                                    </VisualStateGroup>
                                </VisualStateGroupList>
                            </Setter>
                        </Style>
                    </ResourceDictionary>
                </CollectionView.Resources>
                <CollectionView.ItemsLayout>
                    <LinearItemsLayout Orientation="Vertical" ItemSpacing="10"></LinearItemsLayout>
                </CollectionView.ItemsLayout>
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <Frame WidthRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane2.Width}" CornerRadius="10" HeightRequest="60" BackgroundColor="White" Margin="0">
                            <StackLayout HorizontalOptions="Fill" VerticalOptions="Fill"  Orientation="Horizontal">
                                <Label FontSize="Micro" Padding="20,0,20,0" VerticalTextAlignment="Center" WidthRequest="140" Text="{Binding ., StringFormat='Slide Content {0}'}"></Label>
                                <Label FontSize="Small" Padding="20,0,20,0" VerticalTextAlignment="Center" HorizontalOptions="FillAndExpand" BackgroundColor="DarkGray"  Grid.Column="1" Text="{Binding ., StringFormat='Slide {0}'}"></Label>
                            </StackLayout>
                        </Frame>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </dualscreen:TwoPaneView.Pane2>
    </dualscreen:TwoPaneView>
</ContentPage>
```

## <a name="related-links"></a>관련 링크

- [Dual Screen Samples(GitHub)](https://github.com/xamarin/xamarin-forms-samples/tree/pre-release/UserInterface/DualScreenDemos)(이중 화면 샘플(GitHub))
- [Create apps for dual screen devices](index.md)(이중 화면 디바이스용 앱 만들기)