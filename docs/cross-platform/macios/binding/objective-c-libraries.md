---
title: 바인딩 목표-C 라이브러리
description: 이 문서에서는 목표-C 코드에 대 한 바인딩을 만드는 C# 방법에 대 한 개략적인 개요를 제공 하 여 이벤트, 메서드, 사용자 지정 컨트롤 등을 바인딩하는 방법을 설명 합니다.
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
author: davidortinau
ms.author: daortin
ms.date: 03/06/2018
ms.openlocfilehash: 6841e94ad13357c51e6ccf59e35c659dfb9954aa
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79306028"
---
# <a name="binding-objective-c-libraries"></a>바인딩 목표-C 라이브러리

Xamarin.ios 또는 Xamarin.ios로 작업 하는 경우 타사 목표-C 라이브러리를 사용 하려는 경우가 있을 수 있습니다. 이러한 경우 Xamarin 바인딩 프로젝트를 사용 하 여 네이티브 목표-C C# 라이브러리에 대 한 바인딩을 만들 수 있습니다. 프로젝트는 iOS 및 Mac Api를 가져오는 데 사용 하는 것과 동일한 도구를 C#사용 합니다.

이 문서에서는 목적-C Api를 바인딩하는 방법에 대해 설명 합니다. C Api만 바인딩하는 경우에는 [P/Invoke 프레임 워크](https://www.mono-project.com/docs/advanced/pinvoke/)의 표준 .net 메커니즘을 사용 해야 합니다.
C 라이브러리를 정적으로 연결 하는 방법에 대 한 자세한 내용은 [네이티브 라이브러리 연결](~/ios/platform/native-interop.md) 페이지에서 확인할 수 있습니다.

부록 [바인딩 형식 참조 가이드](~/cross-platform/macios/binding/binding-types-reference.md)를 참조 하세요.
또한 내부적으로 발생 하는 상황에 대해 자세히 알아보려면 [바인딩 개요](~/cross-platform/macios/binding/overview.md) 페이지를 참조 하세요.

바인딩은 iOS 및 Mac 라이브러리 모두에 대해 빌드할 수 있습니다.
이 페이지에서는 iOS 바인딩에 대해 작업을 수행 하는 방법을 설명 하지만 Mac 바인딩은 매우 유사 합니다.

**IOS에 대 한 샘플 코드**

[IOS 바인딩 샘플](https://github.com/xamarin/monotouch-samples/tree/master/BindingSample) 프로젝트를 사용 하 여 바인딩을 시험해 볼 수 있습니다.

<a name="Getting_Started" />

## <a name="getting-started"></a>시작

# <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/macos)

바인딩을 만드는 가장 쉬운 방법은 Xamarin.ios 바인딩 프로젝트를 만드는 것입니다.
프로젝트 형식, **iOS > 라이브러리 > 바인딩 라이브러리**를 선택 하 여 Mac용 Visual Studio에서이 작업을 수행할 수 있습니다.

[![](objective-c-libraries-images/00-sml.png "Do this from Visual Studio for Mac by selecting the project type, iOS Library Bindings Library")](objective-c-libraries-images/00.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

바인딩을 만드는 가장 쉬운 방법은 Xamarin.ios 바인딩 프로젝트를 만드는 것입니다.
프로젝트 형식,  **C# visual > Ios > 바인딩 라이브러리 (ios)** 를 선택 하 여 Windows의 visual Studio에서이 작업을 수행할 수 있습니다.

[![](objective-c-libraries-images/00vs-sml.png "iOS Bindings Library iOS")](objective-c-libraries-images/00vs.png#lightbox)

> [!IMPORTANT]
> 참고: **xamarin.ios** 에 대 한 바인딩 프로젝트는 Mac용 Visual Studio 에서만 지원 됩니다.

-----

생성 된 프로젝트에는 편집할 수 있는 작은 템플릿이 포함 되어 있으며, 두 개의 파일 (`ApiDefinition.cs` 및 `StructsAndEnums.cs`포함 되어 있습니다.

API 계약을 정의 하는 `ApiDefinition.cs`입니다 C#.이 파일은 기본 목표 C api가 프로젝션 되는 방법을 설명 하는 파일입니다. 이 파일의 구문과 내용은이 문서에 대 한 설명의 주요 항목이 며,이 문서의 내용은 인터페이스 및 C# C# 대리자 선언으로 제한 됩니다. `StructsAndEnums.cs` 파일은 인터페이스 및 대리자에 필요한 정의를 입력 하는 파일입니다. 여기에는 코드에서 사용할 수 있는 열거형 값 및 구조가 포함 됩니다.

<a name="Binding_an_API" />

## <a name="binding-an-api"></a>API 바인딩

포괄적인 바인딩을 수행 하려면 목표-C API 정의를 이해 하 고 .NET Framework 디자인 지침을 숙지 해야 합니다.

라이브러리를 바인딩하려면 일반적으로 API 정의 파일로 시작 합니다. API 정의 파일은 단순히 바인딩을 구동 C# 하는 데 도움이 C# 되는 몇 가지 특성을 사용 하 여 주석이 추가 된 인터페이스를 포함 하는 소스 파일입니다.  이 파일은 C# 와 목적-C의 계약이 정의 된 것입니다.

예를 들어 라이브러리에 대 한 간단한 api 파일은 다음과 같습니다.

```csharp
using Foundation;

namespace Cocos2D {
  [BaseType (typeof (NSObject))]
  interface Camera {
    [Static, Export ("getZEye")]
    nfloat ZEye { get; }

    [Export ("restore")]
    void Restore ();

    [Export ("locate")]
    void Locate ();

    [Export ("setEyeX:eyeY:eyeZ:")]
    void SetEyeXYZ (nfloat x, nfloat y, nfloat z);

    [Export ("setMode:")]
    void SetMode (CameraMode mode);
  }
}
```

위의 샘플에서는 `NSObject` 기본 형식에서 파생 되 고 (이 형식은 `Foundation.NSObject`에서 제공 됨) 정적 속성 (`ZEye`), 인수를 사용 하지 않는 두 개의 메서드 및 세 개의 인수를 사용 하는 메서드를 정의 하는 `Cocos2D.Camera` 라는 클래스를 정의 합니다.

API 파일의 형식 및 사용할 수 있는 특성에 대 한 자세한 내용은 아래의 [api 정의 파일](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) 섹션에서 설명 합니다.

완전 한 바인딩을 만들려면 일반적으로 다음 네 가지 구성 요소를 처리 합니다.

- API 정의 파일 (템플릿의`ApiDefinition.cs`)입니다.
- 선택 사항: API 정의 파일에 필요한 열거형, 형식, 구조체 (템플릿의`StructsAndEnums.cs`)입니다.
- 선택 사항: 생성 된 바인딩을 확장 하거나 더 C# 친숙 한 API (프로젝트에 추가 하는 모든 C# 파일)를 제공할 수 있는 추가 소스입니다.
- 바인딩할 네이티브 라이브러리입니다.

이 차트에서는 파일 간의 관계를 보여 줍니다.

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png "This chart shows the relationship between the files")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png#lightbox)

API 정의 파일에는 네임 스페이스 및 인터페이스 정의 (인터페이스에 포함 될 수 있는 모든 멤버 포함)만 포함 되며 클래스, 열거형, 대리자 또는 구조체는 포함 되지 않아야 합니다. API 정의 파일은 API를 생성 하는 데 사용 되는 계약 일 뿐입니다.

열거 나 지원 클래스와 같이 필요한 추가 코드는 별도의 파일에 호스팅해야 합니다. 위의 예제에서 "CameraMode"는 CS 파일에 없고 별도의 파일 (예: `StructsAndEnums.cs`)에서 호스팅되어야 하는 열거형 값입니다.

```csharp
public enum CameraMode {
    FlyOver, Back, Follow
}
```

`APIDefinition.cs` 파일은 `StructsAndEnum` 클래스와 결합 되며 라이브러리의 핵심 바인딩을 생성 하는 데 사용 됩니다. 결과로 생성 되는 라이브러리를 그대로 사용할 수 있지만 일반적으로 결과 라이브러리를 조정 하 여 사용자의 혜택에 대 한 C# 몇 가지 기능을 추가 하는 것이 좋습니다. 몇 가지 예로 `ToString()` 메서드 구현, 인덱서 제공 C# , 일부 네이티브 형식에 대 한 암시적 변환 추가 또는 일부 메서드의 강력한 형식의 버전 제공 등이 있습니다. 이러한 향상 된 기능은 추가 C# 파일에 저장 됩니다. 프로젝트에 C# 파일을 추가 하기만 하면이 빌드 프로세스에 포함 됩니다.

`Extra.cs` 파일에서 코드를 구현 하는 방법을 보여 줍니다. Partial 클래스를 사용 하는 것은 `ApiDefinition.cs`와 `StructsAndEnums.cs` 코어 바인딩의 조합에서 생성 되는 partial 클래스를 확대 하는 것입니다.

```csharp
public partial class Camera {
    // Provide a ToString method
    public override string ToString ()
    {
         return String.Format ("ZEye: {0}", ZEye);
    }
}
```

라이브러리를 빌드하면 기본 바인딩이 생성 됩니다.

이 바인딩을 완료 하려면 네이티브 라이브러리를 프로젝트에 추가 해야 합니다.  이 작업을 수행 하려면 프로젝트에서 네이티브 라이브러리를 솔루션 탐색기의 프로젝트에 끌어 놓거나 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **추가** > **파일 추가** 를 선택 하 여 네이티브 라이브러리를 선택 합니다.
규칙에 따라 네이티브 라이브러리는 "lib" 라는 단어로 시작 하 고 ".a" 확장명으로 끝납니다. 이 작업을 수행 하면 Mac용 Visual Studio 파일 및 네이티브 라이브러리에 포함 된 내용에 대 한 정보를 C# 포함 하는 자동으로 채워진 파일 등 두 개의 파일이 추가 됩니다.

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png "Native libraries by convention start with the word lib and end with the extension .a")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png#lightbox)

`libMagicChord.linkwith.cs` 파일의 내용에는이 라이브러리를 사용 하는 방법에 대 한 정보가 포함 되어 있으며이 바이너리를 결과 DLL 파일에 패키지할 IDE에 지시 합니다.

```csharp
using System;
using ObjCRuntime;

[assembly: LinkWith ("libMagicChord.a", SmartLink = true, ForceLoad = true)]
```

[`[LinkWith]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute) 사용 방법에 대 한 전체 정보 
특성은 [바인딩 형식 참조 가이드](~/cross-platform/macios/binding/binding-types-reference.md)에 설명 되어 있습니다.

이제 프로젝트를 빌드할 때 바인딩과 네이티브 라이브러리를 모두 포함 하는 `MagicChords.dll` 파일이 표시 됩니다. 이 프로젝트 또는 결과 DLL을 다른 개발자에 게 직접 사용 하기 위해 배포할 수 있습니다.

경우에 따라 몇 가지 열거 값, 대리자 정의 또는 기타 형식이 필요할 수 있습니다. API 정의 파일에이를 넣지 마세요 .이는 단지 계약 일 뿐입니다.

<a name="The_API_definition_file" />

## <a name="the-api-definition-file"></a>API 정의 파일

API 정의 파일은 다양 한 인터페이스로 구성 됩니다. API 정의의 인터페이스는 클래스 선언으로 설정 되 고, 클래스에 대 한 기본 클래스를 지정 하려면 [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 특성으로 데코레이팅 되어야 합니다.

계약 정의에 대 한 인터페이스 대신 클래스를 사용 하지 않는 이유를 궁금할 수 있습니다. API 정의 파일에 메서드 본문을 제공 하거나 예외를 throw 하거나 의미 있는 값을 반환 해야 하는 본문을 제공 하지 않고도 메서드에 대 한 계약을 작성할 수 있기 때문에 인터페이스를 선택 했습니다.

그러나 인터페이스를 뼈대로 사용 하 여 클래스를 생성 하기 때문에, 계약의 다양 한 부분을 특성으로 데코레이팅 하 여 바인딩을 구동 해야 했습니다.

<a name="Binding_Methods" />

### <a name="binding-methods"></a>바인딩 메서드

가장 간단한 바인딩은 메서드를 바인딩하는 것입니다. C# 명명 규칙을 사용 하 여 인터페이스에서 메서드를 선언 하 고 메서드를 [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 사용 하 여 데코 레이트 합니다.
특성도. [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 특성은 Xamarin.ios 런타임의 목적과 이름 C# 에 연결 하는 것입니다. [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 매개 변수입니다. 
특성은 목표-C 선택기의 이름입니다. 몇 가지 예는 다음과 같습니다.

```csharp
// A method, that takes no arguments
[Export ("refresh")]
void Refresh ();

// A method that takes two arguments and return the result
[Export ("add:and:")]
nint Add (nint a, nint b);

// A method that takes a string
[Export ("draw:atColumn:andRow:")]
void Draw (string text, nint column, nint row);
```

위의 샘플에서는 인스턴스 메서드를 바인딩하는 방법을 보여 줍니다. 정적 메서드를 바인딩하려면 다음과 같이 [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) 특성을 사용 해야 합니다.

```csharp
// A static method, that takes no arguments
[Static, Export ("refresh")]
void Beep ();
```

이는 계약이 인터페이스의 일부 이며 인터페이스에 정적 vs 인스턴스 선언을 포함 하지 않으므로 특성을 다시 사용할 필요가 없기 때문에 필요 합니다. 바인딩에서 특정 메서드를 숨기려면 메서드를 [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) 특성으로 데코레이팅 할 수 있습니다.

`btouch-native` 명령은 참조 매개 변수가 null이 아닌지 확인 합니다. 특정 매개 변수에 null 값을 허용 하려면 [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) 을 사용 합니다.
매개 변수의 특성은 다음과 같습니다.

```csharp
[Export ("setText:")]
string SetText ([NullAllowed] string text);
```

참조 형식을 내보낼 때 [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 키워드를 사용 하 여 할당 의미 체계를 지정할 수도 있습니다. 이는 데이터가 누출 되지 않도록 하기 위해 필요 합니다.

<a name="Binding_Properties" />

### <a name="binding-properties"></a>바인딩 속성

메서드와 마찬가지로, 목표-C 속성은 [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 를 사용 하 여 바인딩됩니다.
특성 및 속성에 C# 직접 매핑됩니다. 메서드와 마찬가지로 속성은 [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) 로 데코레이팅 될 수 있습니다.
및 [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
특성.

에서 속성에 [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 특성을 사용 하면 btouch-native는 실제로 getter와 setter의 두 메서드를 바인딩합니다. 내보내기를 위해 제공 하는 이름은 **basename** 이 고, "set" 단어 앞에 "set" 단어를 앞에 사용 하 여 ( **basename** 의 첫 문자를 대문자로 설정 하 고 선택 기가 인수를 사용 하는 경우) setter가 계산 됩니다. 즉, 속성에 적용 된 `[Export ("label")]`는 실제로 "label" 및 "setLabel:" 객관적인 C 메서드를 바인딩합니다.

경우에 따라 목표 C 속성은 위에서 설명한 패턴을 따르지 않고 이름을 수동으로 덮어씁니다. 이러한 경우 [`[Bind]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAttribute) 를 사용 하 여 바인딩이 생성 되는 방식을 제어할 수 있습니다. 
getter 또는 setter의 특성 (예:)

```csharp
[Export ("menuVisible")]
bool MenuVisible { [Bind ("isMenuVisible")] get; set; }
```

"IsMenuVisible" 및 "setMenuVisible:"을 바인딩합니다. 필요에 따라 속성은 다음 구문을 사용 하 여 바인딩할 수 있습니다.

```csharp
[Category, BaseType(typeof(UIView))]
interface UIView_MyIn
{
  [Export ("name")]
  string Name();

  [Export("setName:")]
  void SetName(string name);
}
```

여기서 getter 및 setter는 위의 `name` 및 `setName` 바인딩에 명시적으로 정의 됩니다.

[`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)를 사용 하는 정적 속성에 대 한 지원 외에도 [`[IsThreadStatic]`](~/cross-platform/macios/binding/binding-types-reference.md#IsThreadStaticAttribute)를 사용 하 여 스레드 정적 속성을 데코레이팅 할 수 있습니다. 예를 들면 다음과 같습니다.

```csharp
[Export ("currentRunLoop")][Static][IsThreadStatic]
NSRunLoop Current { get; }
```

메서드는 일부 매개 변수를 [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)플래그가 지정 될 수 있도록 허용 하는 것과 마찬가지로 [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
속성에 대해 null이 속성의 유효한 값 임을 나타내는 속성 (예:)

```csharp
[Export ("text"), NullAllowed]
string Text { get; set; }
```

Setter에서 직접 [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) 매개 변수를 지정할 수도 있습니다.

```csharp
[Export ("text")]
string Text { get; [NullAllowed] set; }
```

#### <a name="caveats-of-binding-custom-controls"></a>사용자 지정 컨트롤 바인딩에 대 한 주의 사항

사용자 지정 컨트롤에 대 한 바인딩을 설정할 때 다음 주의 사항을 고려해 야 합니다.

1. **바인딩 속성은 정적 이어야 합니다** . 속성의 바인딩을 정의할 때 [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) 특성을 사용 해야 합니다.
2. **속성 이름은 정확히 일치 해야** 합니다. 속성을 바인딩하는 데 사용 되는 이름은 사용자 지정 컨트롤의 속성 이름과 정확히 일치 해야 합니다.
3. **속성 형식이 정확히 일치 해야** 합니다. 속성을 바인딩하는 데 사용 된 변수 형식이 사용자 지정 컨트롤의 속성 형식과 정확히 일치 해야 합니다.
4. **중단점 및 getter/setter** -속성의 getter 또는 setter 메서드에 배치 된 중단점은 적중 되지 않습니다.
5. **콜백 관찰** -관찰 콜백을 사용 하 여 사용자 지정 컨트롤의 속성 값에 대 한 변경 내용을 알리도록 해야 합니다.

위에 나열 된 주의 사항에 주의 하지 않으면 런타임에 바인딩이 자동으로 실패할 수 있습니다.

<a name="MutablePattern" />

#### <a name="objective-c-mutable-pattern-and-properties"></a>목표-C 변경 가능 패턴 및 속성

목표-C 프레임 워크는 변경 가능한 하위 클래스로 일부 클래스를 변경할 수 없는 방법을 사용 합니다. 예를 들어 `NSString`은 변경할 수 없는 버전이 고 `NSMutableString`는 변형을 허용 하는 서브 클래스입니다.

이러한 클래스에는 변경할 수 없는 기본 클래스에 getter가 있지만 setter가 없는 속성이 포함 되어 있는지 확인 하는 것이 일반적입니다. 변경할 수 있는 버전의 경우 setter를 도입 합니다. 에서이 작업 C#을 수행할 수 없으므로에서 작업 하는 방법으로이 방법을 매핑해야 했습니다 C#.

이를 C# 매핑하는 방식은 기본 클래스에 getter와 setter를 모두 추가 하 고 setter에 [`[NotImplemented]`](~/cross-platform/macios/binding/binding-types-reference.md#NotImplementedAttribute) 을 추가 하는 것입니다.
특성도.

그런 다음 변경할 수 있는 하위 클래스에서 [`[Override]`](~/cross-platform/macios/binding/binding-types-reference.md#OverrideAttribute) 를 사용 합니다. 
속성이 실제로 부모의 동작을 재정의 하도록 속성에 대 한 특성입니다.

예제:

```csharp
[BaseType (typeof (NSObject))]
interface MyTree {
    string Name { get; [NotImplemented] set; }
}

[BaseType (typeof (MyTree))]
interface MyMutableTree {
    [Override]
    string Name { get; set; }
}
```

<a name="Binding_Constructors" />

### <a name="binding-constructors"></a>바인딩 생성자

`btouch-native` 도구는 클래스에서 fours 생성자를 자동으로 생성 하며, 지정 된 클래스 `Foo`에 대해를 생성 합니다.

- `Foo ()`: 기본 생성자 (객관적인-C의 "init" 생성자에 매핑됨)
- `Foo (NSCoder)`: NIB 파일의 deserialization 중에 사용 되는 생성자입니다 (의 "initWithCoder:" 생성자에 매핑됨).
- `Foo (IntPtr handle)`: 핸들 기반 생성을 위한 생성자는 런타임이 관리 되지 않는 개체에서 관리 되는 개체를 노출 해야 하는 경우 런타임에서 호출 됩니다.
- `Foo (NSEmptyFlag)`:이는 파생 클래스에서 이중 초기화를 방지 하는 데 사용 됩니다.

사용자가 정의 하는 생성자의 경우 인터페이스 정의 내에서 다음 서명을 사용 하 여 선언 해야 합니다. `IntPtr` 값을 반환 해야 하 고 메서드 이름이 생성자 여야 합니다. 예를 들어 `initWithFrame:` 생성자를 바인딩하려면 다음을 사용 합니다.

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);
```

<a name="Binding_Protocols" />

### <a name="binding-protocols"></a>바인딩 프로토콜

API 디자인 문서에 설명 된 대로 [모델 및 프로토콜](~/ios/internals/api-design/index.md#models)설명 섹션에서 Xamarin.ios는 목표-C 프로토콜을 [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) 플래그가 지정 된 클래스에 매핑합니다.
특성도. 이는 일반적으로 목표-C 대리자 클래스를 구현할 때 사용 됩니다.

일반적인 바인딩된 클래스와 대리자 클래스의 큰 차이점은 대리자 클래스가 하나 이상의 선택적 메서드를 가질 수 있다는 것입니다.

예를 들어 `UIKit` 클래스 `UIAccelerometerDelegate`를 살펴보겠습니다 .이는 Xamarin.ios에서 바인딩되는 방법입니다.

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface UIAccelerometerDelegate {
        [Export ("accelerometer:didAccelerate:")]
        void DidAccelerate (UIAccelerometer accelerometer, UIAcceleration acceleration);
}
```

이는 `UIAccelerometerDelegate`에 대 한 정의에 대 한 선택적 메서드 이므로 다른 작업은 수행할 수 없습니다. 그러나 프로토콜에 필요한 메서드가 있는 경우 [`[Abstract]`](~/cross-platform/macios/binding/binding-types-reference.md#AbstractAttribute) 를 추가 해야 합니다.
메서드에 대 한 특성입니다. 이렇게 하면 구현의 사용자가 실제로 메서드의 본문을 제공 합니다.

일반적으로 프로토콜은 메시지에 응답 하는 클래스에서 사용 됩니다. 일반적으로이 작업은 "delegate" 속성에 프로토콜의 메서드에 응답 하는 개체의 인스턴스를 할당 하 여 목표 C에서 수행 됩니다.

Xamarin.ios의 규칙은 `NSObject` 인스턴스가 대리자에 할당 될 수 있는 목표-C 느슨하게 결합 된 스타일을 모두 지원 하 고 강력한 형식의 버전을 노출 하는 것입니다. 따라서 일반적으로 강력한 형식의 `Delegate` 속성과 느슨하게 형식화 된 `WeakDelegate`를 모두 제공 합니다. 일반적으로 느슨하게 형식화 된 버전을 [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)에 바인딩하고 [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) 특성을 사용 하 여 강력한 형식의 버전을 제공 합니다.

`UIAccelerometer` 클래스를 바인딩하는 방법을 보여 줍니다.

```csharp
[BaseType (typeof (NSObject))]
interface UIAccelerometer {
        [Static] [Export ("sharedAccelerometer")]
        UIAccelerometer SharedAccelerometer { get; }

        [Export ("updateInterval")]
        double UpdateInterval { get; set; }

        [Wrap ("WeakDelegate")]
        UIAccelerometerDelegate Delegate { get; set; }

        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }
}
```

<a name="iOS7ProtocolSupport" />

**Monotouch.dialog 7.0의 새로운**

Monotouch.dialog 7.0부터 새롭게 향상 된 프로토콜 바인딩 기능이 통합 되었습니다.  이 새로운 지원을 통해 지정 된 클래스에서 하나 이상의 프로토콜을 도입 하는 목적-C 관용구을 보다 간단 하 게 사용할 수 있습니다.

목표-C에 `MyProtocol` 모든 프로토콜 정의에 대해 이제 모든 선택적 메서드를 제공 하는 확장 클래스 뿐만 아니라 프로토콜의 모든 필수 메서드를 나열 하는 `IMyProtocol` 인터페이스가 있습니다.  위의 Xamarin Studio 기능을 사용 하면 개발자는 이전 추상 모델 클래스의 개별 하위 클래스를 사용 하지 않고도 프로토콜 메서드를 구현할 수 있습니다.

[`[Protocol]`](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) 특성을 포함 하는 모든 정의는 실제로 프로토콜을 사용 하는 방법을 크게 개선 하는 세 가지 지원 클래스를 생성 합니다.

```csharp
// Full method implementation, contains all methods
class MyProtocol : IMyProtocol {
    public void Say (string msg);
    public void Listen (string msg);
}

// Interface that contains only the required methods
interface IMyProtocol: INativeObject, IDisposable {
    [Export ("say:")]
    void Say (string msg);
}

// Extension methods
static class IMyProtocol_Extensions {
    public static void Optional (this IMyProtocol this, string msg);
    }
}
```

**클래스 구현은** 의 개별 메서드를 재정의 하 고 완전 한 형식 안전성을 얻을 수 있는 완전 한 추상 클래스를 제공 합니다.  그러나 다중 상속 C# 을 지원 하지 않기 때문에 다른 기본 클래스를 포함 해야 하는 시나리오가 있습니다. 그러나 인터페이스를 구현 하려면

생성 된 **인터페이스 정의** 는로 제공 됩니다.  이 인터페이스는 프로토콜에서 필요한 모든 메서드를 포함 하는 인터페이스입니다.  이를 통해 개발자는 단순히 인터페이스를 구현 하는 프로토콜을 구현할 수 있습니다.  런타임은 프로토콜을 채택 하는 형식으로 자동으로 등록 됩니다.

인터페이스는 필요한 메서드만 나열 하 고 선택적 메서드를 노출 합니다.  즉, 프로토콜을 채택 하는 클래스는 필요한 메서드에 대해 전체 형식 검사를 수행 하지만 weak 형식 지정을 사용 해야 합니다. (수동으로 [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 를 사용 하 여 
선택적 프로토콜 메서드의 시그니처와 일치 하는 특성입니다.

프로토콜을 사용 하는 API를 편리 하 게 사용 하기 위해 바인딩 도구는 모든 선택적 메서드를 노출 하는 확장 메서드 클래스를 생성 합니다.  즉, API를 사용 하는 동안 모든 메서드를 포함 하는 것으로 프로토콜을 처리할 수 있습니다.

API에서 프로토콜 정의를 사용 하려면 API 정의에 빈 인터페이스를 작성 해야 합니다.  API에서 MyProtocol을 사용 하려면 다음을 수행 해야 합니다.

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}

interface IMyProtocol {}

[BaseType (typeof(NSObject))]
interface MyTool {
    [Export ("getProtocol")]
    IMyProtocol GetProtocol ();
}
```

위의 내용은 바인딩 시간에 `IMyProtocol` 없으므로 빈 인터페이스를 제공 해야 하기 때문에 필요 합니다.

#### <a name="adopting-protocol-generated-interfaces"></a>프로토콜 생성 인터페이스 채택

프로토콜에 대해 생성 되는 인터페이스 중 하나를 구현할 때마다 다음과 같이 합니다.

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

인터페이스 메서드에 대 한 구현은 적절 한 이름을 사용 하 여 자동으로 내보내지고 다음과 동일 합니다.

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

인터페이스를 암시적 또는 명시적으로 구현 하는 경우에는 중요 하지 않습니다.

<a name="Binding_Class_Extensions" />

### <a name="binding-class-extensions"></a>바인딩 클래스 확장

목표-C에서는 새 메서드를 C#사용 하 여 클래스를 확장할 수 있습니다. 이러한 방법 중 하나가 있는 경우 [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 사용할 수 있습니다. 
메서드를 목표-C 메시지의 수신자가 되도록 플래그를 지정 하는 특성입니다.

예를 들어 Xamarin.ios에서 다음과 같이 `UIKit`를 `NSStringDrawingExtensions`메서드로 가져올 때 `NSString`에 정의 된 확장 메서드를 바인딩합니다.

```csharp
[Category, BaseType (typeof (NSString))]
interface NSStringDrawingExtensions {
    [Export ("drawAtPoint:withFont:")]
    CGSize DrawString (CGPoint point, UIFont font);
}
```

<a name="Binding_Objective-C_Argument_Lists" />

### <a name="binding-objective-c-argument-lists"></a>바인딩 목표-C 인수 목록

Variadic 인수를 지원 합니다. 다음은 그 예입니다.

```objc
- (void) appendWorkers:(XWorker *) firstWorker, ...
  NS_REQUIRES_NIL_TERMINATION ;
```

에서 C# 이 메서드를 호출 하려면 다음과 같은 서명을 만들어야 합니다.

```csharp
[Export ("appendWorkers"), Internal]
void AppendWorkers (Worker firstWorker, IntPtr workersPtr)
```

이렇게 하면 메서드를 internal로 선언 하 고, 사용자 로부터 위의 API를 숨기고, 라이브러리에 노출 합니다. 그런 다음 다음과 같은 메서드를 작성할 수 있습니다.

```csharp
public void AppendWorkers(params Worker[] workers)
{
    if (workers == null)
         throw new ArgumentNullException ("workers");

    var pNativeArr = Marshal.AllocHGlobal(workers.Length * IntPtr.Size);
    for (int i = 1; i < workers.Length; ++i)
        Marshal.WriteIntPtr (pNativeArr, (i - 1) * IntPtr.Size, workers[i].Handle);

    // Null termination
    Marshal.WriteIntPtr (pNativeArr, (workers.Length - 1) * IntPtr.Size, IntPtr.Zero);

    // the signature for this method has gone from (IntPtr, IntPtr) to (Worker, IntPtr)
    WorkerManager.AppendWorkers(workers[0], pNativeArr);
    Marshal.FreeHGlobal(pNativeArr);
}
```

<a name="Binding_Fields" />

### <a name="binding-fields"></a>바인딩 필드

라이브러리에 선언 된 공용 필드에 액세스 하려는 경우가 있습니다.

일반적으로 이러한 필드는 참조 해야 하는 문자열이 나 정수 값을 포함 합니다. 일반적으로 특정 알림과 사전에서 키를 나타내는 문자열로 사용 됩니다.

필드를 바인딩하려면 인터페이스 정의 파일에 속성을 추가 하 고 [`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) 특성을 사용 하 여 속성을 데코 레이트 합니다. 이 특성은 조회할 기호의 C 이름 매개 변수 하나를 사용 합니다. 다음은 그 예입니다.

```csharp
[Field ("NSSomeEventNotification")]
NSString NSSomeEventNotification { get; }
```

`NSObject`에서 파생 되지 않은 정적 클래스에서 다양 한 필드를 래핑하려면 [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute_Class) 
클래스의 특성입니다. 예를 들면 다음과 같습니다.

```csharp
[Static]
interface LonelyClass {
    [Field ("NSSomeEventNotification")]
    NSString NSSomeEventNotification { get; }
}
```

위의는 `NSObject`에서 파생 되지 않으며 `NSString`으로 노출 되는 `NSSomeEventNotification`
에 대 한 바인딩을 포함 하는 `LonelyClass` 생성 합니다.`NSString`

[`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) 특성은 다음 데이터 형식에 적용 될 수 있습니다.

- `NSString` 참조 (읽기 전용 속성에만 해당)
- `NSArray` 참조 (읽기 전용 속성에만 해당)
- 32 비트 정수 (`System.Int32`)
- 64 비트 정수 (`System.Int64`)
- 32 비트 부동 소수점 (`System.Single`)
- 64 비트 부동 소수점 (`System.Double`)
- `System.Drawing.SizeF`
- `CGSize`

네이티브 필드 이름 외에도 라이브러리 이름을 전달 하 여 필드가 있는 라이브러리 이름을 지정할 수 있습니다.

```csharp
[Static]
interface LonelyClass {
    [Field ("SomeSharedLibrarySymbol", "SomeSharedLibrary")]
    NSString SomeSharedLibrarySymbol { get; }
}
```

정적으로 연결 하는 경우 바인딩할 라이브러리가 없으므로 `__Internal` 이름을 사용 해야 합니다.

```csharp
[Static]
interface LonelyClass {
    [Field ("MyFieldFromALibrary", "__Internal")]
    NSString MyFieldFromALibrary { get; }
}
```

<a name="Binding_Enums" />

### <a name="binding-enums"></a>열거형 바인딩

바인딩 파일에 직접 `enum`를 추가 하 여 다른 소스 파일 (바인딩과 최종 프로젝트 모두에서 컴파일해야 하는 다른 소스 파일을 사용 하지 않고 API 정의 내에서 더 쉽게 사용할 수 있음).

예제:

```csharp
[Native] // needed for enums defined as NSInteger in ObjC
enum MyEnum {}

interface MyType {
    [Export ("initWithEnum:")]
    IntPtr Constructor (MyEnum value);
}
```

`NSString` 상수를 대체 하는 고유한 열거형을 만들 수도 있습니다. 이 경우 생성기는 **자동으로** 열거형 값과 nsstring 상수를 변환 하는 메서드를 만듭니다.

예제:

```csharp
enum NSRunLoopMode {

    [DefaultEnumValue]
    [Field ("NSDefaultRunLoopMode")]
    Default,

    [Field ("NSRunLoopCommonModes")]
    Common,

    [Field (null)]
    Other = 1000
}

interface MyType {
    [Export ("performForMode:")]
    void Perform (NSString mode);

    [Wrap ("Perform (mode.GetConstant ())")]
    void Perform (NSRunLoopMode mode);
}
```

위의 예제에서 [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) 특성을 사용 하 여 `void Perform (NSString mode);` 데코레이팅 하도록 결정할 수 있습니다. 그러면 바인딩 소비자가 상수 기반 API를 **숨깁니다** .

그러나 더 좋은 API 대체에서 [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) 특성을 사용 하기 때문에 형식 서브클래싱이 제한 됩니다. 이러한 생성 된 메서드는 `virtual`되지 않습니다. 즉,이 메서드를 재정의할 수 없으며, 그렇지 않을 수도 있습니다.

또는 원래 `NSString`기반 정의를 `[Protected]`으로 표시 하는 것이 좋습니다. 이렇게 하면 서브 클래스화 작업을 수행할 수 있으며, 필요한 경우 wrap'ed 버전은 여전히 작동 하 고 재정의 된 메서드를 호출 합니다.

### <a name="binding-nsvalue-nsnumber-and-nsstring-to-a-better-type"></a>`NSValue`, `NSNumber`및 `NSString`을 더 나은 형식으로 바인딩

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) 특성을 사용 하면 `NSNumber`, `NSValue` 및 `NSString`(열거형)를 보다 정확한 C# 형식으로 바인딩할 수 있습니다. 특성을 사용 하 여 네이티브 API에 대 한 보다 정확한 .NET API를 만들 수 있습니다.

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)를 사용 하 여 메서드 (반환 값의 경우), 매개 변수 및 속성을 데코레이팅 할 수 있습니다. 유일한 제한 사항은 멤버가 [`[Protocol]`](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) 내에 **있지 않아야** 한다는 것입니다. 
또는 인터페이스를 [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) 합니다.

다음은 그 예입니다.

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

출력:

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

내부적으로는 `bool?` <-> `NSNumber` 하 고  <-> 변환 `NSValue` `CGRect`합니다.

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) 은 `NSNumber` `NSValue` 및 `NSString`(열거형)의 배열도 지원 합니다.

