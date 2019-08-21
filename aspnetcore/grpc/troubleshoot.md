---
title: .NET Core での gRPC のトラブルシューティング
author: jamesnk
description: .NET Core で gRPC を使用しているときのエラーのトラブルシューティングを行います。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 08/12/2019
uid: grpc/troubleshoot
ms.openlocfilehash: ad74bfa57d2dde316734d55d86075f463e78ee56
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029035"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="751ca-103">.NET Core での gRPC のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="751ca-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="751ca-104">[James のニュートン-キング](https://twitter.com/jamesnk)別</span><span class="sxs-lookup"><span data-stu-id="751ca-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="751ca-105">クライアントとサービスの SSL/TLS の構成が一致しません</span><span class="sxs-lookup"><span data-stu-id="751ca-105">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="751ca-106">GRPC テンプレートとサンプルでは、[トランスポート層セキュリティ (TLS)](https://tools.ietf.org/html/rfc5246)を使用して、既定で grpc サービスをセキュリティで保護しています。</span><span class="sxs-lookup"><span data-stu-id="751ca-106">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="751ca-107">gRPC クライアントはセキュリティで保護された接続を使用して、セキュリティで保護された gRPC サービスを正常に呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="751ca-107">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="751ca-108">アプリの起動時に書き込まれたログで、ASP.NET Core gRPC サービスが TLS を使用していることを確認できます。</span><span class="sxs-lookup"><span data-stu-id="751ca-108">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="751ca-109">サービスは、HTTPS エンドポイントでリッスンします。</span><span class="sxs-lookup"><span data-stu-id="751ca-109">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="751ca-110">.Net Core クライアントは、サーバー `https`アドレスでを使用して、セキュリティで保護された接続で呼び出しを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="751ca-110">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    var httpClient = new HttpClient();
    // The port number(5001) must match the port of the gRPC server.
    httpClient.BaseAddress = new Uri("https://localhost:5001");
    var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
}
```

<span data-ttu-id="751ca-111">すべての gRPC クライアント実装は TLS をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="751ca-111">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="751ca-112">他の言語の gRPC クライアントには、通常、 `SslCredentials`で構成されたチャネルが必要です。</span><span class="sxs-lookup"><span data-stu-id="751ca-112">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="751ca-113">`SslCredentials`クライアントが使用する証明書を指定します。この証明書は、セキュリティで保護されていない資格情報の代わりに使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="751ca-113">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="751ca-114">異なる gRPC クライアント実装で TLS を使用するように構成する例については、「 [Grpc 認証](https://www.grpc.io/docs/guides/auth/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="751ca-114">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="751ca-115">.NET Core クライアントを使用してセキュリティで保護される gRPC サービスを呼び出す</span><span class="sxs-lookup"><span data-stu-id="751ca-115">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="751ca-116">.NET Core クライアントでセキュリティで保護されていない gRPC サービスを呼び出すには、追加の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="751ca-116">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="751ca-117">Grpc クライアントでは、 `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport`スイッチをに`true`設定し、サーバーアドレスでを使用`http`する必要があります。</span><span class="sxs-lookup"><span data-stu-id="751ca-117">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

var httpClient = new HttpClient();
// The port number(5000) must match the port of the gRPC server.
httpClient.BaseAddress = new Uri("http://localhost:5000");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="751ca-118">MacOS で gRPC アプリを開始できません ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="751ca-118">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="751ca-119">Kestrel では、macOS での TLS と Windows 7 などの古いバージョンの HTTP/2 はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="751ca-119">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="751ca-120">ASP.NET Core gRPC テンプレートとサンプルでは、既定で TLS が使用されます。</span><span class="sxs-lookup"><span data-stu-id="751ca-120">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="751ca-121">GRPC サーバーを起動しようとすると、次のエラーメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="751ca-121">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="751ca-122">IPv4 ループバックインターフェイスの https://localhost:5001 にバインドできません。' HTTP/2 over TLS は、ALPN サポートがないため、macOS ではサポートされていません。 '。</span><span class="sxs-lookup"><span data-stu-id="751ca-122">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="751ca-123">この問題を回避するには、TLS を使用*せず*に HTTP/2 を使用するように Kestrel と grpc クライアントを構成します。</span><span class="sxs-lookup"><span data-stu-id="751ca-123">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="751ca-124">これは開発時にのみ実行してください。</span><span class="sxs-lookup"><span data-stu-id="751ca-124">You should only do this during development.</span></span> <span data-ttu-id="751ca-125">TLS を使用しないと、gRPC メッセージが暗号化なしで送信されます。</span><span class="sxs-lookup"><span data-stu-id="751ca-125">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="751ca-126">Kestrel では、 *Program.cs*で TLS を使用せずに HTTP/2 エンドポイントを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="751ca-126">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="751ca-127">GRPC クライアントは、TLS を使用しないように構成する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="751ca-127">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="751ca-128">詳細については、「 [.Net Core クライアントを使用した安全でない gRPC サービスの呼び出し](#call-insecure-grpc-services-with-net-core-client)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="751ca-128">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="751ca-129">TLS を使用しない HTTP/2 は、アプリの開発中にのみ使用してください。</span><span class="sxs-lookup"><span data-stu-id="751ca-129">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="751ca-130">運用アプリでは、常にトランスポートセキュリティを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="751ca-130">Production apps should always use transport security.</span></span> <span data-ttu-id="751ca-131">詳細については、「 [gRPC のセキュリティに関する考慮事項 ASP.NET Core](xref:grpc/security#transport-security)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="751ca-131">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="751ca-132">grpc C#資産は、  *\*proto*ファイルから生成されたコードではありません</span><span class="sxs-lookup"><span data-stu-id="751ca-132">gRPC C# assets are not code generated from *\*.proto* files</span></span>

<span data-ttu-id="751ca-133">具象クライアントとサービス基底クラスの gRPC コード生成には、protobuf ファイルとツールをプロジェクトから参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="751ca-133">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="751ca-134">次のものを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="751ca-134">You must include:</span></span>

* <span data-ttu-id="751ca-135">`<Protobuf>`項目グループで使用する*プロトコルファイル。*</span><span class="sxs-lookup"><span data-stu-id="751ca-135">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="751ca-136">[インポートさ*れたプロトコル*ファイル](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions)は、プロジェクトによって参照される必要があります。</span><span class="sxs-lookup"><span data-stu-id="751ca-136">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="751ca-137">GRPC ツールパッケージ[grpc](https://www.nuget.org/packages/Grpc.Tools/)に対するパッケージリファレンス。</span><span class="sxs-lookup"><span data-stu-id="751ca-137">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="751ca-138">GRPC C#アセットの生成の詳細について<xref:grpc/basics>は、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="751ca-138">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="751ca-139">既定では、 `<Protobuf>`参照によって具象クライアントとサービス基本クラスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="751ca-139">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="751ca-140">参照要素の属性`GrpcServices`は、資産の生成をC#制限するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="751ca-140">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="751ca-141">有効`GrpcServices`なオプションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="751ca-141">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="751ca-142">`Both`(存在しない場合の既定値)</span><span class="sxs-lookup"><span data-stu-id="751ca-142">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="751ca-143">GRPC サービスをホストしている ASP.NET Core web アプリには、生成されたサービス基本クラスのみが必要です。</span><span class="sxs-lookup"><span data-stu-id="751ca-143">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="751ca-144">GRPC 呼び出しを行う gRPC クライアントアプリでは、具象クライアントのみが生成されます。</span><span class="sxs-lookup"><span data-stu-id="751ca-144">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```