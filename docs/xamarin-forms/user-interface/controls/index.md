---
title: 컨트롤 참조
description: Xamarin Forms 응용 프로그램을 생성 하는 데 사용 되는 모든 사용자 인터페이스 요소에 대 한 설명입니다. 이 문서에서는 Xamarin.Forms 응용 프로그램의 사용자 인터페이스를 구성 하는 컨트롤 그룹을 나열 합니다.
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2019
ms.openlocfilehash: b9436603c17eb8008f470e75a52a93e8e122034f
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488117"
---
# <a name="controls-reference"></a>컨트롤 참조

[![샘플 다운로드](~/media/shared/download.png) 샘플 다운로드](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

Xamarin.ios 응용 프로그램의 사용자 인터페이스는 각 대상 플랫폼의 네이티브 컨트롤에 매핑되는 개체를 구성 합니다. 이렇게 하면 iOS, Android 및 유니버설 Windows 플랫폼에 대 한 플랫폼별 응용 프로그램이 [.NET Standard 라이브러리](~/cross-platform/app-fundamentals/net-standard.md)에 포함 된 xamarin.ios 코드를 사용할 수 있습니다.

Xamarin Forms 응용 프로그램의 사용자 인터페이스를 만드는 데 사용 되는 네 가지 기본 컨트롤 그룹은 다음과 같습니다.

- [**페이지**](pages.md)
- [**레이아웃**](layouts.md)
- [**레이아웃**](views.md)
- [**셀**](cells.md)

Xamarin.Forms 페이지는 일반적으로 전체 화면을 차지 합니다. 일반적으로 페이지 뷰 및 기타 레이아웃을 포함 하는 레이아웃을 포함 합니다. 셀을 함께 사용 하는 특수 한 구성 요소 [ `TableView` ](views.md#tableview) 하 고 [ `ListView` ](views.md#listview)합니다. Xamarin에서 사용자 인터페이스를 빌드하는 데 일반적으로 사용 되는 형식의 계층 구조를 보여 주는 클래스 다이어그램입니다. 폼은 [Xamarin.ios Controls 클래스 계층 구조](~/xamarin-forms/internals/class-hierarchy.md)에서 찾을 수 있습니다.

네 가지 문서에서 [ **페이지**](pages.md)합니다 [ **레이아웃**](layouts.md)를 [ **뷰** ](views.md), 및 [ **셀**](cells.md), 컨트롤의 각 형식 (있는 경우) 해당 API 설명서, 용도 (있는 경우)를 설명 하는 아티클을 하나 이상의 샘플 프로그램에 대 한 링크를 사용 하 여 설명 합니다. 각 컨트롤 형식에는 iOS 및 Android 장치에서 실행 되는 [**양식 갤러리**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) 샘플의 페이지를 보여 주는 스크린샷도 함께 제공 됩니다. C# 페이지에 해당 하는 XAML 페이지에 대 한 소스 코드에 대 한 링크 되며 (필요한 경우) 아래 스크린샷은 각 XAML 페이지에 대 한 C# 코드 숨김 파일입니다.

> [!NOTE]
> 페이지, 레이아웃 및 뷰는 `VisualElement` 클래스에서 파생 됩니다. `VisualElement` 클래스는 클래스를 파생 하는 데 유용한 다양 한 속성, 메서드 및 이벤트를 제공 합니다. 자세한 내용은 [Visualelement 속성, 메서드 및 이벤트](common-properties.md)를 참조 하세요.

Xamarin.ios를 사용 하 여 제공 되는 컨트롤 외에도 타사 컨트롤을 사용할 수 있습니다. 자세한 내용은 [타사 컨트롤](thirdparty.md)을 참조 하세요.

## <a name="related-links"></a>관련 링크

- [Xamarin.Forms FormsGallery 샘플](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.ios Controls 클래스 계층 구조](~/xamarin-forms/internals/class-hierarchy.md)
- [API 문서](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