다음은 그 예입니다.

```csharp
[BindAs (typeof (CAScroll []))]
[Export ("supportedScrollModes")]
NSString [] SupportedScrollModes { get; set; }
```

출력:

```csharp
[Export ("supportedScrollModes")]
CAScroll [] SupportedScrollModes { get; set; }
```

`CAScroll` `NSString` 지원 되는 열거형 이므로 올바른 `NSString` 값을 가져오고 형식 변환을 처리 합니다.

지원 되는 변환 형식에 대 한 자세한 내용은 [`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) 설명서를 참조 하세요.

<a name="Binding_Notifications" />

### <a name="binding-notifications"></a>바인딩 알림

알림은 `NSNotificationCenter.DefaultCenter`에 게시 되 고 응용 프로그램의 한 부분에서 다른 부분으로 메시지를 브로드캐스트하는 메커니즘으로 사용 되는 메시지입니다. 개발자는 일반적으로 [Nsnotificationcenter](xref:Foundation.NSNotificationCenter)의 [AddObserver](xref:Foundation.NSNotificationCenter.AddObserver(Foundation.NSString,System.Action{Foundation.NSNotification})) 메서드를 사용 하 여 알림을 구독 합니다. 응용 프로그램은 알림 센터에 메시지를 게시할 때 일반적으로 [Nsnotification. UserInfo](xref:Foundation.NSNotification.UserInfo) 사전에 저장 된 페이로드를 포함 합니다. 이 사전은 약하게 형식화 되며, 사용자가 사전에 사용할 수 있는 키와 사전에 저장 될 수 있는 값의 유형을 일반적으로 설명서에서 읽어야 하므로 오류가 발생 하기 쉽습니다. 키가 있는 경우에도 부울로 사용 됩니다.

Xamarin.ios 바인딩 생성기는 개발자가 알림을 바인딩할 수 있도록 지원 합니다. 이렇게 하려면 [`[Notification]`](~/cross-platform/macios/binding/binding-types-reference.md#NotificationAttribute) 를 설정 합니다.
[`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) 태그가 지정 된 속성의 특성
속성 (public 또는 private 일 수 있음)

