---
title: 5부 - 실제 코드 공유 전략
description: 이 문서에는 실제 코드 공유 전략 데이터베이스, 파일 액세스, 네트워크 작업 및 비동기 코드와 같은 시나리오에 설명 합니다.
ms.prod: xamarin
ms.assetid: 328D042A-FF78-A7B6-1574-B5AF49A1AADB
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 0e37e138607fb0e00fbdc463ac7c53facf81395d
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76723632"
---
# <a name="part-5---practical-code-sharing-strategies"></a>5부 - 실제 코드 공유 전략

이 섹션에서는 일반적인 응용 프로그램 시나리오에 대 한 코드를 공유 하는 방법의 예제를 제공 합니다.

## <a name="data-layer"></a>데이터 계층

데이터 계층 정보 읽기 및 쓰기 방법 및 저장소 엔진으로 구성 됩니다. 성능, 유연성 및 플랫폼 간 호환성을 SQLite에 대 한 데이터베이스 엔진은 Xamarin 플랫폼 간 응용 프로그램에 권장 됩니다.
다양 한 Windows, Android, iOS 및 Mac.를 비롯 하 여 플랫폼에서 실행

### <a name="sqlite"></a>SQLite

SQLite는 오픈 소스 데이터베이스 구현입니다. 원본 및 설명서는 [SQLite.org](https://www.sqlite.org/)에서 찾을 수 있습니다. SQLite 지원은 각 모바일 플랫폼에서 사용할 수 있습니다.

- **iOS** – 운영 체제에 기본 제공 됩니다.
- **Android** – android 2.2 이후 운영 체제에 기본 제공 됩니다 (API 수준 10).
- **Windows** – [유니버설 Windows 플랫폼 확장은 SQLite](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936)를 참조 하세요.

모든 플랫폼에서 사용할 수 있는 데이터베이스 엔진을 사용 하더라도 데이터베이스에 액세스 하는 네이티브 메서드는 다릅니다. 하지만 IOS 및 Android Xamarin.iOS 또는 Xamarin.Android에서 사용할 수 있는 SQLite에 액세스 하는 기본 제공 Api를 제공, (아마도 SQL 쿼리 자체에 문자열로 저장 된 것으로 가정)이 아닌 코드를 공유 하는 기능이 없습니다 제공 네이티브 SDK 메서드를 사용 하 여 모두 . 네이티브 데이터베이스 기능에 대 한 자세한 내용은 iOS 또는 Android의 `SQLiteOpenHelper` 클래스에서 `CoreData`를 검색 합니다. 이러한 옵션은 플랫폼 간이 아니기 때문에이 문서에서는 다루지 않습니다.

### <a name="adonet"></a>ADO.NET

Xamarin.ios 및 Xamarin.ios는 모두 `System.Data` 및 `Mono.Data.Sqlite`를 지원 합니다 (자세한 내용은 Xamarin.ios [설명서](~/ios/data-cloud/system.data.md) 참조).
이러한 네임 스페이스를 사용 하 여 두 플랫폼 모두에서 작동 하는 ADO.NET 코드를 작성할 수 있습니다. `System.Data.dll` 및 `Mono.Data.Sqlite.dll`를 포함 하도록 프로젝트의 참조를 편집 하 고 다음 using 문을 코드에 추가 합니다.

```csharp
using System.Data;
using Mono.Data.Sqlite;
```

다음 샘플 코드 작동 합니다.

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "items.db3");
bool exists = File.Exists (dbPath);
if (!exists)
    SqliteConnection.CreateFile (dbPath);
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open ();
if (!exists) {
    // This is the first time the app has run and/or that we need the DB.
    // Copy a "template" DB from your assets, or programmatically create one like this:
    var commands = new[]{
        "CREATE TABLE [Items] (Key ntext, Value ntext);",
        "INSERT INTO [Items] ([Key], [Value]) VALUES ('sample', 'text')"
    };
    foreach (var command in commands) {
        using (var c = connection.CreateCommand ()) {
            c.CommandText = command;
            c.ExecuteNonQuery ();
        }
    }
}
// use `connection`... here, we'll just append the contents to a TextView
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT [Key], [Value] from [Items]";
    var r = contents.ExecuteReader ();
    while (r.Read ())
        Console.Write("\n\tKey={0}; Value={1}",
                r ["Key"].ToString (),
                r ["Value"].ToString ());
}
connection.Close ();
```

ADO.NET의 실제 구현은 분명히 다른 메서드 및 클래스 (이 예제는 데모용 으로만 제공)으로 분리 되어 있습니다.

### <a name="sqlite-net--cross-platform-orm"></a>SQLite-NET-플랫폼 간 ORM

ORM (개체 관계형 매퍼 또는) 클래스에서 모델링 하는 데이터의 저장소를 단순화 하려고 합니다. 대신 수동으로 추출 된 데이터를 삽입 및 삭제 클래스 필드 하 고 해당 작업을 수행 하는 코드의 레이어를 추가 하는 속성을 ORM 수동으로 해당 테이블 만들기 또는 선택에는 SQL 쿼리를 작성 합니다. 리플렉션을 사용 하 여 클래스의 구조를 검사, ORM을 자동으로 만들 수는 클래스와 일치 하 고 쿼리 된 데이터 읽기 및 쓰기를 생성 하는 테이블 및 열입니다. 이 응용 프로그램 코드가 단순히 보내고 개체 인스턴스 내부에서 모든 SQL 작업을 처리 하는 ORM 검색할 수 있습니다.

SQLite-NET 저장 하 고 SQLite 클래스를 검색할 수 있는 부분은 단순한 ORM으로 작동 합니다. 크로스 컴파일러 지시문 및 다른 유용한 정보를 조합 하 여 SQLite 액세스 플랫폼의 복잡성을 숨깁니다.

SQLite-NET의 기능은 다음과 같습니다.

- 모델 클래스에 특성을 추가 하 여 테이블 정의 됩니다.
- 데이터베이스 인스턴스는 SQLite 라이브러리의 기본 클래스인 `SQLiteConnection`의 서브 클래스로 표현 됩니다.
- 쿼리 및 삭제 된 개체를 사용 하 여 데이터를 삽입할 수 있습니다. SQL 문이 없습니다는 (하지만 필요한 경우 SQL 문을 작성할 수 있습니다)에 필요 합니다.
- SQLite-NET 반환한 컬렉션에 대해 기본 Linq 쿼리를 수행할 수 있습니다.

SQLite의 소스 코드 및 설명서는 [github의 sqlite](https://github.com/praeclarum/sqlite-net) 에서 사용할 수 있으며 사례 연구에서 구현 되었습니다. 예를 들어 *Tasky Pro* 사례 연구에서 SQLITE-NET 코드의 간단한 예를 볼 수 있습니다.

먼저 `TodoItem` 클래스는 특성을 사용 하 여 필드를 데이터베이스 기본 키로 정의 합니다.

```csharp
public class TodoItem : IBusinessEntity
{
    public TodoItem () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

이를 통해 `SQLiteConnection` 인스턴스에서 다음 코드 줄 (및 SQL 문 없음)을 사용 하 여 `TodoItem` 테이블을 만들 수 있습니다.

```csharp
CreateTable<TodoItem> ();
```

테이블의 데이터는 SQL 문을 요구 하지 않고 `SQLiteConnection`의 다른 메서드를 사용 하 여 조작할 수도 있습니다.

```csharp
Insert (TodoItem); // 'task' is an instance with data populated in its properties
Update (TodoItem); // Primary Key field must be populated for Update to work
Table<TodoItem>.ToList(); // returns all rows in a collection
```

전체 예제에 대 한 사례 연구 소스 코드를 참조 하세요.

## <a name="file-access"></a>파일 액세스

파일 액세스는 특정 응용 프로그램의 핵심 부분입니다. 응용 프로그램의 일부일 수도 있는 파일의 일반적인 예제:

- SQLite 데이터베이스 파일입니다.
- 사용자 생성 데이터 (텍스트, 이미지, 사운드, 비디오).
- 캐싱 (이미지, html 또는 PDF 파일)에 대 한 다운로드 한 데이터입니다.

### <a name="systemio-direct-access"></a>System.IO 직접 액세스

Xamarin.ios 및 Xamarin.ios는 모두 `System.IO` 네임 스페이스의 클래스를 사용 하 여 파일 시스템에 액세스할 수 있습니다.

각 플랫폼에는 고려해 야 하는 다양 한 액세스 제한 사항이지 않습니다 있습니다.

- iOS 응용 프로그램은 매우 제한 된 파일 시스템 액세스를 사용 하 여 샌드박스에서 실행합니다. 추가 Apple 백업 된 특정 위치 (및 기타 없는)를 지정 하 여 파일 시스템을 사용 해야 하는 방법을 지정 합니다. 자세한 내용은 [xamarin.ios에서 파일 시스템 사용](~/ios/app-fundamentals/file-system.md) 가이드를 참조 하세요.
- Android 응용 프로그램에 관련 된 특정 디렉터리에도 액세스를 제한 하지만 또한 지원 외부 미디어 (예: SD 카드) 및 공유 데이터에 액세스 합니다.
- Windows Phone 8 (Silverlight)는 직접 파일 액세스를 허용 하지 않습니다. 파일은 `IsolatedStorage`를 사용 해야만 조작할 수 있습니다.
- Windows 8.1 WinRT 및 Windows 10 UWP 프로젝트는 다른 플랫폼과 다른 `Windows.Storage` Api를 통해서만 비동기 파일 작업을 제공 합니다.

#### <a name="example-for-ios-and-android"></a>IOS 및 Android에 대 한 예제

텍스트 파일을 읽고 작성 하는 간단한 예는 다음과 같습니다.
`Environment.GetFolderPath`를 사용 하면 동일한 코드를 iOS 및 Android에서 실행할 수 있으며, 각각은 파일 시스템 규칙에 따라 올바른 디렉터리를 반환 합니다.

```csharp
string filePath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "MyFile.txt");
System.IO.File.WriteAllText (filePath, "Contents of text file");
Console.WriteLine (System.IO.File.ReadAllText (filePath));
```

IOS 관련 파일 시스템 기능에 대 한 자세한 내용은 [파일 시스템](~/ios/app-fundamentals/file-system.md) 문서를 참조 하세요. 플랫폼 간 파일 액세스 코드를 작성 하는 경우에 일부 파일 시스템 대 소문자를 구분 하 고 다른 디렉터리 구분 기호가 해야 합니다. 파일 또는 디렉터리 경로를 생성할 때 파일 이름에 대해 항상 동일한 대/소문자를 사용 하 고 `Path.Combine()` 방법을 사용 하는 것이 좋습니다.

### <a name="windowsstorage-for-windows-8-and-windows-10"></a>Windows 8 및 Windows 10에 대 한 Windows.Storage

*Xamarin.ios를 사용 하 여 Mobile Apps 만들기* [문서](https://developer.xamarin.com/r/xamarin-forms/book/) 는 [20 장
. Async 및 File i/o](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) [에는 Windows 8.1 및 Windows 10에 대 한 샘플이](https://github.com/xamarin/xamarin-forms-book-preview-2/tree/master/Chapter20)포함 되어 있습니다.

[`DependencyService`](~/xamarin-forms/app-fundamentals/dependency-service/index.md) 사용 하면 지원 되는 api를 사용 하 여 이러한 플랫폼에서 파일을 읽고 파일을 읽을 수 있습니다.

```csharp
StorageFolder localFolder = ApplicationData.Current.LocalFolder;
IStorageFile storageFile = await localFolder.CreateFileAsync("MyFile.txt",
                                        CreationCollisionOption.ReplaceExisting);
await FileIO.WriteTextAsync(storageFile, "Contents of text file");
```

자세한 내용은 [책 장](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) 을 참조 하세요.

<a name="Isolated_Storage" />

### <a name="isolated-storage-on-windows-phone-7--8-silverlight"></a>Windows Phone 7 및 8 (Silverlight)에서 격리 된 저장소

격리 된 저장소는 일반적인 API 저장 하 고 모든 iOS, Android 및 이전 버전의 Windows Phone 플랫폼과 간에 파일을 로드 합니다.

쓸 일반적인 파일 액세스 코드를 허용 하려면 Xamarin.iOS 및 Xamarin.Android에서 구현 된 Windows Phone (Silverlight)에서 파일 액세스를 위한 기본 메커니즘입니다. `System.IO.IsolatedStorage` 클래스는 [공유 프로젝트](~/cross-platform/app-fundamentals/shared-projects.md)의 세 플랫폼에서 모두 참조할 수 있습니다.

자세한 내용은 [격리 된 저장소 Windows Phone 개요](https://msdn.microsoft.com/library/windowsphone/develop/ff402541(v=vs.105).aspx) 를 참조 하세요.

격리 된 저장소 Api는 [이식 가능한 클래스 라이브러리](~/cross-platform/app-fundamentals/pcl.md)에서 사용할 수 없습니다. PCL의 한 가지 대안은 [Pclstorage NuGet](https://pclstorage.codeplex.com/) 입니다.

### <a name="cross-platform-file-access-in-pcls"></a>Pcl에서 플랫폼 간 파일 액세스

또한 Xamarin 지원 플랫폼 및 최신 Windows Api에 대 한 플랫폼 간 파일 액세스 기능을 지 원하는 PCL 호환 NuGet – [Pclstorage](https://www.nuget.org/packages/PCLStorage/) 가 있습니다.

## <a name="network-operations"></a>네트워크 작업

대부분의 모바일 응용 프로그램에는 예를 들어 네트워킹 구성 요소로 제공 됩니다.

- 이미지, 비디오 및 오디오 (예: 다운로드 미리 보기, 사진, 음악)입니다.
- (예: 문서를 다운로드합니다. HTML, PDF)입니다.
- 사용자 데이터 (예: 사진 또는 텍스트)를 업로드 합니다.
- 웹 서비스 또는 제 3 자 (포함 하 여 SOAP, XML 또는 JSON) Api에 액세스합니다.

.NET Framework는 네트워크 리소스에 액세스 하기 위한 몇 가지 클래스 `HttpClient`, `WebClient`및 `HttpWebRequest`를 제공 합니다.

### <a name="httpclient"></a>HttpClient

`System.Net.Http` 네임 스페이스의 `HttpClient` 클래스는 Xamarin.ios, Xamarin Android 및 대부분의 Windows 플랫폼에서 사용할 수 있습니다. 이 API를 이식 가능한 클래스 라이브러리 (및 Windows Phone 8 Silverlight)로 가져오는 데 사용할 수 있는 [MICROSOFT HTTP 클라이언트 라이브러리 NuGet](https://www.nuget.org/packages/Microsoft.Net.Http/) 이 있습니다.

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get, "https://xamarin.com");
var response = await myClient.SendAsync(request);
```

### <a name="webclient"></a>WebClient

`WebClient` 클래스는 원격 서버에서 원격 데이터를 검색 하는 간단한 API를 제공 합니다.

Xamarin.ios 및 Xamarin이 동기 작업 (백그라운드 스레드에서 수행할 수 있음)을 지원 하더라도 유니버설 Windows 플랫폼 작업은 비동기적 이어야 *합니다* .

간단한 보조적 `WebClient` 작업에 대 한 코드는 다음과 같습니다.

```csharp
var webClient = new WebClient ();
webClient.DownloadStringCompleted += (sender, e) =>
{
    var resultString = e.Result;
    // do something with downloaded string, do UI interaction on main thread
};
webClient.Encoding = System.Text.Encoding.UTF8;
webClient.DownloadStringAsync (new Uri ("http://some-server.com/file.xml"));
```

 `WebClient`에는 이진 데이터를 검색 하는 `DownloadFileCompleted` 및 `DownloadFileAsync` 있습니다.

<a name="HttpWebRequest" />

### <a name="httpwebrequest"></a>HttpWebRequest

`HttpWebRequest`는 `WebClient` 보다 더 많은 사용자 지정 기능을 제공 하므로 더 많은 코드를 사용 해야 합니다.

간단한 동기 `HttpWebRequest` 작업에 대 한 코드는 다음과 같습니다.

```csharp
var request = HttpWebRequest.Create(@"http://some-server.com/file.xml ");
request.ContentType = "text/xml";
request.Method = "GET";
using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
{
    if (response.StatusCode != HttpStatusCode.OK)
        Console.WriteLine("Error fetching data. Server returned status code: {0}", response.StatusCode);
    using (StreamReader reader = new StreamReader(response.GetResponseStream()))
    {
        var content = reader.ReadToEnd();
        // do something with downloaded string, do UI interaction on main thread
    }
}
```

[웹 서비스 설명서](~/cross-platform/data-cloud/web-services/index.md)에는 예제가 있습니다.

 <a name="Reachability" />

### <a name="reachability"></a>연결 가능성

모바일 장치에서 빠른 Wi-fi 네트워크 조건의 다양 한 또는 4g 연결 저하 비치 및 느린 지 데이터 연결을 작동합니다. 이 인해 것이 네트워크를 사용할 수 있는지와, 어떤 유형의 네트워크는 경우 원격 서버에 연결 하기 전에 검색 하는 것이 좋습니다.

이러한 상황에서 모바일 앱을 소요 하는 동작은 다음과 같습니다.

- 네트워크를 사용할 수 없는 경우 사용자는 것이 좋습니다. 경우는 수동으로 사용 하지 않도록 설정한 것 (예: 비행기 모드 또는 Wi-fi 해제) 다음 문제를 해결할 수 있습니다.
- 응용 프로그램을 다르게 동작할 수 있습니다 3g 연결을 사용 하는 경우 (예를 들어 Apple 없도록 앱 3g 개 다운로드 20mb 보다 큰). 응용 프로그램이이 정보를 사용 하 여 과도 한 다운로드에 대 한 사용자에 게 알립니다 수 큰 파일을 검색할 때 시간입니다.
- 네트워크를 사용할 수 있는 경우에 것 다른 요청을 시작 하기 전에 대상 서버를 사용 하 여 연결을 확인 하는 것이 좋습니다. 앱의 네트워크 작업 시간 초과 반복적으로 방지 되 고도 사용자에 게 표시 되는 자세한 오류 메시지를 허용 됩니다.

## <a name="webservices"></a>WebServices

Xamarin.ios를 사용 하 여 REST, SOAP 및 WCF 끝점에 액세스 하는 [웹 서비스 작업](~/cross-platform/data-cloud/web-services/index.md)에 대 한 설명서를 참조 하세요. 웹 서비스 요청을 직접 만들 수 있습니다 하 고 개 훨씬 Azure, RestSharp, ServiceStack 등 이렇게 하려면 사용할 수 있는 라이브러리는 응답을 구문 분석 합니다. 기본 WCF 작업 Xamarin 앱에서 액세스할 수 있습니다.

### <a name="azure"></a>Azure

Microsoft Azure는 다양 한 데이터 저장소 및 동기화 및 푸시 알림을 포함 하 여 모바일 앱에 대 한 서비스를 제공 하는 클라우드 플랫폼입니다.

[Azure.microsoft.com](https://azure.microsoft.com/) 을 방문 하 여 무료로 사용해 보세요.

### <a name="restsharp"></a>RestSharp

RestSharp에 웹 서비스에 대 한 액세스를 간소화 하는 REST 클라이언트를 제공 하는 모바일 응용 프로그램에 포함 될 수 있는.NET 라이브러리입니다. 데이터를 요청 하 고 REST 응답을 구문 분석 하는 간단한 API를 제공 하 여 도움이 됩니다. RestSharp 유용할 수 있습니다.

[RestSharp 웹 사이트](http://restsharp.org/) 는 RestSharp를 사용 하 여 REST 클라이언트를 구현 하는 방법에 대 한 [설명서](https://github.com/restsharp/RestSharp/wiki) 를 포함 합니다.
RestSharp는 [github](https://github.com/restsharp/RestSharp/)에서 Xamarin.ios 및 xamarin Android 예제를 제공 합니다.

[웹 서비스 설명서](~/cross-platform/data-cloud/web-services/index.md)에는 xamarin.ios 코드 조각도 있습니다.

 <a name="ServiceStack" />

### <a name="servicestack"></a>ServiceStack

RestSharp, 달리 ServiceStack는 모두는 서버 쪽 솔루션 해당 서비스에 액세스 하는 모바일 응용 프로그램에서 구현 될 수 있는 클라이언트 라이브러리 뿐만 아니라 웹 서비스 호스트입니다.

[Servicestack 웹 사이트](http://servicestack.net/) 는 프로젝트의 목적과 문서 및 코드 샘플에 대 한 링크를 설명 합니다. 예제에 액세스할 수 있는 다양 한 클라이언트 쪽 응용 프로그램 뿐만 아니라 웹 서비스의 완전 한 서버 쪽 구현을 포함 됩니다.

### <a name="wcf"></a>WCF

Xamarin 도구는 일부 Windows Communication Foundation (WCF) 서비스를 사용할 수 있습니다. 일반적으로 Xamarin Silverlight 런타임과 함께 제공 되는 WCF의 동일한 클라이언트 쪽 하위 집합을 지원 합니다. 여기에는 `BasicHttpBinding`를 사용 하 여 HTTP 전송 프로토콜을 통해 텍스트 인코딩된 SOAP 메시지의 가장 일반적인 인코딩 및 프로토콜 구현이 포함 됩니다.

크기 및 WCF 프레임 워크의 복잡성으로 인해 Xamarin 클라이언트 하위 도메인에서 지원 되는 범위 밖에 서 속하는 현재 및 향후 서비스 구현이 있을 수 있습니다. 또한 WCF 지원만 프록시를 생성할 Windows 환경에서 사용할 수 있는 도구의 사용을 해야 합니다.

 <a name="Threading" />

## <a name="threading"></a>스레딩

응용 프로그램 응답성은 모바일 응용 프로그램에 대 한 중요 한 – 사용자가 예상 응용 프로그램을 로드 하 고 신속 하 게 수행 합니다. 사용자 입력을 수락 하는 중지 하도록 지정 하는 응용 프로그램에서 반복적인 충돌이 발생, 네트워크 요청과 같은 장기 실행 차단 호출 또는 느린 로컬 작업 (예: 파일 압축을 푸는)를 사용 하 여 UI 스레드를 연결할 필요가 반드시 표시 되는 '고정된' 화면입니다. 특히 시작 프로세스에는 장기 실행 작업 없어야 합니다.-모든 모바일 플랫폼을 로드 하는 데 오랜를 사용 하는 앱을 종료 합니다.

즉, '진행률 표시기' 또는 '사용'이 고, 그렇지 되는 UI를 표시 하려면 빠른 및 비동기 작업 백그라운드 작업을 수행 하려면 사용자 인터페이스 구현 해야 합니다. 백그라운드 작업 실행 완료 될 때 또는 백그라운드 작업 요구 사항 진행률을 나타내는 주 스레드로 다시 통신 하는 방법이 즉 스레드를 사용을 해야 합니다.

 <a name="Parallel_Task_Library" />

### <a name="parallel-task-library"></a>병렬 작업 라이브러리

병렬 작업 라이브러리를 사용 하 여 만든 작업이 비동기적으로 실행 하 고 해당 사용자 인터페이스를 차단 하지 않고 장기 실행 작업을 트리거하는 데 매우 유용 쉽게 호출 스레드에서 반환 수 있습니다.

간단한 병렬 작업을이 같습니다.

```csharp
using System.Threading.Tasks;
void MainThreadMethod ()
{
    Task.Factory.StartNew (() => wc.DownloadString ("http://...")).ContinueWith (
        t => label.Text = t.Result, TaskScheduler.FromCurrentSynchronizationContext()
    );
}
```

키는 `TaskScheduler.FromCurrentSynchronizationContext()` (여기서는 `MainThreadMethod`를 실행 하는 주 스레드) 메서드를 호출 하는 스레드의 SynchronizationContext를 다시 사용 하 여 해당 스레드에 대 한 호출을 다시 마샬링하는 방법으로 사용 됩니다. 즉, UI 스레드에서 메서드가 호출 되 면 UI 스레드에서 `ContinueWith` 작업을 다시 실행 합니다.

코드를 다른 스레드에서 작업의 시작을 UI 스레드에 대 한 참조를 만들려면 다음 패턴을 사용 하 고 태스크를 다시 호출할 여전히 수 있습니다.

```csharp
static Context uiContext = TaskScheduler.FromCurrentSynchronizationContext();
```

 <a name="Invoking_on_the_UI_Thread" />

### <a name="invoking-on-the-ui-thread"></a>UI 스레드에서 호출

병렬 작업 라이브러리를 활용 하지 않습니다 하는 코드에 대 한 각 플랫폼에 UI 스레드로 다시 마샬링 작업에 대 한 자체 구문이 있습니다.

- **iOS** – `owner.BeginInvokeOnMainThread(new NSAction(action))`
- **Android** – `owner.RunOnUiThread(action)`
- **Xamarin.ios** – `Device.BeginInvokeOnMainThread(action)`
- **Windows** – `Deployment.Current.Dispatcher.BeginInvoke(action)`

IOS 및 Android 구문 즉,이 개체에 필요한 UI 스레드에서 콜백 메서드를 전달 해야 하는 코드를 사용할 수는 '컨텍스트' 클래스에 필요 합니다.

공유 코드에서 UI 스레드를 호출 하려면 [IDispatchOnUIThread 예제](https://www.slideshare.net/follesoe/cross-platform-mobile-apps-using-net) ( [@follesoe](https://twitter.com/follesoe))를 따르세요. 및 프로그램을 공유 코드의 `IDispatchOnUIThread` 인터페이스로 선언 하 고 다음과 같이 플랫폼별 클래스를 구현 합니다.

```csharp
// program to the interface in shared code
public interface IDispatchOnUIThread {
    void Invoke (Action action);
}
// iOS
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly NSObject owner;
    public DispatchAdapter (NSObject owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.BeginInvokeOnMainThread(new NSAction(action));
    }
}
// Android
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly Activity owner;
    public DispatchAdapter (Activity owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.RunOnUiThread(action);
    }
}
// WP7
public class DispatchAdapter : IDispatchOnUIThread {
    public void Invoke (Action action) {
        Deployment.Current.Dispatcher.BeginInvoke(action);
    }
}
```

Xamarin Forms 개발자는 공용 코드 (공유 프로젝트 또는 PCL)에서 [`Device.BeginInvokeOnMainThread`](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads) 를 사용 해야 합니다.

 <a name="Platform_and_Device_Capabilities_and_Degradation" />

## <a name="platform-and-device-capabilities-and-degradation"></a>플랫폼 및 장치 기능 및 성능 저하

더 다양 한 기능을 사용 하 여 처리 하는 특정 예제는 플랫폼 기능 설명서에 제공 됩니다. 검색 다양 한 기능 및 앱의 전체 잠재력을 수행할 수 없습니다 하는 경우에는 뛰어난 사용자 환경을 제공 하는 응용 프로그램을 정상적으로 저하 하는 방법을 다룹니다.
