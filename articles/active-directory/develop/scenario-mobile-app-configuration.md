---
title: Konfigurace mobilních aplikací, které volají webová rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak vytvořit mobilní aplikaci, která volá webová API (konfigurace kódu aplikace)
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
ms.openlocfilehash: fc25f13d0b0b8a264dcd47a5fdebb0533e93fb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132491"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>Konfigurace mobilní aplikace, která volá webová rozhraní API

Po vytvoření aplikace se dozvíte, jak nakonfigurovat kód pomocí parametrů registrace aplikace. Mobilní aplikace představují některé složitosti související s přizpůsobením se jejich vytvoření rámce.

## <a name="find-msal-support-for-mobile-apps"></a>Najít podporu MSAL pro mobilní aplikace

Mobilní aplikace podporují následující typy Knihovny ověřování Microsoft (MSAL).

MSAL | Popis
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Používá se k vývoji přenosných aplikací. MSAL.NET podporuje následující platformy pro vytváření mobilní aplikace: Univerzální platforma Windows (UPW), Xamarin.iOS a Xamarin.Android.
![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | Používá se k vývoji nativních aplikací pro iOS pomocí Objective-C nebo Swift.
![MSAL. Android](media/sample-v2-code/logo_android.png) <br/> MSAL. Android | Používá se k vývoji nativních aplikací pro Android v Javě pro Android.

## <a name="instantiate-the-application"></a>Vytvoření instance aplikace

### <a name="android"></a>Android

Mobilní aplikace `PublicClientApplication` používají třídu. Zde je návod, jak konkretizovat:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Mobilní aplikace v iOS je třeba `MSALPublicClientApplication` vytvořit konkretizovat třídu. Chcete-li vytvořit instanci třídy, použijte následující kód. 

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[Další vlastnosti MSALPublicClientApplicationConfig](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) mohou přepsat výchozí autoritu, zadat identifikátor URI přesměrování nebo změnit chování ukládání tokenů MSAL do mezipaměti. 

### <a name="xamarin-or-uwp"></a>Xamarin nebo UPW

Tato část vysvětluje, jak vytvořit konkretizovat aplikaci pro aplikace Xamarin.iOS, Xamarin.Android a UPW.

#### <a name="instantiate-the-application"></a>Vytvoření instance aplikace

V Xamarin nebo UPW nejjednodušší způsob, jak vytvořit instanci aplikace je pomocí následujícího kódu. V tomto `ClientId` kódu je identifikátor GUID registrované aplikace.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Další `With<Parameter>` metody nastaví nadřazený objekt uživatelského rozhraní, přepíší výchozí autoritu, zaurčují název klienta a verzi pro telemetrii, zadejte identifikátor URI přesměrování a zadejte továrnu HTTP, která se má použít. Továrna HTTP může být použita například ke zpracování proxy serverů a k určení telemetrie a protokolování. 

Následující části obsahují další informace o vytváření instancí aplikace.

##### <a name="specify-the-parent-ui-window-or-activity"></a>Určení nadřazeného ui, okna nebo aktivity

V systému Android musíte před interaktivním ověřováním předat nadřazenou aktivitu. V iOS, když používáte makléře, musíte `ViewController`předat . Stejným způsobem na UPW, můžete chtít předat v nadřazeném okně. Předáte ji, když získáte token. Ale při vytváření aplikace, můžete také zadat zpětné volání jako `UIParent`delegát, který vrátí .

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

V systému Android doporučujeme používat [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin). Výsledný `PublicClientApplication` kód tvůrce vypadá takto:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>Najít další parametry vytváření aplikací

Seznam všech metod, které jsou `PublicClientApplicationBuilder`k dispozici na , naleznete v [seznamu Metody](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).

Popis všech možností, které jsou `PublicClientApplicationOptions`vystaveny v aplikaci , naleznete v [referenční dokumentaci](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions).

## <a name="tasks-for-xamarin-ios"></a>Úkoly pro Xamarin iOS

Pokud používáte MSAL.NET na Xamarin iOS, proveďte následující úkoly.

* [Přepsat a implementovat `OpenUrl` funkci v`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [Povolení skupin řetězce klíčů](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [Povolení sdílení mezipaměti tokenů](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [Povolení přístupu ke klíčence](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Další informace naleznete v [tématu Xamarin iOS aspekty](msal-net-xamarin-ios-considerations.md).

## <a name="tasks-for-msal-for-ios-and-macos"></a>Úkoly pro MSAL pro iOS a macOS

Tyto úkoly jsou nezbytné při použití MSAL pro iOS a macOS:

* [Implementace `openURL` zpětného volání](#brokered-authentication-for-msal-for-ios-and-macos)
* [Povolení přístupových skupin řetězce klíčů](howto-v2-keychain-objc.md)
* [Přizpůsobení prohlížečů a webových zobrazení](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Úkoly pro Xamarin.Android

Pokud používáte Xamarin.Android, proveďte následující úkoly:

- [Ujistěte se, že řízení přejde zpět do MSAL po ukončení interaktivní části toku ověřování](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Aktualizace manifestu Android](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Použití vloženého webového zobrazení (volitelné)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Řešení potíží podle potřeby](msal-net-xamarin-android-considerations.md#troubleshoot)

Další informace naleznete v tématu [Xamarin.Android aspekty](msal-net-xamarin-android-considerations.md).

Důležité informace o prohlížečích v systému Android naleznete v [tématu Xamarin.Android specifické aspekty s MSAL.NET](msal-net-system-browser-android-considerations.md).

#### <a name="tasks-for-uwp"></a>Úkoly pro UPW

V UPW můžete používat podnikové sítě. V následujících částech jsou vysvětleny úkoly, které byste měli provést v podnikovém scénáři.

Další informace naleznete [v tématu důležité informace o uwp s MSAL.NET](msal-net-uwp-considerations.md).

## <a name="configure-the-application-to-use-the-broker"></a>Konfigurace aplikace pro použití zprostředkovatele

V systémech Android a iOS makléři umožňují:

- **Jednotné přihlašování :** Jednotné přihlašování můžete použít pro zařízení, která jsou registrovaná ve službě Azure Active Directory (Azure AD). Při použití služby SSO, vaši uživatelé nemusí přihlásit ke každé aplikaci.
- **Identifikace zařízení**: Toto nastavení umožňuje zásady podmíněného přístupu, které se vztahují k zařízením Azure AD. Proces ověřování používá certifikát zařízení, který byl vytvořen při připojení zařízení k pracovišti.
- **Ověření identifikace aplikace**: Když aplikace zavolá zprostředkovatele, předá adresu URL přesměrování. Pak to makléř ověří.

### <a name="enable-the-broker-on-xamarin"></a>Povolit makléře na Xamarinu

Chcete-li povolit zprostředkovatele na `WithBroker()` Xamarin, `PublicClientApplicationBuilder.CreateApplication` použijte parametr při volání metody. Ve výchozím `.WithBroker()` nastavení je nastavena na hodnotu true. 

Chcete-li povolit zprostředkované ověřování pro Xamarin.iOS, postupujte podle pokynů v [části Xamarin.iOS](#enable-brokered-authentication-for-xamarin-ios) v tomto článku.

### <a name="enable-the-broker-for-msal-for-android"></a>Povolit zprostředkovatele pro MSAL pro Android

Informace o povolení broker a Android, najdete v [tématu Brokered ověřování v systému Android](brokered-auth.md). 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Povolení zprostředkovatele pro MSAL pro iOS a macOS

Zprostředkované ověřování je ve výchozím nastavení povolené pro scénáře Azure AD v MSAL pro iOS a macOS. 

Následující části obsahují pokyny ke konfiguraci aplikace pro podporu zprostředkovaného ověřování pro MSAL pro Xamarin.iOS nebo MSAL pro iOS a macOS. V obou sadách pokynů se některé kroky liší.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Povolení zprostředkovaného ověřování pro Xamarin iOS

Podle pokynů v této části povolte aplikaci Xamarin.iOS mluvit s aplikací [Microsoft Authenticator.](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)

#### <a name="step-1-enable-broker-support"></a>Krok 1: Povolit podporu zprostředkovatele

Podpora brokera je ve výchozím nastavení zakázána. Povolíte pro jednotlivé `PublicClientApplication` třídy. Parametr `WithBroker()` použijte při vytváření `PublicClientApplication` třídy prostřednictvím `PublicClientApplicationBuilder`. Parametr `WithBroker()` je ve výchozím nastavení nastaven na hodnotu true.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Krok 2: Aktualizace appdelegate pro zpracování zpětného volání

Když MSAL.NET zavolá makléře, makléř pak zavolá zpět do vaší aplikace. Volá zpět pomocí `AppDelegate.OpenUrl` metody. Vzhledem k tomu, že MSAL čeká na odpověď od zprostředkovatele, vaše aplikace musí spolupracovat volat MSAL.NET zpět. Toto chování nastavíte `AppDelegate.cs` aktualizací souboru přepsat metodu, jak ukazuje následující kód.

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

Tato metoda je vyvolána při každém spuštění aplikace. Je to příležitost zpracovat odpověď od zprostředkovatele a dokončit proces ověřování, který MSAL.NET spuštěn.

#### <a name="step-3-set-a-uiviewcontroller"></a>Krok 3: Nastavení UIViewController()

Pro Xamarin iOS obvykle není nutné nastavit okno objektu. Ale v tomto případě byste měli nastavit tak, že můžete odesílat a přijímat odpovědi od makléře. Chcete-li nastavit okno `AppDelegate.cs`objektu, `ViewController`nastavte v aplikaci .

Chcete-li nastavit okno objektu, postupujte takto:

1. V `AppDelegate.cs`, `App.RootViewController` nastavte na `UIViewController()`nový . Toto nastavení zajišťuje, že volání `UIViewController`zprostředkovatele zahrnuje . Pokud není správně nastavena, může se zobrazí tato chyba:

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. Při `AcquireTokenInteractive` volání použijte `.WithParentActivityOrWindow(App.RootViewController)`. Předajte odkaz na okno objektu, které budete používat. Tady je příklad:

    V `App.cs`:
    ```csharp
       public static object RootViewController { get; set; }
    ```
    V `AppDelegate.cs`:
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    Ve `AcquireToken` výzvě:
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```
    
#### <a name="step-4-register-a-url-scheme"></a>Krok 4: Registrace schématu adres URL

MSAL.NET používá adresy URL k vyvolání zprostředkovatele a pak vrátit odpověď zprostředkovatele zpět do vaší aplikace. Chcete-li dokončit odezvu, zaregistrujte schéma `Info.plist` adres URL aplikace v souboru. 

Pokud chcete zaregistrovat schéma adres URL aplikace, postupujte takto:

1. `CFBundleURLSchemes` Předpona `msauth`s . 
1. Přidejte `CFBundleURLName` na konec. Postupujte podle tohoto vzoru: 

   `$"msauth.(BundleId)"`

   Zde `BundleId` jednoznačně identifikuje vaše zařízení. Pokud `BundleId` je `yourcompany.xforms`například , je `msauth.com.yourcompany.xforms`schéma adresy URL .
    
   > [!NOTE]
   > Toto schéma adres URL se stane součástí identifikátoru URI přesměrování, který jednoznačně identifikuje vaši aplikaci, když obdrží odpověď zprostředkovatele.
    
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
    
#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>Krok 5: Přidání do oddílu LSApplicationQueriesSchemes

MSAL `–canOpenURL:` používá ke kontrole, zda je v zařízení nainstalován zprostředkovatel. V iOS 9 Apple uzamkl schémata, na která se aplikace může dotazovat.

Přidejte `msauthv2` `LSApplicationQueriesSchemes` do části `Info.plist` souboru, jako v následujícím příkladu kódu:

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Zprostředkované ověřování pro MSAL pro iOS a macOS

Zprostředkované ověřování je ve výchozím nastavení povolené pro scénáře Azure AD.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Krok 1: Aktualizace appdelegate pro zpracování zpětného volání

Když MSAL pro iOS a macOS volá zprostředkovatele, zprostředkovatel `openURL` volá zpět do vaší aplikace pomocí metody. Vzhledem k tomu, že MSAL čeká na odpověď od zprostředkovatele, vaše aplikace musí spolupracovat na volání zpět MSAL. Nastavte tuto funkci aktualizací `AppDelegate.m` souboru přepsat metodu, jak ukazují následující příklady kódu.

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

> [!NOTE]
> Pokud jste `UISceneDelegate` přijali na iOS 13 nebo novější, pak `scene:openURLContexts:` `UISceneDelegate` místo volání MSAL do of instead. MSAL `handleMSALResponse:sourceApplication:` musí být volána pouze jednou pro každou adresu URL.
>
> Další informace naleznete v [dokumentaci společnosti Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc).

#### <a name="step-2-register-a-url-scheme"></a>Krok 2: Registrace schématu adres URL

MSAL pro iOS a macOS používá adresy URL k vyvolání zprostředkovatele a pak vrátit odpověď zprostředkovatele do vaší aplikace. Chcete-li dokončit zpáteční cestu, zaregistrujte schéma `Info.plist` adres URL pro vaši aplikaci v souboru.

Registrace schématu pro vaši aplikaci: 

1. Předpona vlastního schématu adres URL pomocí aplikace `msauth`. 

1. Přidejte identifikátor sady na konec schématu. Postupujte podle tohoto vzoru: 

   `$"msauth.(BundleId)"`

   Zde `BundleId` jednoznačně identifikuje vaše zařízení. Pokud `BundleId` je `yourcompany.xforms`například , je `msauth.com.yourcompany.xforms`schéma adresy URL .
  
   > [!NOTE]
   > Toto schéma adres URL se stane součástí identifikátoru URI přesměrování, který jednoznačně identifikuje vaši aplikaci, když obdrží odpověď zprostředkovatele. Ujistěte se, že identifikátor `msauth.(BundleId)://auth` URI přesměrování ve formátu je registrovaný pro vaši aplikaci na [webu Azure Portal](https://portal.azure.com).
  
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

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>Krok 3: Přidání lSApplicationQueriesSchemes

Chcete-li `LSApplicationQueriesSchemes` povolit volání do aplikace Microsoft Authenticator, pokud je nainstalovaná.

> [!NOTE]
> Schéma `msauthv3` je potřeba, když je vaše aplikace kompilována pomocí Xcode 11 a novějšího. 

Zde je příklad, jak `LSApplicationQueriesSchemes`přidat :

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Zprostředkované ověřování pro Xamarin.Android

MSAL.NET nepodporuje makléře pro Android.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Získání tokenu](scenario-mobile-acquire-token.md)
