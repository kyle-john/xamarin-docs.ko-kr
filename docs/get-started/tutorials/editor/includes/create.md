---
ms.openlocfilehash: fe7e11f37f303c2ec1f5bc58e98953449694191c
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77135057"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

이 자습서를 완료하려면 **.NET을 사용한 모바일 개발** 워크로드가 설치된 Visual Studio 2019(최신 릴리스)가 있어야 합니다. 또한 iOS에서 자습서 애플리케이션을 빌드하려면 페어링된 Mac이 필요합니다. Xamarin 플랫폼 설치에 대한 자세한 내용은 [Xamarin 설치](~/get-started/installation/index.md)를 참조하세요. Visual Studio 2019를 Mac 빌드 호스트에 연결하는 방법에 대한 자세한 내용은 [Xamarin.iOS 개발을 위해 Mac에 페어링](~/ios/get-started/installation/windows/connecting-to-mac/index.md)을 참조하세요.

1. Visual Studio를 실행하고 **EditorTutorial**라는 새 Xamarin.Forms 애플리케이션을 만듭니다. 앱이 공유 코드 메커니즘으로 .NET 표준을 사용하는지를 확인합니다.

    > [!IMPORTANT]
    > 이 자습서의 C# 및 XAML 코드 조각은 솔루션의 이름이 **EditorTutorial**이어야 합니다. 이 자습서의 코드를 솔루션으로 복사할 때 다른 이름을 사용하면 빌드 오류가 발생합니다.

    생성된 .NET 표준 라이브러리에 대한 자세한 내용은 [Xamarin.Forms 빠른 시작 심층 분석](~/get-started/first-app/index.md)에서 [Xamarin.Forms 애플리케이션 분석](~/get-started/first-app/index.md)을 참조하세요.

1. **솔루션 탐색기**의 **EditorTutorial** 프로젝트에서 **MainPage.xaml**을 두 번 클릭하여 엽니다. 그런 다음, **MainPage.xaml**에서 템플릿 코드를 모두 제거하고 다음 코드로 바꿉니다.

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="EditorTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Editor Placeholder="Enter multi-line text here"
                    HeightRequest="200" />
        </StackLayout>
    </ContentPage>
    ```

    이 코드는 [`StackLayout`](xref:Xamarin.Forms.StackLayout)에서 [`Editor`](xref:Xamarin.Forms.Editor)로 구성된 페이지에 대한 사용자 인터페이스를 선언적으로 정의합니다. [`Editor.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) 속성은 `Editor`가 처음 표시될 때 표시되는 자리 표시자 텍스트를 지정합니다. 또한 [`HeightRequest`](xref:Xamarin.Forms.VisualElement) 속성은 디바이스 독립적 단위로 `Editor`의 높이를 지정합니다.

1. Visual Studio 도구 모음에서 선택한 원격 iOS 시뮬레이터 또는 Android 에뮬레이터 내에서 애플리케이션을 시작하려면 **시작** 단추(재생 단추와 비슷한 삼각형 모양의 단추)를 누릅니다.

    [![iOS 및 Android에서 편집기의 스크린샷](../images/create-editor.png "자리 표시자 텍스트를 포함하는 편집기")](../images/create-editor-large.png#lightbox "자리 표시자 텍스트를 포함하는 편집기")

    > [!NOTE]
    > Android는 [`Editor`](xref:Xamarin.Forms.Editor)의 높이를 나타내지만 iOS는 그렇지 않습니다.

# <a name="visual-studio-for-mactabvsmac"></a>[Mac용 Visual Studio](#tab/vsmac)

이 자습서를 완료하려면 iOS 및 Android 플랫폼 지원이 설치된 Mac용 Visual Studio(최신 릴리스)가 있어야 합니다. 또한 Xcode(최신 릴리스)도 필요합니다. Xamarin 플랫폼 설치에 대한 자세한 내용은 [Xamarin 설치](~/get-started/installation/index.md)를 참조하세요.

1. Mac용 Visual Studio를 실행하고 **EditorTutorial**라는 새 Xamarin.Forms 애플리케이션을 만듭니다. 앱이 공유 코드 메커니즘으로 .NET 표준을 사용하는지를 확인합니다.

    > [!IMPORTANT]
    > 이 자습서의 C# 및 XAML 코드 조각은 솔루션의 이름이 **EditorTutorial**이어야 합니다. 이 자습서의 코드를 솔루션으로 복사할 때 다른 이름을 사용하면 빌드 오류가 발생합니다.

    생성된 .NET 표준 라이브러리에 대한 자세한 내용은 [Xamarin.Forms 빠른 시작 심층 분석](~/get-started/first-app/index.md)에서 [Xamarin.Forms 애플리케이션 분석](~/get-started/first-app/index.md)을 참조하세요.

1. **Solution Pad**의 **EditorTutorial** 프로젝트에서 **MainPage.xaml**을 두 번 클릭하여 엽니다. 그런 다음, **MainPage.xaml**에서 템플릿 코드를 모두 제거하고 다음 코드로 바꿉니다.

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="EditorTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Editor Placeholder="Enter multi-line text here"
                    HeightRequest="200" />
        </StackLayout>
    </ContentPage>
    ```

    이 코드는 [`StackLayout`](xref:Xamarin.Forms.StackLayout)에서 [`Editor`](xref:Xamarin.Forms.Editor)로 구성된 페이지에 대한 사용자 인터페이스를 선언적으로 정의합니다. [`Editor.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) 속성은 `Editor`가 처음 표시될 때 표시되는 자리 표시자 텍스트를 지정합니다. 또한 [`HeightRequest`](xref:Xamarin.Forms.VisualElement) 속성은 디바이스 독립적 단위로 `Editor`의 높이를 지정합니다.

1. Mac용 Visual Studio 도구 모음에서 선택한 iOS 시뮬레이터 또는 Android 에뮬레이터 내에서 애플리케이션을 시작하려면 **시작** 단추(재생 단추와 비슷한 삼각형 모양의 단추)를 누릅니다.

    [![iOS 및 Android에서 편집기의 스크린샷](../images/create-editor.png "자리 표시자 텍스트를 포함하는 편집기")](../images/create-editor-large.png#lightbox "자리 표시자 텍스트를 포함하는 편집기")

    > [!NOTE]
    > Android는 [`Editor`](xref:Xamarin.Forms.Editor)의 높이를 나타내지만 iOS는 그렇지 않습니다.
