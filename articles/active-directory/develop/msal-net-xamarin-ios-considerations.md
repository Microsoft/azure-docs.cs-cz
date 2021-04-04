---
title: Předpoklady pro Xamarin iOS (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Seznamte se s důležitými informacemi o používání Xamarin iOS s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 62eb4ab9eb6e4b0e7be0f7aadae1173950d21615
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064483"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Pokyny pro používání Xamarin iOS s MSAL.NET

Při použití knihovny Microsoft Authentication Library pro .NET (MSAL.NET) na platformě Xamarin iOS byste měli:

- Přepište a implementujte `OpenUrl` funkci v `AppDelegate` .
- Povolte skupiny řetězce klíčů.
- Povolte sdílení mezipaměti tokenů.
- Povolte přístup k řetězci klíčů.
- Pochopení známých problémů se systémy iOS 12 a iOS 13 a ověřováním.

## <a name="implement-openurl"></a>Implementovat OpenUrl

Přepsat `OpenUrl` metodu `FormsApplicationDelegate` odvozené třídy a volání `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs` . Tady je příklad:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Proveďte také následující úlohy:

* Definujte schéma identifikátoru URI přesměrování.
* Vyžadovat oprávnění pro vaši aplikaci pro volání jiné aplikace.
* Mít konkrétní formulář pro identifikátor URI přesměrování.
* [Zaregistrujte identifikátor URI přesměrování](quickstart-register-app.md#add-a-redirect-uri) v Azure Portal.

### <a name="enable-keychain-access"></a>Povolit přístup k řetězci klíčů

Pokud chcete povolit přístup pomocí řetězce klíčů, ujistěte se, že má vaše aplikace přístupovou skupinu pro řetězce klíčů. Přístupovou skupinu pro řetězce klíčů můžete nastavit při vytváření aplikace pomocí `WithIosKeychainSecurityGroup()` rozhraní API.

Pokud chcete využít výhod mezipaměti a jednotného přihlašování (SSO), nastavte přístupovou skupinu pro řetězce klíčů na stejnou hodnotu ve všech vašich aplikacích.

Tento příklad instalace používá MSAL 4. x:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

V souboru také povolte přístup k řetězci klíčů `Entitlements.plist` . Použijte buď následující skupinu přístupu, nebo vlastní přístupovou skupinu.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

Když použijete `WithIosKeychainSecurityGroup()` rozhraní API, MSAL automaticky připojí skupinu zabezpečení na konec *ID týmu* aplikace ( `AppIdentifierPrefix` ). MSAL přidá vaši skupinu zabezpečení, protože když sestavíte aplikaci v Xcode, bude to mít stejnou. To je důvod, proč oprávnění v `Entitlements.plist` souboru musí být zahrnutá `$(AppIdentifierPrefix)` před přístupovou skupinou pro řetězce klíčů.

Další informace najdete v dokumentaci k [oprávněním pro iOS](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps).

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Povolit sdílení mezipaměti tokenů napříč aplikacemi pro iOS

Od MSAL 2. x můžete zadat přístupovou skupinu pro řetězce klíčů, která bude uchovávat mezipaměť tokenů napříč více aplikacemi. Toto nastavení umožňuje sdílet mezipaměť tokenů mezi několika aplikacemi, které mají stejnou přístupovou skupinu pro řetězce klíčů. Mezipaměť tokenů můžete sdílet mezi [ADAL.NET](https://aka.ms/adal-net) aplikacemi, MSAL.NET aplikacemi Xamarin. iOS a nativními aplikacemi pro iOS, které byly vyvinuty v [ADAL. objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) nebo [MSAL. objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Sdílením mezipaměti tokenů povolíte jednotné přihlašování (SSO) mezi všemi aplikacemi, které používají stejnou přístupovou skupinu pro řetězce klíčů.

Pokud chcete povolit toto sdílení mezipaměti, pomocí `WithIosKeychainSecurityGroup()` metody nastavte přístupovou skupinu pro řetězce klíčů na stejnou hodnotu ve všech aplikacích, které sdílejí stejnou mezipaměť. První příklad kódu v tomto článku ukazuje, jak použít metodu.

Dříve v tomto článku jste se naučili, že MSAL přidá, `$(AppIdentifierPrefix)` kdykoli použijete `WithIosKeychainSecurityGroup()` rozhraní API. MSAL přidá tento prvek, protože ID týmu `AppIdentifierPrefix` zajišťuje, že přístup k řetězci klíčů může sdílet jenom aplikace, které provádí tentýž Vydavatel.

> [!NOTE]
> `KeychainSecurityGroup`Vlastnost je zastaralá. `iOSKeychainSecurityGroup`Místo toho použijte vlastnost. `TeamId`Když použijete, předpona se nevyžaduje `iOSKeychainSecurityGroup` .

### <a name="use-microsoft-authenticator"></a>Použít Microsoft Authenticator

Aplikace může použít Microsoft Authenticator jako zprostředkovatele k povolení:

- **SSO**: Když povolíte jednotné přihlašování, uživatelé se nebudou muset přihlašovat ke každé aplikaci.
- **Identifikace zařízení**: pomocí identifikace zařízení se můžete ověřit přístupem k certifikátu zařízení. Tento certifikát se vytvoří na zařízení, když je připojený k pracovišti. Vaše aplikace bude připravena, pokud správci klienta povolí podmíněný přístup týkající se zařízení.
- **Ověření identifikace aplikace**: když aplikace volá zprostředkovatele, PŘEDÁ adresu URL pro přesměrování. Zprostředkovatel ověří adresu URL pro přesměrování.

Podrobnosti o tom, jak povolit zprostředkovatele, najdete v tématu [použití Microsoft Authenticator nebo Microsoft Intune portál společnosti v aplikacích Xamarin iOS a Android](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>Známé problémy se systémem iOS 12 a ověřováním

Společnost Microsoft vydala [informační zpravodaj zabezpečení](https://github.com/aspnet/AspNetCore/issues/4647) pro nekompatibilitu mezi iOS 12 a některými typy ověřování. Nekompatibilita přerušují sociální, WSFed a OIDC přihlášení. Informační zpravodaj zabezpečení vám pomůže pochopit, jak odebrat omezení zabezpečení ASP.NET z vašich aplikací, aby byly kompatibilní se systémem iOS 12.

Při vývoji aplikací MSAL.NET na platformě Xamarin iOS se může při pokusu o přihlášení k webům z iOS 12 zobrazit nekonečná smyčka. Toto chování je podobné tomuto problému ADAL na GitHubu: [nekonečná smyčka při pokusu o přihlášení k webu z iOS 12 #1329](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329).

Můžete se také setkat s přerušením ASP.NET Core ověřování OIDC s iOS 12 Safari. Další informace najdete v tomto [WebKit problému](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="known-issues-with-ios-13-and-authentication"></a>Známé problémy se systémem iOS 13 a ověřováním

Pokud vaše aplikace vyžaduje podmíněný přístup nebo podporu ověřování certifikátů, umožněte aplikaci, aby komunikovala s aplikací Microsoft Authenticator Broker. MSAL je pak zodpovědný za zpracování požadavků a odpovědí mezi vaší aplikací a Microsoft Authenticator.

V systému iOS 13 Společnost Apple provedla změnu rozhraní API odebráním schopnosti aplikace číst zdrojovou aplikaci, když obdrží odpověď z externí aplikace pomocí vlastních schémat adres URL.

Dokumentace společnosti Apple pro [UIApplicationOpenURLOptionsSourceApplicationKey](https://developer.apple.com/documentation/uikit/uiapplicationopenurloptionssourceapplicationkey?language=objc) stavy:

> *Pokud žádost pochází z jiné aplikace patřící vašemu týmu, UIKit nastaví hodnotu tohoto klíče na ID této aplikace. Pokud se identifikátor týmu zdrojové aplikace liší od identifikátoru týmu aktuální aplikace, hodnota klíče je Nil.*

Tato změna je vyrušují pro MSAL, protože se spoléhala na `UIApplication.SharedApplication.OpenUrl` ověření komunikace mezi MSAL a Microsoft Authenticator aplikací.

V systému iOS 13 je navíc vývojář nutný k poskytnutí prezentačního kontroleru při použití `ASWebAuthenticationSession` .

Vaše aplikace je ovlivněná, pokud sestavíte s Xcode 11 a používáte buď zprostředkovatele iOS, nebo `ASWebAuthenticationSession` .

V takových případech použijte [MSAL.NET 4.4.0 +](https://www.nuget.org/packages/Microsoft.Identity.Client/) k povolení úspěšného ověření.

### <a name="additional-requirements"></a>Další požadavky

- Při použití nejnovějších knihoven MSAL se ujistěte, že je na zařízení nainstalovaný **Microsoft Authenticator verze 6.3.19 + +** .
- Při aktualizaci na MSAL.NET 4.4.0 + aktualizujte `LSApplicationQueriesSchemes` soubor *info. plist* a přidejte `msauthv3` :

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

    Přidání `msauthv3` do souboru *info. plist* je nezbytné k detekci přítomnosti nejnovější Microsoft Authenticator aplikace na zařízení, které podporuje iOS 13.

## <a name="report-an-issue"></a>Nahlášení problému

Pokud máte dotazy nebo chcete ohlásit problém, který jste našli v MSAL.NET, otevřete problém v úložišti [AzureAD/Microsoft-Authentication-Library-for-dotnet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues) na GitHubu.

## <a name="next-steps"></a>Další kroky

Informace o vlastnostech pro Xamarin iOS najdete v článku věnovaném [důležitým](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) údajům pro iOS v následujícím ukázkovém souboru Readme.MD:

Ukázka | Platforma | Description
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, Univerzální platforma Windows (UWP) | Jednoduchá aplikace Xamarin Forms, která ukazuje, jak používat MSAL k ověřování osobních účtů Microsoft a Azure AD prostřednictvím koncového bodu Azure AD 2,0. Aplikace také ukazuje, jak použít výsledný token pro přístup k Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
