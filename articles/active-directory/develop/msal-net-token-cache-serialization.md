---
title: Serializace mezipaměti tokenů v knihovně ověřování Microsoft pro .NET
titleSuffix: Microsoft identity platform
description: Přečtěte si o serializaci a zaserializaci mezipaměti tokenů pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cae1a755b9f79ce2fd5084653f7b3c177f29832
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802724"
---
# <a name="token-cache-serialization-in-msalnet"></a>Serializace mezipaměti tokenů v MSAL.NET
Po [získání tokenu](msal-acquire-cache-tokens.md)je uložen do mezipaměti v rámci knihovny Microsoft Authentication Library (MSAL).  Kód aplikace by se měl pokusit získat token z mezipaměti před získáním tokenu jinou metodou.  Tento článek popisuje výchozí a vlastní serializaci mezipaměti tokenů v MSAL.NET.

Tento článek je určen pro MSAL.NET 3. x. Pokud vás zajímá MSAL.NET 2. x, přečtěte si téma [serializace mezipaměti tokenů v MSAL.NET 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>Výchozí serializace pro mobilní platformy

V MSAL.NET je ve výchozím nastavení poskytována mezipaměť tokenů v paměti. Ve výchozím nastavení je k dispozici serializace pro platformy, kde je k dispozici zabezpečené úložiště pro uživatele jako součást platformy. Jedná se o případ Univerzální platforma Windows (UWP), Xamarin. iOS a Xamarin. Android.

> [!Note]
> Když migrujete projekt Xamarin. Android z MSAL.NET 1. x do MSAL.NET 3. x, můžete chtít přidat `android:allowBackup="false"` do projektu, abyste předešli tomu, že se staré tokeny v mezipaměti nebudou vracet, když nasazení sady Visual Studio aktivuje obnovení místního úložiště. Viz [#659 problému](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Vlastní serializace pro desktopové aplikace pro Windows a webové aplikace/webová rozhraní API

Nezapomeňte, že vlastní serializace není k dispozici na mobilních platformách (UWP, Xamarin. iOS a Xamarin. Android). MSAL už pro tyto platformy definuje zabezpečený a výkonné mechanizmus serializace. Aplikace .NET Desktop a .NET Core ale mají proměnlivé architektury a MSAL nemůžou implementovat mechanizmus serializace pro obecné účely. Například weby se můžou rozhodnout ukládat tokeny do mezipaměti Redis nebo desktopové aplikace ukládají tokeny v zašifrovaném souboru. Takže serializace není poskytována předem. Chcete-li mít aplikaci trvalé mezipaměti tokenů v rozhraní .NET Desktop nebo .NET Core, je nutné tuto serializaci přizpůsobit.

V serializaci mezipaměti tokenu se používají následující třídy a rozhraní:

- `ITokenCache`, který definuje události pro přihlášení k odběru požadavků na serializaci mezipaměti tokenu a metody pro serializaci nebo deserializaci mezipaměti v různých formátech (ADAL v 3.0, MSAL 2. x a MSAL 3. x = ADAL v 5.0).
- `TokenCacheCallback` je zpětné volání předávané do událostí, aby bylo možné zpracovat serializaci. Budou volány s argumenty typu `TokenCacheNotificationArgs`.
- `TokenCacheNotificationArgs` poskytuje pouze `ClientId` aplikace a odkaz na uživatele, pro který je token k dispozici.

  ![Diagram tříd](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET vytvoří mezipaměť tokenů za vás a poskytne vám `IToken` cache při volání vlastností `UserTokenCache` a `AppTokenCache` aplikace. Nechcete implementovat rozhraní sami. Vaše zodpovědnost při implementaci serializace mezipaměti vlastního tokenu je následující:
> - Reagují na `BeforeAccess` a `AfterAccess` "události" (nebo jejich asynchronní charakter). `BeforeAccess` delegát zodpovídá za deserializaci mezipaměti, zatímco `AfterAccess` jedna je zodpovědná za serializaci mezipaměti.
> - Součást těchto událostí ukládá nebo načítá objekty blob, které jsou předány argumentem události do libovolného úložiště, které chcete.

Strategie se liší v závislosti na tom, jestli píšete serializaci mezipaměti tokenů pro [veřejnou klientskou aplikaci](msal-client-applications.md) (Desktop) nebo [důvěrnou klientskou aplikaci](msal-client-applications.md)(Web App/Web API, démon App).

### <a name="token-cache-for-a-public-client"></a>Mezipaměť tokenů pro veřejného klienta 

Vzhledem k tomu, že MSAL.NET v2. x máte několik možností, jak serializovat mezipaměť tokenů veřejného klienta. Mezipaměť můžete serializovat jenom ve formátu MSAL.NET (sjednocené mezipaměť formátu je společná napříč MSAL a platformami).  Můžete také podporovat serializaci mezipaměti na [starší](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) verzi knihovny ADAL v3.

Přizpůsobení serializace mezipaměti tokenů pro sdílení stavu jednotného přihlašování mezi ADAL.NET 3. x, ADAL.NET 5. x a MSAL.NET je vysvětleno v rámci následující ukázky: [Active-Directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> Formát mezipaměti tokenu MSAL.NET 1.1.4-Preview již není podporován v MSAL 2. x. Pokud máte aplikace s využitím MSAL.NET 1. x, budou se uživatelé muset znovu přihlásit. Alternativně je podporována migrace z knihovny ADAL 4. x (a 3. x).

#### <a name="simple-token-cache-serialization-msal-only"></a>Jednoduchá serializace mezipaměti tokenů (jenom MSAL)

Níže je uveden příklad implementace Naive vlastní serializace mezipaměti tokenů pro aplikace klasické pracovní plochy. V tomto případě je mezipaměť tokenu uživatele souborem ve stejné složce jako aplikace.

Po sestavení aplikace povolte serializaci voláním metody `TokenCacheHelper.EnableSerialization()` a předáním `UserTokenCache`aplikace.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Pomocná třída `TokenCacheHelper` je definována takto:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
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

Náhled serializátoru založený na souboru mezipaměti s tokeny kvality produktu pro veřejné klientské aplikace (pro desktopové aplikace běžící v systémech Windows, Mac a Linux) je k dispozici v open source knihovně [Microsoft. identity. Client. Extensions. Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) . Můžete ji zahrnout do svých aplikací z následujícího balíčku NuGet: [Microsoft. identity. Client. Extensions. Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

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

V případě webových aplikací nebo webových rozhraní API může mezipaměť využít relaci, Redis Cache nebo databázi.

Ve webových aplikacích nebo webových rozhraních API ponechte jednu mezipaměť tokenů na účet.  Pro webové aplikace by měla být mezipaměť tokenů nastavená ID účtu.  Pro webová rozhraní API by měl být účet nastaven pomocí hash tokenu, který se používá k volání rozhraní API. MSAL.NET poskytuje serializaci mezipaměti vlastního tokenu v .NET Framework a podplatformách .NET Core. Události jsou vyvolány, když je mezipaměť k dispozici, aplikace mohou zvolit, zda má být mezipaměť serializována nebo deserializována. V tajných klientských aplikacích, které zpracovávají uživatele (webové aplikace, které přihlásily uživatele, volají webová rozhraní API a webová rozhraní API volající webová rozhraní API) může existovat mnoho uživatelů a uživatelé se zpracovávají paralelně. Z důvodu zabezpečení a výkonu doporučujeme, abyste měli v úmyslu serializovat jednu mezipaměť na uživatele. Události serializace počítají klíč mezipaměti na základě identity zpracovávaného uživatele a serializace/deserializace mezipaměti tokenu pro tohoto uživatele.

Příklady použití mezipamětí tokenů pro webové aplikace a webová rozhraní API jsou k dispozici v [kurzu ASP.NET Core webové aplikace](https://ms-identity-aspnetcore-webapp-tutorial) v [mezipaměti tokenu fáze 2-2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). U implementací se podívejte na složku [TokenCacheProviders](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/Microsoft.Identity.Web/TokenCacheProviders) v knihovně [Microsoft-Authentication-for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) (ve složce [Microsoft. identity. Client. Extensions. Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) . 

## <a name="next-steps"></a>Další kroky
Následující ukázky ilustrují serializaci mezipaměti tokenů.

| Ukázka | Platforma | Popis|
| ------ | -------- | ----------- |
|[Active-Directory-dotnet-Desktop-MSGraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Plocha (WPF) | Aplikace Windows Desktop .NET (WPF), která volá rozhraní Microsoft Graph API. ![Topologie](media/msal-net-token-cache-serialization/topology.png)|
|[Active-Directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Plocha (konzola) | Sada řešení pro Visual Studio, která ilustruje migraci aplikací Azure AD v 1.0 (pomocí ADAL.NET) do aplikací Azure AD v 2.0, ale také pojmenovaných aplikací (pomocí MSAL.NET), konkrétně při [migraci mezipaměti tokenů](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|
