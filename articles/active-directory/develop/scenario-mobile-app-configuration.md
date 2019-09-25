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
ms.openlocfilehash: e7b731c9936ab85b19428687330044a46c563c49
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268372"
---
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>Mobilní aplikace, která volá webovou rozhraní API – konfigurace kódu

Po vytvoření aplikace se dozvíte, jak nakonfigurovat kód pomocí parametrů registrace aplikace. Mobilní aplikace mají také několik složitých specifických, která musí dělat s přizpůsobením rozhraní, které se používá k vytváření těchto aplikací.

## <a name="msal-libraries-supporting-mobile-apps"></a>Knihovny MSAL podporující mobilní aplikace

Knihovny Microsoftu podporující mobilní aplikace jsou:

  Knihovna MSAL | Popis
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | K vývoji přenosných aplikací. MSAL.NET podporované platformy pro sestavení mobilní aplikace jsou UWP, Xamarin. iOS a Xamarin. Android.
  ![MSAL. iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL. iOS | Vývoj nativních aplikací pro iOS s cílem – C nebo SWIFT
  ![MSAL. Svém](media/sample-v2-code/logo_android.png) <br/> MSAL. Svém | Vývoj nativních aplikací pro Android v jazyce Java pro Android

## <a name="instantiating-the-application"></a>Vytvoření instance aplikace

### <a name="android"></a>Android

Mobilní aplikace používají `PublicClientApplication` třídu. Tady je postup vytvoření instance:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Mobilní aplikace v systému iOS potřebují vytvořit instanci `MSALPublicClientApplication` třídy.

Cíl-C:

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift:
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

Existují [Další vlastnosti MSALPublicClientApplicationConfig](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) , které mohou přepsat výchozí autoritu, určit identifikátor URI pro přesměrování nebo změnit chování při ukládání tokenu MSAL. 

### <a name="xamarin-or-uwp"></a>Xamarin nebo UWP

Následující článek vysvětluje, jak vytvořit instanci aplikace pro aplikace Xamarin. iOS, Xamarin. Android a UWP.

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

## <a name="xamarin-ios-specific-considerations"></a>Konkrétní předpoklady pro Xamarin iOS

V systému Xamarin iOS je při používání MSAL.NET potřeba vzít v úvahu několik důležitých informací:

1. [Přepsání a implementace `OpenUrl` funkce v`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
1. [Povolit skupiny řetězce klíčů](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
1. [Povolit sdílení mezipaměti tokenů](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
1. [Povolit přístup k řetězci klíčů](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Podrobnosti jsou uvedené v [informacích pro Xamarin iOS](msal-net-xamarin-ios-considerations.md)

## <a name="msal-for-ios-and-macos-specific-considerations"></a>MSAL pro konkrétní otázky týkající se iOS a macOS

Podobné požadavky platí při použití MSAL pro iOS a macOS:

1. [`openURL` Implementace zpětného volání](#brokered-authentication-for-msal-for-ios-and-macos)
2. [Povolit přístupové skupiny pro řetězce klíčů](howto-v2-keychain-objc.md)
3. [Přizpůsobení prohlížečů a webových zobrazení](customize-webviews.md)

## <a name="xamarin-android-specific-considerations"></a>Konkrétní předpoklady pro Xamarin Android

Tady jsou specifiky pro Xamarin Android:

- [Zajistěte, aby se řízení vrátilo zpátky na MSAL, jakmile skončí interaktivní část toku ověřování.](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [Aktualizace manifestu pro Android](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Použít vložené webové zobrazení (volitelné)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Odstraňování potíží](msal-net-xamarin-android-considerations.md#troubleshooting)

Podrobnosti najdete v [informacích pro Xamarin Android](msal-net-xamarin-android-considerations.md) .

Nakonec existují určité informace o prohlížečích v Androidu. Jsou vysvětleny v tématu [požadavky pro Xamarin Android, které se týkají MSAL.NET](msal-net-system-browser-android-considerations.md)

#### <a name="uwp-specific-considerations"></a>Konkrétní předpoklady pro UWP

Na UWP můžete používat podnikové sítě. Další informace o tom, jak používat knihovnu MSAL s UWP, najdete v tématu [informace o Univerzální platforma Windows specifických pro použití s MSAL.NET](msal-net-uwp-considerations.md).

## <a name="configuring-the-application-to-use-the-broker"></a>Konfigurace aplikace pro použití zprostředkovatele

### <a name="why-use-brokers-in-ios-and-android-applications"></a>Proč používat zprostředkovatele v aplikacích pro iOS a Android?

V zařízeních s Androidem a iOS poskytují zprostředkovatelé tyto akce:

- Jednotné přihlašování (SSO), když je zařízení zaregistrované v AAD. Uživatelé se nebudou muset přihlašovat ke každé aplikaci.
- Identifikace zařízení. Pomocí zásad podmíněného přístupu pro zařízení Azure AD povolí přístup k certifikátu zařízení vytvořenému v zařízení, když se připojil k síti na pracovišti.
- Ověření identifikace aplikace Když aplikace volá zprostředkovatele, předá adresu URL pro přesměrování a zprostředkovatel ji ověří.

### <a name="enable-the-brokers-on-xamarin"></a>Povolení zprostředkovatelů na Xamarin

Chcete-li povolit jednu z těchto funkcí, `WithBroker()` použijte parametr při `PublicClientApplicationBuilder.CreateApplication` volání metody. `.WithBroker()`ve výchozím nastavení je nastaveno na hodnotu true. Postupujte podle následujících kroků pro [Xamarin. iOS](#brokered-authentication-for-xamarinios).

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Povolení zprostředkovatele pro MSAL pro iOS a macOS

Zprostředkované ověřování je ve výchozím nastavení povolené pro scénáře AAD v MSAL pro iOS a macOS. Podle následujících kroků nakonfigurujte aplikaci pro podporu zprostředkovaných ověřování pro [MSAL pro iOS a MacOS](#brokered-authentication-for-msal-for-ios-and-macos). Všimněte si, že některé kroky se liší od [MSAL pro Xamarin. iOS](#brokered-authentication-for-xamarinios) a [MSAL pro iOS a MacOS](#brokered-authentication-for-msal-for-ios-and-macos).

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

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Zprostředkované ověřování pro MSAL pro iOS a macOS

Zprostředkované ověřování je ve výchozím nastavení povolené pro scénáře AAD.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Krok 1: Aktualizace AppDelegate pro zpracování zpětného volání

Když MSAL pro iOS a MacOS volá zprostředkovatele, zprostředkovatel pak zase volá zpět do vaší aplikace prostřednictvím `openURL` metody. Vzhledem k tomu, že MSAL bude čekat na odpověď od služby Broker, musí vaše aplikace spolupracovat a volat MSAL zpátky. Provedete to tak, `AppDelegate.m` že aktualizujete soubor, abyste přepsali níže uvedenou metodu.

Cíl-C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

Všimněte si, že pokud jste přijali UISceneDelegate pro iOS 13 +, je třeba místo toho umístit MSAL `scene:openURLContexts:` zpětné volání do UISceneDelegate (viz [Dokumentace společnosti Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)). MSAL `handleMSALResponse:sourceApplication:` se musí volat jenom jednou pro každou adresu URL.

#### <a name="step-2-register-a-url-scheme"></a>Krok 2: Registrace schématu adresy URL

MSAL pro iOS a macOS používá adresy URL k vyvolání zprostředkovatele a pak vrátí odpověď zprostředkovatele zpátky do vaší aplikace. Pokud chcete dokončit zpáteční cestu, musíte v `Info.plist` souboru zaregistrovat schéma URL pro vaši aplikaci.

Použijte předponu vlastního schématu `msauth`URL. Pak na konec přidejte **identifikátor sady prostředků** .

`msauth.(BundleId)`

**Například:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Toto schéma URL se stane součástí RedirectUri, který slouží k jednoznačné identifikaci vaší aplikace při přijímání odpovědi od zprostředkovatele. Ujistěte se, že RedirectUri ve formátu `msauth.(BundleId)://auth` aplikace je zaregistrován na webu [Azure Portal](https://portal.azure.com).

```XML
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-lsapplicationqueriesschemes"></a>Krok 3: LSApplicationQueriesSchemes

**Přidat`LSApplicationQueriesSchemes`** pro povolení volání Microsoft Authenticator, pokud je nainstalováno.
Všimněte si, že při kompilování aplikace s Xcode 11 a novějším je potřeba schéma "msauthv3". 

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Zprostředkované ověřování pro Xamarin. Android

MSAL.NET ještě nepodporuje zprostředkovatele pro Android.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Získání tokenu](scenario-mobile-acquire-token.md)
