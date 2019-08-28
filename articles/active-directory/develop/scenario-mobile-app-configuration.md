---
title: Mobilní aplikace, která volá webová rozhraní API (konfigurace kódu) – Microsoft Identity Platform | Azure
description: Zjistěte, jak vytvořit mobilní aplikaci, která volá webová rozhraní API (konfigurace kódu aplikace).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ebf524d932322fa08729f229a451afe656900d5
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061397"
---
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>Mobilní aplikace, která volá webovou rozhraní API – konfigurace kódu

Po vytvoření aplikace se dozvíte, jak nakonfigurovat kód pomocí parametrů registrace aplikace. Mobilní aplikace mají také několik složitých specifických, která musí dělat s přizpůsobením rozhraní, které se používá k vytváření těchto aplikací.

## <a name="msal-libraries-supporting-mobile-apps"></a>Knihovny MSAL podporující mobilní aplikace

Knihovny Microsoftu podporující mobilní aplikace jsou:

  Knihovna MSAL | Popis
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | K vývoji přenosných aplikací. MSAL.NET podporované platformy pro sestavení mobilní aplikace jsou UWP, Xamarin. iOS a Xamarin. Android.
  ![MSAL. iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL. iOS | Vývoj nativních aplikací pro iOS s cílem C nebo SWIFT
  ![MSAL. Svém](media/sample-v2-code/logo_android.png) <br/> MSAL. Svém | Vývoj nativních aplikací pro Android v jazyce Java pro Android

## <a name="configuring-the-application"></a>Konfigurace aplikace

Mobilní aplikace používají `PublicClientApplication` třídu. Tady je postup vytvoření instance:

### <a name="android"></a>Android

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

```swift
// Initialize the app.
guard let authorityURL = URL(string: kAuthority) else {
    self.loggingText.text = "Unable to create authority URL"
    return
}
let authority = try MSALAADAuthority(url: authorityURL)
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
}
```

### <a name="xamarin-or-uwp"></a>Xamarin nebo UWP

Následující článek vysvětluje, jak nakonfigurovat kód aplikace pro Xamarin. iOS, Xamarin. Android a aplikace UWP. Prvním krokem je vytvoření instance aplikace. Volitelným krokem je konfigurace zprostředkovatele.

#### <a name="instantiating-the-application"></a>Vytvoření instance aplikace

V Xamarin nebo UWP nejjednodušší způsob vytvoření instance aplikace je následující, kde `ClientId` je identifikátor GUID vaší registrované aplikace.

```CSharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Existují další metody*parametrů* , které nastavují nadřazený objekt uživatelského rozhraní, přepíší výchozí autority, určují název klienta a verzi (pro telemetrii), určují identifikátor URI pro přesměrování, určují továrnu protokolu HTTP, který se má použít (například pro zpracování proxy serverů), zadejte telemetrie a protokolování). Toto je téma v následujících odstavcích.

##### <a name="specifying-the-parent-uiwindowactivity"></a>Zadání nadřazeného uživatelského rozhraní/okna/aktivity

V Androidu je nutné před provedením interaktivního ověřování předat nadřazenou aktivitu. V případě iOS musíte při použití zprostředkovatele předat soubor viewcontroller. Stejně jako u UWP byste mohli chtít předat nadřazenému oknu. To je možné při získání tokenu, ale je také možné zadat zpětné volání při vytvoření aplikace delegáta, který vrací UIParent.

```CSharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

V Androidu doporučujeme použít `CurrentActivityPlugin` [zde](https://github.com/jamesmontemagno/CurrentActivityPlugin).  `PublicClientApplication` Kód Tvůrce pak bude vypadat takto:

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="more-app-building-parameters"></a>Další parametry sestavování aplikací

- Seznam všech modifikátorů dostupných na `PublicClientApplicationBuilder`najdete v referenční dokumentaci [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods) .
- Popis všech možností vystavených v `PublicClientApplicationOptions` tématu [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)najdete v referenční dokumentaci.

#### <a name="xamarin-ios-specific-considerations"></a>Konkrétní předpoklady pro Xamarin iOS

V systému Xamarin iOS je při používání MSAL.NET potřeba vzít v úvahu několik důležitých informací:

1. [Přepsání a implementace `OpenUrl` funkce v`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
1. [Povolit skupiny řetězce klíčů](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
1. [Povolit sdílení mezipaměti tokenů](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
1. [Povolit přístup k řetězci klíčů](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Podrobnosti jsou uvedené v [informacích pro Xamarin iOS](msal-net-xamarin-ios-considerations.md)

#### <a name="other-xamarin-android-specific-considerations"></a>Další předpoklady pro Xamarin Android

Tady jsou specifiky pro Xamarin Android:

- [Zajistěte, aby se řízení vrátilo zpátky na MSAL, jakmile skončí interaktivní část toku ověřování.](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [Aktualizace manifestu pro Android](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Použít vložené webové zobrazení (volitelné)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Odstraňování potíží](msal-net-xamarin-android-considerations.md#troubleshooting)

Podrobnosti najdete v informacích pro [Xamarin Android](msal-net-xamarin-android-considerations.md) .

Nakonec existují určité informace o prohlížečích v Androidu. Jsou vysvětleny v tématu [požadavky pro Xamarin Android, které se týkají MSAL.NET](msal-net-system-browser-android-considerations.md)

#### <a name="uwp-specific-considerations"></a>Konkrétní předpoklady pro UWP

Na UWP můžete používat podnikové sítě. Další informace o tom, jak používat knihovnu MSAL s UWP, najdete v tématu [informace o Univerzální platforma Windows specifických pro použití s MSAL.NET](msal-net-uwp-considerations.md).

## <a name="configuring-the-application-to-use-the-broker"></a>Konfigurace aplikace pro použití zprostředkovatele

### <a name="why-use-brokers-on-xamarinios-and-xamarinandroid-applications"></a>Proč používat zprostředkovatele v aplikacích Xamarin. iOS a Xamarin. Android?

V zařízeních s Androidem a iOS poskytují zprostředkovatelé tyto akce:

- Jednotné přihlašování (SSO). Uživatelé se nebudou muset přihlašovat ke každé aplikaci.
- Identifikace zařízení. Pomocí zásad podmíněného přístupu pro zařízení Azure AD povolí přístup k certifikátu zařízení vytvořenému v zařízení, když se připojil k síti na pracovišti.
- Ověření identifikace aplikace Když aplikace volá zprostředkovatele, předá adresu URL pro přesměrování a zprostředkovatel ji ověří.

### <a name="enable-the-brokers-on-xamarin"></a>Povolení zprostředkovatelů na Xamarin

Chcete-li povolit jednu z těchto funkcí, `WithBroker()` použijte parametr při `PublicClientApplicationBuilder.CreateApplication` volání metody. `.WithBroker()`ve výchozím nastavení je nastaveno na hodnotu true. Postupujte podle následujících kroků pro [iOS](#brokered-authentication-for-xamarinios).

### <a name="brokered-authentication-for-xamarinios"></a>Zprostředkované ověřování pro Xamarin. iOS

Použijte následující postup, chcete-li aplikaci Xamarin. iOS povolit komunikaci s aplikací [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

#### <a name="step-1-enable-broker-support"></a>Krok 1: Povolit podporu zprostředkovatele

Podpora zprostředkovatele je povolena po jednotlivých`PublicClientApplication` případech. Ve výchozím nastavení je zakázaný. Při `WithBroker()` vytvářeníprostřednictvím`PublicClientApplicationBuilder`použijteparametr (ve výchozím nastavení je nastaven na hodnotu true). `PublicClientApplication`

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Krok 2: Aktualizace AppDelegate pro zpracování zpětného volání

Když MSAL.NET volá zprostředkovatele, zprostředkovatel pak zase volá zpět do vaší aplikace prostřednictvím `AppDelegate.OpenUrl` metody. Vzhledem k tomu, že MSAL bude čekat na odpověď od služby Broker, musí vaše aplikace spolupracovat a volat MSAL.NET zpátky. Provedete to tak, `AppDelegate.cs` že aktualizujete soubor, abyste přepsali níže uvedenou metodu.

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

Tato metoda je vyvolána při každém spuštění aplikace a slouží jako příležitost ke zpracování odpovědi od zprostředkovatele a dokončení procesu ověřování iniciované nástrojem MSAL.NET.

#### <a name="step-3-set-a-uiviewcontroller"></a>Krok 3: Nastavit UIViewController ()

V případě Xamarin iOS nemusíte normálně nastavovat okno objektu, ale v takovém případě budete moci odesílat a přijímat odpovědi od zprostředkovatele. Pořád v `AppDelegate.cs`, nastavte soubor viewcontroller.

Chcete-li nastavit okno objektu, proveďte následující kroky:

1) V `AppDelegate.cs`portálu `UIViewController()`nastavte na nový. `App.RootViewController` Tím se zajistí, že bude existovat `UIViewController` volání služby Broker. Pokud není správně nastavená, může se zobrazit tato chyba:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) V volání AcquireTokenInteractive použijte `.WithParentActivityOrWindow(App.RootViewController)` příkaz a předejte odkaz na okno objektu, které použijete.

**Příklad:**

V `App.cs`:
```CSharp
   public static object RootViewController { get; set; }
```
V `AppDelegate.cs`:
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
V volání metody získání tokenu:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

#### <a name="step-4-register-a-url-scheme"></a>Krok 4: Registrace schématu adresy URL

MSAL.NET používá adresy URL k vyvolání zprostředkovatele a pak vrátí odpověď zprostředkovatele zpátky do vaší aplikace. Pokud chcete dokončit zpáteční cestu, musíte v `Info.plist` souboru zaregistrovat schéma URL pro vaši aplikaci.

Předponu `msauth`s. `CFBundleURLSchemes` Pak přidejte `CFBundleURLName` na konec.

`$"msauth.(BundleId)"`

**Například:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Toto schéma URL se stane součástí RedirectUri, který slouží k jednoznačné identifikaci vaší aplikace při přijímání odpovědi od zprostředkovatele.

```XML
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

#### <a name="step-5-lsapplicationqueriesschemes"></a>Krok 5: LSApplicationQueriesSchemes

MSAL používá `–canOpenURL:` ke kontrole, jestli je na zařízení nainstalovaný zprostředkovatel. V systému iOS 9 společnost Apple zablokoval, ke kterým schématům se může aplikace dotazovat.

**Přidat** **`msauthv2`** do`LSApplicationQueriesSchemes` části souboru`Info.plist` .

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Zprostředkované ověřování pro Xamarin. Android

MSAL.NET ještě nepodporuje zprostředkovatele pro Android.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získání tokenu](scenario-mobile-acquire-token.md)
