---
title: Přizpůsobení prohlížečů a webových zobrazení
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak přizpůsobit prostředí prohlížeče používané MSAL pro iOS a macOS pro přihlášení uživatelů.
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcb314e46094bb6c283a17508c35b7fc17e010e5
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803375"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>Postupy: přizpůsobení prohlížečů a webových zobrazení pro iOS/macOS

Pro interaktivní ověřování je vyžadován webový prohlížeč. V systému iOS knihovna Microsoft Authentication Library (MSAL) ve výchozím nastavení používá systémový webový prohlížeč (který se může zobrazit v horní části vaší aplikace), aby se mohli přihlásit k uživatelům pomocí interaktivního ověřování. Použití prohlížeče systému má výhodu sdílení stavu jednotného přihlašování (SSO) s jinými aplikacemi a webovými aplikacemi.

Můžete změnit prostředí úpravou konfigurace na jiné možnosti zobrazení webového obsahu, jako například:

Jenom pro iOS:

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

Pro iOS a macOS:

- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

MSAL pro macOS podporuje pouze `WKWebView`.

## <a name="system-browsers"></a>Systémové prohlížeče

Pro iOS se jako systémové prohlížeče považují `ASWebAuthenticationSession`, `SFAuthenticationSession` a `SFSafariViewController`. Obecně platí, že systémové prohlížeče sdílí soubory cookie a jiná data webu pomocí prohlížeče Safari.

Ve výchozím nastavení MSAL dynamicky detekuje verzi iOS a vybere doporučený prohlížeč systému, který je v této verzi k dispozici. V systému iOS 12 + se `ASWebAuthenticationSession`. 

| Version | Webový prohlížeč |
|:-------------:|:-------------:|
| iOS 12 + | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

Vývojáři můžou také vybrat jiný systémový prohlížeč pro aplikace MSAL:

- `SFAuthenticationSession` je verze iOS 11 `ASWebAuthenticationSession`.
- `SFSafariViewController` je obecnější a poskytuje rozhraní pro procházení webu a dá se použít i pro účely přihlášení. V systému iOS 9 a 10 se soubory cookie a další webová data sdílí s Safari, ale ne v iOS 11 a novějších.

## <a name="in-app-browser"></a>Prohlížeč v aplikaci

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) je prohlížeč v aplikaci, který zobrazuje webový obsah. Nesdílí soubory cookie a data webu s jinými **WKWebView** instancemi nebo s prohlížečem Safari. WKWebView je prohlížeč pro více platforem, který je dostupný pro iOS i pro macOS.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Sdílení souborů cookie a důsledky jednotného přihlašování (SSO)

Prohlížeč, který použijete, má vliv na možnosti jednotného přihlašování z důvodu sdílení souborů cookie. Následující tabulky shrnují možnosti jednotného přihlašování na prohlížeč.

| Technologie    | Typ prohlížeče  | dostupnost iOS | dostupnost macOS | Sdílí soubory cookie a jiná data  | Dostupnost MSAL | JEDNOTNÉ |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | Systém | iOS12 a nahoru | macOS 10,15 a až | Ano | jenom iOS | instance w/Safari
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | Systém | iOS11 a nahoru | Nevztahuje se | Ano | jenom iOS |  instance w/Safari
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | Systém | iOS11 a nahoru | Nevztahuje se | Ne | jenom iOS | Ne * *
| **SFSafariViewController** | Systém | iOS10 | Nevztahuje se | Ano | jenom iOS |  instance w/Safari
| **WKWebView**  | V aplikaci | iOS8 a nahoru | macOS 10,10 a až | Ne | iOS a macOS | Ne * *

\* * Aby jednotné přihlašování fungovalo, musí být tokeny sdílené mezi aplikacemi. To vyžaduje mezipaměť tokenů nebo aplikaci zprostředkovatele, například Microsoft Authenticator pro iOS.

## <a name="change-the-default-browser-for-the-request"></a>Změna výchozího prohlížeče pro požadavek

Pomocí prohlížeče v aplikaci nebo konkrétního webového prohlížeče v závislosti na požadavcích na uživatelské prostředí můžete změnit následující vlastnost v `MSALWebviewParameters`:

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Změnit na interaktivní požadavek

Každý požadavek je možné nakonfigurovat tak, aby přepsal výchozí prohlížeč změnou vlastnosti `MSALInteractiveTokenParameters.webviewParameters.webviewType` před předáním do rozhraní API `acquireTokenWithParameters:completionBlock:`.

Kromě toho MSAL podporuje předávání do vlastního `WKWebView` nastavením vlastnosti `MSALInteractiveTokenParameters.webviewParameters.customWebView`.

Například:

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithParentViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(parentViewController: myParentController)
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
#if TARGET_OS_IPHONE
    // For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession
    // or SFAuthenticationSession).
    // For older versions, with AuthenticationSession not being available, uses
    // SafariViewController.
    MSALWebviewTypeDefault,
    
    // Use SFAuthenticationSession/ASWebAuthenticationSession
    MSALWebviewTypeAuthenticationSession,
    
    // Use SFSafariViewController for all versions.
    MSALWebviewTypeSafariViewController,
    
#endif
    // Use WKWebView
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>Další kroky

Další informace o [tocích ověřování a scénářích aplikací](authentication-flows-app-scenarios.md)
