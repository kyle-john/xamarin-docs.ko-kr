---
title: 빌드 프로세스
ms.prod: xamarin
ms.assetid: 3BE5EE1E-3FF6-4E95-7C9F-7B443EE3E94C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/06/2020
ms.openlocfilehash: bce2b6f29129894ed446100c87b5e92d3572ed2f
ms.sourcegitcommit: 60d2243809d8e980fca90b9f771e72f8c0e64d71
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78946266"
---
# <a name="build-process"></a>빌드 프로세스

## <a name="overview"></a>개요

Xamarin.Android 빌드 프로세스는 [`Resource.designer.cs`를 생성하고](~/android/internals/api-design.md), `AndroidAsset`, `AndroidResource` 및 다른 [빌드 동작](#Build_Actions)을 지원하고, [Android에서 호출 가능한 래퍼](~/android/platform/java-integration/android-callable-wrappers.md)를 생성하고, Android 디바이스에서 실행할 `.apk`를 생성하는 등 모든 것을 결합하는 역할을 합니다.

## <a name="application-packages"></a>애플리케이션 패키지

Xamarin.Android 빌드 시스템이 생성할 수 있는 Android 애플리케이션 패키지(`.apk` 파일)의 종류는 크게 두 가지가 있습니다.

- 완전히 자체 포함되어 있으며 실행할 추가 패키지가 필요 없는 **릴리스** 빌드. 앱 스토어에 제공되는 패키지입니다.

- 이와 반대되는 **디버그** 빌드.

이는 우연치 않게 패키지를 생성하는 MSBuild `Configuration`과 일치합니다.

### <a name="shared-runtime"></a>공유 런타임

*공유 런타임*은 기본 클래스 라이브러리(`mscorlib.dll` 등) 및 Android 바인딩 라이브러리(`Mono.Android.dll` 등)를 제공하는 추가적인 Android 패키지의 쌍입니다. 디버그 빌드는 Android 애플리케이션 패키지 내 기본 클래스 라이브러리 및 바인딩 어셈블리를 포함하는 대신 공유 런타임에 의존하므로 디버그 패키지 크기가 줄어듭니다.

공유 런타임은 디버그 빌드에서 `$(AndroidUseSharedRuntime)` 속성을 `False`로 설정하여 비활성화할 수 있습니다.

<a name="Fast_Deployment" />

### <a name="fast-deployment"></a>빠른 배포

*빠른 배포*는 공유 런타임과 함께 작동하여 Android 애플리케이션의 패키지 크기를 더욱 줄입니다. 이를 위해 패키지 내에 앱의 어셈블리를 묶지 않습니다. 대신 `adb push`를 통해 대상에 복사합니다. 어셈블리가 변경될 *경우에만* 패키지가 다시 설치되지 않으므로 빌드/배포/디버그 주기가 단축됩니다. 대신 업데이트된 어셈블리만 대상 디바이스에 다시 동기화합니다.

빠른 배포는 `adb`가 `/data/data/@PACKAGE_NAME@/files/.__override__` 디렉터리에 동기화되지 않게 차단하는 디바이스에서 실패하는 것으로 알려져 있습니다.

빠른 배포는 기본적으로 활성화되며, 디버그 빌드에서 `$(EmbedAssembliesIntoApk)` 속성을 `True`로 설정하여 비활성화할 수 있습니다.

## <a name="msbuild-projects"></a>MSBuild 프로젝트

Xamarin.Android 빌드 프로세스는 Mac용 Visual Studio 및 Visual Studio에서 사용하는 프로젝트 파일 형식이기도 한 MSBuild에 기반을 두고 있습니다.
일반적으로 사용자는 MSBuild 파일을 직접 편집할 필요가 없습니다. IDE가 완전히 작동하는 프로젝트를 만들고, 여기에 변경 사항을 업데이트하며, 필요에 따라 빌드 대상을 자동으로 호출하기 때문입니다.

고급 사용자가 IDE의 GUI에서 지원하지 않는 작업을 수행할 수도 있으므로, 프로젝트 파일을 직접 편집하여 빌드 프로세스를 사용자 지정할 수 있습니다.
이 페이지에서는 Xamarin.Android 관련 기능과 사용자 지정만 설명하지만 일반적인 MSBuild 항목, 속성 및 대상을 사용하여 여러 가지 작업을 수행할 수 있습니다.

<a name="Build_Targets" />

## <a name="build-targets"></a>빌드 대상

Xamarin.Android 프로젝트에는 다음 빌드 대상이 정의됩니다.

- **Build** - 패키지를 빌드합니다.

- **BuildAndStartAotProfiling** &ndash; 포함된 AOT 프로파일러를 사용하여 앱을 빌드하고, 프로파일러 TCP 포트를 `$(AndroidAotProfilerPort)`로 설정하고, 기본 작업을 시작합니다.

  기본 TCP 포트는 `9999`입니다.

  Xamarin.Android 10.2에 추가되었습니다.

- **Clean** - 빌드 프로세스에서 생성된 파일을 모두 제거합니다.

- **FinishAotProfiling** &ndash; TCP 포트 `$(AndroidAotProfilerPort)`를 통해 디바이스 또는 에뮬레이터에서 AOT 프로파일러 데이터를 수집하고 이를 `$(AndroidAotCustomProfilePath)`에 씁니다.

  포트 및 사용자 지정 프로필의 기본값은 `9999` 및 `custom.aprof`입니다.

  `aprofutil`에 추가 옵션을 전달하려면 `$(AProfUtilExtraOptions)` 속성에서 설정합니다.

  다음 코드와 동일합니다.

  ```
  aprofutil $(AProfUtilExtraOptions) -s -v -f -p $(AndroidAotProfilerPort) -o "$(AndroidAotCustomProfilePath)"
  ```

  Xamarin.Android 10.2에 추가되었습니다.

- **Install** - 기본 디바이스 또는 가상 디바이스에 패키지를 설치합니다.

- **SignAndroidPackage** - 패키지(`.apk`)를 만들고 서명합니다. 자체 포함 "릴리스" 패키지를 생성하려면 `/p:Configuration=Release`와 함께 사용합니다.

- **StartAndroidActivity** &ndash; 디바이스 또는 실행 중 에뮬레이터에서 기본 작업을 시작합니다. 다른 작업을 시작하려면 `$(AndroidLaunchActivity)` 속성을 작업 이름으로 설정합니다.

  `adb shell am start @PACKAGE_NAME@/$(AndroidLaunchActivity)`와 같습니다.

  Xamarin.Android 10.2에 추가되었습니다.

- **StopAndroidPackage** &ndash; 디바이스 또는 실행 중 에뮬레이터에서 애플리케이션 패키지를 완전히 중지합니다.

  `adb shell am force-stop @PACKAGE_NAME@`와 같습니다.

  Xamarin.Android 10.2에 추가되었습니다.

- **Uninstall** - 기본 디바이스 또는 가상 디바이스에서 패키지를 제거합니다.

- **UpdateAndroidResources** - `Resource.designer.cs` 파일을 업데이트합니다. 이 대상은 일반적으로 프로젝트에 새 리소스가 추가될 때 IDE에 의해 호출됩니다.

## <a name="build-extension-points"></a>빌드 확장점

Xamarin.Android 빌드 시스템은 빌드 프로세스에 연결하려는 사용자를 위한 몇 가지 공용 확장점을 공개합니다. 이 확장점 중 하나를 사용하려면 사용자 지정 대상을 `PropertyGroup`의 해당하는 MSBuild 속성에 추가해야 합니다. 예를 들어:

```xml
<PropertyGroup>
   <AfterGenerateAndroidManifest>
      $(AfterGenerateAndroidManifest);
      YourTarget;
   </AfterGenerateAndroidManifest>
</PropertyGroup>
```

빌드 프로세스를 확장하는 방법에 대한 주의 사항: 제대로 작성되지 않은 빌드 확장은 특히 모든 빌드에서 실행된다면 빌드 성능에 영향을 줄 수 있습니다. 해당 확장을 구현하기 전에 MSBuild [설명서](https://docs.microsoft.com/visualstudio/msbuild/msbuild)를 읽는 것이 좋습니다.

- **AfterGenerateAndroidManifest** - 이 속성에 나열된 대상은 내부 `_GenerateJavaStubs` 대상 바로 뒤에 실행됩니다. 여기서 `AndroidManifest.xml` 파일은 `$(IntermediateOutputPath)`에서 생성됩니다. 따라서 생성된 `AndroidManifest.xml` 파일을 수정하려는 경우 이 확장점을 사용하여 수정할 수 있습니다.

  Xamarin.Android 9.4에 추가되었습니다.

- **BeforeGenerateAndroidManifest** - 이 속성에 나열된 대상은 `_GenerateJavaStubs` 바로 앞에 실행됩니다.

  Xamarin.Android 9.4에 추가되었습니다.

## <a name="build-properties"></a>빌드 속성

MSBuild 속성은 대상의 동작을 제어합니다. [MSBuild PropertyGroup 요소](https://docs.microsoft.com/visualstudio/msbuild/propertygroup-element-msbuild) 내 프로젝트 파일(예: **MyApp.csproj**)에 지정됩니다.

- **Configuration** - 사용할 빌드 구성(예: “디버그” 또는 “릴리스”)을 지정합니다. Configuration 속성은 대상 동작을 결정하는 다른 속성에 대한 기본값을 결정하는 데 사용됩니다. IDE 내에 추가 구성을 만들 수 있습니다.

  *기본적으로*`Debug` 구성은 `Install` 및 `SignAndroidPackage` 대상에서 다른 파일과 패키지가 존재해야 작동하는 소형 Android 패키지를 생성하도록 합니다.

  기본 `Release` 구성은 `Install` 및 `SignAndroidPackage` 대상에서 다른 패키지나 파일을 설치하지 않고도 사용할 수 있는 ‘독립 실행형’ Android 패키지를 생성하도록 합니다. 

- **DebugSymbols** - `$(DebugType)` 속성과 함께 사용되며, Android 패키지가 ‘디버그 가능’한지 결정하는 부울 값입니다.  디버그 가능한 패키지는 디버그 기호를 포함하고, `//application/@android:debuggable` 특성을 `true`로 설정하며, 디버거가 프로세스에 연결할 수 있도록 `INTERNET` 권한을 자동으로 추가합니다. `DebugSymbols`가 `True`이고 `DebugType`이 빈 문자열이거나 `Full`이면 애플리케이션을 디버그할 수 있습니다. 

- **DebugType** - 빌드의 일부로 생성할 [디버그 기호의 유형](https://docs.microsoft.com/visualstudio/msbuild/csc-task)을 지정하며, 애플리케이션의 디버그 가능 여부에도 영향을 줍니다. 가능한 값은 다음과 같습니다.

  - **전체**: 전체 기호가 생성됩니다. `DebugSymbols` MSBuild 속성도 `True`일 경우 애플리케이션 패키지를 디버그할 수 있습니다.

  - **PdbOnly**: "PDB" 기호가 생성됩니다. 애플리케이션 패키지를 디버그할 수 *없습니다*.

  `DebugType`이 설정되지 않았거나 빈 문자열이면 `DebugSymbols` 속성에서 애플리케이션의 디버그 가능 여부를 제어합니다.

  - **AndroidGenerateLayoutBindings** - `true`로 설정된 경우 [레이아웃 코드 숨김](https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/LayoutCodeBehind.md)의 생성을 활성화하거나, `false`로 설정된 경우 완전히 비활성화합니다. 기본값은 `false`입니다.

### <a name="install-properties"></a>설치 속성

설치 속성은 `Install` 및 `Uninstall` 대상의 동작을 제어합니다.

- **AdbTarget** - Android 패키지를 설치하거나 제거할 수 있는 Android 대상 디바이스를 지정합니다. 이 속성의 값은 [`adb` 대상 디바이스 옵션](https://developer.android.com/tools/help/adb.html#issuingcommands)과 동일합니다.

  ```bash
  # Install package onto emulator via -e
  # Use `/Library/Frameworks/Mono.framework/Commands/msbuild` on OS X
  MSBuild /t:Install ProjectName.csproj /p:AdbTarget=-e
  ```

### <a name="packaging-properties"></a>패키징 속성

패키징 속성은 Android 패키지의 생성을 제어하며, `Install` 및 `SignAndroidPackage` 대상에서 사용합니다.
릴리스 애플리케이션을 패키지하는 경우에는 [서명 속성](#Signing_Properties)도 관련이 있습니다.

- **AndroidAotProfiles** &ndash; 개발자가 명령줄에서 AOT 프로필을 추가할 수 있는 문자열 속성입니다. 세미콜론 또는 쉼표로 구분된 절대 경로 목록입니다.

  Xamarin.Android 10.1에 추가되었습니다.

- **AndroidApkDigestAlgorithm** - `jarsigner -digestalg`와 함께 사용하는 다이제스트 알고리즘을 지정하는 문자열 값입니다.

  기본값은 `SHA-256`입니다. Xamarin.Android 10.0 및 이전 버전에서는 기본값이 `SHA1`이었습니다.

  Xamarin.Android 9.4에 추가되었습니다.

- **AndroidApkSignerAdditionalArguments** - 개발자가 `apksigner` 도구에 추가 인수를 제공할 수 있는 문자열 속성입니다.

  Xamarin.Android 8.2에 추가되었습니다.

- **AndroidApkSigningAlgorithm** -`jarsigner -sigalg`와 함께 사용하는 서명 알고리즘을 지정하는 문자열 값입니다.

  기본값은 `SHA256withRSA`입니다. Xamarin.Android 10.0 및 이전 버전에서는 기본값이 `md5withRSA`이었습니다.

  Xamarin.Android 8.2에 추가되었습니다.

- **AndroidApplication** - 프로젝트가 Android 애플리케이션용(`True`)인지, Android 라이브러리 프로젝트용(`False` 또는 없음)인지를 나타내는 부울 값입니다.

  Android 패키지에는 `<AndroidApplication>True</AndroidApplication>`인 프로젝트가 하나만 존재할 수 있습니다. (하지만 이는 아직 확인되지 않았으며, Android 리소스와 관련하여 미묘하고 기이한 오류를 유발할 수 있습니다.)

- **AndroidApplicationJavaClass** - 클래스가 [Android.App.Application](xref:Android.App.Application)에서 상속되는 경우 `android.app.Application` 대신 사용할 전체 Java 클래스 이름입니다.

  이 속성은 일반적으로 `$(AndroidEnableMultiDex)` MSBuild 속성과 같은 *다른* 속성으로 설정됩니다.

  Xamarin.Android 6.1에 추가되었습니다.

- **AndroidBinUtilsPath** &ndash; 네이티브 링커 `ld`, 네이티브 어셈블러 `as` 같은 Android [binutils][binutils]를 포함하는 디렉터리의 경로입니다. 이러한 도구는 Android NDK의 일부이며 Xamarin.Android 설치에도 포함되어 있습니다.

  기본값은 `$(MonoAndroidBinDirectory)\ndk\`입니다.

  Xamarin.Android 10.0에 추가되었습니다.

  [binutils]: https://android.googlesource.com/toolchain/binutils/

- **AndroidBoundExceptionType** &ndash; Xamarin.Android 제공 형식이 Java 형식(예: `Android.Runtime.InputStreamInvoker` 및 `System.IO.Stream` 또는 `Android.Runtime.JavaDictionary` 및 `System.Collections.IDictionary`)을 기준으로 .NET 형식 또는 인터페이스를 구현하는 경우 예외를 전파하는 방법을 지정하는 문자열 값입니다.

  - `Java`: 원래 Java 예외 유형은 그대로 전파됩니다.

    즉, `Stream.Read()`에서 `System.IO.IOException`가 아니라 `Java.IO.IOException`이 throw될 수 있으므로 `InputStreamInvoker`는 `System.IO.Stream` API를 제대로 구현하지 않습니다.

    이는 10.2 이전의 모든 Xamarin.Android 릴리스에서 예외 전파 동작입니다.

    이는 Xamarin. Android 10.2의 기본값입니다.

  - `System`: 원래 Java 예외 유형이 적절한 .NET 예외 유형으로 catch되어 래핑됩니다.

    즉, 예를 들어 `InputStreamInvoker`는 `System.IO.Stream`을 올바르게 구현하고, `Stream.Read()`는 `Java.IO.IOException` 인스턴스를 throw하지 *않습니다*.  (대신 `Java.IO.IOException`을 `Exception.InnerException` 값으로 포함하는 `System.IO.IOException`을 throw할 수 있습니다.)

    이는 Xamarin. Android 11.0의 기본값이 됩니다.

  Xamarin.Android 10.2에 추가되었습니다.

- **AndroidBuildApplicationPackage** - 패키지(.apk)를 만들고 서명할지 여부를 나타내는 부울 값입니다. 이 값을 `True`로 설정하는 것은 [SignAndroidPackage](#Build_Targets) 빌드 대상을 사용하는 것과 동일합니다.

  이 속성에 대한 지원은 Xamarin.Android 7.1 이후에 추가되었습니다.

  기본적으로 이 속성은 `False`입니다.

- **AndroidBundleConfigurationFile** &ndash; Android 앱 번들을 빌드할 때 `bundletool`에 대한 [구성 파일][bundle-config-format]로 사용할 파일 이름을 지정합니다. 이 파일은 APK를 생성하기 위해 번들이 분할되는 차원과 같이 번들에서 APK가 생성되는 방식을 제어합니다. Xamarin.Android는 압축되지 않은 상태로 유지되는 파일 확장명의 목록을 포함하여 이러한 설정의 일부를 자동으로 구성합니다.

  이 속성은 `$(AndroidPackageFormat)`이 `aab`로 설정된 경우에만 해당됩니다.

  Xamarin.Android 10.3에 추가되었습니다.

  [bundle-config-format]: https://developer.android.com/studio/build/building-cmdline#bundleconfig

- **AndroidDexTool** - 유효한 값이 `dx` 또는 `d8`인 열거형 스타일 속성입니다. Xamarin.Android 빌드 프로세스 중에 Android [dex][dex] 컴파일러가 사용됨을 나타냅니다.
  현재 기본값은 `dx`입니다. 자세한 내용은 [D8 및 R8][d8-r8]에 대한 설명서를 참조하세요.

  [dex]: https://source.android.com/devices/tech/dalvik/dalvik-bytecode
  [d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

- **AndroidEnableDesugar** - `desugar`가 활성화되어있는지 여부를 확인하는 부울 속성입니다. Android는 현재 모든 Java 8 기능을 지원하지 않으며, 기본 도구 체인은 `javac` 컴파일러의 출력에서 `desugar`라는 바이트 코드 변환을 수행하여 새 언어 기능을 구현합니다. `AndroidDexTool=dx`를 사용하는 경우 기본값은 `False`이며 `AndroidDexTool=d8`을 사용하는 경우 기본값은 `True`입니다.

- **AndroidEnableGooglePlayStoreChecks** - 개발자가 다음 Google Play 스토어 검사를 사용하지 않도록 설정할 수 있는 부울 속성입니다. XA1004, XA1005 및 XA1006. 이 기능은 Google Play 스토어를 대상으로 하지 않고 해당 검사를 실행하지 않으려는 개발자에게 유용합니다.

  Xamarin.Android 9.4에 추가되었습니다.

- **AndroidEnableMultiDex** - 최종 `.apk`에서 Multi-Dex 지원이 사용되는지 여부를 결정하는 부울 속성입니다.

  이 속성에 대한 지원은 Xamarin.Android 5.1에 추가되었습니다.

  기본적으로 이 속성은 `False`입니다.

- **AndroidEnablePreloadAssemblies** - 애플리케이션 패키지 내에 번들로 포함된 모든 관리형 어셈블리가 프로세스 시작 중에 로드되는지 여부를 제어하는 부울 속성입니다.

  `True`로 설정하며 애플리케이션 패키지 내에 번들로 포함된 모든 어셈블리가 프로세스 시작 중에, 애플리케이션 코드가 호출되기 전에 로드됩니다.
  이러한 동작은 Xamarin.Android 9.2 이전 릴리스에서 Xamarin.Android가 수행한 작업과 일관성이 있습니다.

  `False`로 설정하면 어셈블리는 필요에 따라서만 로드됩니다.
  이를 통해 애플리케이션을 더 빠르게 시작할 수 있고 데스크톱 .NET 의미 체계와 좀 더 일관성이 있게 됩니다.  시간 절감 효과를 확인하려면 `timing`을 포함하도록 `debug.mono.log` 시스템 속성을 설정하고 `adb logcat` 내에서 `Finished loading assemblies: preloaded` 메시지를 찾으세요.

  종속성 주입을 사용하는 애플리케이션 또는 라이브러리의 경우 어셈블리가 필요하지 않더라도 `AppDomain.CurrentDomain.GetAssemblies()`에서 애플리케이션 번들 내의 모든 어셈블리를 반환해야 하는 경우 이 속성을 *반드시*`True`로 지정해야 할 수 있습니다.

  기본적으로 이 값은 `True`로 설정됩니다.

  Xamarin.Android 9.2에 추가되었습니다.

- **AndroidEnableProfiledAot** - AOT(Ahead-Of-Time) 컴파일 중에 AOT 프로필을 사용할지 여부를 결정하는 부울 속성입니다.

  프로필은 `AndroidAotProfile` 항목 그룹에 나열됩니다. 이 ItemGroup에는 기본 프로필이 포함되어 있습니다. 이 프로필은 기존 항목을 제거하고 고유한 AOT 프로필을 추가하여 재정의할 수 있습니다.

  이 속성에 대한 지원은 Xamarin.Android 9.4에 추가되었습니다.

  기본적으로 이 속성은 `False`입니다.

- **AndroidEnableSGenConcurrent** - Mono의 [동시 GC 수집기](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#concurrent-sgen)를 사용할지 여부를 결정하는 부울 속성입니다.

  이 속성에 대한 지원은 Xamarin.Android 7.2에 추가되었습니다.

  기본적으로 이 속성은 `False`입니다.

- **AndroidErrorOnCustomJavaObject** - 형식이 `Java.Lang.Object` 또는 `Java.Lang.Throwable`에서도 상속되지 ‘않고’ `Android.Runtime.IJavaObject`
  를 구현할 수 있는지 여부를 결정하는 부울 속성입니다. 

  ```csharp
  class BadType : IJavaObject {
      public IntPtr Handle {
          get {return IntPtr.Zero;}
      }

      public void Dispose()
      {
      }
  }
  ```

  True이면 XA4212 오류가 생성되고, 그렇지 않으면 XA4212 경고가 생성됩니다.

  이 속성에 대한 지원은 Xamarin.Android 8.1에 추가되었습니다.

  기본적으로 이 속성은 `True`입니다.

- **AndroidExtraAotOptions** &ndash; `$(AndroidEnableProfiledAot)` 또는 `$(AotAssemblies)`가 `true`로 설정된 프로젝트에 대한 `Aot` 작업 중에 Mono 컴파일러에 추가 옵션을 전달할 수 있는 문자열 속성입니다. Mono 크로스 컴파일러를 호출할 때 속성의 문자열 값이 지시 파일에 추가됩니다.

  일반적으로 이 속성은 비워 두어야 하지만 일부 특수한 시나리오에서는 유용한 유연성을 제공할 수 있습니다.

  이 속성은 관련된 `$(AndroidAotAdditionalArguments)` 속성과는 다릅니다. 이 속성은 쉼표로 구분된 인수를 Mono 컴파일러의 `--aot` 옵션에 배치합니다. `$(AndroidExtraAotOptions)`는 `--verbose` 또는 `--debug` 같은 공백으로 구분된 완전 독립 실행형 옵션을 컴파일러에 대신 전달합니다.

  Xamarin.Android 10.2에 추가되었습니다.

- **AndroidFastDeploymentType** - `$(EmbedAssembliesIntoApk)` MSBuild 속성이 `False`일 경우 대상 디바이스에서 [빠른 배포 디렉터리](#Fast_Deployment)에 배포할 수 있는 형식을 제어하는 값의 콜론(`:`)으로 구분된 목록입니다. 리소스가 빠르게 배포될 경우 생성된 `.apk`에 포함되지 *않아* 배포 시간을 줄일 수 있습니다. (빠르게 배포되는 리소스가 많을수록 `.apk`를 재빌드해야 하는 경우가 줄어들고 설치 프로세스가 빨라질 수 있습니다.) 유효한 값은 다음과 같습니다.

  - `Assemblies`: 애플리케이션 어셈블리를 배포합니다.

  - `Dexes`: `.dex` 파일, Android 리소스 및 Android 자산을 배포합니다. **이 값은 *오직* Android 4.4 이상(API-19)을 실행 중인 디바이스에서만 사용할 수 있습니다.**

  기본값은 `Assemblies`입니다.

  **실험적**. Xamarin.Android 6.1에 추가되었습니다.

- **AndroidGenerateJniMarshalMethods** - 빌드 프로세스의 일부로 JNI 마샬링 메서드 생성을 활성화하는 부울 속성입니다. 이는 바인딩 도우미 코드의 System.Reflection 사용량을 크게 줄입니다.

  기본적으로 이 값은 False로 설정됩니다. 개발자가 새 JNI 마샬링 메서드 기능을 사용하고자 하는 경우

  ```xml
  <AndroidGenerateJniMarshalMethods>True</AndroidGenerateJniMarshalMethods>
  ```

  해당 .csproj에서 설정할 수 있습니다. 또는 다음을 통해 명령줄에 속성을 제공합니다.

  ```
  /p:AndroidGenerateJniMarshalMethods=True
  ```

  **실험적**. Xamarin.Android 9.2에 추가되었습니다.
  기본값은 False입니다.

- **AndroidGenerateJniMarshalMethodsAdditionalArguments** - `jnimarshalmethod-gen.exe` 호출에 추가 매개 변수를 추가하는 데 사용할 수 있는 문자열 속성입니다.  이는 디버깅에 유용하므로 `-v`, `-d` 또는 `--keeptemp`와 같은 옵션을 사용할 수 있습니다.

  기본값은 빈 문자열입니다. .csproj 파일 또는 명령줄에서 설정할 수 있습니다. 예를 들어:

  ```xml
  <AndroidGenerateJniMarshalMethodsAdditionalArguments>-v -d --keeptemp</AndroidGenerateJniMarshalMethodsAdditionalArguments>
  ```

  또는

  ```
  /p:AndroidGenerateJniMarshalMethodsAdditionalArguments="-v -d --keeptemp"
  ```

  Xamarin.Android 9.2에 추가되었습니다.

- **AndroidHttpClientHandlerType** - `System.Net.Http.HttpClient` 기본 생성자에서 사용할 기본 `System.Net.Http.HttpMessageHandler` 구현을 제어합니다. 값은 [`System.Type.GetType(string)`](https://docs.microsoft.com/dotnet/api/system.type.gettype?view=netcore-2.0#System_Type_GetType_System_String_)과 함께 사용하기에 적합한 `HttpMessageHandler` 서브클래스의 정규화된 어셈블리 형식 이름입니다.
  이 속성의 가장 일반적인 값은 다음과 같습니다.

  - `Xamarin.Android.Net.AndroidClientHandler`: Android Java API를 사용하여 네트워크 요청을 수행합니다. 기본 Android 버전이 TLS 1.2를 지원할 때 TLS 1.2 URL 액세스를 허용합니다. Android 5.0 이상에서만 Java를 통해 TLS 1.2 지원을 안정적으로 제공할 수 있습니다.

    이 항목은 Visual Studio 속성 페이지의 **Android** 옵션 및 Mac용 Visual Studio 속성 페이지의 **AndroidClientHandler** 옵션에 해당합니다.

    새 프로젝트 마법사는 Visual Studio에서 **최소 Android 버전**이 **Android 5.0(Lollipop)** 이상으로 구성되거나 Mac용 Visual Studio에서 **대상 플랫폼**이 **가장 유용한 최신**으로 설정된 경우 새 프로젝트에 대해 이 옵션을 선택합니다.

  - 설정 해제/빈 문자열: `System.Net.Http.HttpClientHandler, System.Net.Http`와 동일합니다.

    Visual Studio 속성 페이지의 **Default** 옵션에 해당합니다.

    새 프로젝트 마법사는 Visual Studio에서 **최소 Android 버전**이 **Android 4.4.87** 이하로 구성되거나 Mac용 Visual Studio에서 **대상 플랫폼**이 **최신 개발** 또는 **최대 호환성**으로 설정된 경우 새 프로젝트에 대해 이 옵션을 선택합니다.

  - `System.Net.Http.HttpClientHandler, System.Net.Http`: 관리형 `HttpMessageHandler`를 사용합니다.

    Visual Studio 속성 페이지의 **Managed** 옵션에 해당합니다.

  > [!NOTE]
  > Android 버전 5.0 이전에서 TLS 1.2 지원이 필요*하거나* TLS 1.2 지원이 `System.Net.WebClient` 및 관련 API에 필요한 경우 `$(AndroidTlsProvider)`를 사용해야 합니다.

  > [!NOTE]
  > 이 속성에 대한 지원은 [`XA_HTTP_CLIENT_HANDLER_TYPE` 환경 변수](~/android/deploy-test/environment.md)를 설정하여 작동됩니다.
  > 파일에서 `$XA_HTTP_CLIENT_HANDLER_TYPE` 값을 찾았으며 `@(AndroidEnvironment)`의 빌드 동작이 우선됩니다.

  Xamarin.Android 6.1에 추가되었습니다.

- **AndroidLinkMode** - Android 패키지 내에 포함된 어셈블리에 대해 수행해야 하는 [연결](~/android/deploy-test/linker.md) 유형을 지정합니다. Android 애플리케이션 프로젝트 내에서만 사용됩니다. 기본값은 *SdkOnly*입니다. 유효한 값은

  - **없음**: 연결을 시도하지 않습니다.

  - **SdkOnly**: 연결이 기본 클래스 라이브러리에서만 수행되며, 사용자 어셈블리에서는 수행되지 않습니다.

  - **전체**: 연결이 기본 클래스 라이브러리와 사용자 어셈블리에서 수행됩니다.

    > [!NOTE]
    > `AndroidLinkMode`에 *Full* 값을 사용할 경우 앱이 손상될 수 있습니다(특히 리플렉션을 사용하는 경우). *제대로* 알지 않는 한 이 값을 사용하지 마세요.

  ```xml
  <AndroidLinkMode>SdkOnly</AndroidLinkMode>
  ```

- **AndroidLinkSkip** - 연결해서는 안 되는 어셈블리의 어셈블리 이름 목록을 파일 확장명 없이 세미콜론(`;`)으로 구분하여 지정합니다. Android 애플리케이션 프로젝트 내에서만 사용됩니다.

  ```xml
  <AndroidLinkSkip>Assembly1;Assembly2</AndroidLinkSkip>
  ```

- **AndroidLinkTool** - 유효한 값이 `proguard` 또는 `r8`인 열거형 스타일 속성입니다. Java 코드에 사용되는 코드 축소기를 나타냅니다. 현재 기본값은 빈 문자열이거나 `$(AndroidEnableProguard)`가 `True`이면 `proguard`입니다. 자세한 내용은 [D8 및 R8][d8-r8]에 대한 설명서를 참조하세요.

  [d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

- **AndroidLintEnabled** - 개발자가 패키징 프로세스의 일부로 Android `lint` 도구를 실행할 수 있게 해주는 부울 속성입니다.

  - **AndroidLintEnabledIssues** - 활성화할 lint 이슈의 쉼표로 구분된 목록입니다.

  - **AndroidLintDisabledIssues** - 비활성화할 lint 이슈의 쉼표로 구분된 목록입니다.

  - **AndroidLintCheckIssues** - 확인할 lint 이슈의 쉼표로 구분된 목록입니다.
    참고: 이러한 문제만 확인됩니다.

  - **AndroidLintConfig** - lint 스타일 구성 파일의 빌드 작업입니다. 이는 확인할 문제를 활성화/비활성화하는 데 사용될 수 있습니다. 해당 콘텐츠가 병합될 것이기 때문에 여러 파일이 이 빌드 작업을 사용할 수 있습니다.

  Android `lint` 도구에 대한 자세한 내용은 [Lint 도움말](https://developer.android.com/studio/write/lint)을 참조하세요.

- **AndroidManagedSymbols** - `Release` 스택 추적에서 파일 이름과 줄 번호 정보를 추출할 수 있도록 시퀀스 위치를 생성할지 여부를 제어하는 부울 속성입니다.

  Xamarin.Android 6.1에 추가되었습니다.

- **AndroidManifest** - 앱 [`AndroidManifest.xml`](~/android/platform/android-manifest.md)의 템플릿으로 사용할 파일 이름을 지정합니다.
  빌드 중에 필요한 다른 모든 값이 병합되어 실제 `AndroidManifest.xml`이 생성됩니다.
  `$(AndroidManifest)`는 `/manifest/@package` 특성에 패키지 이름을 포함해야 합니다.

- **AndroidManifestMerger** &ndash; *AndroidManifest.xml* 파일을 병합하는 구현을 지정합니다. 이 속성은 `legacy`가 원래 C# 구현을 선택하고 `manifestmerger.jar`가 Google의 Java 구현을 선택하는 열거형 스타일 속성입니다.

  기본값은 현재 `legacy`입니다. 이후 릴리스에서는 Android Studio에 맞게 동작을 조정하기 위해 `manifestmerger.jar`로 변경됩니다.

  Google의 병합기는 [Android 설명서][manifest-merger]에 설명된 대로 `xmlns:tools="http://schemas.android.com/tools"`를 지원합니다.

  Xamarin.Android 10.2에 추가되었습니다.

  [manifest-merger]: https://developer.android.com/studio/build/manifest-merge

- **AndroidMultiDexClassListExtraArgs** - 개발자가 `multidex.keep` 파일을 생성할 때 추가 인수를 `com.android.multidex.MainDexListBuilder`에 전달할 수 있는 문자열 속성입니다.

  한 가지 특별한 사례는 `dx` 컴파일 중에 다음 오류가 발생하는 경우입니다.

  ```
  com.android.dex.DexException: Too many classes in --main-dex-list, main dex capacity exceeded
  ```

  이 오류가 발생하면 .csproj에 다음을 추가할 수 있습니다.

  ```xml
  <DxExtraArguments>--force-jumbo </DxExtraArguments>
  <AndroidMultiDexClassListExtraArgs>--disable-annotation-resolution-workaround</AndroidMultiDexClassListExtraArgs>
  ```

  이렇게 하면 `dx` 단계가 성공할 수 있습니다.

  Xamarin.Android 8.3에 추가되었습니다.

- **AndroidPackageFormat** - 유효한 값이 `apk` 또는 `aab`인 열거형 스타일 속성입니다. Android 애플리케이션을 [APK 파일][apk] 또는 [Android 앱 번들][bundle]로 패키지할지 여부를 나타냅니다. 앱 번들은 Google Play에서 전송용으로 사용되는 `Release` 빌드의 새로운 형식입니다. 현지 이 값은 기본적으로 `apk`로 설정됩니다.

  `$(AndroidPackageFormat)`가 `aab`로 설정되면 Android 앱 번들에 필요한 기타 MSBuild 속성이 설정됩니다.

  - `$(AndroidUseAapt2)`이(가) `True`인 경우
  - `$(AndroidUseApkSigner)`이(가) `False`인 경우
  - `$(AndroidCreatePackagePerAbi)`이(가) `False`인 경우

  [apk]: https://en.wikipedia.org/wiki/Android_application_package
  [bundle]: https://developer.android.com/platform/technology/app-bundle

- **AndroidPackageNamingPolicy** &ndash; 생성된 Java 소스 코드의 Java 패키지 이름을 지정하는 열거형 스타일 속성입니다.

  Xamarin.Android 10.2 이상에서 지원되는 유일한 값은 `LowercaseCrc64`입니다.

  Xamarin.Android 10.1에서는 전환 `LowercaseMD5` 값을 사용하여 Xamarin.Android 10.0 및 이전 버전에서 사용되는 원래 Java 패키지 이름 스타일로 다시 전환할 수 있었습니다. 이 옵션은 FIPS 준수가 적용된 빌드 환경과의 호환성을 향상시키기 위해 Xamarin.Android 10.2에서 제거되었습니다.

  Xamarin.Android 10.1에 추가되었습니다.

- **AndroidR8JarPath** - r8 dex 컴파일러 및 shrinker와 함께 사용할 `r8.jar`의 경로입니다. Xamarin.Android 설치 경로의 기본값입니다. 자세한 내용은 [D8 및 R8][d8-r8]에 대한 설명서를 참조하세요.

- **AndroidSdkBuildToolsVersion** - Android SDK 빌드 도구 패키지는 **aapt**, **zipalign** 등의 도구를 제공합니다. 여러 가지 버전의 빌드-도구 패키지를 동시에 설치할 수 있습니다. 패키징할 빌드-도구 패키지는 “권장” 빌드-도구 버전을 확인하고 사용하는 방식으로 선택됩니다(있는 경우). “권장” 버전이 ‘없을’ 경우 설치된 빌드-도구 패키지 중 가장 높은 버전이 사용됩니다. 

  `$(AndroidSdkBuildToolsVersion)` MSBuild 속성에는 기본 설정 빌드 도구 버전이 포함되어 있습니다. Xamarin.Android 빌드 시스템에서 `Xamarin.Android.Common.targets`에 기본값을 제공하고, 최신 aapt가 충돌하고 있지만 이전 버전의 aapt가 작동하는 것으로 알려져 있는 경우와 같이 다른 빌드 도구 버전을 선택하기 위해 프로젝트 파일 내에서 기본값을 재정의할 수 있습니다.

- **AndroidSupportedAbis** - `.apk`에 포함되어야 하는 ABI의 세미콜론(`;`)으로 구분된 목록을 포함하는 문자열 속성입니다.

  지원되는 값은 다음과 같습니다.

  - `armeabi-v7a`
  - `x86`
  - `arm64-v8a`: Xamarin.Android 5.1 이상이 필요합니다.
  - `x86_64`: Xamarin.Android 5.1 이상이 필요합니다.

- **AndroidTlsProvider** - 애플리케이션에서 사용할 TLS 공급자를 지정하는 문자열 값입니다. 가능한 값은

  - 설정 해제/빈 문자열: Xamarin.Android 7.3 이상에서는 `btls`에 해당합니다.

    Xamarin.Android 7.1에서는 `legacy`에 해당합니다.

    Visual Studio 속성 페이지의 **Default** 설정에 해당합니다.

  - `btls`: [HttpWebRequest](xref:System.Net.HttpWebRequest)와의 TLS 통신에는 [Boring SSL](https://boringssl.googlesource.com/boringssl)을 사용합니다.

    이렇게 하면 모든 Android 버전에서 TLS 1.2를 사용할 수 있습니다.

    Visual Studio 속성 페이지의 **Native TLS 1.2+** 설정에 해당합니다.

  - `legacy`: Xamarin.Android 10.1 및 이전 버전에서는 네트워크 상호 작용을 위해 기록 관리 SSL 구현을 사용합니다. 이는 TLS 1.2를 지원하지 *않습니다*.

    Visual Studio 속성 페이지의 **Managed TLS 1.0** 설정에 해당합니다.

    Xamarin.Android 10.2 이상에서는 이 값이 무시되고 `btls` 설정이 사용됩니다.

  - `default`: 이 값은 Xamarin.Android 프로젝트에 사용될 가능성이 없습니다. 이 값은 Visual Studio 속성 페이지의 **Default** 설정에 해당하는 빈 문자열을 대신 사용하는 것이 좋습니다.

    `default` 값은 Visual Studio 속성 페이지에서 제공되지 않습니다.

    현재 `legacy`에 해당합니다.

  Xamarin.Android 7.1에 추가되었습니다.

- **AndroidUseApkSigner** - 개발자가 `jarsigner` 대신 `apksigner` 도구를 사용할 수 있게 해주는 부울 속성입니다.

    Xamarin.Android 8.2에 추가되었습니다.

- **AndroidUseDefaultAotProfile** &ndash; 개발자가 기본 AOT 프로필의 사용을 억제할 수 있는 부울 속성입니다.

  기본 AOT 프로필을 표시하지 않으려면 이 속성을 `false`로 설정합니다.

  Xamarin.Android 10.1에 추가되었습니다.

- **AndroidUseLegacyVersionCode** - 부울 속성을 사용하면 개발자가 versionCode 계산을 이전의 사전 Xamarin.Android 8.2 동작으로 되돌릴 수 있습니다. 이러한 방법은 Google Play 스토어에서 기존 애플리케이션을 사용하는 개발자만 사용해야 합니다. 새 `$(AndroidVersionCodePattern)` 속성을 사용하는 것이 좋습니다.

  Xamarin.Android 8.2에 추가되었습니다.

- **AndroidUseManagedDesignTimeResourceGenerator** - `aapt` 대신 관리형 리소스 파서를 사용하는 디자인 타임 빌드로 전환하는 부울 속성입니다.

  Xamarin.Android 8.1에 추가되었습니다.

- **AndroidUseSharedRuntime** - 대상 디바이스에서 애플리케이션을 실행하기 위해 *공유 런타임 패키지*가 필요한지 여부를 결정하는 부울 속성입니다. 공유 런타임 패키지를 사용하면 애플리케이션 패키지의 크기를 줄여 패키지 생성 및 배포 프로세스의 속도를 높일 수 있습니다. 그러면 빌드, 배포, 디버그 소요 주기가 빨라집니다.

  이 속성은 디버그 빌드의 경우 `True`, 릴리스 프로젝트의 경우 `False`여야 합니다.

- **AndroidVersionCodePattern** - 개발자가 매니페스트의 `versionCode`를 사용자 지정할 수 있게 해주는 문자열 속성입니다.
  `versionCode`를 확인하는 방법에 대한 자세한 내용은 [APK의 버전 코드 만들기](~/android/deploy-test/building-apps/abi-specific-apks.md)를 참조하세요.

  예를 들어 `abi`가 `armeabi`이고 매니페스트의 `versionCode`가 `123`일 경우 `{abi}{versionCode}`는 `$(AndroidCreatePackagePerAbi)`가 True일 때 `1123`의 versionCode를 생성하고, 그렇지 않을 경우 값 123을 생성합니다.
  `abi`가 `x86_64`이고 매니페스트의 `versionCode`가 `44`일 경우. `$(AndroidCreatePackagePerAbi)`가 True일 때 `544`를 생성하고, 그렇지 않을 경우 값 `44`를 생성합니다.

  왼쪽 안쪽 여백 문자열 `{abi}{versionCode:0000}`을 포함할 경우 `versionCode`의 왼쪽 안쪽 여백이 `0`이므로 `50044`가 생성됩니다. 또는 `{abi}{versionCode:D4}`와 같은 10진수 패딩을 사용할 수 있습니다.
  이전 예제와 동일한 역할을 합니다.

  값이 정수여야 하므로 '0' 및 'Dx' 같은 안쪽 여백 형식만 지원됩니다.

  미리 정의된 주요 항목

  - **abi** - 앱의 대상 abi를 삽입합니다.
    - 2 &ndash; `armeabi-v7a`
    - 3 &ndash; `x86`
    - 4 &ndash; `arm64-v8a`
    - 5 &ndash; `x86_64`

  - **minSDK** - 아무것도 정의되지 않은 경우 `AndroidManifest.xml` 또는 `11`에서 지원되는 최소 SDK 값을 삽입합니다.

  - **versionCode** - `Properties\AndroidManifest.xml`의 버전 코드를 직접 사용합니다.

  `$(AndroidVersionCodeProperties)` 속성(다음에 정의됨)을 사용하여 사용자 지정 항목을 정의할 수 있습니다.

  기본적으로 값은 `{abi}{versionCode:D6}`으로 설정됩니다. 개발자가 이전 동작을 유지하려는 경우 `$(AndroidUseLegacyVersionCode)` 속성을 `true`로 설정하여 기본값을 재정의할 수 있습니다.

  Xamarin.Android 7.2에 추가되었습니다.

- **AndroidVersionCodeProperties** - 개발자가 `AndroidVersionCodePattern`과 함께 사용할 사용자 지정 항목을 정의할 수 있게 해주는 문자열 속성입니다. `key=value` 쌍 형식입니다. `value`의 모든 항목은 정수 값이어야 합니다. 예: `screen=23;target=$(_AndroidApiLevel)` 보시다시피 문자열에 기존 또는 사용자 지정 MSBuild 속성을 활용할 수 있습니다.

  Xamarin.Android 7.2에 추가되었습니다.

- **AotAssemblies** - 어셈블리를 네이티브 코드에 Ahead-of-Time 컴파일하고 `.apk`에 포함할지 여부를 결정하는 부울 속성입니다.

  이 속성에 대한 지원은 Xamarin.Android 5.1에 추가되었습니다.

  기본적으로 이 속성은 `False`입니다.

- **EmbedAssembliesIntoApk** - 앱의 어셈블리를 애플리케이션 패키지에 포함해야 하는지 여부를 결정하는 부울 속성입니다.

  이 속성은 릴리스 빌드의 경우 `True`, 디버그 빌드의 경우 `False`여야 합니다. 빠른 배포에서 대상 디바이스를 지원하지 않는 경우 디버그 빌드에서 `True`가 되어야 *할 수 있습니다*.

  이 속성이 `False`이면 `$(AndroidFastDeploymentType)` MSBuild 속성에서 `.apk`에 포함될 항목도 제어하며, 이로 인해 배포 및 다시 빌드 시간에 영향을 줍니다.

- **EnableLLVM** - Ahead-of-Time에서 어셈블리를 네이티브 코드로 컴파일할 때 LLVM을 사용할지 여부를 결정하는 부울 속성입니다.

  이 속성을 사용하는 프로젝트를 빌드하려면 Android NDK를 설치해야 합니다.

  이 속성에 대한 지원은 Xamarin.Android 5.1에 추가되었습니다.

  기본적으로 이 속성은 `False`입니다.

  `$(AotAssemblies)` MSBuild 속성이 `True`가 아닐 경우 이러한 속성이 무시됩니다.

- **EnableProguard** - Java 코드를 연결하기 위해 패키징 프로세스의 일부로 [proguard](https://developer.android.com/tools/help/proguard.html)를 실행할지 여부를 결정하는 부울 속성입니다.

  이 속성에 대한 지원은 Xamarin.Android 5.1에 추가되었습니다.

  기본적으로 이 속성은 `False`입니다.

  `True`일 경우 [ProguardConfiguration](#ProguardConfiguration) 파일을 사용하여 `proguard` 실행을 제어합니다.

- **JavaMaximumHeapSize** - 패키징 프로세스의 일부로 `.dex` 파일을 빌드할 때 사용할 **java**
  `-Xmx` 매개 변수 값을 지정합니다. 지정하지 않으면 `-Xmx` 옵션은 **java**에 값을 `1G`로 제공합니다. 이는 다른 플랫폼에 비해 Windows에서 일반적으로 필요하다는 것을 알수 있었습니다.

  [`_CompileDex` 대상이 `java.lang.OutOfMemoryError`를 throw](https://bugzilla.xamarin.com/show_bug.cgi?id=18327)할 경우 이 속성을 지정해야 합니다.

  다음을 변경하여 값을 사용자 지정합니다.

  ```xml
  <JavaMaximumHeapSize>1G</JavaMaximumHeapSize>
  ```

- **JavaOptions** - `.dex` 파일을 빌드할 때 **java**에 전달할 추가 명령줄 옵션을 지정합니다.

- **LinkerDumpDependencies** - 링커 종속성 파일 생성을 활성화하는 부울 속성입니다. 이 파일은 [illinkanalyzer](https://github.com/mono/linker/blob/master/src/analyzer/README.md) 도구에 대한 입력으로 사용할 수 있습니다.

  기본값은 False입니다.

- **MandroidI18n** - 애플리케이션에 포함할 국제화 지원(예: 데이터 정렬, 정렬 테이블)을 지정합니다. 값은 다음과 같은 대/소문자 구분 값이 하나 이상 포함된 쉼표 또는 세미콜론으로 구분된 목록입니다.

  - **없음**: 추가 인코딩을 포함하지 않습니다.

  - **All**: 사용 가능한 모든 인코딩을 포함합니다.

  - **CJK**: ‘일본어(EUC)’ \[enc-jp, CP51932\], ‘일본어(Shift-JIS)’ \[iso-2022-jp, shift\_jis, CP932\], ‘일본어(JIS)’ \[CP50220\], ‘중국어 간체(GB2312)’ \[gb2312, CP936\], ‘한국어(UHC)’ \[ks\_c\_5601-1987, CP949\], ‘한국어(EUC)’ \[euc-kr, CP51949\], ‘중국어 번체(Big5)’ \[big5, CP950\] 및 ‘중국어 간체(GB18030)’ \[GB18030, CP54936\] 같은 중국어, 일본어 및 한국어 인코딩을 포함합니다.        

  - **MidEast**: ‘터키어(Windows)’ \[iso-8859-9, CP1254\], ‘히브리어(Windows)’ \[windows-1255, CP1255\], ‘아랍어(Windows)’ \[windows-1256, CP1256\], ‘아랍어(ISO)’ \[iso-8859-6, CP28596\], ‘히브리어(ISO)’ \[iso-8859-8, CP28598\], ‘라틴어 5(ISO)’ \[iso-8859-9, CP28599\] 및 ‘히브리어(Iso Alternative)’ \[iso-8859-8, CP38598\] 같은 중동 인코딩을 포함합니다.       

  - **기타**: ‘키릴 자모(Windows)’ \[CP1251\], ‘발트어(Windows)’ \[iso-8859-4, CP1257\], ‘베트남어(Windows)’ \[CP1258\], ‘키릴 자모(KOI8-R)’ \[koi8-r, CP1251\], ‘우크라이나어(KOI8-U)’ \[koi8-u, CP1251\], ‘발트어(ISO)’ \[iso-8859-4, CP1257\], ‘키릴 자모(ISO)’ \[iso-8859-5, CP1251\], ‘ISCII 데바나가리 문자’ \[x-iscii-de, CP57002\], ‘ISCII 벵골어’ \[x-iscii-be, CP57003\], ‘ISCII 타밀어’ \[x-iscii-ta, CP57004\], ‘ISCII 텔루구어’ \[x-iscii-te, CP57005\], ‘ISCII 아삼어’ \[x-iscii-as, CP57006\], ‘ISCII 오리야어’ \[x-iscii-or, CP57007\], ‘ISCII 칸나다어’ \[x-iscii-ka, CP57008\], ‘ISCII 말라얄람어’ \[x-iscii-ma, CP57009\], ‘ISCII 구자라트어’ \[x-iscii-gu, CP57010\], ‘ISCII 펀잡어’ \[x-iscii-pa, CP57011\] 및 ‘태국어(Windows)’ \[CP874\] 같은 기타 인코딩을 포함합니다.                  

  - **Rare**: ‘IBM EBCDIC(터키어)’ \[CP1026\], ‘IBM EBCDIC(개방형 시스템 라틴어 1)’ \[CP1047\], ‘IBM EBCDIC(미국-캐나다와 유로)’ \[CP1140\], ‘IBM EBCDIC(독일과 유로)’ \[CP1141\], ‘IBM EBCDIC(덴마크/노르웨이와 유로)’ \[CP1142\], ‘IBM EBCDIC(핀란드/스웨덴과 유로)’ \[CP1143\], ‘IBM EBCDIC(이탈리아와 유로)’ \[CP1144\], ‘IBM EBCDIC(라틴 아메리카/스페인과 유로)’ \[CP1145\], ‘IBM EBCDIC(영국과 유로)’ \[CP1146\], ‘IBM EBCDIC(프랑스와 유로)’ \[CP1147\], ‘IBM EBCDIC(국가별 설정과 유로)’ \[CP1148\], ‘IBM EBCDIC(아이슬란드어와 유로)’ \[CP1149\], ‘IBM EBCDIC(독일)’ \[CP20273\], ‘IBM EBCDIC(덴마크/노르웨이)’ \[CP20277\], ‘IBM EBCDIC(핀란드/스웨덴)’ \[CP20278\], ‘IBM EBCDIC(이탈리아)’ \[CP20280\], ‘IBM EBCDIC(라틴 아메리카/스페인)’ \[CP20284\], ‘IBM EBCDIC(영국)’ \[CP20285\], ‘IBM EBCDIC(일본어 가타카나 확장)’ \[CP20290\], ‘IBM EBCDIC(프랑스)’ \[CP20297\], ‘IBM EBCDIC(아랍어)’ \[CP20420\], ‘IBM EBCDIC(히브리어)’ \[CP20424\], ‘IBM EBCDIC(아이슬란드어)’ \[CP20871\], ‘IBM EBCDIC(키릴 자모 - 세르비아어, 불가리아어)’ \[CP21025\], ‘IBM EBCDIC(미국-캐나다)’ \[CP37\], IBM EBCDIC(국가별 설정)’ \[CP500\], ‘아랍어(ASMO 708)’ \[CP708\], ‘중앙 유럽어(DOS)’ \[CP852\], ‘키릴 자모(DOS)’ \[CP855\], ‘터키어(DOS)’ \[CP857\], ‘서유럽어(DOS와 유로)’ \[CP858\], ‘히브리어(DOS)’ \[CP862\], ‘아랍어(DOS)’ \[CP864\], ‘러시아어(DOS)’ \[CP866\], ‘그리스어(DOS)’ \[CP869\], ‘IBM EBCDIC(라틴어 2)’ \[CP870\] 및 ‘IBM EBCDIC(그리스어)’ \[CP875\] 같은 희귀한 인코딩을 포함합니다.                                     

  - **West**: ‘서유럽어(Mac)’ \[macintosh, CP10000\], ‘아이슬란드어(Mac)’ \[x-mac-icelandic, CP10079\], ‘중앙 유럽어(Windows)’ \[iso-8859-2, CP1250\], ‘서유럽어(Windows)’ \[iso-8859-1, CP1252\], ‘그리스어(Windows)’ \[iso-8859-7, CP1253\], ‘중앙 유럽어(ISO)’ \[iso-8859-2, CP28592\], ‘라틴어 3(ISO)’ \[iso-8859-3, CP28593\], ‘그리스어(ISO)’ \[iso-8859-7, CP28597\], ‘라틴어 9(ISO)’ \[iso-8859-15, CP28605\], ‘OEM 미국’ \[CP437\], ‘서유럽어(DOS)’ \[CP850\], ‘포르투갈어(DOS)’ \[CP860\], ‘아이슬란드어(DOS)’ \[CP861\], ‘프랑스어(캐나다)(DOS)’ \[CP863\] 및 ‘북유럽어(DOS)’ \[CP865\] 같은 서부 인코딩을 포함합니다.               

  ```xml
  <MandroidI18n>West</MandroidI18n>
  ```

- **MonoSymbolArchive** - 릴리스 스택 추적에서 &ldquo;실제&rdquo; 파일 이름 및 줄 번호 정보를 추출하기 위해 나중에 `mono-symbolicate`와 함께 사용할 `.mSYM` 아티팩트를 만들지 여부를 제어하는 부울 속성입니다.

  디버깅 기호가 활성화된 &ldquo;릴리스&rdquo; 앱의 경우 이는 기본적으로 True입니다. `$(EmbedAssembliesIntoApk)`, `$(DebugSymbols)`, `$(Optimize)` 모두 True입니다.

  Xamarin.Android 7.1에 추가되었습니다.

### <a name="binding-project-build-properties"></a>프로젝트 빌드 속성 바인딩

다음 MSBuild 속성을 [바인딩 프로젝트](~/android/platform/binding-java-library/index.md)와 함께 사용할 수 있습니다.

- **AndroidClassParser** - `.jar` 파일이 구문 분석되는 방법을 제어하는 문자열 속성입니다. 가능한 값은 다음과 같습니다.

  - **클래스 구문 분석**: JVM의 보조 없이 `class-parse.exe`를 사용하여 Java 바이트코드의 구문 분석을 바로 수행합니다. 이 값은 실험적입니다.

  - **jar2xml**: `jar2xml.jar`을 통해 Java 리플렉션을 사용하여 `.jar` 파일에서 형식과 멤버를 추출합니다.

  `jar2xml`보다 `class-parse`가 더 나은 점은 다음과 같습니다.

  - `class-parse`는 *디버그* 기호를 포함하는 Java 바이트 코드(예: `javac -g`로 컴파일된 바이트코드)에서 매개 변수 이름을 추출할 수 있습니다.

  - `class-parse`는 확인할 수 없는 형식의 멤버에서 상속하거나 이러한 멤버를 포함하는 클래스를 “건너뛰지” 않습니다.

  **실험적**. Xamarin.Android 6.0에 추가되었습니다.

  기본값은 `jar2xml`입니다.

  이 기본값은 향후 릴리스에서 변경될 예정입니다.

- **AndroidCodegenTarget** - 코드 생성 대상 ABI를 제어하는 문자열 속성입니다. 가능한 값은 다음과 같습니다.

  - **XamarinAndroid**: Mono for Android 1.0 이후에 제공되는 JNI 바인딩 API를 사용합니다. Xamarin.Android 5.0 이상을 사용하여 빌드된 바인딩 어셈블리는 Xamarin.Android 5.0 이상(API/ABI 추가)에서만 실행할 수 있지만 *소스*는 이전 제품 버전과 호환됩니다.

  - **XAJavaInterop1**: JNI 호출용 Java.Interop을 사용합니다. `XAJavaInterop1`을 사용하는 바인딩 어셈블리는 Xamarin.Android 6.1 이상을 통해서만 빌드하고 실행할 수 있습니다. Xamarin.Android 6.1 이상은 이 값과 `Mono.Android.dll`을 바인딩합니다.

    `XAJavaInterop1`의 이점은 다음과 같습니다.

    - 더 작은 어셈블리.

    - `base` 메서드 호출을 위한 `jmethodID` 캐싱(상속 계층 구조의 다른 모든 바인딩 형식이 `XAJavaInterop1` 이상으로 빌드된 경우에 한함).

    - 관리되는 서브클래스를 위한 Java Callable Wrapper 생성자 `jmethodID` 캐싱.

    기본값은 `XAJavaInterop1`입니다.

### <a name="resource-properties"></a>리소스 속성

리소스 속성은 Android 리소스에 대한 액세스를 제공하는 `Resource.designer.cs` 파일의 생성을 제어합니다.

- **AndroidAapt2CompileExtraArgs** - Android 자산 및 리소스 처리 시 **aapt2 컴파일** 명령에 전달할 추가 명령줄 옵션을 지정합니다.

  Xamarin.Android 9.1에 추가되었습니다.

- **AndroidAapt2LinkExtraArgs** - Android 자산 및 리소스 처리 시 **aapt2 링크** 명령에 전달할 추가 명령줄 옵션을 지정합니다.

  Xamarin.Android 9.1에 추가되었습니다.

- **AndroidExplicitCrunch** - 많은 로컬 드로어블을 사용하여 앱을 빌드할 경우 초기 빌드(또는 재빌드)를 완성하는 데 몇 분이 걸릴 수 있습니다. 빌드 프로세스 속도를 높이려면 이 속성을 포함하고 `True`로 설정해 보세요. 이 속성이 설정되면 빌드 프로세스가 .png 파일을 사전에 크런치합니다.

  참고: 이 옵션은 `$(AndroidUseAapt2)` 옵션과 호환되지 않습니다. `$(AndroidUseAapt2)`를 사용하면 이 기능은 사용할 수 없습니다. 이 기능을 계속 사용하려면 `$(AndroidUseAapt2)`를 `False`로 설정합니다.

  **실험적**. Xamarin.Android 7.0에 추가되었습니다.

- **AndroidResgenExtraArgs** - Android 자산 및 리소스 처리 시 **aapt** 명령에 전달할 추가 명령줄 옵션을 지정합니다.

- **AndroidResgenFile** - 생성할 리소스 파일의 이름을 지정합니다. 기본 템플릿은 이를 `Resource.designer.cs`로 설정합니다.

- **AndroidUseAapt2** - 개발자가 패키징을 위한 `aapt2` 도구 사용을 제어할 수 있게 해 주는 부울 속성입니다.
  기본적으로 이 값은 False로 설정되며 `aapt`를 사용합니다.
  개발자가 새로운 `aapt2` 기능을 사용하려는 경우 설정할 수 있습니다.

  ```xml
  <AndroidUseAapt2>True</AndroidUseAapt2>
  ```

  해당 .csproj에서 설정할 수 있습니다. 또는 다음을 통해 명령줄에 속성을 제공합니다.

  ```
  /p:AndroidUseAapt2=True
  ```

  Xamarin.Android 8.3에 추가되었습니다.

- **MonoAndroidResourcePrefix** - 빌드 작업이 `AndroidResource`인 파일 이름의 앞부분에서 제거되는 ‘경로 접두사’를 지정합니다.  리소스가 있는 위치를 변경하도록 하기 위함입니다.

  기본값은 `Resources`입니다. Java 프로젝트 구조체의 경우 이를 `res`로 변경합니다.

<a name="Signing_Properties" />

### <a name="signing-properties"></a>서명 속성

서명 속성은 Android 디바이스에 설치할 수 있도록 애플리케이션 패키지에 서명하는 방법을 제어합니다. 더욱 빠른 빌드 반복을 위해 Xamarin.Android 작업은 빌드 프로세스 중에 패키지에 서명되지 않습니다. 서명 속도가 상당히 느리기 때문입니다. 대신, IDE 또는 *설치* 빌드 대상을 통해 설치 전이나 내보내기 중에 서명합니다(필요할 경우). *SignAndroidPackage* 대상을 호출하면 출력 디렉터리에 접미사가 `-Signed.apk`인 패키지가 생성됩니다.

기본적으로 서명 대상은 필요한 경우 새 디버그 서명 키를 생성합니다. 예를 들어 빌드 서버에서 특정 키를 사용하려는 경우 다음 MSBuild 속성을 사용할 수 있습니다.

- **AndroidDebugKeyAlgorithm** - `debug.keystore`에 사용할 기본 알고리즘을 지정합니다. 기본값은 `RSA`입니다.

- **AndroidDebugKeyValidity** - `debug.keystore`에 사용할 기본 유효성을 지정합니다. 기본값은 `10950`, `30 * 365` 또는 `30 years`입니다.

- **AndroidDebugStoreType** &ndash; `debug.keystore`에 사용할 키 저장소 파일 형식을 지정합니다. 기본값은 `pkcs12`입니다.

  Xamarin.Android 10.2에 추가되었습니다.

- **AndroidKeyStore** - 사용자 지정 서명 정보를 사용할지 여부를 나타내는 부울 값입니다. 기본값은 `False`이며, 이는 기본 디버그 서명 키를 사용하여 패키지에 서명한다는 의미입니다.

- **AndroidSigningKeyAlias** - 키 저장소의 키 별칭을 지정합니다. 이는 키 저장소를 만들 때 사용되는 **keytool -alias** 값입니다.

- **AndroidSigningKeyPass** - 키 저장소 파일 내에서 키의 암호를 지정합니다. 이는 `keytool`이 **$(AndroidSigningKeyAlias)에 대한 키 암호 입력**을 요청하는 경우에 입력하는 값입니다.

  Xamarin.Android 10.0 및 이전 버전에서 이 속성은 일반 텍스트 암호만 지원합니다.

  Xamarin.Android 10.1 이상에서 이 속성은 암호를 포함하는 환경 변수 또는 파일을 지정하는 데 사용할 수 있는 `env:` 및 `file:` 접두사도 지원합니다. 이러한 옵션은 빌드 로그에 암호가 표시되지 않도록 하는 방법을 제공합니다.

  예를 들어 *AndroidSigningPassword*라는 환경 변수를 사용하려면

  ```xml
  <PropertyGroup>
      <AndroidSigningKeyPass>env:AndroidSigningPassword</AndroidSigningKeyPass>
  </PropertyGroup>
  ```

  `C:\Users\user1\AndroidSigningPassword.txt`에 있는 파일을 사용하려면

  ```xml
  <PropertyGroup>
      <AndroidSigningKeyPass>file:C:\Users\user1\AndroidSigningPassword.txt</AndroidSigningKeyPass>
  </PropertyGroup>
  ```

  > [!NOTE]
  > `$(AndroidPackageFormat)`이 `aab`로 설정된 경우에는 `env:` 접두사가 지원되지 않습니다.

- **AndroidSigningKeyStore** - `keytool`에서 만든 키 저장소 파일의 파일 이름을 지정합니다. 이는 **keytool -keystore** 옵션에 입력된 값에 해당합니다.

- **AndroidSigningStorePass** - `$(AndroidSigningKeyStore)`의 암호를 지정합니다. 이는 키 저장소 파일을 만들 때 `keytool`에 제공되고 **키 저장소 암호 입력:** 을 요청하는 값입니다.

  Xamarin.Android 10.0 및 이전 버전에서 이 속성은 일반 텍스트 암호만 지원합니다.

  Xamarin.Android 10.1 이상에서 이 속성은 암호를 포함하는 환경 변수 또는 파일을 지정하는 데 사용할 수 있는 `env:` 및 `file:` 접두사도 지원합니다. 이러한 옵션은 빌드 로그에 암호가 표시되지 않도록 하는 방법을 제공합니다.

  예를 들어 *AndroidSigningPassword*라는 환경 변수를 사용하려면

  ```xml
  <PropertyGroup>
      <AndroidSigningStorePass>env:AndroidSigningPassword</AndroidSigningStorePass>
  </PropertyGroup>
  ```

  `C:\Users\user1\AndroidSigningPassword.txt`에 있는 파일을 사용하려면

  ```xml
  <PropertyGroup>
      <AndroidSigningStorePass>file:C:\Users\user1\AndroidSigningPassword.txt</AndroidSigningStorePass>
  </PropertyGroup>
  ```

  > [!NOTE]
  > `$(AndroidPackageFormat)`이 `aab`로 설정된 경우에는 `env:` 접두사가 지원되지 않습니다.

- **JarsignerTimestampAuthorityCertificateAlias** &ndash; 이 속성을 사용하면 키 저장소에서 타임스탬프 인증 기관에 대한 별칭을 지정할 수 있습니다.
  자세한 내용은 Java [서명 타임스탬프 지원](https://docs.oracle.com/javase/8/docs/technotes/guides/security/time-of-signing.html) 설명서를 참조하세요.

  ```xml
  <PropertyGroup>
      <JarsignerTimestampAuthorityCertificateAlias>Alias</JarsignerTimestampAuthorityCertificateAlias>
  </PropertyGroup>
  ```

- **JarsignerTimestampAuthorityUrl** &ndash; 이 속성을 사용하면 타임스탬프 인증 기관 서비스에 URL을 지정할 수 있습니다. 이를 사용하여 `.apk` 서명에 타임스탬프가 포함되도록 할 수 있습니다.
  자세한 내용은 Java [서명 타임스탬프 지원](https://docs.oracle.com/javase/8/docs/technotes/guides/security/time-of-signing.html) 설명서를 참조하세요.

  ```xml
  <PropertyGroup>
      <JarsignerTimestampAuthorityUrl>http://example.tsa.url</JarsignerTimestampAuthorityUrl>
  </PropertyGroup>
  ```

예를 들어 다음 `keytool` 호출을 살펴봅니다.

```shell
$ keytool -genkey -v -keystore filename.keystore -alias keystore.alias -keyalg RSA -keysize 2048 -validity 10000
Enter keystore password: keystore.filename password
Re-enter new password: keystore.filename password
...
Is CN=... correct?
  [no]:  yes

Generating 2,048 bit RSA key pair and self-signed certificate (SHA1withRSA) with a validity of 10,000 days
        for: ...
Enter key password for keystore.alias
        (RETURN if same as keystore password): keystore.alias password
[Storing filename.keystore]
```

위에서 생성한 키 저장소를 사용하려면 속성 그룹을 사용합니다.

```xml
<PropertyGroup>
    <AndroidKeyStore>True</AndroidKeyStore>
    <AndroidSigningKeyStore>filename.keystore</AndroidSigningKeyStore>
    <AndroidSigningStorePass>keystore.filename password</AndroidSigningStorePass>
    <AndroidSigningKeyAlias>keystore.alias</AndroidSigningKeyAlias>
    <AndroidSigningKeyPass>keystore.alias password</AndroidSigningKeyPass>
</PropertyGroup>
```

<a name="Build_Actions" />

## <a name="build-actions"></a>빌드 작업

*빌드 동작*은 프로젝트 내 [파일에 적용](https://docs.microsoft.com/visualstudio/msbuild/common-msbuild-project-items)되며, 파일이 처리되는 방식을 제어합니다.

### <a name="androidaarlibrary"></a>AndroidAarLibrary

.aar 파일을 직접 참조하려면 `AndroidAarLibrary`의 빌드 동작을 사용해야 합니다. 이 빌드 동작은 Xamarin 구성 요소에서 가장 일반적으로 사용됩니다. 즉 Google Play 및 기타 서비스를 작동하는 데 필요한 .aar 파일에 대한 참조를 포함합니다.

이 빌드 동작을 사용하는 파일은 라이브러리 프로젝트에 있는 포함 리소스와 비슷한 방식으로 처리됩니다. .aar은 중간 디렉터리로 추출됩니다. 그런 다음, 모든 자산, 자원 및 .jar 파일이 적절한 항목 그룹에 포함됩니다.

### <a name="androidboundlayout"></a>AndroidBoundLayout

`AndroidGenerateLayoutBindings` 속성이 `false`로 설정된 경우 레이아웃 파일에 대해 코드 숨김을 생성해야 함을 나타냅니다. 다른 모든 측면에서는 위에서 설명한 `AndroidResource`와 동일합니다. 이 작업은 레이아웃 파일에서**만** 사용할 수 있습니다.

```xml
<AndroidBoundLayout Include="Resources\layout\Main.axml" />
```

<a name="AndroidEnvironment" />

### <a name="androidenvironment"></a>AndroidEnvironment

빌드 동작이 `AndroidEnvironment`인 파일은 [프로세스 시작 시 환경 변수 및 시스템 속성을 초기화](~/android/deploy-test/environment.md)하는 데 사용됩니다.
`AndroidEnvironment` 빌드 동작은 여러 파일에 적용될 수 있으며, 특정 순서로 평가되지 않습니다(따라서 여러 파일에 동일한 환경 변수나 시스템 속성을 지정해서는 안 됨).

### <a name="androidfragmenttype"></a>AndroidFragmentType

레이아웃 바인딩 코드를 생성할 때 모든 `<fragment>` 레이아웃 요소에 사용할 기본 정규화된 형식을 지정합니다. 이 속성의 기본값은 표준 Android `Android.App.Fragment` 형식입니다.

### <a name="androidjavalibrary"></a>AndroidJavaLibrary

빌드 동작이 `AndroidJavaLibrary`인 파일은 최종 Android 패키지에 포함될 Java 아카이브(`.jar` 파일)입니다.

### <a name="androidjavasource"></a>AndroidJavaSource

빌드 동작이 `AndroidJavaSource`인 파일은 최종 Android 패키지에 포함될 Java 소스 코드입니다.

### <a name="androidlintconfig"></a>AndroidLintConfig

빌드 작업 'AndroidLintConfig'는 `AndroidLintEnabled` 빌드 속성과 함께 사용해야 합니다. 이 빌드 작업이 포함된 파일은 함께 병합되어 Android `lint` 도구에 전달됩니다. 테스트를 활성화/비활성화할 정보가 포함된 XML 파일이어야 합니다.

자세한 내용은 [lint 설명서](https://developer.android.com/studio/write/lint)를 참조하세요.

### <a name="androidnativelibrary"></a>AndroidNativeLibrary

[네이티브 라이브러리](~/android/platform/native-libraries.md)는 `AndroidNativeLibrary`에 빌드 동작을 설정하여 빌드에 추가됩니다.

Android는 여러 ABI(애플리케이션 이진 인터페이스)를 지원하므로, 빌드 시스템은 네이티브 라이브러리가 대상으로 하는 ABI를 알아야 합니다. 이는 두 가지 방법으로 수행할 수 있습니다.

1. 경로 "검색".
2. `Abi` 항목 특성 사용.

경로 검색을 사용하면 네이티브 라이브러리의 부모 디렉터리 이름을 사용하여 라이브러리가 대상으로 하는 ABI를 지정할 수 있습니다. 따라서 빌드에 `lib/armeabi-v7a/libfoo.so`를 추가하면 ABI가 `armeabi-v7a`로 "검색"됩니다.

#### <a name="item-attribute-name"></a>항목 특성 이름

**Abi** - 네이티브 라이브러리의 ABI를 지정합니다.

```xml
<ItemGroup>
  <AndroidNativeLibrary Include="path/to/libfoo.so">
    <Abi>armeabi-v7a</Abi>
  </AndroidNativeLibrary>
</ItemGroup>
```

### <a name="androidresource"></a>AndroidResource

빌드 동작이 *AndroidResource*인 모든 파일은 빌드 프로세스 중에 Android 리소스에 컴파일되며, `$(AndroidResgenFile)`을 통해 액세스할 수 있게 됩니다.

```xml
<ItemGroup>
  <AndroidResource Include="Resources\values\strings.xml" />
</ItemGroup>
```

고급 사용자는 동일한 유효 경로를 사용하여 여러 구성에서 여러 리소스를 사용하기를 원할 수 있습니다. 이를 위해서는 여러 리소스 디렉터리가 있고 이러한 여러 디렉터리에 동일한 상대 경로를 가진 파일이 있어야 하며, 여러 구성의 여러 파일을 조건부로 포함하는 MSBuild 조건을 사용해야 합니다. 예를 들어:

```xml
<ItemGroup Condition="'$(Configuration)'!='Debug'">
  <AndroidResource Include="Resources\values\strings.xml" />
</ItemGroup>
<ItemGroup  Condition="'$(Configuration)'=='Debug'">
  <AndroidResource Include="Resources-Debug\values\strings.xml"/>
</ItemGroup>
<PropertyGroup>
  <MonoAndroidResourcePrefix>Resources;Resources-Debug<MonoAndroidResourcePrefix>
</PropertyGroup>
```

**LogicalName** - 리소스 경로를 명시적으로 지정합니다. 여러 개별 리소스 이름으로 사용할 수 있도록 &ldquo;별칭&rdquo; 파일을 허용합니다.

```xml
<ItemGroup Condition="'$(Configuration)'!='Debug'">
  <AndroidResource Include="Resources/values/strings.xml"/>
</ItemGroup>
<ItemGroup Condition="'$(Configuration)'=='Debug'">
  <AndroidResource Include="Resources-Debug/values/strings.xml">
    <LogicalName>values/strings.xml</LogicalName>
  </AndroidResource>
</ItemGroup>
```

### <a name="androidresourceanalysisconfig"></a>AndroidResourceAnalysisConfig

빌드 작업 `AndroidResourceAnalysisConfig`는 파일을 Xamarin Android Designer 레이아웃 진단 도구에 대한 심각도 수준 구성 파일로 표시합니다. 이는 현재 레이아웃 편집기에서만 사용되며 빌드 메시지에는 사용되지 않습니다.

자세한 내용은 [Android 리소스 분석 설명서](https://aka.ms/androidresourceanalysis)를 참조하세요.

Xamarin.Android 10.2에 추가되었습니다.

### <a name="content"></a>콘텐츠

일반적인 `Content` 빌드 동작이 지원되지 않습니다(비용이 많이 드는 최초 실행 단계 없이 지원하는 방법을 파악하지 못했기 때문).

Xamarin.Android 5.1부터 `@(Content)` 빌드 작업을 사용하려고 하면 `XA0101` 경고가 표시됩니다.

### <a name="linkdescription"></a>LinkDescription

빌드 동작이 *LinkDescription*인 파일은 [링커 동작을 제어](~/cross-platform/deploy-test/linker.md)하는 데 사용됩니다.

<a name="ProguardConfiguration" />

### <a name="proguardconfiguration"></a>ProguardConfiguration

빌드 동작이 *ProguardConfiguration*인 파일에는 `proguard` 동작을 제어하는 데 사용되는 옵션이 포함됩니다. 이 빌드 동작에 대한 자세한 내용은 [ProGuard](~/android/deploy-test/release-prep/proguard.md)를 참조하세요.

`$(EnableProguard)` MSBuild 속성이 `True`가 아닐 경우 이러한 파일이 무시됩니다.

## <a name="target-definitions"></a>대상 정의

빌드 프로세스의 Xamarin.Android 관련 부분은 `$(MSBuildExtensionsPath)\Xamarin\Android\Xamarin.Android.CSharp.targets`에 정의되지만 어셈블리를 빌드하기 위해 *Microsoft.CSharp.targets*와 같은 일반적인 언어 관련 대상도 필요합니다.

모든 언어 대상을 가져오기 전에 다음과 같은 빌드 속성을 설정해야 합니다.

```xml
<PropertyGroup>
  <TargetFrameworkIdentifier>MonoDroid</TargetFrameworkIdentifier>
  <MonoDroidVersion>v1.0</MonoDroidVersion>
  <TargetFrameworkVersion>v2.2</TargetFrameworkVersion>
</PropertyGroup>
```

C#에서는 *Xamarin.Android.CSharp.targets*를 가져와서 이러한 대상과 속성을 모두 포함할 수 있습니다.

```xml
<Import Project="$(MSBuildExtensionsPath)\Xamarin\Android\Xamarin.Android.CSharp.targets" />
```

이 파일은 다른 언어에 맞게 쉽게 조정할 수 있습니다.
