---
title: Xamarin에서 AvtvOS Player를 사용 하 여 소리 재생
description: 이 문서에서는 도우미 클래스를 사용 하 여 Xamarin.ios 응용 프로그램에서 Avxamarin.ios 플레이어를 사용 하는 소리 재생을 제어 하는 방법을 보여 줍니다.
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 4dddde8d4408df6a9b9d73c0a3efff62f563591a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030774"
---
# <a name="playing-sound-in-tvos-with-avaudioplayer-in-xamarin"></a>Xamarin에서 AvtvOS Player를 사용 하 여 소리 재생

## <a name="about-the-avaudioplayer"></a>Av오디오 플레이어 정보

`AVAudioPlayer`는 메모리 나 파일에서 오디오 데이터를 재생 하는 데 사용 됩니다. 네트워크 스트리밍을 수행 하거나 대기 시간이 짧은 오디오 i/o를 요구 하지 않는 한 Apple은이 클래스를 사용 하 여 앱에서 오디오를 재생 하는 것을 권장 합니다.

`AVAudioPlayer`를 사용 하 여 다음을 수행할 수 있습니다.

- 선택적인 반복이 있는 모든 기간의 소리를 재생 합니다.
- 선택적 동기화를 사용 하 여 여러 소리를 동시에 재생 합니다.
- 재생 하는 각 사운드의 볼륨, 재생 빈도 및 스테레오 위치를 제어 합니다.
- 빨리 감기 또는 되감기와 같은 기능을 지원 합니다.
- 재생 수준 계량 데이터를 가져옵니다.

`AVAudioPlayer`는 iOS, tvOS 및 OS X (예: `.aif`, `.wav` 또는 `.mp3`)에서 제공 하는 오디오 형식의 소리를 지원 합니다.

## <a name="playing-sounds-in-tvos"></a>TvOS에서 소리 재생

TvOS는 iOS와 동일한 오디오 도구 상자 클래스를 지원 하기 때문에 tvOS 앱에서 오디오를 재생 하는 방법에 대 한 자세한 내용은 [Avaudio player를 사용 하](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer) 는 ios 재생 문서를 참조 하세요.

## <a name="related-links"></a>관련 링크

- [Av오디오 플레이어 참조](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/)
