---
title: Serializace mezipaměti tokenů (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informace o serializaci a serializaci mezipaměti tokenů zákazníkem pomocí knihovny Microsoft Authentication Library pro rozhraní .NET (MSAL.NET).
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
ms.custom: aaddev
ms.openlocfilehash: 1bd348ad27d892d0421b13c16ce81bc4f5dfb021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262799"
---
# <a name="token-cache-serialization-in-msalnet"></a>Serializace mezipaměti tokenů v MSAL.NET
Po [získání tokenu je](msal-acquire-cache-tokens.md)uložen do mezipaměti knihovnou Ověřování společnosti Microsoft (MSAL).  Kód aplikace by se měl pokusit získat token z mezipaměti před získáním tokenu jinou metodou.  Tento článek popisuje výchozí a vlastní serializaci mezipaměti tokenů v MSAL.NET.

Tento článek je určen pro MSAL.NET 3.x. Pokud máte zájem o MSAL.NET 2.x, přečtěte si informace [o serializaci mezipaměti tokenů v MSAL.NET 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>Výchozí serializace pro mobilní platformy

V MSAL.NET je ve výchozím nastavení k dispozici mezipaměť tokenů v paměti. Serializace je k dispozici ve výchozím nastavení pro platformy, kde zabezpečené úložiště je k dispozici pro uživatele jako součást platformy. To je případ univerzální platformy Windows (UPW), Xamarin.iOS a Xamarin.Android.

> [!Note]
> Při migraci projektu Xamarin.Android z MSAL.NET 1.x na MSAL.NET 3.x, můžete přidat `android:allowBackup="false"` do projektu, aby se zabránilo staré tokeny uložené v mezipaměti z návratu, když nasazení sady Visual Studio aktivovat obnovení místního úložiště. Viz [Problém #659](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Vlastní serializace pro desktopové aplikace pro Windows a webové aplikace/webová api

Nezapomeňte, že vlastní serializace není k dispozici na mobilních platformách (UPW, Xamarin.iOS a Xamarin.Android). MSAL již definuje zabezpečené a výkonné serializace mechanismus pro tyto platformy. Desktopové a základní aplikace .NET však mají různé architektury a služba MSAL nemůže implementovat mechanismus serializace pro obecné účely. Webové servery se například mohou rozhodnout ukládat tokeny do mezipaměti Redis nebo ukládat tokeny aplikací klasické pracovní plochy do šifrovaného souboru. Takže serializace není poskytována izv. Chcete-li mít aplikaci trvalé mezipaměti tokenů na ploše rozhraní .NET nebo v centru .NET Core, je třeba serializaci přizpůsobit.

Následující třídy a rozhraní se používají při serializaci mezipaměti tokenů:

- `ITokenCache`, který definuje události přihlásit k odběru požadavků na serializaci mezipaměti tokenů, stejně jako metody serializace nebo deserializace mezipaměti v různých formátech (ADAL v3.0, MSAL 2.x a MSAL 3.x = ADAL v5.0).
- `TokenCacheCallback`je zpětné volání předané události, takže můžete zpracovat serializace. Budou voláni s argumenty `TokenCacheNotificationArgs`typu .
- `TokenCacheNotificationArgs`poskytuje `ClientId` pouze aplikace a odkaz na uživatele, pro které je token k dispozici.

  ![Diagram třídy](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET vytvoří mezipaměti tokenů pro `IToken` vás a poskytuje cache `UserTokenCache` při `AppTokenCache` volání aplikace a vlastnosti. Nemáte implementovat rozhraní sami. Vaše odpovědnost, při implementaci vlastní serializace mezipaměti tokenů, je:
> - Reagovat `BeforeAccess` `AfterAccess` na a "události" (nebo jejich asynchronní příchutě). Delegát `BeforeAccess` je zodpovědný za rekonstrukci mezipaměti, `AfterAccess` zatímco ten je zodpovědný za serializaci mezipaměti.
> - Část těchto událostí ukládat nebo načíst objekty BLOB, které jsou předány prostřednictvím argumentu události do libovolného úložiště, které chcete.

Strategie se liší v závislosti na tom, zda píšete serializaci mezipaměti tokenů pro [veřejnou klientskou aplikaci](msal-client-applications.md) (desktop) nebo [důvěrnou klientskou aplikaci](msal-client-applications.md)) (webová aplikace / webové rozhraní API, aplikace pro daemon).

### <a name="token-cache-for-a-public-client"></a>Mezipaměť tokenů pro veřejného klienta 

Vzhledem k tomu, MSAL.NET v2.x máte několik možností pro serializaci mezipaměti tokenů veřejného klienta. Mezipaměť lze serializovat pouze do formátu MSAL.NET (mezipaměť jednotného formátu je běžná napříč msal a platformami).  Můžete také podporovat [serializaci starší](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) chod tokenu adal v3.

Přizpůsobení serializace mezipaměti tokenů tak, aby bylo sdílení stavu jednotného přihlašování mezi ADAL.NET 3.x, ADAL.NET 5.x a MSAL.NET je vysvětleno v části následující ukázky: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> Formát mezipaměti tokenu MSAL.NET 1.1.4-preview již není v msal 2.x podporován. Pokud máte aplikace využívající MSAL.NET 1.x, uživatelé se budou muset znovu přihlásit. Alternativně je podporována migrace z ADAL 4.x (a 3.x).

#### <a name="simple-token-cache-serialization-msal-only"></a>Jednoduchá serializace mezipaměti tokenů (pouze MSAL)

Níže je uveden příklad naivní implementace vlastní serializace mezipaměti tokenů pro desktopové aplikace. Zde je mezipaměť tokenů uživatele souborem ve stejné složce jako aplikace.

Po sestavení aplikace povolíte serializaci voláním `TokenCacheHelper.EnableSerialization()` metody a `UserTokenCache`předáním aplikace .

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Pomocná `TokenCacheHelper` třída je definována jako:

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

Náhled serializátoru na základě souborů mezipaměti tokenů kvality produktu pro veřejné klientské aplikace (pro desktopové aplikace spuštěné v systémech Windows, Mac a Linux) je k dispozici v open source knihovně [Microsoft.Identity.Client.Extensions.Msal.](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) Můžete jej zahrnout do svých aplikací z následujícího balíčku nuget: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Serializace mezipaměti se dvěma tokeny (sjednocená vyrovnávací paměť MSAL a ADAL v3)

Pokud chcete implementovat serializaci mezipaměti tokenů s jednotným formátem mezipaměti (společné pro ADAL.NET 4.x, MSAL.NET 2.x a další mise MSAL stejné generace nebo starší na stejné platformě), podívejte se na následující kód:

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

Tentokrát pomocná třída, jak je definována jako:

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

Ve webových aplikacích nebo webových api cache může využít relace, Redis cache nebo databáze.

Ve webových aplikacích nebo webových apich uchovávejte jednu mezipaměť tokenů na účet.  U webových aplikací by měla být mezipaměť tokenů zadána pomocí ID účtu.  Pro webová rozhraní API by měl být účet zadal pomocí hash tokenu používaného k volání rozhraní API. MSAL.NET poskytuje vlastní serializaci mezipaměti tokenů v rozhraní .NET Framework a podplatformách .NET Core. Události jsou aktivovány při přístupu ke mezipaměti, aplikace můžete zvolit, zda serializovat nebo rekonstruovat mezipaměť. V důvěrných klientských aplikacích, které zpracovávají uživatele (webové aplikace, které přihlašují uživatele a volají webová api, a webová api volající podřízená webová síťOVÁ řešení API) může být mnoho uživatelů a uživatelé jsou zpracováváni paralelně. Z důvodů zabezpečení a výkonu doporučujeme serializovat jednu mezipaměť na uživatele. Události serializace vypočítat klíč mezipaměti na základě identity zpracovaného uživatele a serializovat/rekonstruovat mezipaměť tokenů pro tohoto uživatele.

Příklady použití mezipamětí tokenů pro webové aplikace a webová rozhraní API jsou k dispozici v [kurzu ASP.NET základní webové aplikace](https://ms-identity-aspnetcore-webapp-tutorial) ve fázi [2-2 Token Cache](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). Pro implementace se podívejte na složku [TokenCacheProviders](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/Microsoft.Identity.Web/TokenCacheProviders) v knihovně [Microsoft-authentication-extensions-for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) (ve složce [Microsoft.Identity.Client.Extensions.Web.](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) 

## <a name="next-steps"></a>Další kroky
Následující ukázky ilustrují serializaci mezipaměti tokenů.

| Ukázka | Platforma | Popis|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Plocha (WPF) | Aplikace Windows Desktop .NET (WPF), která volá rozhraní Microsoft Graph API. ![Topologie](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Plocha (konzola) | Sada řešení Visual Studia ilustrující migraci aplikací Azure AD v1.0 (pomocí ADAL.NET) do aplikací Azure AD v2.0, s názvem konvergované aplikace (pomocí MSAL.NET), zejména [migrace tokencache](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|