페이로드를 사용 하지 않는 알림에 대해 인수를 사용 하지 않고이 특성을 사용 하거나, 일반적으로 이름이 "EventArgs"로 끝나는 API 정의의 다른 인터페이스를 참조 하는 `System.Type`을 지정할 수 있습니다. 생성기는 인터페이스를 서브 클래스 `EventArgs` 하는 클래스로 전환 하 고 여기에 나열 된 모든 속성을 포함 합니다. EventArgs 클래스에서 값을 인출 하기 위해 목표-C 사전을 조회 하는 데 사용 되는 키의 이름을 나열 하려면 [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 특성을 사용 해야 합니다.

다음은 그 예입니다.

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

위의 코드는 다음 메서드를 사용 하 여 `MyClass.Notifications` 중첩 된 클래스를 생성 합니다.

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
   }
}
```

그러면 코드 사용자는 다음과 같은 코드를 사용 하 여 [Nsdefaultcenter](xref:Foundation.NSNotificationCenter.DefaultCenter) 에 게시 된 알림을 쉽게 구독할 수 있습니다.

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

`ObserveDidStart`에서 반환 된 값을 사용 하 여 다음과 같은 알림 수신을 쉽게 중지할 수 있습니다.

```csharp
token.Dispose ();
```

또는 [Nsnotification](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject)) 를 호출 하 고 토큰을 전달할 수 있습니다. 알림이 매개 변수를 포함 하는 경우 다음과 같이 도우미 `EventArgs` 인터페이스를 지정 해야 합니다.

```csharp
interface MyClass {
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}

