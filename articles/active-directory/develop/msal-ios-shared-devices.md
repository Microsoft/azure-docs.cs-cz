---
title: Režim sdíleného zařízení pro zařízení s iOS
titleSuffix: Microsoft identity platform | Azure
description: Naučte se povolit režim sdíleného zařízení, aby prvotní pracovníci mohli sdílet zařízení s iOS.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: c67c5d7b46c04e7f1aea020127ee798878c43d60
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578766"
---
# <a name="shared-device-mode-for-ios-devices"></a>Režim sdíleného zařízení pro zařízení s iOS

>[!IMPORTANT]
> Tato funkce [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Prvotní pracovní procesy, jako jsou maloobchodníci, členové posádky a pracovníci polních služeb často používají ke své práci sdílené mobilní zařízení. Tato sdílená zařízení mohou představovat bezpečnostní rizika, pokud uživatelé sdílejí svá hesla nebo kódy PIN, úmyslně nebo nechtějí získat přístup k datům zákazníků a podnikových dat na sdíleném zařízení.

Režim sdíleného zařízení umožňuje nakonfigurovat zařízení se systémem iOS 13 nebo vyšším, aby je bylo snazší a bezpečně sdílené zaměstnanci. Zaměstnanci se můžou rychle přihlašovat a přistupovat k informacím o zákaznících. Až se dokončí s jejich přesunutím nebo úlohou, můžou se odhlásit ze zařízení a hned ho začít používat pro dalšího zaměstnance.

Režim sdíleného zařízení taky poskytuje správu zařízení, která je v rámci identity založená na identitě Microsoftu.

Tato funkce používá [aplikaci Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) ke správě uživatelů na zařízení a k distribuci [modulu plug-in Microsoft Enterprise SSO pro zařízení Apple](apple-sso-plugin.md).

## <a name="create-a-shared-device-mode-app"></a>Vytvoření aplikace v režimu sdíleného zařízení

Pokud chcete vytvořit sdílenou aplikaci v režimu zařízení, vývojáři a správci cloudových zařízení spolupracují:

1. **Vývojáři aplikací** napíší aplikace s jedním účtem (aplikace s více účty se v režimu sdíleného zařízení nepodporují) a napíší kód, který bude zpracovávat věci, jako je například odhlášení ze sdíleného zařízení.

1. **Správci zařízení** připraví zařízení pro sdílení pomocí poskytovatele správy mobilních zařízení (MDM), jako je Microsoft Intune ke správě zařízení v jejich organizaci. MDM vloží aplikaci Microsoft Authenticator do zařízení a zapne "sdílený režim" pro každé zařízení prostřednictvím aktualizace profilu na zařízení. V tomto nastavení sdíleného režimu se mění chování podporovaných aplikací na zařízení. Tato konfigurace od poskytovatele MDM nastavuje režim sdíleného zařízení pro zařízení a povoluje [modul plug-in Microsoft Enterprise SSO pro zařízení Apple](apple-sso-plugin.md) , která se vyžadují pro režim sdíleného zařízení.

1. [**Vyžadováno pouze během Public Preview**] Uživatel s rolí [správce cloudového zařízení](../roles/permissions-reference.md#cloud-device-administrator) musí potom spustit [aplikaci Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) a připojit své zařízení k organizaci.

    Pokud chcete nakonfigurovat členství vaší organizační role v Azure Portal: **Azure Active Directory**  >  **role a správci**  >  **cloudového zařízení správce**

Následující části vám pomůžou aktualizovat aplikaci tak, aby podporovala režim sdíleného zařízení.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Povolení režimu sdíleného zařízení &ho rozšíření jednotného přihlašování pomocí Intune

> [!NOTE]
> Následující krok je vyžadován pouze během veřejné verze Preview.

Vaše zařízení musí být nakonfigurované tak, aby podporovalo režim sdíleného zařízení. Musí mít nainstalované iOS 13 + a musí být zaregistrované v MDM. Konfigurace MDM taky musí povolit [modul plug-in Microsoft Enterprise SSO pro zařízení Apple](apple-sso-plugin.md). Další informace o rozšíření jednotného přihlašování najdete v tématu [Apple video](https://developer.apple.com/videos/play/tech-talks/301/).

1. Na portálu konfigurace Intune řekněte zařízení, aby povolilo [modul plug-in Microsoft Enterprise SSO pro zařízení Apple](apple-sso-plugin.md) s následující konfigurací:

    - **Typ**: přesměrování
    - **ID rozšíření**: com. Microsoft. azureauthenticator. ssoextension
    - **ID týmu**: (Toto pole není pro iOS potřeba)
    - **Adresy URL**:   
        - `https://login.microsoftonline.com`
        - `https://login.microsoft.com`
        - `https://sts.windows.net`
        - `https://login.partner.microsoftonline.cn`
        - `https://login.chinacloudapi.cn`
        - `https://login.microsoftonline.de`
        - `https://login.microsoftonline.us`
        - `https://login.usgovcloudapi.net`
        - `https://login-us.microsoftonline.com`
    - **Další data ke konfiguraci**:
      - Klíč: sharedDeviceMode
      - Typ: Boolean
      - Hodnota: true

    Další informace o konfiguraci služby Intune najdete v dokumentaci ke [konfiguraci Intune](/intune/configuration/ios-device-features-settings).

1. Potom nakonfigurujte MDM tak, aby se aplikace Microsoft Authenticator do zařízení vložila přes profil MDM.

    Nastavte následující možnosti konfigurace, abyste zapnuli režim sdíleného zařízení:

    - Konfigurace 1:
      - Klíč: sharedDeviceMode
      - Typ: Boolean
      - Hodnota: true

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>Úprava aplikace pro iOS tak, aby podporovala režim sdíleného zařízení

Vaši uživatelé závisí na tom, abyste zajistili, že jejich data nebudou nevrácená jiným uživatelem. V následujících částech jsou uvedeny užitečné signály, které označují vaši aplikaci, že došlo ke změně a měla by být zpracována.

Zodpovídáte za kontrolu stavu uživatele v zařízení pokaždé, když se vaše aplikace používá, a pak vymazat data předchozího uživatele. To zahrnuje, pokud se znovu načte z pozadí při více úlohách.

Při změně uživatele byste měli zkontrolovat, že se data předchozího uživatele vymažou a že se z aplikace odeberou všechna data uložená v mezipaměti. Důrazně doporučujeme, abyste vy a vaše společnost prováděli proces kontroly zabezpečení po aktualizaci aplikace na podporu režimu sdíleného zařízení.

### <a name="detect-shared-device-mode"></a>Detekovat režim sdíleného zařízení

Zjišťování režimu sdíleného zařízení je pro vaši aplikaci důležité. Mnoho aplikací bude při použití aplikace na sdíleném zařízení vyžadovat změnu v uživatelském prostředí (UX). Například vaše aplikace může mít funkci "registrace", která není vhodná pro pracovní proces prvotní, protože už pravděpodobně má účet. Pokud je v režimu sdíleného zařízení, můžete také pro zpracování dat aplikace přidat další zabezpečení.

`getDeviceInformationWithParameters:completionBlock:` `MSALPublicClientApplication` K určení, jestli je aplikace spuštěná na zařízení v režimu sdíleného zařízení, použijte rozhraní API v.

Následující fragmenty kódu ukazují příklady použití `getDeviceInformationWithParameters:completionBlock:` rozhraní API.

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Získá přihlášeného uživatele a určí, jestli se na zařízení změnil nějaký uživatel.

Další důležitou součástí podpory režimu sdíleného zařízení je určení stavu uživatele v zařízení a vymazání dat aplikací, pokud se uživatel změnil nebo pokud na zařízení není vůbec žádný uživatel. Zodpovídáte za to, že nebudete moct data nevrácená jinému uživateli.

`getCurrentAccountWithParameters:completionBlock:`Rozhraní API můžete použít k dotazování aktuálně přihlášeného účtu v zařízení.

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>Globálně přihlašovat uživatele

Když je zařízení nakonfigurované jako sdílené zařízení, může vaše aplikace volat `acquireTokenWithParameters:completionBlock:` rozhraní API pro přihlášení k účtu. Účet bude k dispozici globálně pro všechny opravňující aplikace na zařízení po přihlášení první aplikace k účtu.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>Globálně odhlásit uživatele

Následující kód odstraní přihlášený účet a vymaže tokeny uložené v mezipaměti nejen z aplikace, ale také ze zařízení, které je v režimu sdíleného zařízení. Ale nevymaže *data* z vaší aplikace. Je nutné vymazat data z aplikace a vymazat všechna data uložená v mezipaměti, která vaše aplikace může zobrazit uživateli.

#### <a name="clear-browser-state"></a>Vymazat stav prohlížeče

> [!NOTE]
> Následující krok je vyžadován pouze během veřejné verze Preview.

V této verzi Public Preview [modul plug-in Microsoft Enterprise SSO pro zařízení Apple](apple-sso-plugin.md) vymaže stav jenom pro aplikace. V prohlížeči Safari není tento stav jasný. Doporučujeme, abyste ručně vymazali relaci prohlížeče, abyste se ujistili, že nezbývá žádné trasování stavu uživatele. `signoutFromBrowser`K vymazání všech souborů cookie můžete použít volitelnou vlastnost uvedenou níže. To způsobí, že se prohlížeč na zařízení krátce spustí.

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>Další kroky

Pokud chcete v akci zobrazit režim sdíleného zařízení, následující ukázka kódu na GitHubu obsahuje příklad spuštění aplikace pracovníka prvotní na zařízení s iOS v režimu sdíleného zařízení:

[Ukázka rozhraní MSAL pro iOS SWIFT Microsoft Graph](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
