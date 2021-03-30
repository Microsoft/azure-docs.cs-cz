---
title: Přizpůsobení prohlížečů & webviews (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak přizpůsobit prostředí MSAL pro iOS/macOS pro uživatele, kteří se přihlásí.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: a8486ec87b5198231a33b1dab382ba457c8c8066
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85478123"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>Postupy: přizpůsobení prohlížečů a webových zobrazení pro iOS/macOS

Pro interaktivní ověřování je vyžadován webový prohlížeč. V systémech iOS a macOS 10.15 + služba Microsoft Authentication Library (MSAL) standardně používá systémový webový prohlížeč (který se může zobrazit v horní části vaší aplikace), aby se přihlásili uživatelům pomocí interaktivního ověřování. Použití prohlížeče systému má výhodu sdílení stavu jednotného přihlašování (SSO) s jinými aplikacemi a webovými aplikacemi.

Můžete změnit prostředí úpravou konfigurace na jiné možnosti zobrazení webového obsahu, jako například:

Jenom pro iOS:

- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

Pro iOS a macOS:

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

MSAL pro macOS podporuje jenom `WKWebView` starší verze operačního systému. `ASWebAuthenticationSession` je podporován pouze v macOS 10,15 a vyšších. 

## <a name="system-browsers"></a>Systémové prohlížeče

Pro iOS, `ASWebAuthenticationSession` , `SFAuthenticationSession` a `SFSafariViewController` se považují za systémové prohlížeče. Pro macOS `ASWebAuthenticationSession` je k dispozici pouze. Obecně platí, že systémové prohlížeče sdílí soubory cookie a jiná data webu pomocí prohlížeče Safari.

Ve výchozím nastavení MSAL dynamicky detekuje verzi iOS a vybere doporučený prohlížeč systému, který je v této verzi k dispozici. V systému iOS 12 + bude `ASWebAuthenticationSession` . 

### <a name="default-configuration-for-ios"></a>Výchozí konfigurace pro iOS

| Verze | Webový prohlížeč |
|:-------------:|:-------------:|
| iOS 12 + | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

### <a name="default-configuration-for-macos"></a>Výchozí konfigurace pro macOS

| Verze | Webový prohlížeč |
|:-------------:|:-------------:|
| macOS 10.15 + | ASWebAuthenticationSession |
| jiné verze | WKWebView |

Vývojáři můžou také vybrat jiný systémový prohlížeč pro aplikace MSAL:

- `SFAuthenticationSession` je verze iOS 11 systému `ASWebAuthenticationSession` .
- `SFSafariViewController` je obecnější a poskytuje rozhraní pro procházení webu a dá se použít i pro účely přihlášení. V systému iOS 9 a 10 se soubory cookie a další webová data sdílí s Safari, ale ne v iOS 11 a novějších.

## <a name="in-app-browser"></a>Prohlížeč v aplikaci

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) je prohlížeč v aplikaci, který zobrazuje webový obsah. Nesdílí soubory cookie a data webu s jinými **WKWebView** instancemi nebo s prohlížečem Safari. WKWebView je prohlížeč pro více platforem, který je dostupný pro iOS i pro macOS.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Sdílení souborů cookie a důsledky jednotného přihlašování (SSO)

Prohlížeč, který použijete, má vliv na možnosti jednotného přihlašování z důvodu sdílení souborů cookie. Následující tabulky shrnují možnosti jednotného přihlašování na prohlížeč.

| Technologie    | Typ prohlížeče  | dostupnost iOS | dostupnost macOS | Sdílí soubory cookie a jiná data  | Dostupnost MSAL | Jednotné přihlašování |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | Systém | iOS12 a nahoru | macOS 10,15 a až | Ano | iOS a macOS 10.15 + | instance w/Safari
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | Systém | iOS11 a nahoru | Není k dispozici | Ano | Jenom iOS |  instance w/Safari
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | Systém | iOS11 a nahoru | – | Ne | Jenom iOS | Ne * *
| **SFSafariViewController** | Systém | iOS10 | Není k dispozici | Ano | Jenom iOS |  instance w/Safari
| **WKWebView**  | V aplikaci | iOS8 a nahoru | macOS 10,10 a až | Ne | iOS a macOS | Ne * *

* * Aby jednotné přihlašování fungovalo, musí být tokeny sdílené mezi aplikacemi. To vyžaduje mezipaměť tokenů nebo aplikaci zprostředkovatele, například Microsoft Authenticator pro iOS.

## <a name="change-the-default-browser-for-the-request"></a>Změna výchozího prohlížeče pro požadavek

Pomocí prohlížeče v aplikaci nebo konkrétního webového prohlížeče v závislosti na vašich požadavcích na uživatelské prostředí můžete změnit následující vlastnost v nástroji `MSALWebviewParameters` :

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Změnit na interaktivní požadavek

Každý požadavek lze nakonfigurovat tak, aby potlačil výchozí prohlížeč změnou `MSALInteractiveTokenParameters.webviewParameters.webviewType` vlastnosti před předáním do `acquireTokenWithParameters:completionBlock:` rozhraní API.

Kromě toho MSAL podporuje předávání vlastní nastavením `WKWebView` `MSALInteractiveTokenParameters.webviewParameters.customWebView` Vlastnosti.

Například:

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(authPresentationViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

Pokud používáte vlastní webové zobrazení, oznámení se používají k označení stavu zobrazeného webového obsahu, například:

```objc
/*! Fired at the start of a resource load in the webview. The URL of the load, if available, will be in the @"url" key in the userInfo dictionary */
extern NSString *MSALWebAuthDidStartLoadNotification;

/*! Fired when a resource finishes loading in the webview. */
extern NSString *MSALWebAuthDidFinishLoadNotification;

/*! Fired when web authentication fails due to reasons originating from the network. Look at the @"error" key in the userInfo dictionary for more details.*/
extern NSString *MSALWebAuthDidFailNotification;

/*! Fired when authentication finishes */
extern NSString *MSALWebAuthDidCompleteNotification;

/*! Fired before ADAL invokes the broker app */
extern NSString *MSALWebAuthWillSwitchToBrokerApp;
```

### <a name="options"></a>Možnosti

Všechny typy webových prohlížečů podporované MSAL jsou deklarované ve [výčtu MSALWebviewType](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47) .

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
    /**
     For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession or SFAuthenticationSession).
     For older versions, with AuthenticationSession not being available, uses SafariViewController.
     For macOS 10.15 and above uses ASWebAuthenticationSession
     For older macOS versions uses WKWebView
     */
    MSALWebviewTypeDefault,
    
    /** Use ASWebAuthenticationSession where available.
     On older iOS versions uses SFAuthenticationSession
     Doesn't allow any other webview type, so if either of these are not present, fails the request*/
    MSALWebviewTypeAuthenticationSession,
    
#if TARGET_OS_IPHONE
    
    /** Use SFSafariViewController for all versions. */
    MSALWebviewTypeSafariViewController,
    
#endif
    /** Use WKWebView */
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>Další kroky

Další informace o [tocích ověřování a scénářích aplikací](authentication-flows-app-scenarios.md)
