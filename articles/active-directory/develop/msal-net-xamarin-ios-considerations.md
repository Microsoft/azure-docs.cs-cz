---
title: Xamarin iOS úvahy (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o aspektech používání xamarinu iOS s Knihovnou ověřování Microsoftu pro rozhraní .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76e614b605cd07cd5dc454824dd204447f806907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262708"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Důležité informace o používání xamarinu iOS s MSAL.NET
Při použití Knihovny ověřování Microsoft pro rozhraní .NET (MSAL.NET) v systému Xamarin iOS byste měli: 

- Přepsat a implementovat `OpenUrl` `AppDelegate`funkci v aplikaci .
- Povolte skupiny klíčů.
- Povolte sdílení mezipaměti tokenů.
- Povolte přístup ke klíčence.
- Seznamte se se známými problémy s iOS 12 a ověřováním.

## <a name="implement-openurl"></a>Implementace adresy OpenUrl

Přepsat `OpenUrl` metodu `FormsApplicationDelegate` odvozené třídy `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`a volání . Tady je příklad:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Proveďte také následující úkoly: 
* Definujte schéma adresy URL.
* Vyžadovat oprávnění pro vaši aplikaci volat jinou aplikaci.
* Mají konkrétní formulář pro adresu URL přesměrování.
* Zaregistrujte adresu URL přesměrování na [webu Azure Portal](https://portal.azure.com).

### <a name="enable-keychain-access"></a>Povolení přístupu ke klíčence

Chcete-li povolit přístup k řetězci klíčů, ujistěte se, že vaše aplikace má skupinu přístupu k řetězci klíčů. Přístupovou skupinu řetězce klíčů můžete nastavit při `WithIosKeychainSecurityGroup()` vytváření aplikace pomocí rozhraní API.

Chcete-li těžit z mezipaměti a jednotného přihlašování (SSO), nastavte skupinu přístupu řetězce klíčů na stejnou hodnotu ve všech aplikacích.

Tento příklad nastavení používá MSAL 4.x:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Povolte také přístup `Entitlements.plist` ke řetězci klíčů v souboru. Použijte následující přístupovou skupinu nebo vlastní přístupovou skupinu.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

Při použití `WithIosKeychainSecurityGroup()` rozhraní API, MSAL automaticky připojí skupinu zabezpečení na konec *id týmu* aplikace (`AppIdentifierPrefix`). MSAL přidá skupinu zabezpečení, protože při vytváření aplikace v Xcode, bude totéž. To je důvod, proč `Entitlements.plist` nároky v `$(AppIdentifierPrefix)` souboru musí obsahovat před skupinou přístupu k řetězci klíčů.

Další informace naleznete v [dokumentaci k nárokům iOS](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps). 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Povolení sdílení mezipaměti tokenů v aplikacích iOS

Počínaje MSAL 2.x, můžete zadat skupinu přístupu řetězce klíčů zachovat mezipaměti tokenů ve více aplikacích. Toto nastavení umožňuje sdílet mezipaměť tokenů mezi několika aplikacemi, které mají stejnou skupinu přístupu ke řetězci klíčů. Můžete sdílet hotovost tokenu mezi [aplikacemi ADAL.NET,](https://aka.ms/adal-net) MSAL.NET aplikace Xamarin.iOS a nativní aplikace iOS, které byly vyvinuty v [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) nebo [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Sdílením mezipaměti tokenů povolíte jednotné přihlašování (SSO) mezi všemi aplikacemi, které používají stejnou skupinu přístupu k řetězci klíčů.

Chcete-li povolit toto sdílení mezipaměti, použijte metodu `WithIosKeychainSecurityGroup()` k nastavení skupiny přístupu řetězce klíčů na stejnou hodnotu ve všech aplikacích, které sdílejí stejnou mezipaměť. První příklad kódu v tomto článku ukazuje, jak použít metodu.

Dříve v tomto článku jste zjistili, že MSAL přidá `$(AppIdentifierPrefix)` vždy, když používáte `WithIosKeychainSecurityGroup()` rozhraní API. MSAL přidá tento prvek, `AppIdentifierPrefix` protože ID týmu zajišťuje, že pouze aplikace, které jsou vyrobeny stejným vydavatelem můžete sdílet přístup klíčenky.

> [!NOTE]
> Vlastnost `KeychainSecurityGroup` je zastaralá.
> 
> Počínaje MSAL 2.x vývojáři byli `TeamId` nuceni zahrnout předponu při použití vlastnosti. `KeychainSecurityGroup` Ale počínaje MSAL 2.7.x, při `iOSKeychainSecurityGroup` použití nové vlastnosti, `TeamId` MSAL řeší předponu během běhu. Při použití této vlastnosti nezahrnujte `TeamId` předponu do hodnoty. Předpona není vyžadována.
>
> Vzhledem `KeychainSecurityGroup` k tomu, `iOSKeychainSecurityGroup` že vlastnost je zastaralá, použijte vlastnost.

### <a name="use-microsoft-authenticator"></a>Použití aplikace Microsoft Authenticator

Aplikace může použít Microsoft Authenticator jako zprostředkovatele povolit:

- **STAK:** Když povolíte přihlašování, vaši uživatelé nemusí přihlásit ke každé aplikaci.
- **Identifikace zařízení**: Pomocí identifikace zařízení se ověřte přístupem k certifikátu zařízení. Tento certifikát se vytvoří na zařízení, když je připojen k pracovišti. Vaše aplikace bude připravena, pokud správci klienta povolí podmíněný přístup související se zařízeními.
- **Ověření identifikace aplikace**: Když aplikace zavolá zprostředkovatele, předá adresu URL přesměrování. Zprostředkovatel ověří adresu URL přesměrování.

Podrobnosti o povolení zprostředkovatele najdete [v tématu Použití Microsoft Authenticator nebo Microsoft Intune Portál společnosti na Xamarin iOS a Android aplikace](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>Známé problémy s iOS 12 a ověřováním
Společnost Microsoft vydala [informační zpravodaj zabezpečení](https://github.com/aspnet/AspNetCore/issues/4647) o nekompatibilitě mezi iOS 12 a některými typy ověřování. Nekompatibilita přeruší sociální, WSFed a OIDC přihlášení. Informační zpravodaj zabezpečení pomáhá vývojářům pochopit, jak odebrat ASP.NET omezení zabezpečení ze svých aplikací, aby byly kompatibilní s iOS 12.  

Při vývoji MSAL.NET aplikací v Xamarin iOS, může se zobrazit nekonečná smyčka při pokusu o přihlášení k webovým stránkám z iOS 12. Toto chování je podobné tomuto [problému ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Můžete také vidět přerušení v ASP.NET základní ověřování OIDC s iOS 12 Safari. Další informace naleznete v tomto [problému webkit](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="next-steps"></a>Další kroky

Informace o vlastnostech pro Xamarin iOS najdete v tématu [aspekty specifické pro iOS odstavec](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) následující ukázky README.md souboru:

Ukázka | Platforma | Popis
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, Univerzální platforma Windows (UPW) | Jednoduchá aplikace Xamarin Forms, která ukazuje, jak používat MSAL k ověření osobních účtů Microsoftu a Azure AD prostřednictvím koncového bodu Azure AD 2.0. Aplikace také ukazuje, jak používat výsledný token pro přístup k Microsoft Graphu.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->