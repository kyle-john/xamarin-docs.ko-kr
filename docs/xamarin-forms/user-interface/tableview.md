---
title: Xamarin.ios TableView
description: 이 문서에서는 Xamarin.ios TableView 클래스를 사용 하 여 응용 프로그램에서 스크롤 메뉴, 설정 및 입력 폼을 표시 하는 방법을 설명 합니다.
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
ms.openlocfilehash: 67625aa413880023cce6d3e5e21e4d3bd0ec8e4c
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72695981"
---
# <a name="xamarinforms-tableview"></a>Xamarin.ios TableView

[![샘플 다운로드](~/media/shared/download.png) 샘플 다운로드](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)

[`TableView`](xref:Xamarin.Forms.TableView) 는 스크롤 가능한 데이터 목록이 나 같은 템플릿을 공유 하지 않는 행이 있는 선택 항목을 표시 하기 위한 뷰입니다. [ListView](~/xamarin-forms/user-interface/listview/index.md)와 달리 `TableView`에는 `ItemsSource` 개념이 없으므로 항목을 수동으로 자식으로 추가 해야 합니다.

![TableView 예제](tableview-images/tableview-all-sml.png)

<a name="Use_Cases" />

## <a name="use-cases"></a>사용 사례

[`TableView`](xref:Xamarin.Forms.TableView) 은 다음과 같은 경우에 유용 합니다.

- 설정 목록 표시
- 폼에서 데이터 수집 또는
- 행 마다 다르게 표시 되는 데이터 (예: 숫자, 비율 및 이미지)를 표시 합니다.

[`TableView`](xref:Xamarin.Forms.TableView) 는 위의 시나리오에 대 한 일반적인 요구 사항이 매력적인 섹션에서 행 스크롤 및 레이아웃을 처리 합니다. @No__t_0 컨트롤은 사용 가능한 경우 각 플랫폼의 기본 동일 뷰를 사용 하 여 각 플랫폼에 대 한 네이티브 모양을 만듭니다.

<a name="TableView_Structure" />

## <a name="structure"></a>구조체

[@No__t_1](xref:Xamarin.Forms.TableView) 요소는 섹션으로 구성 됩니다. @No__t_0의 루트에 있는 [`TableRoot`](xref:Xamarin.Forms.TableRoot)는 하나 이상의 [`TableSection`](xref:Xamarin.Forms.TableSection) 인스턴스의 부모입니다. 각 [`TableSection`](xref:Xamarin.Forms.TableSection) 는 제목과 하나 이상의 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 인스턴스로 구성 됩니다.

```xaml
<TableView Intent="Settings">
    <TableRoot>
        <TableSection Title="Ring">
            <SwitchCell Text="New Voice Mail" />
            <SwitchCell Text="New Mail" On="true" />
        </TableSection>
    </TableRoot>
</TableView>
```

해당하는 C# 코드는 다음과 같습니다.

```csharp
Content = new TableView
{
    Root = new TableRoot
    {
        new TableSection("Ring")
        {
          // TableSection constructor takes title as an optional parameter
          new SwitchCell { Text = "New Voice Mail" },
          new SwitchCell { Text = "New Mail", On = true }
        }
    },
    Intent = TableIntent.Settings
};
```

<a name="TableView_Appearance" />

## <a name="appearance"></a>모양

[`TableView`](xref:Xamarin.Forms.TableView) 는 [`TableIntent`](xref:Xamarin.Forms.TableIntent) 열거형 멤버로 설정할 수 있는 [`Intent`](xref:Xamarin.Forms.TableView.Intent) 속성을 노출 합니다.

- `Data` – 데이터 항목을 표시할 때 사용 됩니다. [ListView](~/xamarin-forms/user-interface/listview/index.md) 는 데이터 목록을 스크롤 하는 데 더 나은 옵션 일 수 있습니다.
- `Form` – TableView가 폼 역할을 하는 경우 사용 합니다.
- `Menu` – 선택 항목 메뉴를 표시할 때 사용 합니다.
- `Settings` – 구성 설정 목록을 표시 하는 데 사용 됩니다.

선택한 [`TableIntent`](xref:Xamarin.Forms.TableIntent) 값은 각 플랫폼에서 [`TableView`](xref:Xamarin.Forms.TableView) 표시 되는 방식에 영향을 줄 수 있습니다. 명확한 차이가 없는 경우에도 테이블 사용 방법과 가장 일치 하는 `TableIntent`를 선택 하는 것이 가장 좋습니다.

또한 `TextColor` 속성을 [`Color`](xref:Xamarin.Forms.Color)로 설정 하 여 각 [`TableSection`](xref:Xamarin.Forms.TableSection) 에 대해 표시 되는 텍스트의 색을 변경할 수 있습니다.

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>기본 제공 셀

