---
title: 알려진 문제 & 해결 방법
description: 이 문서에서는 Xamarin Workbooks에 대 한 알려진 문제 및 해결 방법을 설명 합니다. CultureInfo 문제, JSON 문제 등에 대해 설명 합니다.
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: c7b9f93c2d6339ba1fd26b27742ecfc0f438c5de
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73018171"
---
# <a name="known-issues--workarounds"></a>알려진 문제 & 해결 방법

## <a name="persistence-of-cultureinfo-across-cells"></a>셀 간 CultureInfo 지 속성

Mono [의 `AppContext.SetSwitch`구현에서 버그로][appcontext-bug] 인해 Mono 기반 통합 문서 대상 (Mac, IOS 및 Android)의 통합 문서 셀 간에는 `System.Threading.CurrentThread.CurrentCulture` 또는 `System.Globalization.CultureInfo.CurrentCulture`를 설정할 수 없습니다.

### <a name="workarounds"></a>해결 방법

- 응용 프로그램 도메인 로컬 `DefaultThreadCurrentCulture`를 설정 합니다.

```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

- 또는 통합 문서 1.2.1 이상으로 업데이트 하 여 `System.Threading.CurrentThread.CurrentCulture` 및 `System.Globalization.CultureInfo.CurrentCulture`에 대 한 할당을 다시 작성 하 여 Mono 버그를 해결 하는 데 필요한 동작을 제공 합니다.

## <a name="unable-to-use-newtonsoftjson"></a>Newtonsoft.json를 사용할 수 없습니다.

### <a name="workaround"></a>해결 방법

- Newtonsoft.json 9.0.1를 설치 하는 통합 문서 1.2.1로 업데이트 합니다.
  현재 알파 채널의 1.3 통합 문서는 버전 10 이상을 지원 합니다.

### <a name="details"></a>설명

Newtonsoft.json에 만큼 증가을 `dynamic`지원 하기 위해 제공 되는 버전 통합 문서와 충돌 하는 10이 릴리스 되었습니다. 이 문제는 통합 문서 1.3 미리 보기 릴리스에서 해결 되었지만 지금은 Newtonsoft.json를 버전 9.0.1에 고정 하 여 해결할 수 있습니다.

Newtonsoft.json 10 이상에 따라 명시적으로 NuGet 패키지는 현재 알파 채널에 있는 통합 문서 1.3 에서만 지원 됩니다.

## <a name="code-tooltips-are-blank"></a>코드 도구 설명이 비어 있습니다.

Mac 통합 문서 앱에서 사용 되는 Safari/WebKit의 [모나코 편집기에 버그가][monaco-bug] 있으며,이는 텍스트 없이 코드 도구 설명 렌더링을 생성 합니다.

![](general-images/monaco-signature-help-bug.png)

### <a name="workaround"></a>해결 방법

- 표시 되 면 도구 설명을 클릭 하면 텍스트가 렌더링 됩니다.

- 또는 통합 문서 1.2.1 이상으로 업데이트

[appcontext-bug]: https://bugzilla.xamarin.com/show_bug.cgi?id=54448
[monaco-bug]: https://github.com/Microsoft/monaco-editor/issues/408

## <a name="skiasharp-renderers-are-missing-in-workbooks-13"></a>통합 문서 1.3에 SkiaSharp 렌더러에서 누락 됨

통합 문서 1.3부터 microsoft는 microsoft [SDK](~/tools/workbooks/sdk/index.md)를 사용 하 여 렌더러 자체를 제공 하는 SkiaSharp를 위해 통합 문서 0.99.0에서 배송 된 SkiaSharp 렌더러를 제거 했습니다.

### <a name="workaround"></a>해결 방법

- SkiaSharp를 NuGet의 최신 버전으로 업데이트 합니다. 이 문서를 작성할 당시에는 1.57.1입니다.

## <a name="related-links"></a>관련 링크

- [버그 보고](~/tools/workbooks/install.md#reporting-bugs)
