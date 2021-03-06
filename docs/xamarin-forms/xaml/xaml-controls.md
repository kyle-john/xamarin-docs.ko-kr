---
title: XAML 컨트롤
description: Xamarin.ios에 정의 된 모든 뷰는 XAML 파일에서 참조할 수 있습니다.
ms.topic: article
ms.prod: xamarin
ms.assetid: 639BD392-1496-41BB-BB09-7652273AC9D8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/14/2020
ms.openlocfilehash: 12c0688a6a4ac600a82385b5fedad9a147dd98e4
ms.sourcegitcommit: 5d22f37dfc358678df52a4d17c57261056a72cb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77674557"
---
# <a name="xaml-controls"></a>XAML 컨트롤

[![샘플 다운로드](~/media/shared/download.png) 샘플 다운로드](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

뷰는 다른 그래픽 프로그래밍 환경에서 일반적으로 *컨트롤이* 나 *위젯* 으로 알려진 레이블, 단추 및 슬라이더와 같은 사용자 인터페이스 개체입니다. Xamarin.ios에서 지원 되는 뷰는 모두 [`View`](xref:Xamarin.Forms.View) 클래스에서 파생 됩니다.

Xamarin.ios에 정의 된 모든 뷰는 XAML 파일에서 참조할 수 있습니다.

## <a name="views-for-presentation"></a>프레젠테이션 뷰

|     |     |
| --- | --- |
| <h3>BoxView</h3>특정 색의 사각형을 표시 합니다.<p align="center">![BoxView의 스크린샷](xaml-controls-images/BoxView.png "BoxView")</p>[API](xref:Xamarin.Forms.BoxView) / [가이드](~/xamarin-forms/user-interface/boxview.md) | <pre valign="center">&lt;BoxView Color="Accent"<br />         WidthRequest="150"<br />         HeightRequest="150"<br />         HorizontalOptions="Center"&gt;</pre></p> |
| <h3>이미지</h3>비트맵을 표시 합니다.<p align="center">![이미지 스크린샷](xaml-controls-images/Image.png "이미지")</p>[API](xref:Xamarin.Forms.Image) / [가이드](~/xamarin-forms/user-interface/images.md) | <pre>&lt;Image Source="https://aka.ms/campus.jpg"<br />       Aspect="AspectFit"<br />       HorizontalOptions="Center" /&gt;</pre></p> |
| <h3>레이블</h3>하나 이상의 텍스트 줄을 표시 합니다.<p align="center">![레이블의 스크린샷](xaml-controls-images/Label.png "레이블")</p>[API](xref:Xamarin.Forms.Label) / [가이드](~/xamarin-forms/user-interface/text/label.md) | <p valign="center"><pre>&lt;Label Text="Hello, Xamarin.Forms!"<br />       FontSize="Large"<br />       FontAttributes="Italic"<br />       HorizontalTextAlignment="Center" /&gt;</pre></p> |
| <h3>맵</h3>지도를 표시 합니다.<p align="center">![지도의 스크린샷](xaml-controls-images/Map.png "맵")</p>[API](xref:Xamarin.Forms.Maps.Map) / [가이드](~/xamarin-forms/user-interface/map/index.md) | <p valign="center"><pre>&lt;maps:Map ItemsSource="{Binding Locations}" /&gt;</pre></p> |
| <h3>MediaElement</h3>비디오 또는 오디오를 재생 합니다.<p align="center">![MediaElement의 스크린샷](xaml-controls-images/MediaElement.png "MediaELement")</p>[API](xref:Xamarin.Forms.MediaElement) / [가이드](~/xamarin-forms/user-interface/mediaelement.md) | <p valign="center"><pre>&lt;MediaElement Source="https://sec.ch9.ms/ch9/XamarinShow_mid.mp4"<br />              AutoPlay="True"<br />              ShowsPlaybackControls="True" /&gt;</pre></p> |
| <h3>WebView</h3>웹 페이지 또는 HTML 콘텐츠를 표시 합니다.<p align="center">![웹 보기의 스크린샷](xaml-controls-images/WebView.png "WebView")</p>[API](xref:Xamarin.Forms.WebView) / [가이드](~/xamarin-forms/user-interface/webview.md) | <p valign="center"><pre>&lt;WebView Source="https://docs.microsoft.com/xamarin/"<br/>         VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-initiate-commands"></a>시작 명령 뷰

|     |     |
| --- | --- |
| <h3>단추</h3>사각형 개체에 텍스트를 표시 합니다.<p align="center">![단추의 스크린샷](xaml-controls-images/Button.png "단추")</p>[API](xref:Xamarin.Forms.Button) / [가이드](~/xamarin-forms/user-interface/button.md) | <p valign="center"><pre>&lt;Button Text="Click Me!"<br />        Font="Large"<br />        BorderWidth="1"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand"<br />        Clicked="OnButtonClicked" /&gt;</pre></p> |
| <h3>ImageButton</h3>사각형 개체에 이미지를 표시 합니다.<p align="center">![ImageButton의 스크린샷](xaml-controls-images/ImageButton.png "ImageButton")</p>[API](xref:Xamarin.Forms.ImageButton) / [가이드](~/xamarin-forms/user-interface/imagebutton.md) | <p valign="center"><pre>&lt;ImageButton Source="XamarinLogo.png"<br />             HorizontalOptions="Center"<br />             VerticalOptions="CenterAndExpand"<br />             Clicked="OnImageButtonClicked" /&gt;</pre></p> |
| <h3>RefreshView</h3>스크롤할 수 있는 콘텐츠를 위한 끌어오기-새로 고침 기능을 제공 합니다.<p align="center">![RefreshView의 스크린샷](xaml-controls-images/RefreshView.png "RefreshView")</p>[도움이](~/xamarin-forms/user-interface/refreshview.md) | <p valign="center"><pre>&lt;RefreshView IsRefreshing="{Binding IsRefreshing}"<br />             Command="{Binding RefreshCommand}" &gt;<br />    &lt;!-- Scrollable control goes here --&gt;<br />&lt;/RefreshView&gt;</pre></p> |
| <h3>SearchBar</h3> 검색을 수행 하는 데 사용 하는 사용자 입력을 허용 합니다.<p align="center">![SearchBar의 스크린샷](xaml-controls-images/SearchBar.png "SearchBar")</p>[도움이](~/xamarin-forms/user-interface/searchbar.md) | <p valign="center"><pre>&lt;SearchBar Placeholder=&quot;Enter search term&quot;<br />           SearchButtonPressed="OnSearchBarButtonPressed" /&gt;</pre></p> |
| <h3>SwipeView</h3> 살짝 밀기 제스처로 표시 되는 상황에 맞는 메뉴 항목을 제공 합니다.<p align="center">![SwipeView 스크린샷](xaml-controls-images/SwipeView.png "SwipeView")</p>[도움이](~/xamarin-forms/user-interface/swipeview.md) | <p valign="center"><pre>&lt;SwipeView&gt;<br />    &lt;SwipeView.LeftItems&gt;<br />        &lt;SwipeItems&gt;<br />            &lt;SwipeItem Text="Delete"<br />                       IconImageSource="delete.png"<br />                       BackgroundColor="LightPink"<br />                       Invoked="OnDeleteInvoked" /&gt;<br />        &lt;/SwipeItems&gt;<br />    &lt;/SwipeView.LeftItems&gt;<br />    &lt;!-- Content --&gt;<br />&lt;/SwipeView&gt;</pre></p> |
|     |     |

## <a name="views-for-setting-values"></a>값 설정 뷰

|     |     |
| --- | --- |
| <h3>CheckBox</h3>`boolean` 값을 선택할 수 있습니다.<p align="center">![CheckBox의 스크린샷](xaml-controls-images/CheckBox.png "CheckBox")</p> [도움이](~/xamarin-forms/user-interface/checkbox.md) | <p valign="center"><pre>&lt;CheckBox IsChecked="true"<br />          HorizontalOptions="Center"<br />          VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>슬라이더</h3>연속 범위에서 `double` 값을 선택할 수 있습니다.<p align="center">![슬라이더의 스크린샷](xaml-controls-images/Slider.png "슬라이더")</p>[API](xref:Xamarin.Forms.Slider) / [가이드](~/xamarin-forms/user-interface/slider.md) | <p valign="center"><pre>&lt;Slider Minimum="0"<br />        Maximum="100"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>Stepper</h3>증분 범위에서 `double` 값을 선택할 수 있습니다.<p align="center">![스텝 퍼의 스크린샷](xaml-controls-images/Stepper.png "Stepper")</p>[API](xref:Xamarin.Forms.Stepper) / [가이드](~/xamarin-forms/user-interface/stepper.md) | <p valign="center"><pre>&lt;Stepper Minimum="0"<br />         Maximum="10"<br />         Increment="0.1"<br />         HorizontalOptions="Center"<br />         VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>전환</h3>`boolean` 값을 선택할 수 있습니다.<p align="center">![스위치 스크린샷](xaml-controls-images/Switch.png "전환")</p>[API](xref:Xamarin.Forms.Switch) / [가이드](~/xamarin-forms/user-interface/switch.md)| <p valign="center"><pre>&lt;Switch IsToggled="false"<br />        HorizontalOptions="Center"<br />        VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>DatePicker</h3>날짜를 선택할 수 있습니다.<p align="center">![DatePicker 스크린샷](xaml-controls-images/DatePicker.png "DatePicker")</p>[API](xref:Xamarin.Forms.DatePicker) / [가이드](~/xamarin-forms/user-interface/datepicker.md) | <p valign="center"><pre>&lt;DatePicker Format="D"<br/>            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>TimePicker</h3>시간을 선택할 수 있습니다.<p align="center">![TimePicker의 스크린샷](xaml-controls-images/TimePicker.png "TimePicker")</p>[API](xref:Xamarin.Forms.TimePicker) / [가이드](~/xamarin-forms/user-interface/timepicker.md) | <p valign="center"><pre>&lt;TimePicker Format="T"<br />            VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-for-editing-text"></a>텍스트 편집 뷰

|     |     |
| --- | --- |
| <h3>항목</h3>한 줄의 텍스트를 입력 하 고 편집할 수 있습니다.<p align="center">![항목의 스크린샷](xaml-controls-images/Entry.png "항목")</p>[API](xref:Xamarin.Forms.Entry) / [가이드](~/xamarin-forms/user-interface/text/entry.md) | <p valign="center"><pre>&lt;Entry Keyboard="Email"<br />       Placeholder="Enter email address"<br />       VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>편집기</h3>여러 줄의 텍스트를 입력 하 고 편집할 수 있습니다.<p align="center">![편집기의 스크린샷](xaml-controls-images/Editor.png "레이블")</p>[API](xref:Xamarin.Forms.Editor) / [가이드](~/xamarin-forms/user-interface/text/editor.md) | <p valign="center"><pre>&lt;Editor VerticalOptions="FillAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-to-indicate-activity"></a>작업 표시 뷰

|     |     |
| --- | --- |
| <h3>ActivityIndicator</h3>진행률을 표시 하지 않고 응용 프로그램이 긴 작업에서 사용 되 고 있음을 보여 주는 애니메이션을 표시 합니다.<p align="center">![ActivityIndicator의 스크린샷](xaml-controls-images/ActivityIndicator.png "ActivityIndicator")</p>[API](xref:Xamarin.Forms.ActivityIndicator) / [가이드](~/xamarin-forms/user-interface/activityindicator.md) | <p valign="center"><pre>&lt;ActivityIndicator IsRunning="True"<br />                   VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
| <h3>ProgressBar</h3>응용 프로그램에서 시간이 오래 걸리는 작업을 진행 하 고 있음을 보여 주는 애니메이션을 표시 합니다.<p align="center">![ProgressBar의 스크린샷](xaml-controls-images/ProgressBar.png "ProgressBar")</p>[API](xref:Xamarin.Forms.ProgressBar) / [가이드](~/xamarin-forms/user-interface/progressbar.md) | <p valign="center"><pre>&lt;ProgressBar Progress=".5"<br />             VerticalOptions="CenterAndExpand" /&gt;</pre></p> |
|     |     |

## <a name="views-that-display-collections"></a>컬렉션 표시 뷰

|     |     |
| --- | --- |
| <h3>CarouselView</h3>스크롤 가능한 데이터 항목 목록을 표시 합니다.<p align="center">![CarouselView 스크린샷](xaml-controls-images/CarouselView.png "CarouselView")</p>[도움이](~/xamarin-forms/user-interface/carouselview/index.md) | <p valign="center"><pre>&lt;CarouselView ItemsSource="{Binding Monkeys}"&gt;<br/>              ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p>|
| <h3>CollectionView</h3>다른 레이아웃 사양을 사용 하 여 선택 가능한 데이터 항목의 스크롤 가능한 목록을 표시 합니다.<p align="center">![CollectionView 스크린샷](xaml-controls-images/CollectionView.png "CollectionView")</p>[도움이](~/xamarin-forms/user-interface/collectionview/index.md) | <p valign="center"><pre>&lt;CollectionView ItemsSource="{Binding Monkeys}"&gt;<br/>                ItemTemplate="{StaticResource MonkeyTemplate}"<br />    &lt;CollectionView.ItemsLayout&gt;<br />       &lt;GridItemsLayout Orientation="Vertical"<br />                        Span="2" /&gt;<br />    &lt;/CollectionView.ItemsLayout&gt;<br />&lt;/CollectionView/&gt;</pre></p> |
| <h3>IndicatorView</h3>`CarouselView`의 항목 수를 나타내는 표시기를 표시 합니다.<p align="center">![IndicatorView의 스크린샷](xaml-controls-images/IndicatorView.png "IndicatorView")</p>[도움이](~/xamarin-forms/user-interface/indicatorview.md) | <p valign="center"><pre>&lt;IndicatorView x:Name="indicatorView"<br />               IndicatorColor="LightGray"<br />               SelectedIndicatorColor="DarkGray" /&gt;</pre></p> |
| <h3>ListView</h3>스크롤할 수 있는 선택 가능한 데이터 항목의 목록을 표시 합니다.<p align="center">![ListView의 스크린샷](xaml-controls-images/ListView.png "ListView")</p>[API](xref:Xamarin.Forms.ListView) / [가이드](~/xamarin-forms/user-interface/listview/index.md) | <p valign="center"><pre>&lt;ListView ItemsSource="{Binding Monkeys}"&gt;<br />          ItemTemplate="{StaticResource MonkeyTemplate}" /&gt;</pre></p> |
| <h3>선택기</h3>텍스트 문자열 목록에서 선택 항목을 표시 합니다.<p align="center">![선택의 스크린샷](xaml-controls-images/Picker.png "선택기")</p>[API](xref:Xamarin.Forms.Picker) / [가이드](~/xamarin-forms/user-interface/picker/index.md) | <p valign="center"><pre>&lt;Picker Title="Select a monkey"<br />        TitleColor="Red"&gt;<br />  &lt;Picker.ItemsSource&gt;<br />    &lt;x:Array Type="{x:Type x:String}"&gt;<br />      &lt;x:String&gt;Baboon&lt;/x:String&gt;<br />      &lt;x:String&gt;Capuchin Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Blue Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Squirrel Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Golden Lion Tamarin&lt;/x:String&gt;<br />      &lt;x:String&gt;Howler Monkey&lt;/x:String&gt;<br />      &lt;x:String&gt;Japanese Macaque&lt;/x:String&gt;<br />    &lt;/x:Array&gt;<br />  &lt;/Picker.ItemsSource&gt;<br />&lt;/Picker&gt;</pre></p> |
| <h3>TableView</h3>대화형 행 목록을 표시 합니다.<p align="center">![TableView 스크린샷](xaml-controls-images/TableView.png "TableView")</p>[API](xref:Xamarin.Forms.TableView) / [가이드](~/xamarin-forms/user-interface/tableview.md) | <p valign="center"><pre>&lt;TableView Intent="Settings"&gt;<br />    &lt;TableRoot&gt;<br />        &lt;TableSection Title="Ring"&gt;<br />            &lt;SwitchCell Text="New Voice Mail" /&gt;<br />            &lt;SwitchCell Text="New Mail" On="true" /&gt;<br />        &lt;/TableSection&gt;<br />    &lt;/TableRoot&gt;<br />&lt;/TableView&gt;</pre></p> |
|     |     |

## <a name="related-links"></a>관련 링크

- [Xamarin.ios 양식 갤러리 샘플](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms 샘플](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms API 설명서](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