// The helper EventArgs declaration
interface MyScreenChangedEventArgs {
    [Export ("ScreenXKey")]
    nint ScreenX { get; set; }

    [Export ("ScreenYKey")]
    nint ScreenY { get; set; }

    [Export ("DidGoOffKey")]
    [ProbePresence]
    bool DidGoOff { get; }
}
```

위의에서는 `ScreenX` 및 `ScreenY` 속성을 사용 하 여 `MyScreenChangedEventArgs` 클래스를 생성 합니다 .이 클래스는 "ScreenXKey" 및 "Screenxkey"를 각각 사용 하 여 적절 한 변환을 적용 합니다 [.](xref:Foundation.NSNotification.UserInfo) `[ProbePresence]` 특성은 값을 추출 하는 대신 `UserInfo`에서 키가 설정 되어 있는 경우 생성기를 검색 하는 데 사용 됩니다. 키가 값 (일반적으로 부울 값의 경우) 인 경우에 사용 됩니다.

이렇게 하면 다음과 같은 코드를 작성할 수 있습니다.

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

<a name="Binding_Categories" />

### <a name="binding-categories"></a>바인딩 범주

범주는 클래스에서 사용할 수 있는 메서드 및 속성 집합을 확장 하는 데 사용 되는 목표 C 메커니즘입니다.   실제로는 특정 프레임 워크가 연결 되어 있을 때 (예: `UIKit`) 기본 `NSObject`클래스의 기능을 확장 하 여 해당 메서드를 사용할 수 있도록 하는 데 사용 되며, 새 프레임 워크가에 연결 된 경우에만 사용 됩니다.   다른 경우에는 기능을 통해 클래스의 기능을 구성 하는 데 사용 됩니다.   이러한 메서드는 C# 확장 메서드와 유사 합니다. 다음은 목표-C에서 범주가 표시 되는 모양입니다.

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

위의 예에서는 라이브러리에 있는 `UIView` `makeBackgroundRed`메서드를 사용 하 여 인스턴스를 확장 합니다.

이를 바인딩하려면 인터페이스 정의에 [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) 특성을 사용할 수 있습니다.  [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) 사용 하는 경우
특성, [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 의 의미 
확장할 형식으로 확장할 기본 클래스를 지정 하는 데 사용 되는 특성 변경입니다.

다음은 `UIView` 확장을 바인딩하고 확장 메서드로 전환 C# 하는 방법을 보여 줍니다.

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

위의에서는 `MakeBackgroundRed` 확장 메서드를 포함 하는 클래스 `MyUIViewExtension`를 만듭니다.  즉, 이제는 `UIView` 서브 클래스에서 "MakeBackgroundRed"를 호출 하 여 목표에 대해 얻을 수 있는 것과 동일한 기능을 제공할 수 있습니다. 다른 경우에는 범주를 사용 하 여 시스템 클래스를 확장 하는 대신 기능을 구성 하는 용도로만 사용 됩니다.  다음과 같이:

```csharp
@interface SocialNetworking (Twitter)
- (void) postToTwitter:(Message *) message;
@end