Xamarin.ios는 정보를 수집 하 고 표시 하기 위한 기본 제공 셀과 함께 제공 됩니다. [@No__t_1](xref:Xamarin.Forms.ListView) 및 [`TableView`](xref:Xamarin.Forms.TableView) 에서 동일한 셀을 모두 사용할 수 있지만 [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) 및 [`EntryCell`](xref:Xamarin.Forms.EntryCell) 는 `TableView` 시나리오와 가장 관련이 있습니다.

[Textcell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) 및 [ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell)에 대 한 자세한 설명은 [ListView 셀 모양](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) 을 참조 하세요.

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) 는 설정/해제 또는 `true` / `false` 상태를 표시 하 고 캡처하는 데 사용 하는 컨트롤입니다. 다음 속성을 정의 합니다.

- `Text` – 스위치 옆에 표시할 텍스트입니다.
- `On` – 스위치를 on 또는 off로 표시할지 여부를 지정 합니다.
- `OnColor` – 스위치가 on 위치에 있을 때의 [`Color`](xref:Xamarin.Forms.Color) 입니다.

이러한 속성은 모두 바인딩할 수 있습니다.

또한 [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) 는 `OnChanged` 이벤트를 노출 하므로 셀의 상태 변경 내용에 응답할 수 있습니다.

![SwitchCell 예제](tableview-images/switch-cell.png)

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell

[`EntryCell`](xref:Xamarin.Forms.EntryCell) 은 사용자가 편집할 수 있는 텍스트 데이터를 표시 해야 하는 경우에 유용 합니다. 다음 속성을 정의 합니다.

- `Keyboard` – 편집 하는 동안 표시할 키보드입니다. 숫자 값, 전자 메일, 전화 번호 등과 같은 항목에 대 한 옵션이 있습니다. [API 문서를 참조](xref:Xamarin.Forms.Keyboard)하세요.
- `Label` – 텍스트 입력 필드 왼쪽에 표시할 레이블 텍스트입니다.
- `LabelColor` – 레이블 텍스트의 색입니다.
- `Placeholder` – 입력 필드가 null 이거나 비어 있는 경우 입력 필드에 표시 되는 텍스트입니다. 텍스트 항목이 시작 되 면이 텍스트가 사라집니다.
- `Text`-입력 필드의 텍스트입니다.
- `HorizontalTextAlignment` – 텍스트의 가로 맞춤입니다. 값은 가운데, 왼쪽 또는 오른쪽 맞춤입니다. [API 문서를 참조](xref:Xamarin.Forms.TextAlignment)하세요.
- `VerticalTextAlignment` – 텍스트의 세로 맞춤입니다. 값은 `Start`, `Center` 또는 `End`입니다.

또한 [`EntryCell`](xref:Xamarin.Forms.EntryCell) 는 텍스트를 편집 하는 동안 사용자가 키보드의 ' 완료 ' 단추를 누를 때 발생 하는 `Completed` 이벤트를 노출 합니다.

![EntryCell 예제](tableview-images/entry-cell.png)

<a name="Custom_Cells" />

## <a name="custom-cells"></a>사용자 지정 셀

기본 제공 셀이 충분 하지 않으면 사용자 지정 셀을 사용 하 여 응용 프로그램에 적합 한 방식으로 데이터를 표시 하 고 캡처할 수 있습니다. 예를 들어 사용자가 이미지의 불투명도를 선택할 수 있도록 슬라이더를 표시할 수 있습니다.

모든 사용자 지정 셀은 기본 제공 셀 형식이 모두 사용 하는 것과 동일한 기본 클래스인 [`ViewCell`](xref:Xamarin.Forms.ViewCell)에서 파생 되어야 합니다.

다음은 사용자 지정 셀의 예입니다.

![사용자 지정 셀 예제](tableview-images/custom-cell.png)

다음 예제에서는 위의 스크린샷에 [`TableView`](xref:Xamarin.Forms.TableView) 를 만드는 데 사용 되는 XAML을 보여 줍니다.

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DemoTableView.TablePage"
             Title="TableView">
      <TableView Intent="Settings">
          <TableRoot>
              <TableSection Title="Getting Started">
                  <ViewCell>
                      <StackLayout Orientation="Horizontal">
                          <Image Source="bulb.png" />
                          <Label Text="left"
                                 TextColor="#f35e20" />
                          <Label Text="right"
                                 HorizontalOptions="EndAndExpand"
                                 TextColor="#503026" />
                      </StackLayout>
                  </ViewCell>
              </TableSection>
          </TableRoot>
      </TableView>
