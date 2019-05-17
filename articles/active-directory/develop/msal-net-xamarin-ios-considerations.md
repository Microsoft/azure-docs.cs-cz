---
title: Aspekty Xamarin iOS (knihovna Microsoft Authentication Library pro .NET) | Azure
description: Další informace o konkrétní aspekty při používání Xamarin pro iOS s knihovna Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf236bff2300129ec97d3b8946c4c2a2748bca77
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602132"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Důležité informace specifické pro iOS Xamarin pomocí MSAL.NET
V Xamarin pro iOS je třeba mít na paměti, které musí vzít v úvahu při použití MSAL.NET

- [Známé problémy s iOS 12 a ověřování](#known-issues-with-ios-12-and-authentication)
- [Přepsat a implementovat `OpenUrl` fungovat v `AppDelegate`](#implement-openurl)
- [Povolit skupiny řetězce klíčů](#enable-keychain-groups)
- [Povolit sdílení mezipaměť tokenu](#enable-token-cache-sharing-across-ios-applications)
- [Povolit přístup do řetězce klíčů](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>Známé problémy s iOS 12 a ověřování
Společnost Microsoft vydala [informační zpravodaj zabezpečení](https://github.com/aspnet/AspNetCore/issues/4647) zadání informací o nekompatibility mezi iOS12 a některé typy ověřování. Sociální konce nekompatibility, WSFed a OIDC přihlášení. Tento informační zpravodaj také obsahuje pokyny k co mohou vývojáři odebrat aktuální omezení zabezpečení do své aplikace budou kompatibilní s iOS12 technologií ASP.NET.  

Při vývoji aplikací MSAL.NET v Xamarin pro iOS, může se zobrazit nekonečná smyčka při pokusu o přihlášení k webovým stránkám ze iOS 12 (podobně jako tento [ADAL problém](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Můžete si také prohlédnout odmlky v ASP.NET Core OIDC ověřování s Iosem 12 Safari, jak je popsáno v tomto [WebKit problém](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="implement-openurl"></a>Implementace OpenUrl

Nejdřív je potřeba přepsat `OpenUrl` metodu `FormsApplicationDelegate` odvozenou třídu a volání `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Budete také muset definovat schéma adresy URL, vyžadují oprávnění pro vaše aplikace volat jiné aplikace, máte konkrétní tvar pro adresy URL pro přesměrování a zaregistrovat tuto adresu URL pro přesměrování v [webu Azure portal](https://portal.azure.com).

## <a name="enable-keychain-groups"></a>Povolit skupiny řetězce klíčů

Aby bylo možné token mezipaměti práce a mít `AcquireTokenSilentAsync` metoda práci, musí být následován několik kroků:
1. Povolit přístup do řetězce klíčů v vaše *`* Entitlements.plist* souboru a zadejte **skupiny klíčenky** v identifikátor sady.
2. Vyberte *`*Entitlements.plist*`* soubor **vlastní oprávnění** pole v okně serveru iOS projektu možnosti **zobrazení podepsání sady prostředků**.
3. Při podepisování certifikátu, ujistěte se, že XCode používá stejné Apple ID.

## <a name="enable-token-cache-sharing-across-ios-applications"></a>Povolit mezipaměť tokenu sdílení mezi aplikacemi pro iOS

Počínaje MSAL 2.x, můžete zadat skupinu řetězce klíčů zabezpečení pro uchování mezipaměti tokenů napříč více aplikacemi. To umožňuje sdílet mezipaměť tokenů mezi několik aplikací, které mají stejnou skupinu zabezpečení řetězce klíčů, včetně těch, které byly vyvinuty v sadě [ADAL.NET](https://aka.ms/adal-net), aplikace MSAL.NET Xamarin.iOS a nativní aplikace pro iOS aplikace vyvinuté s [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) nebo [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)).

Sdílení tokenu mezipaměti umožňuje jednotné přihlašování (SSO) mezi všechny aplikace, které používají stejnou skupinu zabezpečení řetězce klíčů.

Pokud chcete povolit jednotné přihlašování, budete muset nastavit `PublicClientApplication.iOSKeychainSecurityGroup` vlastnost na stejnou hodnotu ve všech aplikacích.

Příklad s využitím MSAL v3.x by:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

Příklad s využitím MSAL v2.7.x by:

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

> [!NOTE]
> `KeychainSecurityGroup` Vlastnost je zastaralá. Dříve se v MSAL 2.x, vývojáři se musí obsahovat předponu TeamId při použití `KeychainSecurityGroup` vlastnost. 
> 
> Nyní, počínaje MSAL 2.7.x MSAL vyřeší předponu TeamId za běhu při použití `iOSKeychainSecurityGroup` vlastnost. Při použití této vlastnosti, hodnota by neměla obsahovat předponu TeamId. 
> 
> Pomocí nové `iOSKeychainSecurityGroup` vlastnost, která nevyžaduje vývojářům TeamId. `KeychainSecurityGroup` Vlastnost je zastaralá. 

## <a name="enable-keychain-access"></a>Povolit přístup do řetězce klíčů

V MSAL 2.x a ADAL 4.x, TeamId slouží k přístupu k řetězce klíčů, který umožňuje knihoven ověřování k poskytování jednotného přihlašování (SSO) mezi aplikacemi stejného vydavatele. 

Co je [TeamIdentifierPrefix](/xamarin/ios/deploy-test/provisioning/entitlements?tabs=vsmac) (TeamId)? Je jedinečný identifikátor (firemní nebo osobní účely) v App Store. AppId je jedinečný pro aplikaci. Pokud máte více než jednu aplikaci, TeamId pro všechny aplikace budou stejné, ale AppId se bude lišit. Přístupové skupiny řetězce klíčů má předponu TeamId automaticky pro každou skupinu systému. Je to, jak operační systém vynutí, že aplikace od stejného vydavatele, můžou přistupovat ke sdílenému řetězci klíčů. 

Při inicializaci `PublicClientApplication`, pokud se zobrazí `MsalClientException` s touto zprávou: `TeamId returned null from the iOS keychain...`, budete muset provést následující akce v aplikaci Xamarin iOS:

1. V sadě Visual Studio na kartě ladění, přejděte na nameOfMyApp.iOS vlastnosti...
2. Potom přejděte k podepsání sady prostředků aplikace pro iOS 
3. V části vlastní oprávnění, klikněte... a vyberte soubor do souboru Entitlements.plist z vaší aplikace
4. V souboru csproj aplikace pro iOS měli byste tento řádek se teď zahrnout: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. **Znovu sestavit** projektu.

Toto je *kromě* se povolí přístup do řetězce klíčů v `Entitlements.plist` souboru, buď pomocí níže skupiny pro řetězce klíčů nebo vlastní:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
</plist>
```

## <a name="next-steps"></a>Další postup

Další podrobnosti jsou uvedeny v [iOS konkrétní aspekty](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) odstavec souboru readme.md v následující ukázce:

Ukázka | Platforma | Popis 
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Jednoduchá aplikace Xamarin Forms která ukazuje použití MSAL k ověřování MSA a Azure AD prostřednictvím koncového bodu AAD V2.0 a přístup k Microsoft Graphu pomocí výsledný token. <br>![Topologie](media/msal-net-xamarin-ios-considerations/topology.png)