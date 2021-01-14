---
title: Konfigurace mobilních aplikací, které volají webová rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak nakonfigurovat kód mobilní aplikace pro volání webového rozhraní API.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/16/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e2c632de3d602fe2d3e5bfa74f78e90f48412067
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208909"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>Konfigurace mobilní aplikace, která volá webová rozhraní API

Po vytvoření aplikace se dozvíte, jak nakonfigurovat kód pomocí parametrů registrace aplikace. Mobilní aplikace představují některé složitosti, které souvisí s přizpůsobením do jejich prostředí pro vytváření.

## <a name="find-msal-support-for-mobile-apps"></a>Najít podporu MSAL pro mobilní aplikace

Následující typy Microsoft Authentication Library (MSAL) podporují mobilní aplikace.

MSAL | Popis
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Slouží k vývoji přenosných aplikací. MSAL.NET podporuje následující platformy pro vytváření mobilních aplikací: Univerzální platforma Windows (UWP), Xamarin. iOS a Xamarin. Android.
![MSAL. iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL. iOS | Slouží k vývoji nativních aplikací pro iOS pomocí objektivu-C nebo SWIFT.
![MSAL. Svém](media/sample-v2-code/logo_android.png) <br/> MSAL. Svém | Slouží k vývoji nativních aplikací pro Android v jazyce Java pro Android.

## <a name="instantiate-the-application"></a>Vytvoření instance aplikace

### <a name="android"></a>Android

Mobilní aplikace používají `PublicClientApplication` třídu. Tady je postup vytvoření instance:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Mobilní aplikace v systému iOS potřebují vytvořit instanci `MSALPublicClientApplication` třídy. Chcete-li vytvořit instanci třídy, použijte následující kód.

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[Další vlastnosti MSALPublicClientApplicationConfig](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) můžou přepsat výchozí autoritu, určit identifikátor URI pro přesměrování nebo změnit chování ukládání do mezipaměti tokenu MSAL.

### <a name="xamarin-or-uwp"></a>Xamarin nebo UWP

V této části se dozvíte, jak vytvořit instanci aplikace pro aplikace Xamarin. iOS, Xamarin. Android a UWP.

#### <a name="instantiate-the-application"></a>Vytvoření instance aplikace

V Xamarin nebo UWP je nejjednodušší způsob, jak vytvořit instanci aplikace, pomocí následujícího kódu. V tomto kódu `ClientId` je identifikátor GUID vaší registrované aplikace.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Další `With<Parameter>` metody nastaví nadřazený ovládací prvek uživatelského rozhraní, přepíší výchozí autoritu, určíte název a verzi klienta pro telemetrii, určíte identifikátor URI pro přesměrování a určíte továrnu http, která se má použít. Objekt pro vytváření HTTP se může použít například ke zpracování proxy serverů a k určení telemetrie a protokolování.

Následující části obsahují další informace o vytváření instancí aplikace.

##### <a name="specify-the-parent-ui-window-or-activity"></a>Zadání nadřazeného uživatelského rozhraní, okna nebo aktivity

V Androidu je nutné před provedením interaktivního ověřování předat nadřazenou aktivitu. Když v systému iOS používáte zprostředkovatele, musíte se předat `ViewController` . Stejně jako u UWP byste mohli chtít předat nadřazenému oknu. Po získání tokenu ho předáte. Ale při vytváření aplikace můžete také určit zpětné volání jako delegát, který vrátí `UIParent` .

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

V Androidu doporučujeme použít [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin) . Výsledný `PublicClientApplication` Tvůrce kódu vypadá jako v tomto příkladu:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>Najít další parametry sestavování aplikací

Seznam všech metod, které jsou k dispozici v systému `PublicClientApplicationBuilder` , naleznete v [seznamu metod](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).

Popis všech možností, které jsou k dispozici v nástroji `PublicClientApplicationOptions` , naleznete v [referenční dokumentaci](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions).

## <a name="tasks-for-xamarin-ios"></a>Úkoly pro Xamarin iOS

Pokud používáte MSAL.NET v Xamarin iOS, proveďte následující úkoly.

* [Přepsání a implementace `OpenUrl` funkce v `AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [Povolit skupiny řetězce klíčů](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [Povolit sdílení mezipaměti tokenů](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [Povolit přístup k řetězci klíčů](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Další informace najdete v tématu [předpoklady pro Xamarin iOS](msal-net-xamarin-ios-considerations.md).

## <a name="tasks-for-msal-for-ios-and-macos"></a>Úlohy pro MSAL pro iOS a macOS

Tyto úlohy jsou nezbytné při použití MSAL pro iOS a macOS:

* [Implementace `openURL` zpětného volání](#brokered-authentication-for-msal-for-ios-and-macos)
* [Povolit přístupové skupiny pro řetězce klíčů](howto-v2-keychain-objc.md)
* [Přizpůsobení prohlížečů a webových zobrazení](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Úkoly pro Xamarin. Android

Pokud používáte Xamarin. Android, proveďte následující úlohy:

- [Zajistěte, aby se řízení vrátilo zpátky na MSAL po ukončení interaktivní části toku ověřování.](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Aktualizace manifestu pro Android](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Použít vložené webové zobrazení (volitelné)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Řešení potíží podle potřeby](msal-net-xamarin-android-considerations.md#troubleshooting)

Další informace najdete v tématu [požadavky pro Xamarin. Android](msal-net-xamarin-android-considerations.md).

Informace o prohlížečích v Androidu najdete v tématu [předpoklady pro Xamarin. Android pro MSAL.NET](msal-net-system-browser-android-considerations.md).

#### <a name="tasks-for-uwp"></a>Úlohy pro UWP

Na UWP můžete používat podnikové sítě. V následujících částech jsou vysvětleny úlohy, které byste měli provést v podnikovém scénáři.

Další informace najdete v tématu věnovaném [důležitým hledisku pro UWP pomocí MSAL.NET](msal-net-uwp-considerations.md).

## <a name="configure-the-application-to-use-the-broker"></a>Konfigurace aplikace pro použití zprostředkovatele

V zařízeních s Androidem a iOS poskytují zprostředkovatelé tyto akce:

- **Jednotné přihlašování (SSO)**: jednotné přihlašování můžete použít pro zařízení zaregistrovaná ve službě Azure Active Directory (Azure AD). Když použijete jednotné přihlašování, uživatelé se nemusí přihlašovat ke každé aplikaci.
- **Identifikace zařízení**: Toto nastavení umožňuje zásady podmíněného přístupu, které se vztahují k zařízením Azure AD. Proces ověřování používá certifikát zařízení, který byl vytvořen v době, kdy bylo zařízení připojeno k pracovní ploše.
- **Ověření identifikace aplikace**: když aplikace volá zprostředkovatele, PŘEDÁ adresu URL pro přesměrování. Zprostředkovatel je pak ověří.

### <a name="enable-the-broker-on-xamarin"></a>Povolení zprostředkovatele v Xamarin

Chcete-li povolit zprostředkovatele na Xamarin, použijte `WithBroker()` parametr při volání `PublicClientApplicationBuilder.CreateApplication` metody. Ve výchozím nastavení `.WithBroker()` je nastavena na hodnotu true.

Pokud chcete povolit zprostředkované ověřování pro Xamarin. iOS, postupujte podle kroků v [části Xamarin. iOS](#enable-brokered-authentication-for-xamarin-ios) v tomto článku.

### <a name="enable-the-broker-for-msal-for-android"></a>Povolení zprostředkovatele pro MSAL pro Android

Informace o povolení zprostředkovatele v Androidu najdete v tématu zprostředkované [ověřování na Androidu](msal-android-single-sign-on.md).

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Povolení zprostředkovatele pro MSAL pro iOS a macOS

Zprostředkované ověřování je ve výchozím nastavení povolené pro scénáře Azure AD v MSAL pro iOS a macOS.

Následující části obsahují pokyny ke konfiguraci aplikace pro podporu zprostředkovaných ověřování pro MSAL pro Xamarin. iOS nebo MSAL pro iOS a macOS. V těchto dvou sadách instrukcí se některé kroky liší.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Povolit zprostředkované ověřování pro Xamarin iOS

Postupujte podle kroků v této části, abyste aplikaci Xamarin. iOS mohli komunikovat s aplikací [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

#### <a name="step-1-enable-broker-support"></a>Krok 1: povolení podpory zprostředkovatele

Podpora zprostředkovatele je ve výchozím nastavení zakázaná. Povolíte ji pro jednotlivé `PublicClientApplication` třídy. Použijte `WithBroker()` parametr při vytváření `PublicClientApplication` třídy prostřednictvím `PublicClientApplicationBuilder` . `WithBroker()`Ve výchozím nastavení je parametr nastaven na hodnotu true.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Krok 2: aktualizace AppDelegate pro zpracování zpětného volání

Když MSAL.NET volá zprostředkovatele, zprostředkovatel pak zavolá zpět do vaší aplikace. Volá zpět pomocí `AppDelegate.OpenUrl` metody. Vzhledem k tomu, že MSAL čeká na odpověď od služby Broker, musí vaše aplikace spolupracovat a volat MSAL.NET zpátky. Toto chování nastavíte tak, že aktualizujete `AppDelegate.cs` soubor pro přepsání metody, jak ukazuje následující kód.

```csharp
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

Tato metoda je vyvolána při každém spuštění aplikace. Je příležitost zpracovat odpověď od služby Broker a dokončit proces ověřování, který MSAL.NET zahájil.

#### <a name="step-3-set-a-uiviewcontroller"></a>Krok 3: nastavení UIViewController ()

Pro Xamarin iOS nemusíte normálně nastavovat okno objektu. V takovém případě byste však měli nastavit, aby bylo možné odesílat a přijímat odpovědi od zprostředkovatele. Chcete-li nastavit okno objektu, v `AppDelegate.cs` , nastavte `ViewController` .

Chcete-li nastavit okno objektu, postupujte podle následujících kroků:

1. V portálu `AppDelegate.cs` nastavte na `App.RootViewController` nový `UIViewController()` . Toto nastavení zajišťuje, že volání do služby Broker zahrnuje `UIViewController` . Pokud není správně nastavená, může se zobrazit tato chyba:

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. Při `AcquireTokenInteractive` volání použijte `.WithParentActivityOrWindow(App.RootViewController)` . Předejte odkaz na okno objektu, které budete používat. Tady je příklad:

    V `App.cs`:
    ```csharp
       public static object RootViewController { get; set; }
    ```
    V `AppDelegate.cs`:
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    V `AcquireToken` volání:
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

#### <a name="step-4-register-a-url-scheme"></a>Krok 4: registrace schématu adresy URL

MSAL.NET používá adresy URL k vyvolání zprostředkovatele a pak vrátí odpověď zprostředkovatele zpátky do vaší aplikace. Pokud chcete dokončit zpáteční cestu, zaregistrujte do souboru schéma URL vaší aplikace `Info.plist` .

Pokud chcete zaregistrovat schéma URL vaší aplikace, použijte následující postup:

1. Předpona `CFBundleURLSchemes` s `msauth` .
1. Přidejte `CFBundleURLName` na konec. Postupujte podle tohoto vzoru:

   `$"msauth.(BundleId)"`

   Tady můžete `BundleId` zařízení jednoznačně identifikovat. Například pokud `BundleId` je `yourcompany.xforms` , vaše schéma URL je `msauth.com.yourcompany.xforms` .

  
      Toto schéma URL se stane součástí identifikátoru URI přesměrování, který jedinečně identifikuje vaši aplikaci, když obdrží odpověď zprostředkovatele.

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

#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>Krok 5: přidání do oddílu LSApplicationQueriesSchemes

MSAL používá `–canOpenURL:` ke kontrole, jestli je na zařízení nainstalovaný zprostředkovatel. V systému iOS 9 byla společnost Apple uzamčena schématy, pro které se aplikace může dotazovat.

Přidejte `msauthv2` do `LSApplicationQueriesSchemes` části `Info.plist` souboru, jako v následujícím příkladu kódu:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Zprostředkované ověřování pro MSAL pro iOS a macOS

Zprostředkované ověřování je ve výchozím nastavení povolené pro scénáře Azure AD.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Krok 1: aktualizace AppDelegate pro zpracování zpětného volání

Když MSAL pro iOS a macOS volá zprostředkovatele, zprostředkovatel volá zpět do vaší aplikace pomocí `openURL` metody. Vzhledem k tomu, že MSAL čeká na odpověď od služby Broker, musí vaše aplikace spolupracovat na zpětné volání MSAL. Tuto schopnost nastavte tak, že aktualizujete `AppDelegate.m` soubor pro přepsání metody, jak ukazuje následující příklad kódu.

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

Pokud jste přijali `UISceneDelegate` v systému iOS 13 nebo novějším, umístěte zpětné volání MSAL `scene:openURLContexts:` na `UISceneDelegate` místo. MSAL `handleMSALResponse:sourceApplication:` se musí volat jenom jednou pro každou adresu URL.

Další informace najdete v [dokumentaci společnosti Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc).

#### <a name="step-2-register-a-url-scheme"></a>Krok 2: registrace schématu adresy URL

MSAL pro iOS a macOS používá adresy URL k vyvolání zprostředkovatele a pak vrátí odpověď zprostředkovatele do vaší aplikace. Pokud chcete dokončit zpáteční cestu, zaregistrujte do souboru schéma URL pro vaši aplikaci `Info.plist` .

Postup při registraci schématu pro aplikaci:

1. Použijte předponu vlastního schématu URL `msauth` .

1. Přidejte svůj identifikátor sady prostředků na konec vašeho schématu. Postupujte podle tohoto vzoru:

   `$"msauth.(BundleId)"`

   Tady můžete `BundleId` zařízení jednoznačně identifikovat. Například pokud `BundleId` je `yourcompany.xforms` , vaše schéma URL je `msauth.com.yourcompany.xforms` .

    Toto schéma URL se stane součástí identifikátoru URI přesměrování, který jedinečně identifikuje vaši aplikaci, když obdrží odpověď zprostředkovatele. Ujistěte se, že identifikátor URI přesměrování ve formátu `msauth.(BundleId)://auth` je zaregistrován pro vaši aplikaci v [Azure Portal](https://portal.azure.com).

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

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>Krok 3: Přidání LSApplicationQueriesSchemes

Přidejte `LSApplicationQueriesSchemes` pro povolení volání aplikace Microsoft Authenticator, pokud je nainstalovaná.

> [!NOTE]
> `msauthv3`Schéma je potřeba, když se vaše aplikace zkompiluje pomocí Xcode 11 a novějších.

Tady je příklad, jak přidat `LSApplicationQueriesSchemes` :

```XML
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Zprostředkované ověřování pro Xamarin. Android

Informace o povolení zprostředkovatele v Androidu najdete v tématu zprostředkované [ověřování na Xamarin. Android](msal-net-use-brokers-with-xamarin-apps.md#brokered-authentication-for-android).

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři, který získá [token](scenario-mobile-acquire-token.md).