---
title: Token mezipaměti serializaci v knihovna Microsoft Authentication Library pro .NET | Azure
description: Další informace o serializace a serializace zákazníka mezipaměť tokenu pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4a4c4ca1925a501b10cb86a2cf60646af1e5b57
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544250"
---
# <a name="token-cache-serialization-in-msalnet"></a>Serializace mezipaměť tokenu v MSAL.NET
Po [získat token](msal-acquire-cache-tokens.md), se uloží do mezipaměti Microsoft Authentication Library (MSAL).  Kód aplikace by měl pokusit získat token z mezipaměti před získávání tokenu jinou metodou.  Tento článek popisuje výchozí a vlastní serializace mezipaměti MSAL.NET na token.

Tento článek je určený pro MSAL.NET 3.x. Pokud vás zajímá MSAL.NET 2.x, naleznete v tématu [Token serializace mezipaměti v MSAL.NET 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>Výchozí serializace pro mobilní platformy

V MSAL.NET ve výchozím nastavení poskytuje mezipaměť tokenu v paměti. Serializace je k dispozici ve výchozím nastavení pro platformy, kde zabezpečeného úložiště je k dispozici pro uživatele v rámci platformy. To platí pro univerzální platformu Windows (UPW), Xamarin.iOS a Xamarin.Android.

> [!Note]
> Když migrujete ze MSAL.NET projektu Xamarin.Android 1.x do MSAL.NET 3.x, můžete chtít přidat `android:allowBackup="false"` do vašeho projektu, aby se zabránilo staré s mezipamětí tokenů z zpět při nasazení sady Visual Studio spustit obnovení z místního úložiště. Zobrazit [vydat #659](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Vlastní serializace pro aplikace klasické pracovní plochy Windows a webové aplikace/webové rozhraní API

Mějte na paměti, vlastní serializace není k dispozici na mobilní platformy (UPW, Xamarin.iOS a Xamarin.Android). Knihovna MSAL je již definováno zabezpečené a výkonné mechanismus serializace pro tyto platformy. .NET desktop a aplikace .NET Core, ale mají různé architektury a MSAL nemůže implementovat mechanismus serializace pro obecné účely. Například webové stránky můžete ukládat tokeny v mezipaměti redis cache nebo tokeny úložiště aplikací klasické pracovní plochy v šifrovaném souboru. Takže serializace není k dispozici out-of-the-box. Pokud chcete, aby trvalou mezipaměť tokenu aplikace v .NET desktop nebo .NET Core, budete muset přizpůsobit serializace.

Následující třídy a rozhraní se používají v serializaci mezipaměť tokenů:

- `ITokenCache`, který definuje události k odběru mezipaměť tokenu serializaci požadavků, jakož i metody pro serializovat nebo deserializovat do mezipaměti v různých formátech (ADAL v3.0, MSAL 2.x a MSAL 3.x = ADAL v5.0).
- `TokenCacheCallback` zpětné volání předána události tak, aby bylo možné zpracovat serializace. Volaná s argumenty typu `TokenCacheNotificationArgs`.
- `TokenCacheNotificationArgs` poskytuje pouze `ClientId` aplikace a odkaz na uživatele, pro kterou token, který je k dispozici.

  ![Diagram tříd](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET pro vás vytvoří token mezipaměti a nabízí `IToken` ukládat do mezipaměti při volání aplikace `GetUserTokenCache` a `GetAppTokenCache` metody. Není by měl implementovat rozhraní. Vaše odpovědnosti, Pokud implementujete vlastní mezipaměť tokenu serializace, je:
> - Reakce na ně `BeforeAccess` a `AfterAccess` "události". `BeforeAccess` Delegáta odpovídá k deserializaci do mezipaměti, že `AfterAccess` jeden je zodpovědná za serializaci do mezipaměti.
> - Součástí těchto událostí uložit nebo načíst objekty BLOB, které se předává argument události do jakékoli úložiště, které chcete.

Strategie se liší v závislosti na při psaní mezipaměť tokenu serializace pro [aplikace veřejným klientem](msal-client-applications.md) (plocha), nebo [důvěrnému klientovi aplikace](msal-client-applications.md)) (webová aplikace / webové rozhraní API, proces démon aplikace ).

### <a name="token-cache-for-a-public-client"></a>Mezipaměť tokenů pro veřejné klienta 

Od verze MSAL.NET v2.x máte několik možností pro serializaci mezipamětí tokenů veřejným klientem. Může serializovat mezipaměti pouze na formát MSAL.NET (sjednoceném formátu mezipaměti je společná knihovna MSAL a platformy, pro).  Také můžete podporovat [starší verze](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) token mezipaměti serializaci modul ADAL V3.

Přizpůsobení serializaci mezipaměť tokenu sdílet jednotné přihlašování – stav mezi ADAL.NET 3.x ADAL.NET 5.x a MSAL.NET je vysvětleno v části o následující ukázka: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> Formát tokenu mezipaměti 1.1.4-preview MSAL.NET už není podporovaná v MSAL 2.x. Pokud máte aplikace využívající MSAL.NET 1.x, vaši uživatelé budou muset znovu-přihlášení se změnami. Alternativně se podporuje migrace z ADAL 4.x (a 3.x).

#### <a name="simple-token-cache-serialization-msal-only"></a>Serializace jednoduchou mezipaměť tokenu (pouze MSAL)

Níže je příklad naivní implementace vlastní serializace mezipaměť tokenu pro desktopové aplikace. Mezipaměť tokenu uživatele, je soubor ve stejné složce jako aplikace.

Po sestavení aplikace, povolíte serializace pomocí volání `TokenCacheHelper.EnableSerialization()` metoda a předáním aplikace `UserTokenCache`.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

`TokenCacheHelper` Pomocná třída je definována jako:

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

Ve verzi preview produktu kvality mezipaměť tokenu souboru na základě serializátor pro aplikace veřejným klientem (pro desktopové aplikace běžící na Windows, Mac a Linux) je k dispozici [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) knihovny Open source. Můžete ho zahrnout do své aplikace z následující balíček nuget: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Duální mezipaměť tokenu serializace (MSAL unified mezipaměti a modul ADAL v3)

Pokud chcete implementovat mezipaměť tokenu serializace oba ve formátu jednotné mezipaměti (běžné ADAL.NET 4.x, MSAL.NET 2.x a dalších MSALs ke stejné generaci nebo starší, v rámci téže platformy), podívejte se na následující kód:

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

Tentokrát pomocnou třídu, jak jsou definovány jako:

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
  /// Get the user token cache
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
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   usertokenCache = cache;
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   usertokenCache.SetBeforeAccess(BeforeAccessNotification);
   usertokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Token cache
  /// </summary>
  static ITokenCache usertokenCache;

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

### <a name="token-cache-for-a-web-app-confidential-client-application"></a>Mezipaměť tokenu pro webovou aplikaci (důvěrnému klientovi aplikace)

Webové aplikace nebo webové rozhraní API může využít mezipaměti relace, Redis cache nebo v databázi.

Důležité mít na paměti je, že pro webové aplikace a webová rozhraní API, měla by existovat jeden token mezipaměti na uživatele (v rámci účtu). Potřebujete k serializaci mezipamětí tokenů pro jednotlivé účty.

Příklady toho, jak použít token mezipaměti pro webové aplikace a webová rozhraní API jsou k dispozici v [kurz vývoje webové aplikace ASP.NET Core](https://ms-identity-aspnetcore-webapp-tutorial) ve fázi [mezipaměť tokenu 2-2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). Implementace máte najdete v následující složce [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) v [microsoft ověřování extensions pro dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) knihovny (v [ Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) složky. 

## <a name="next-steps"></a>Další postup
Následující ukázky ukazují serializace mezipaměť tokenu.

| Ukázka | Platforma | Popis|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Plochy (WPF) | Aplikace .NET Desktop Windows (WPF) volání rozhraní Microsoft Graph API. ![Topologie](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Plocha (konzola) | Sada Visual Studio řešení ilustrující migrace v1.0 aplikacím služby Azure AD (s použitím ADAL.NET) pro aplikace Azure AD v2.0, také s názvem konvergované aplikace (pomocí MSAL.NET), zejména [Token mezipaměti migrace](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|