---
title: Předpoklady pro Xamarin iOS (Microsoft Authentication Library pro .NET)
titleSuffix: Microsoft identity platform
description: Přečtěte si o konkrétních doporučeních pro použití Xamarin iOS s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cc4eb0084e0606251d3fbd80d08723701c6a260
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175620"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Otázky specifické pro Xamarin iOS s MSAL.NET
V systému Xamarin iOS je při používání MSAL.NET potřeba vzít v úvahu několik důležitých informací.

- [Známé problémy se systémem iOS 12 a ověřováním](#known-issues-with-ios-12-and-authentication)
- [Přepsání a implementace funkce `OpenUrl` v `AppDelegate`](#implement-openurl)
- [Povolit skupiny řetězce klíčů](#enable-keychain-access)
- [Povolit sdílení mezipaměti tokenů](#enable-token-cache-sharing-across-ios-applications)
- [Povolit přístup k řetězci klíčů](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>Známé problémy se systémem iOS 12 a ověřováním
Společnost Microsoft vydala [informační zpravodaj zabezpečení](https://github.com/aspnet/AspNetCore/issues/4647) , který poskytuje informace o nekompatibilitě mezi iOS12 a některými typy ověřování. Nekompatibilita přeruší sociální, WSFed a OIDC přihlášení. Tento informační zpravodaj také poskytuje pokyny k tomu, jak můžou vývojáři odebrat aktuální omezení zabezpečení přidaná ASP.NET do svých aplikací, aby se staly kompatibilními s iOS12.  

Při vývoji aplikací MSAL.NET na platformě Xamarin iOS se může při pokusu o přihlášení k webům ze systému iOS 12 zobrazit nekonečná smyčka (podobně jako tento [problém ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Můžete se také setkat s přerušením v ASP.NET Core ověřování OIDC se systémem iOS 12 Safari, jak je popsáno v tomto [problému WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="implement-openurl"></a>Implementovat OpenUrl

Nejprve je třeba přepsat metodu `OpenUrl` `FormsApplicationDelegate` odvozené třídy a volat `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`.

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Budete taky muset definovat schéma adresy URL, vyžadovat oprávnění, aby vaše aplikace volala jinou aplikaci, měla pro adresu URL pro přesměrování konkrétní formulář a tuto adresu URL pro přesměrování zaregistrovat v [Azure Portal](https://portal.azure.com)

### <a name="enable-keychain-access"></a>Povolit přístup k řetězci klíčů

Aby bylo možné povolit přístup pomocí řetězce klíčů, musí mít vaše aplikace přístupovou skupinu pro řetězce klíčů.
Přístupovou skupinu pro řetězce klíčů můžete nastavit pomocí rozhraní `WithIosKeychainSecurityGroup()` API při vytváření aplikace, jak je znázorněno níže:

Pokud chcete povolit jednotné přihlašování, musíte nastavit vlastnost `PublicClientApplication.iOSKeychainSecurityGroup` na stejnou hodnotu ve všech aplikacích.

Příkladem použití MSAL v3. x bude:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

Oprávnění. plist by se měla aktualizovat tak, aby vypadala jako následující fragment kódu XML:

Tato změna je *navíc* k povolení přístupu k řetězci klíčů v souboru `Entitlements.plist`, a to buď pomocí níže uvedené skupiny přístupu, nebo podle vlastního:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.msalrocks</string>
  </array>
</dict>
</plist>
```

Příkladem je použití MSAL v4. x:

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

Když použijete rozhraní `WithIosKeychainSecurityGroup()` API, MSAL automaticky připojí skupinu zabezpečení na konec ID týmu aplikace (AppIdentifierPrefix), protože když sestavíte aplikaci pomocí Xcode, bude to mít stejnou. [Další podrobnosti najdete v dokumentaci k oprávněním pro iOS](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps). To je důvod, proč potřebujete aktualizovat oprávnění, aby zahrnoval $ (AppIdentifierPrefix) před přístupovou skupinou pro řetězce klíčů v oprávněních. plist.

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Povolit sdílení mezipaměti tokenů napříč aplikacemi pro iOS

Z MSAL 2. x můžete zadat přístupovou skupinu pro řetězce klíčů, která se má použít pro uchování mezipaměti tokenů napříč více aplikacemi. Toto nastavení umožňuje sdílet mezipaměť tokenů mezi několika aplikacemi se stejnou přístupovou skupinou pro řetězce klíčů, včetně těch vyvinutých pomocí aplikací [ADAL.NET](https://aka.ms/adal-net), MSAL.NET Xamarin. iOS a nativních aplikací pro iOS vyvinutých pomocí [nástroje. ADAL. objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) nebo [MSAL. objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)).

Sdílení mezipaměti tokenů umožňuje jednotné přihlašování mezi všemi aplikacemi, které používají stejnou přístupovou skupinu pro řetězce klíčů.

Pokud chcete povolit toto sdílení mezipaměti, musíte nastavit metodu "WithIosKeychainSecurityGroup ()" pro nastavení přístupové skupiny pro řetězce klíčů na stejnou hodnotu ve všech aplikacích, které sdílejí stejnou mezipaměť, jak je znázorněno v předchozím příkladu.

Dřív bylo zmíněno, že MSAL přidala $ (AppIdentifierPrefix) vždy, když použijete rozhraní `WithIosKeychainSecurityGroup()` API. Důvodem je, že AppIdentifierPrefix nebo "ID týmu" slouží k zajištění, aby přístup k řetězci klíčů mohl sdílet jenom aplikace, které provádí tentýž Vydavatel.

> [!NOTE]
> **Vlastnost `KeychainSecurityGroup` je zastaralá.**
> 
> Předtím, od MSAL 2. x, museli vývojáři při použití vlastnosti `KeychainSecurityGroup` obsahovat předponu TeamId.
>
>  Pokud při použití vlastnosti New `iOSKeychainSecurityGroup` použijete novou vlastnost 2.7. x, MSAL během běhu vyřeší předponu TeamId. Pokud použijete tuto vlastnost, hodnota by neměla obsahovat předponu TeamId.
>  Použijte novou vlastnost `iOSKeychainSecurityGroup`, která nevyžaduje zadání TeamId, protože předchozí vlastnost `KeychainSecurityGroup` je nyní zastaralá.

### <a name="use-microsoft-authenticator"></a>Použít Microsoft Authenticator

Vaše aplikace může použít Microsoft Authenticator (zprostředkovatel) k povolení:

- Jednotné přihlašování (SSO). Uživatelé se nebudou muset přihlašovat ke každé aplikaci.
- Identifikace zařízení. Přístupem k certifikátu zařízení vytvořenému v zařízení, když se připojil k síti na pracovišti. Vaše aplikace bude připravena, pokud správci tenanta povolí podmíněný přístup týkající se zařízení.
- Ověření identifikace aplikace Když aplikace volá zprostředkovatele, předá adresu URL pro přesměrování a zprostředkovatel ji ověří.

Podrobnosti o tom, jak povolit zprostředkovatele, najdete v tématu [použití Microsoft Authenticator nebo Microsoft Intune portálu společnosti v aplikacích Xamarin iOS a Android](msal-net-use-brokers-with-xamarin-apps.md).

### <a name="sample-illustrating-xamarin-ios-specific-properties"></a>Ukázka ilustrující vlastnosti specifické pro Xamarin iOS

Další podrobnosti najdete v části [požadavky na konkrétní iOS](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) v následujícím souboru Readme.MD ukázky:

Ukázka | Platforma | Popis
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Jednoduchá aplikace Xamarin Forms předvádí, jak pomocí MSAL ověřovat MSA a Azure AD prostřednictvím koncového bodu Azure AD V 2.0 a přistupovat k Microsoft Graph s výsledným tokenem.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
