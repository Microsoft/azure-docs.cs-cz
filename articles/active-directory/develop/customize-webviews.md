---
title: Přizpůsobení prohlížečů & webviews (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak přizpůsobit prostředí prohlížeče MSAL iOS/macOS pro přihlášení uživatelů.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 8552fc8555207c5b6ca59bbd0da0fdebaae2e87b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546112"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>Postup: Přizpůsobení prohlížečů a zobrazení WebViews pro iOS/macOS

Pro interaktivní ověřování je vyžadován webový prohlížeč. V iOS a macOS 10.15+ používá Microsoft Authentication Library (MSAL) ve výchozím nastavení systémový webový prohlížeč (který se může zobrazit nad vaší aplikací) k interaktivnímu ověřování pro přihlášení uživatelů. Použití systémového prohlížeče má tu výhodu, že sdílí stav jednotného přihlášení (SSO) s jinými aplikacemi a s webovými aplikacemi.

Prostředí můžete změnit přizpůsobením konfigurace s dalšími možnostmi zobrazení webového obsahu, například:

Pouze pro iOS:

- [Relace ověřování sf](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [Řadič SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

Pro iOS a macOS:

- [Relace aswebauthenticationsession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

MSAL pro macOS `WKWebView` podporuje pouze starší verze operačního systému. `ASWebAuthenticationSession`je podporována pouze v systému macOS 10.15 a vyšší. 

## <a name="system-browsers"></a>Systémové prohlížeče

Pro `ASWebAuthenticationSession`iOS, `SFAuthenticationSession`, `SFSafariViewController` a jsou považovány za systémové prohlížeče. Pro macOS `ASWebAuthenticationSession` je k dispozici pouze. Obecně platí, že systémové prohlížeče sdílejí soubory cookie a další data webových stránek s aplikací prohlížeče Safari.

Ve výchozím nastavení msal dynamicky detekuje verzi systému iOS a vybere doporučený systémový prohlížeč, který je k dispozici v této verzi. Na iOS 12+ `ASWebAuthenticationSession`to bude . 

### <a name="default-configuration-for-ios"></a>Výchozí konfigurace pro iOS

| Version | Webový prohlížeč |
|:-------------:|:-------------:|
| iOS 12+ | Relace aswebauthenticationsession |
| iOS 11 | Relace ověřování sf |
| iOS 10 | Řadič SFSafariViewController |

### <a name="default-configuration-for-macos"></a>Výchozí konfigurace pro macOS

| Version | Webový prohlížeč |
|:-------------:|:-------------:|
| macOS 10.15+ | Relace aswebauthenticationsession |
| jiné verze | Zobrazení WKWebView |

Vývojáři mohou také vybrat jiný systémový prohlížeč pro aplikace MSAL:

- `SFAuthenticationSession`je iOS 11 `ASWebAuthenticationSession`verze aplikace .
- `SFSafariViewController`je obecnější účel a poskytuje rozhraní pro prohlížení webu a může být použit pro účely přihlášení stejně. V iOS 9 a 10 jsou soubory cookie a další data webových stránek sdílena se Safari - ale ne v iOS 11 a novějším.

## <a name="in-app-browser"></a>Prohlížeč v aplikaci

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) je prohlížeč v aplikaci, který zobrazuje webový obsah. Nesdílí soubory cookie ani data webových stránek s jinými instancemi **WKWebView** ani s prohlížečem Safari. WKWebView je prohlížeč pro různé platformy, který je k dispozici pro iOS i macOS.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Sdílení souborů cookie a jednotné přihlašování (SSO) důsledky

Prohlížeč, který používáte, má vliv na prostředí s přistajeno, protože sdílejí soubory cookie. Následující tabulky shrnují možnosti s přistahou k přistajení na prohlížeč.

| Technologie    | Typ prohlížeče  | dostupnost iOS | dostupnost v systému macOS | Sdílí soubory cookie a další údaje  | Dostupnost msal | Jednotné přihlašování |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [Relace aswebauthenticationsession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | Systém | iOS12 a více | macOS 10.15 a více | Ano | iOS a macOS 10.15+ | w/ Instance Safari
| [Relace ověřování sf](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | Systém | iOS11 a více | Není dostupné. | Ano | Jenom iOS |  w/ Instance Safari
| [Řadič SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | Systém | iOS11 a více | Není dostupné. | Ne | Jenom iOS | Ne**
| **Řadič SFSafariViewController** | Systém | iOS10 | Není dostupné. | Ano | Jenom iOS |  w/ Instance Safari
| **Zobrazení WKWebView**  | V aplikaci | iOS8 a více | macOS 10.10 a více | Ne | iOS a macOS | Ne**

** Aby služby SSO fungovaly, je třeba mezi aplikacemi sdílet tokeny. To vyžaduje mezipaměť tokenů nebo zprostředkovatelskou aplikaci, například Microsoft Authenticator pro iOS.

## <a name="change-the-default-browser-for-the-request"></a>Změna výchozího prohlížeče pro požadavek

V závislosti na požadavcích uživatelského prostředí můžete použít prohlížeč v aplikaci nebo `MSALWebviewParameters`konkrétní systémový prohlížeč změnou následující vlastnosti v aplikaci:

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Změna podle interaktivního požadavku

Každý požadavek lze nakonfigurovat tak, aby `MSALInteractiveTokenParameters.webviewParameters.webviewType` přepsat výchozí prohlížeč `acquireTokenWithParameters:completionBlock:` změnou vlastnost před předáním do rozhraní API.

Navíc MSAL podporuje předávání vlastní `WKWebView` nastavením `MSALInteractiveTokenParameters.webviewParameters.customWebView` vlastnosti.

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

Pokud používáte vlastní webové zobrazení, oznámení se používají k označení stavu webového obsahu, který se zobrazuje, například:

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

Všechny typy webových prohlížečů podporované službou MSAL jsou deklarovány ve [výčtu MSALWebviewType](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)

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