</ContentPage>
```

해당하는 C# 코드는 다음과 같습니다.

```csharp
var table = new TableView();
table.Intent = TableIntent.Settings;
var layout = new StackLayout() { Orientation = StackOrientation.Horizontal };
layout.Children.Add (new Image() { Source = "bulb.png"});
layout.Children.Add (new Label()
{
    Text = "left",
    TextColor = Color.FromHex("#f35e20"),
    VerticalOptions = LayoutOptions.Center
});
layout.Children.Add (new Label ()
{
    Text = "right",
    TextColor = Color.FromHex ("#503026"),
    VerticalOptions = LayoutOptions.Center,
    HorizontalOptions = LayoutOptions.EndAndExpand
});
table.Root = new TableRoot ()
{
    new TableSection("Getting Started")
    {
        new ViewCell() {View = layout}
    }
};
Content = table;
```

[@No__t_1](xref:Xamarin.Forms.TableView) 아래에 있는 루트 요소는 [`TableRoot`](xref:Xamarin.Forms.TableRoot)이며 `TableRoot` 바로 아래에 [`TableSection`](xref:Xamarin.Forms.TableSection) 있습니다. [@No__t_1](xref:Xamarin.Forms.ViewCell) 은 `TableSection` 바로 아래에서 정의 되며, 여기서는 레이아웃을 사용 하는 경우에도 사용자 지정 셀의 레이아웃을 관리 하는 데 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 사용 됩니다.

> [!NOTE]
> [@No__t_1](xref:Xamarin.Forms.ListView)와 달리 [`TableView`](xref:Xamarin.Forms.TableView) 에서는 사용자 지정 (또는 임의) 셀이 `ItemTemplate`에 정의 되어 있지 않아도 됩니다.

## <a name="row-height"></a>행 높이

[@No__t_1](xref:Xamarin.Forms.TableView) 클래스에는 셀의 행 높이를 변경 하는 데 사용할 수 있는 두 가지 속성이 있습니다.

- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) – 각 행의 높이를 `int` 설정 합니다.
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) – `true`로 설정 된 경우 행의 높이가 달라 집니다. 이 속성을 `true`로 설정 하면 행 높이가 자동으로 계산 되어 Xamarin.ios에서 적용 됩니다.

[@No__t_1](xref:Xamarin.Forms.TableView) 의 셀에 있는 콘텐츠의 높이가 변경 되 면 Android 및 유니버설 WINDOWS 플랫폼 (UWP)에서 행 높이가 암시적으로 업데이트 됩니다. 그러나 iOS에서는 [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) 속성을 `true`로 설정 하 고 [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) 메서드를 호출 하 여 강제로 업데이트 해야 합니다.

다음 XAML 예제에서는 [`ViewCell`](xref:Xamarin.Forms.ViewCell)를 포함 하는 [`TableView`](xref:Xamarin.Forms.TableView) 를 보여 줍니다.

```xaml
<ContentPage ...>
    <TableView ...
               HasUnevenRows="true">
        <TableRoot>
            ...
            <TableSection ...>
                ...
                <ViewCell x:Name="_viewCell"
                          Tapped="OnViewCellTapped">
                    <Grid Margin="15,0">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="Auto" />
                        </Grid.RowDefinitions>
                        <Label Text="Tap this cell." />
                        <Label x:Name="_target"
                               Grid.Row="1"
                               Text="The cell has changed size."
                               IsVisible="false" />
                    </Grid>
                </ViewCell>
            </TableSection>
        </TableRoot>
    </TableView>
</ContentPage>
```

[@No__t_1](xref:Xamarin.Forms.ViewCell) 탭 하면 `OnViewCellTapped` 이벤트 처리기가 실행 됩니다.

```csharp
void OnViewCellTapped(object sender, EventArgs e)
{
    _target.IsVisible = !_target.IsVisible;
    _viewCell.ForceUpdateSize();
}
```

@No__t_0 이벤트 처리기는 [`ViewCell`](xref:Xamarin.Forms.ViewCell)에서 두 번째 [`Label`](xref:Xamarin.Forms.Label) 를 표시 하거나 숨기고 [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) 메서드를 호출 하 여 셀의 크기를 명시적으로 업데이트 합니다.

다음 스크린샷에는를 탭 하기 전의 셀이 표시 됩니다.

![크기를 조정 하기 전의 ViewCell](tableview-images/cell-beforeresize.png)

다음 스크린샷은 셀을 탭 하 여 표시 합니다.

![크기를 조정한 후의 ViewCell](tableview-images/cell-afterresize.png)

> [!IMPORTANT]
> 이 기능이 남용 경우 성능이 저하 될 가능성이 있습니다.

## <a name="related-links"></a>관련 링크

- [TableView (샘플)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)