@interface SocialNetworking (Facebook)
- (void) postToFacebook:(Message *) message andPicture: (UIImage*)
picture;
@end
```

[`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) 를 사용할 수 있지만
또한이 장식 스타일 선언에 대 한 특성은 모두 클래스 정의에만 추가할 수 있습니다.  이 두 가지 모두 동일한 작업을 수행 합니다.

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Twitter {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Facebook {
    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

이러한 경우에는 범주를 병합 하는 것이 더 간단 합니다.

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);

    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

<a name="Binding_Blocks" />

### <a name="binding-blocks"></a>바인딩 블록

블록은 기능을 목표로 하는 C# 무명 메서드와 동등한 기능을 제공 하기 위해 Apple에서 도입 된 새로운 구문입니다. 예를 들어, `NSSet` 클래스는 이제이 메서드를 노출 합니다.

```csharp
- (void) enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop) block
```

위의 설명은 `block`라는 인수 하나를 사용 하는 `enumerateObjectsUsingBlock:` 라는 메서드를 선언 합니다. 이 블록은 현재 환경 캡처 C# ("this" 포인터, 지역 변수 및 매개 변수에 대 한 액세스)를 지원 한다는 점에서 무명 메서드와 비슷합니다. `NSSet`에서 위의 메서드는 두 매개 변수 `NSObject` (`id obj` 부분)와 부울 (`BOOL *stop`) 부분에 대 한 포인터를 사용 하 여 블록을 호출 합니다.

이러한 종류의 API를 btouch와 바인딩하려면 먼저 블록 형식 시그니처를 C# 대리자로 선언 하 고 다음과 같이 api 진입점에서 참조 해야 합니다.

```csharp
// This declares the callback signature for the block:
delegate void NSSetEnumerator (NSObject obj, ref bool stop)

// Later, inside your definition, do this:
[Export ("enumerateObjectUsingBlock:")]
void Enumerate (NSSetEnumerator enum)
```

이제 코드에서 C#함수를 호출할 수 있습니다.

```csharp
var myset = new NSMutableSet ();
myset.Add (new NSString ("Foo"));

s.Enumerate (delegate (NSObject obj, ref bool stop){
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

원하는 경우에는 다음과 같은 경우에도 람다 식을 사용할 수 있습니다.

```csharp
var myset = new NSMutableSet ();
mySet.Add (new NSString ("Foo"));

s.Enumerate ((obj, stop) => {
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

<a name="GeneratingAsync" />

### <a name="asynchronous-methods"></a>비동기 메서드

바인딩 생성기는 특정 클래스의 메서드를 비동기 방식 메서드로 변환할 수 있습니다 (작업 또는&lt;태스크를 반환 하는 메서드&gt;).

[`[Async]`](~/cross-platform/macios/binding/binding-types-reference.md#AsyncAttribute) 를 사용할 수 있습니다. 
void를 반환 하 고 마지막 인수가 콜백 인 메서드의 특성입니다.  이를 메서드에 적용 하는 경우 바인딩 생성기는 `Async`접미사를 사용 하 여 해당 메서드의 버전을 생성 합니다.  콜백이 매개 변수를 사용 하지 않는 경우 반환 값은 `Task`가 됩니다. 콜백에서 매개 변수를 사용 하는 경우 결과는 `Task<T>`됩니다.  콜백이 여러 매개 변수를 사용 하는 경우 `ResultType` 또는 `ResultTypeName`를 설정 하 여 모든 속성을 보유할 생성 된 형식의 원하는 이름을 지정 해야 합니다.

예제:

```csharp
[Export ("loadfile:completed:")]
[Async]
void LoadFile (string file, Action<string> completed);
```

위의 코드에서는 Assembly.loadfile 메서드와를 모두 생성 합니다.

```csharp
[Export ("loadfile:completed:")]
Task<string> LoadFileAsync (string file);
```

<a name="Surfacing_Strong_Types" />

### <a name="surfacing-strong-types-for-weak-nsdictionary-parameters"></a>약한 NSDictionary 매개 변수에 대 한 강력한 형식

목표-C API의 많은 위치에서 매개 변수는 특정 키와 값이 포함 된 약한 형식의 `NSDictionary` Api로 전달 되지만 이러한 오류는 오류가 발생 하기 쉽습니다. (잘못 된 키를 전달 하 고 경고를 받을 수 있습니다. 잘못 된 값을 전달 하 고 경고를 받을 수 있음), 가능한 키 이름 및 값을 조회 하기 위해 문서를 여러 번 이동 해야 하므로 사용 하기에

이 솔루션은 강력한 형식의 API를 제공 하는 강력한 형식의 버전을 제공 하 고 내부적으로는 다양 한 기본 키 및 값을 매핑하는 것입니다.

예를 들어, 목표-C API가 `NSDictionary`를 수락 하 고 0.0에서 1.0 까지의 볼륨 값과 문자열을 사용 하는 `XyzCaptionKey` `NSNumber`를 사용 하는 키 `XyzVolumeKey`를 사용 하는 것으로 설명 된 경우 사용자에 게 다음과 같은 멋진 API를 사용 하도록 할 수 있습니다.

```csharp
public class  XyzOptions {
    public nfloat? Volume { get; set; }
    public string Caption { get; set; }
}
```

`Volume` 속성은 nullable float로 정의 됩니다. 즉, 목표-C의 규칙은 이러한 사전에 값을 지정할 필요가 없기 때문에 값이 설정 되지 않은 시나리오가 있습니다.

이렇게 하려면 다음과 같은 몇 가지 작업을 수행 해야 합니다.

- [DictionaryContainer](xref:Foundation.DictionaryContainer) 는 강력한 형식의 클래스를 만들고 각 속성에 대해 다양 한 getter 및 setter를 제공 합니다.
- 새 강력한 형식의 버전을 사용 `NSDictionary` 하는 메서드에 대 한 오버 로드를 선언 합니다.

강력한 형식의 클래스를 수동으로 만들거나 생성기를 사용 하 여 작업을 수행할 수 있습니다.  먼저이 작업을 수동으로 수행 하는 방법 및 자동 접근 방법을 파악 하는 방법을 알아봅니다.

이에 대 한 지원 파일을 만들어야 하며 계약 API로 이동 하지 않습니다.  XyzOptions 클래스를 만들기 위해 작성 해야 하는 작업은 다음과 같습니다.

```csharp
public class XyzOptions : DictionaryContainer {
# if !COREBUILD
    public XyzOptions () : base (new NSMutableDictionary ()) {}
    public XyzOptions (NSDictionary dictionary) : base (dictionary){}

    public nfloat? Volume {
       get { return GetFloatValue (XyzOptionsKeys.VolumeKey); }
       set { SetNumberValue (XyzOptionsKeys.VolumeKey, value); }
    }
    public string Caption {
       get { return GetStringValue (XyzOptionsKeys.CaptionKey); }
       set { SetStringValue (XyzOptionsKeys.CaptionKey, value); }
    }
# endif
}
```

그런 다음 하위 수준 API를 기반으로 상위 수준 API를 표시 하는 래퍼 메서드를 제공 해야 합니다.

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

API를 덮어쓰지 않아도 되는 경우 [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) 를 사용 하 여 NSDICTIONARY 기반 api를 안전 하 게 숨길 수 있습니다.
특성도.

여기에서 볼 수 있듯이 [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) 를 사용 합니다.
특성을 사용 하 여 새 API 진입점을 표시할 수 있으며, 강력한 형식의 `XyzOptions` 클래스를 사용 하 여 해당 진입점을 노출 합니다.  래퍼 메서드를 사용 하 여 null을 전달할 수도 있습니다.

여기서 언급 하지 않은 한 가지 사항은 `XyzOptionsKeys` 값의 출처입니다.  일반적으로 다음과 같이 API가 `XyzOptionsKeys`와 같은 정적 클래스의 키를 그룹화 합니다.

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
```

이러한 강력한 형식의 사전을 만들기 위한 자동 지원을 살펴보겠습니다.  이렇게 하면 다양 한 상용구가 사용 되지 않으며 외부 파일을 사용 하는 대신 API 계약에서 직접 사전을 정의할 수 있습니다.

강력한 형식의 사전을 만들려면 API에서 인터페이스를 도입 하 고 [StrongDictionary](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) 특성을 사용 하 여 데코 레이트 합니다.  이는 `DictionaryContainer`에서 파생 되 고 강력한 형식의 접근자를 제공 하는 인터페이스와 동일한 이름으로 클래스를 만들도록 생성기에 지시 합니다.

[`[StrongDictionary]`](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) 특성은 사전 키를 포함 하는 정적 클래스의 이름인 하나의 매개 변수를 사용 합니다.  그런 다음 인터페이스의 각 속성이 강력한 형식의 접근자가 됩니다.  기본적으로이 코드는 접근자를 만들기 위해 정적 클래스에 "Key" 라는 접미사를 사용 하 여 속성 이름을 사용 합니다.

즉, 강력한 형식의 접근자를 만들 때 더 이상 외부 파일이 필요 하지 않으며, 모든 속성에 대해 getter 및 setter를 수동으로 만들지 않고도 수동으로 키를 조회 하지 않아도 됩니다.

전체 바인딩이 다음과 같이 표시 됩니다.

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
[StrongDictionary ("XyzOptionKeys")]
interface XyzOptions {
    nfloat Volume { get; set; }
    string Caption { get; set; }
}

[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

`XyzOption` 멤버에서을 참조 해야 하는 경우 `Key`접미사가 있는 속성 이름이 아닌 다른 필드를 사용 하 여 속성을 데코레이팅 할 수 있습니다 [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
사용 하려는 이름을 가진 특성입니다.

<a name="Type_mappings" />

## <a name="type-mappings"></a>형식 매핑

이 섹션에서는 목표-C 형식을 형식에 매핑하는 C# 방법에 대해 설명 합니다.

<a name="Simple_Types" />

### <a name="simple-types"></a>단순 형식

다음 표에서는 목표-C 및 CocoaTouch 세계의 형식을 Xamarin.ios 세계에 매핑하는 방법을 보여 줍니다.

|목표-C 형식 이름|Xamarin.ios Unified API 형식|
|---|---|
|`BOOL`, `GLboolean`|`bool`|
|`NSInteger`|`nint`|
|`NSUInteger`|`nuint`|
|`CFTimeInterval` / `NSTimeInterval`|`double`|
|`NSString` ([NSString 바인딩에 대 한 자세한 정보](~/ios/internals/api-design/nsstring.md))|`string`|
|`char *`|`string` (참고 항목: [`[PlainString]`](~/cross-platform/macios/binding/binding-types-reference.md#plainstring))|
|`CGRect`|`CGRect`|
|`CGPoint`|`CGPoint`|
|`CGSize`|`CGSize`|
|`CGFloat`, `GLfloat`|`nfloat`|
|CoreFoundation 형식 (`CF*`)|`CoreFoundation.CF*`|
|`GLint`|`nint`|
|`GLfloat`|`nfloat`|
|기본 형식 (`NS*`)|`Foundation.NS*`|
|`id`|`Foundation`.`NSObject`|
|`NSGlyph`|`nint`|
|`NSSize`|`CGSize`|
|`NSTextAlignment`|`UITextAlignment`|
|`SEL`|`ObjCRuntime.Selector`|
|`dispatch_queue_t`|`CoreFoundation.DispatchQueue`|
|`CFTimeInterval`|`double`|
|`CFIndex`|`nint`|
|`NSGlyph`|`nuint`|

<a name="Arrays" />

### <a name="arrays"></a>배열

Xamarin.ios 런타임은 `NSArrays`로 배열을 자동으로 변환 C# 하 고 변환 하는 작업을 자동으로 수행 하므로, 예를 들어 `UIViews`의 `NSArray` 반환 하는 허수-C 메서드를 사용할 수 있습니다.

```csharp
// Get the peer views - untyped
- (NSArray *)getPeerViews ();

// Set the views for this container
- (void) setViews:(NSArray *) views
```

는 다음과 같이 바인딩됩니다.

```csharp
[Export ("getPeerViews")]
UIView [] GetPeerViews ();

[Export ("setViews:")]
void SetViews (UIView [] views);
```

이를 위해 강력한 형식의 C# 배열을 사용 하는 것이 좋습니다. 이렇게 하면 IDE에서 사용자가 추측 하지 않고도 실제 형식으로 적절 한 코드 완성 기능을 제공 하거나 설명서를 조회 하 여 배열에 포함 된 개체의 실제 형식을 확인할 수 있습니다.

배열에 포함 된 실제 가장 많이 파생 된 형식을 추적할 수 없는 경우에는 `NSObject []`를 반환 값으로 사용할 수 있습니다.

<a name="Selectors" />

### <a name="selectors"></a>선택기

선택기는 `SEL`특수 형식으로 목표-C API에 표시 됩니다. 선택기를 바인딩할 때 형식을 `ObjCRuntime.Selector`에 매핑합니다.  일반적으로 선택기는 개체, 대상 개체 및 대상 개체에서 호출할 선택기를 모두 사용 하 여 API에서 노출 됩니다. 이러한 두 가지 메서드를 모두 제공 하 C# 는 것은 대리자에 해당 하며 메서드를 호출할 개체 뿐만 아니라 호출할 메서드를 모두 캡슐화 합니다.

바인딩이 다음과 같이 표시 됩니다.

```csharp
interface Button {
   [Export ("setTarget:selector:")]
   void SetTarget (NSObject target, Selector sel);
}
```

일반적으로 응용 프로그램에서 메서드를 사용 하는 방법입니다.

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        b.SetTarget (this, new Selector ("print"));
    }

    [Export ("print")]
    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

더 좋은를 개발자에 C# 게 제공 하기 위해 일반적으로 `NSAction` 매개 변수를 사용 하는 메서드를 제공 합니다. C# 이 메서드를 사용 하면 `Target+Selector`대신 대리자 및 람다를 사용할 수 있습니다. 이렇게 하려면 일반적으로 `SetTarget` 메서드를 [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) 에 플래그를 사용 하 여 숨깁니다.
그런 다음 다음과 같이 새 도우미 메서드를 노출 합니다.

```csharp
// API.cs
interface Button {
   [Export ("setTarget:selector:"), Internal]
   void SetTarget (NSObject target, Selector sel);
}

// Extensions.cs
public partial class Button {
     public void SetTarget (NSAction callback)
     {
         SetTarget (new NSActionDispatcher (callback), NSActionDispatcher.Selector);
     }
}
```

이제 사용자 코드를 다음과 같이 작성할 수 있습니다.

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        // First Style
        b.SetTarget (ThePrintMethod);

        // Lambda style
        b.SetTarget (() => {  /* print here */ });
    }

    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

<a name="Strings" />

### <a name="strings"></a>문자열

`NSString`를 사용 하는 메서드를 바인딩하는 경우 반환 형식 및 매개 변수에서이 C# 를 문자열 형식으로 바꿀 수 있습니다.

문자열이 토큰으로 사용 되는 경우에만 `NSString`을 직접 사용할 수 있습니다. 문자열 및 `NSString`에 대 한 자세한 내용은 [NSString에서 API 디자인](~/ios/internals/api-design/nsstring.md) 문서를 참조 하세요.

드문 경우 지만 API에서 목표-C 문자열 (`NSString *`) 대신 C 유사 문자열 (`char *`)을 노출할 수 있습니다. 이러한 경우 매개 변수에 주석을 달 수 있습니다 [`[PlainString]`](~/cross-platform/macios/binding/binding-types-reference.md#plainstring)
특성도.

<a name="outref_parameters" />

### <a name="outref-parameters"></a>out/ref 매개 변수

일부 Api는 매개 변수에서 값을 반환 하거나 매개 변수를 참조로 전달 합니다.

일반적으로 시그니처는 다음과 같습니다.

```csharp
- (void) someting:(int) foo withError:(NSError **) retError
- (void) someString:(NSObject **)byref
```

첫 번째 예제는 오류 코드를 반환 하는 일반적인 목표 C 방법, `NSError` 포인터에 대 한 포인터 및 반환 시 값이 설정 된 경우를 보여 줍니다.   두 번째 메서드는 목표 C 메서드가 개체를 사용 하 고 해당 내용을 수정 하는 방법을 보여 줍니다.   이는 순수 출력 값이 아닌 참조로 전달 됩니다.

바인딩은 다음과 같습니다.

```csharp
[Export ("something:withError:")]
void Something (nint foo, out NSError error);
[Export ("someString:")]
void SomeString (ref NSObject byref);
```

<a name="Memory_management_attributes" />

### <a name="memory-management-attributes"></a>메모리 관리 특성

[`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 특성을 사용 하 고 호출 된 메서드에 의해 유지 되는 데이터를 전달 하는 경우 두 번째 매개 변수로 전달 하 여 인수 의미 체계를 지정할 수 있습니다. 예를 들면 다음과 같습니다.

```csharp
[Export ("method", ArgumentSemantic.Retain)]
```

위의 예에서는 "Retain" 의미 체계가 있는 값에 플래그를 지정 합니다. 사용 가능한 의미 체계는 다음과 같습니다.

- 할당
- 복사
- 보유

<a name="Style_Guidelines" />

### <a name="style-guidelines"></a>스타일 지침

<a name="Using_[Internal]" />

#### <a name="using-internal"></a>[내부] 사용

[`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) 를 사용할 수 있습니다.
공용 API에서 메서드를 숨기는 특성입니다. 노출 된 API가 너무 적고이 메서드에 따라 별도의 파일에 높은 수준의 구현을 제공 하려는 경우이 작업을 수행 하는 것이 좋습니다.

바인딩 생성기에서 제한이 발생할 경우에도이를 사용할 수 있습니다. 예를 들어 일부 고급 시나리오는 바인딩되지 않은 형식을 노출 하 고 사용자 고유의 방식으로 바인딩하려는 방식으로 해당 형식을 직접 래핑할 수 있습니다.

<a name="Event_Handlers_and_Callbacks" />

## <a name="event-handlers-and-callbacks"></a>이벤트 처리기 및 콜백

일반적으로 목표-C 클래스는 대리자 클래스 (목적-C 대리자)에 메시지를 전송 하 여 알림 또는 요청 정보를 브로드캐스트합니다.

이 모델은 Xamarin.ios에서 완전히 지원 되 고 표시 되는 경우가 종종 있습니다. Xamarin.ios는 이러한 상황에서 C# 사용할 수 있는 이벤트 패턴 및 메서드 콜백 시스템을 클래스에 노출 합니다. 이렇게 하면 다음과 같은 코드를 실행할 수 있습니다.

```csharp
button.Clicked += delegate {
    Console.WriteLine ("I was clicked");
};
```

바인딩 생성기는 목표 C 패턴을 C# 패턴에 매핑하는 데 필요한 입력 크기를 줄일 수 있습니다.

Xamarin.ios 1.4 부터는 특정 목표-C 대리자에 대 한 바인딩을 생성 하 고 호스트 형식에 대 한 이벤트 및 속성으로 C# 대리자를 노출 하도록 생성기에 지시할 수도 있습니다.

이 프로세스에는 두 개의 클래스가 포함 되어 있습니다 .이 클래스는 현재 이벤트를 내보내는 호스트 클래스 이며 `Delegate` 또는 `WeakDelegate` 및 실제 대리자 클래스로 보냅니다.

다음 설정 고려:

```csharp
[BaseType (typeof (NSObject))]
interface MyClass {
    [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
    NSObject WeakDelegate { get; set; }

    [Wrap ("WeakDelegate")][NullAllowed]
    MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
    [Export ("loaded:bytes:")]
    void Loaded (MyClass sender, int bytes);
}
```

클래스를 래핑하려면 다음을 수행 해야 합니다.

- 호스트 클래스에서 [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 에 추가 합니다.  
   선언 대리자 역할을 하는 형식 및 사용자가 노출 C# 한 이름입니다. 위의 예제에서 `typeof (MyClassDelegate)` 하 고 각각 `WeakDelegate` 합니다.
- 대리자 클래스에서 매개 변수가 세 개 이상인 각 메서드에서 자동으로 생성 된 EventArgs 클래스에 사용할 형식을 지정 해야 합니다.

바인딩 생성기는 단일 이벤트 대상만 래핑하는 것으로 제한 되지 않습니다. 일부 목표 C 클래스는 메시지를 두 개 이상의 대리자로 내보낼 수 있으므로이 설정을 지원 하기 위해 배열을 제공 해야 합니다. 대부분의 경우에는이 기능이 필요 하지 않지만 생성기는 이러한 사례를 지원할 준비가 된 것입니다.

결과로 생성 되는 코드는 다음과 같습니다.

```csharp
[BaseType (typeof (NSObject),
    Delegates=new string [] {"WeakDelegate"},
    Events=new Type [] { typeof (MyClassDelegate) })]
interface MyClass {
        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }

        [Wrap ("WeakDelegate")][NullAllowed]
        MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
        [Export ("loaded:bytes:"), EventArgs ("MyClassLoaded")]
        void Loaded (MyClass sender, int bytes);
}
```

`EventArgs`는 생성 될 `EventArgs` 클래스의 이름을 지정 하는 데 사용 됩니다. 서명 당 하나를 사용 해야 합니다 .이 예제에서는 `EventArgs`에 nint 형식의 `With` 속성이 포함 됩니다.

위의 정의를 사용 하면 생성기는 생성 된 MyClass에서 다음 이벤트를 생성 합니다.

```csharp
public MyClassLoadedEventArgs : EventArgs {
        public MyClassLoadedEventArgs (nint bytes);
        public nint Bytes { get; set; }
}

public event EventHandler<MyClassLoadedEventArgs> Loaded {
        add; remove;
}
```

이제 다음과 같이 코드를 사용할 수 있습니다.

```csharp
MyClass c = new MyClass ();
c.Loaded += delegate (sender, args){
        Console.WriteLine ("Loaded event with {0} bytes", args.Bytes);
};
```

콜백은 이벤트 호출과 마찬가지로 여러 개의 잠재적 구독자를 포함 하는 것이 아니라 여러 개의 잠재적 구독자를 포함 하는 것과 같은 차이점이 있습니다. 예를 들어 여러 메서드가 `Clicked` 이벤트 나 `DownloadFinished` 이벤트에 연결할 수 있습니다. 콜백은 단일 구독자만 가질 수 있습니다.

프로세스는 동일 합니다. 생성 되는 `EventArgs` 클래스의 이름을 노출 하는 대신, EventArgs는 실제로 결과 C# 대리자 이름의 이름을 지정할 때 사용 됩니다.

대리자 클래스의 메서드가 값을 반환 하는 경우 바인딩 생성기는이를 이벤트 대신 부모 클래스의 대리자 메서드에 매핑합니다. 이러한 경우 사용자가 대리자에 연결 하지 않은 경우 메서드에서 반환 되어야 하는 기본값을 제공 해야 합니다. [`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) 를 사용 하 여이 작업을 수행 합니다.
또는 특성을 [`[DefaultValueFromArgument]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute) 합니다.

[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) 은 반환 값을 하드 코딩 하는 동안 [`[DefaultValueFromArgument]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)
는 반환 되는 입력 인수를 지정 하는 데 사용 됩니다.

<a name="Enumerations_and_Base_Types" />

## <a name="enumerations-and-base-types"></a>열거형 및 기본 형식

Btouch 인터페이스 정의 시스템에서 직접 지원 하지 않는 열거형 또는 기본 형식을 참조할 수도 있습니다. 이렇게 하려면 열거형 및 핵심 형식을 별도의 파일에 넣고 btouch에 제공 하는 추가 파일 중 하나의 일부로 포함 합니다.

<a name="Linking_the_Dependencies" />

## <a name="linking-the-dependencies"></a>종속성 연결

응용 프로그램의 일부가 아닌 Api를 바인딩하는 경우 실행 파일이 이러한 라이브러리에 대해 연결 되어 있는지 확인 해야 합니다.

Xamarin.ios에서 라이브러리를 연결 하는 방법에 대해 설명 해야 합니다 .이 작업은 "-gcc_flags" 옵션을 사용 하 여 새 라이브러리와 연결 하는 방법을 지정 하는 추가 빌드 인수를 사용 하 여 `mtouch` 명령을 호출 하도록 빌드 구성을 변경 하 고, 다음과 같이 프로그램에 필요한 모든 추가 라이브러리를 포함 하는 따옴표 붙은 문자열을 사용 하 여 수행할 수 있습니다.

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load -lSystemLibrary -framework CFNetwork -ObjC"
```

위의 예에서는 `libMyLibrary.a`, `libSystemLibrary.dylib` 및 `CFNetwork` framework 라이브러리를 최종 실행 파일에 연결 합니다.

또는 계약 파일에 포함할 수 있는 어셈블리 수준 [`[LinkWithAttribute]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)(예: `AssemblyInfo.cs`)를 활용할 수 있습니다.
[`[LinkWithAttribute]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)사용 하는 경우 네이티브 라이브러리가 응용 프로그램에 포함 될 수 있도록 바인딩을 만들 때 사용할 수 있는 네이티브 라이브러리가 있어야 합니다. 다음은 그 예입니다.

```csharp
// Specify only the library name as a constructor argument and specify everything else with properties:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget = LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]

// Or you can specify library name *and* link target as constructor arguments:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]
```

`-force_load` 명령이 필요한 이유는 무엇 이며,-ObjC 플래그가에서 코드를 컴파일하는 경우에는 Xamarin.ios를 위해 런타임에 필요한 범주 (링커/컴파일러 데드 코드 제거 스트립)를 지 원하는 데 필요한 메타 데이터를 유지 하지 않는 이유가 궁금할 수 있습니다.

<a name="Assisted_References" />

## <a name="assisted-references"></a>지원 참조

작업 시트 및 경고 상자와 같은 일부 일시적 개체는 개발자를 위해 추적 하기가 복잡 하 고 바인딩 생성기는 약간의 도움이 될 수 있습니다.

예를 들어 메시지를 표시 한 다음 `Done` 이벤트를 생성 하는 클래스가 있는 경우이를 처리 하는 일반적인 방법은 다음과 같습니다.

```csharp
class Demo {
    MessageBox box;

    void ShowError (string msg)
    {
        box = new MessageBox (msg);
        box.Done += { box = null; ... };
    }
}
```

위의 시나리오에서 개발자는 자신에 게 개체에 대 한 참조를 그대로 유지 하 고 자신에 대 한 참조를 누설 하거나 적극적으로 지워야 합니다.  코드를 바인딩하는 동안 생성기는 사용자에 대 한 참조를 추적 하는 것을 지원 하 고 특수 한 메서드가 호출 될 때이를 지우도록 합니다. 그러면 위의 코드가 다음과 같이 됩니다.

```csharp
class Demo {
    void ShowError (string msg)
    {
        var box = new MessageBox (msg);
        box.Done += { ... };
    }
}
```

변수에 변수를 유지 하는 것은 더 이상 필요 하지 않으며, 지역 변수를 사용 하 고 개체를 소멸 시킬 때 참조를 지울 필요가 없다는 것을 알 수 있습니다.

이를 활용 하려면 클래스에 [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 선언에 설정 된 Events 속성이 있어야 하 고, 다음과 같이 개체가 작업을 완료할 때 호출 되는 메서드의 이름으로 설정 된 `KeepUntilRef` 변수를 사용 해야 합니다.

```csharp
[BaseType (typeof (NSObject), KeepUntilRef="Dismiss"), Delegates=new string [] { "WeakDelegate" }, Events=new Type [] { typeof (SomeDelegate) }) ]
class Demo {
    [Export ("show")]
    void Show (string message);
}
```

<a name="Inheriting_Protocols" />

## <a name="inheriting-protocols"></a>프로토콜 상속

Xamarin.ios v 3.2부터 [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) 속성으로 표시 된 프로토콜에서 상속을 지원 합니다. 이는 `MKOverlay` 프로토콜이 `MKAnnotation` 프로토콜에서 상속 되 고 `NSObject`에서 상속 되는 여러 클래스에서 채택 하는 `MapKit`와 같은 특정 API 패턴에서 유용 합니다.

지금까지 모든 구현에 프로토콜을 복사 해야 하지만,이 경우에는 `MKShape` 클래스가 `MKOverlay` 프로토콜에서 상속 하 고 필요한 모든 메서드를 자동으로 생성할 수 있습니다.

## <a name="related-links"></a>관련 링크

- [바인딩 샘플](https://docs.microsoft.com/samples/xamarin/ios-samples/bindingsample/)
