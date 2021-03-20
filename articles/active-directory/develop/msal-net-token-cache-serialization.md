---
title: Serializace mezipaměti tokenů (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o serializaci a serializaci mezipaměti s tokeny pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 60ce3d32ffa20fc9117890528eac053d1af9fdf2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99583904"
---
# <a name="token-cache-serialization-in-msalnet"></a>Serializace mezipaměti tokenů v MSAL.NET
Po [získání tokenu](msal-acquire-cache-tokens.md)je uložen do mezipaměti v rámci knihovny Microsoft Authentication Library (MSAL).  Kód aplikace by se měl pokusit získat token z mezipaměti před získáním tokenu jinou metodou.  Tento článek popisuje výchozí a vlastní serializaci mezipaměti tokenů v MSAL.NET.

Tento článek je určen pro MSAL.NET 3. x. Pokud vás zajímá MSAL.NET 2. x, přečtěte si téma [serializace mezipaměti tokenů v MSAL.NET 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>Výchozí serializace pro mobilní platformy

V MSAL.NET je ve výchozím nastavení poskytována mezipaměť tokenů v paměti. Ve výchozím nastavení je k dispozici serializace pro platformy, kde je k dispozici zabezpečené úložiště pro uživatele jako součást platformy. Jedná se o případ Univerzální platforma Windows (UWP), Xamarin. iOS a Xamarin. Android.

> [!Note]
> Když migrujete projekt Xamarin. Android z MSAL.NET 1. x do MSAL.NET 3. x, můžete chtít přidat `android:allowBackup="false"` do projektu a vyhnout se tak starým tokenům uloženým v mezipaměti, když nasazení sady Visual Studio aktivují obnovení místního úložiště. Viz [#659 problému](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Vlastní serializace pro desktopové aplikace pro Windows a webové aplikace/webová rozhraní API

Nezapomeňte, že vlastní serializace není k dispozici na mobilních platformách (UWP, Xamarin. iOS a Xamarin. Android). MSAL už pro tyto platformy definuje zabezpečený a výkonné mechanizmus serializace. Aplikace .NET Desktop a .NET Core ale mají proměnlivé architektury a MSAL nemůžou implementovat mechanizmus serializace pro obecné účely. Například weby se můžou rozhodnout ukládat tokeny do mezipaměti Redis nebo desktopové aplikace ukládají tokeny v zašifrovaném souboru. Takže serializace není poskytována předem. Chcete-li mít aplikaci trvalé mezipaměti tokenů v rozhraní .NET Desktop nebo .NET Core, přizpůsobte serializaci.

V serializaci mezipaměti tokenu se používají následující třídy a rozhraní:

- `ITokenCache`, který definuje události pro přihlášení k odběru požadavků na serializaci mezipaměti tokenu a také metody pro serializaci nebo deserializaci mezipaměti v různých formátech (ADAL v 3.0, MSAL 2. x a MSAL 3. x = ADAL v 5.0).
- `TokenCacheCallback` je zpětné volání předané do událostí, aby bylo možné zpracovat serializaci. Budou volány s argumenty typu `TokenCacheNotificationArgs` .
- `TokenCacheNotificationArgs` poskytuje pouze `ClientId` aplikaci a odkaz na uživatele, pro který je token k dispozici.

  ![Diagram tříd](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET vytvoří mezipaměť tokenů za vás a poskytne `IToken` mezipaměť při volání `UserTokenCache` vlastností aplikace a `AppTokenCache` . Nechcete implementovat rozhraní sami. Vaše zodpovědnost při implementaci serializace mezipaměti vlastního tokenu je následující:
> - Reagovat na `BeforeAccess` a `AfterAccess` "události" (nebo jejich asynchronní charakter). `BeforeAccess`Delegát zodpovídá za deserializaci mezipaměti, zatímco `AfterAccess` jedna je zodpovědná za serializaci mezipaměti.
> - Součást těchto událostí ukládá nebo načítá objekty blob, které jsou předány argumentem události do libovolného úložiště, které chcete.

Strategie se liší v závislosti na tom, jestli píšete serializaci mezipaměti tokenů pro [veřejnou klientskou aplikaci](msal-client-applications.md) (Desktop) nebo [důvěrnou klientskou aplikaci](msal-client-applications.md)(Web App/Web API, démon App).

### <a name="token-cache-for-a-public-client"></a>Mezipaměť tokenů pro veřejného klienta

Vzhledem k tomu, že MSAL.NET v2. x máte několik možností, jak serializovat mezipaměť tokenů veřejného klienta. Mezipaměť můžete serializovat jenom ve formátu MSAL.NET (sjednocené mezipaměť formátu je společná napříč MSAL a platformami).  Můžete také podporovat serializaci mezipaměti na [starší](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) verzi knihovny ADAL v3.

Přizpůsobení serializace mezipaměti tokenů pro sdílení stavu jednotného přihlašování mezi ADAL.NET 3. x, ADAL.NET 5. x a MSAL.NET je vysvětleno v rámci následující ukázky: [Active-Directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> Formát mezipaměti tokenu MSAL.NET 1.1.4-Preview již není podporován v MSAL 2. x. Pokud máte aplikace s využitím MSAL.NET 1. x, budou se uživatelé muset znovu přihlásit. Alternativně je podporována migrace z knihovny ADAL 4. x (a 3. x).

#### <a name="simple-token-cache-serialization-msal-only"></a>Jednoduchá serializace mezipaměti tokenů (jenom MSAL)

Níže je uveden příklad implementace Naive vlastní serializace mezipaměti tokenů pro aplikace klasické pracovní plochy. V tomto případě je mezipaměť tokenu uživatele souborem ve stejné složce jako aplikace.

Po sestavení aplikace povolte serializaci voláním `TokenCacheHelper.EnableSerialization()` metody a předáním aplikace `UserTokenCache` .

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

`TokenCacheHelper`Pomocná třída je definována takto:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache. Note that this could be something different for instance for MSIX applications:
  /// private static readonly string CacheFilePath =
  /// $"{Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData)}\{AppName}\msalcache.bin";
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Serializátor založený na souborové mezipaměti tokenu kvality produktu pro veřejné klientské aplikace (pro desktopové aplikace běžící na Windows, Mac a Linux) je k dispozici v open source knihovně [Microsoft. identity. Client. Extensions. Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) . Můžete ji zahrnout do svých aplikací z následujícího balíčku NuGet: [Microsoft. identity. Client. Extensions. Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Serializace mezipaměti s duálním tokenem (MSAL Unified cache a ADAL V3)

Pokud chcete implementovat serializaci mezipaměti tokenů současně s formátem Unified cache (společný pro ADAL.NET 4. x, MSAL.NET 2. x a další MSALs stejné generace nebo starších verzí, na stejné platformě), podívejte se na následující kód:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Tentokrát pomocná třída definovaná jako:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Enables the serialization of the token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache tokenCache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

### <a name="token-cache-for-a-web-app-confidential-client-application"></a>Mezipaměť tokenů pro webovou aplikaci (důvěrná klientská aplikace)

V rámci webových aplikací nebo webových rozhraní API může mezipaměť využít relaci, Redis Cache nebo databázi. V Web Apps nebo webových rozhraních API byste měli uchovávat jednu mezipaměť tokenů na jeden účet. 

Pro webové aplikace by měla být mezipaměť tokenů nastavená ID účtu.

Pro webová rozhraní API by měl být účet nastaven pomocí hash tokenu, který se používá k volání rozhraní API.

MSAL.NET poskytuje serializaci mezipaměti vlastního tokenu v .NET Framework a podplatformách .NET Core. Události jsou vyvolány, když je mezipaměť k dispozici, aplikace mohou zvolit, zda má být mezipaměť serializována nebo deserializována. V tajných klientských aplikacích, které zpracovávají uživatele (webové aplikace, které přihlásily uživatele, volají webová rozhraní API a webová rozhraní API volající webová rozhraní API) může existovat mnoho uživatelů a uživatelé se zpracovávají paralelně. Z důvodu zabezpečení a výkonu doporučujeme, abyste měli v úmyslu serializovat jednu mezipaměť na uživatele. Události serializace počítají klíč mezipaměti v závislosti na identitě zpracovaného uživatele a serializaci/deserializaci mezipaměti tokenů pro daného uživatele.

Knihovna [Microsoft. identity. Web](https://github.com/AzureAD/microsoft-identity-web) obsahuje verzi Preview balíčku NuGet [Microsoft. identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) obsahující serializaci mezipaměti tokenů:

| Metoda rozšíření | Microsoft. identity. Web – dílčí obor názvů | Description  |
| ---------------- | --------- | ------------ |
| `AddInMemoryTokenCaches` | `TokenCacheProviders.InMemory` | V serializaci mezipaměti tokenů paměti. Tato implementace je skvělé v ukázkách. Je to také dobré v produkčních aplikacích, pokud nezáleží na tom, jestli se při restartování webové aplikace ztratí mezipaměť tokenu. `AddInMemoryTokenCaches` přebírá volitelný parametr typu `MsalMemoryTokenCacheOptions` , který umožňuje zadat dobu, po jejímž uplynutí vyprší platnost položky mezipaměti, pokud se nepoužije.
| `AddSessionTokenCaches` | `TokenCacheProviders.Session` | Mezipaměť tokenů je svázána s uživatelskou relací. Tato možnost není ideální, pokud token ID obsahuje mnoho deklarací identity, protože soubor cookie by byl příliš velký.
| `AddDistributedTokenCaches` | `TokenCacheProviders.Distributed` | Mezipaměť tokenu je adaptér s `IDistributedCache` implementací ASP.NET Core, takže můžete vybrat mezi distribuovanou mezipamětí, mezipamětí Redis, distribuovaným NCacheem nebo mezipamětí SQL Server. Podrobnosti o `IDistributedCache` implementacích naleznete v tématu https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache .

Tady je příklad použití mezipaměti v paměti v metodě [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) [spouštěcí](/aspnet/core/fundamentals/startup) třídy v aplikaci ASP.NET Core:

```C#
// or use a distributed Token Cache by adding
    services.AddSignIn(Configuration);
    services.AddWebAppCallsProtectedWebApi(Configuration, new string[] { scopesToRequest })
            .AddInMemoryTokenCaches();
```

Příklady možných distribuovaných mezipamětí:

```C#
// or use a distributed Token Cache by adding
    services.AddSignIn(Configuration);
    services.AddWebAppCallsProtectedWebApi(Configuration, new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// and then choose your implementation

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache()

// Or a Redis cache
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Jejich použití je vybrané v [kurzu ASP.NET Core webové aplikace](/aspnet/core/tutorials/first-mvc-app/) v [mezipaměti tokenu fáze 2-2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache).

## <a name="next-steps"></a>Další kroky

Následující ukázky ilustrují serializaci mezipaměti tokenů.

| Ukázka | Platforma | Description|
| ------ | -------- | ----------- |
|[Active-Directory-dotnet-Desktop-MSGraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Plocha (WPF) | Aplikace Windows Desktop .NET (WPF), která volá rozhraní Microsoft Graph API. ![Diagram znázorňuje topologii s desktopovou aplikací W P TodoListClient Flowing to Azure A D tím, že se token interaktivně a Microsoft Graph.](media/msal-net-token-cache-serialization/topology.png)|
|[Active-Directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Plocha (konzola) | Sada řešení sady Visual Studio, která ilustruje migraci aplikací Azure AD v 1.0 (pomocí ADAL.NET) do aplikací Microsoft Identity Platform (pomocí MSAL.NET). Zejména viz [migrace mezipaměti tokenů](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|
