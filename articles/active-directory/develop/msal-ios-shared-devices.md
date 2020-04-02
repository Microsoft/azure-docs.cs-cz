---
title: Režim sdíleného zařízení pro zařízení se systémem iOS
titleSuffix: Microsoft identity platform | Azure
description: Přečtěte si, jak povolit režim sdíleného zařízení, aby mohli pracovníci Firstline dělnické služby sdílet iOS zařízení.
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
ms.openlocfilehash: 7cecbc48eb362c2c0f1741352e6f7f5f6ad40c9e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550250"
---
# <a name="shared-device-mode-for-ios-devices"></a>Režim sdíleného zařízení pro zařízení se systémem iOS

> [!NOTE]
> Tato funkce je ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Firstline Pracovníci, jako jsou maloobchodní partneři, členové letové posádky a pracovníci terénních služeb, často používají ke své práci sdílené mobilní zařízení. Tato sdílená zařízení mohou představovat bezpečnostní rizika, pokud uživatelé záměrně nebo nesdílejí svá hesla nebo PIN kódy pro přístup k zákaznickým a obchodním datům na sdíleném zařízení.

Režim sdíleného zařízení umožňuje nakonfigurovat zařízení se systémem iOS 13 nebo vyšší tak, aby bylo snadněji a bezpečněji sdíleno zaměstnanci. Zaměstnanci se mohou rychle přihlásit a získat přístup k informacím o zákaznících. Po dokončení směny nebo úkolu se mohou odhlásit ze zařízení a je okamžitě připraveno k použití dalším zaměstnancem.

Režim sdíleného zařízení také poskytuje správu zařízení podporovanou identitou společnosti Microsoft.

Tato funkce používá [aplikaci Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) ke správě uživatelů v zařízení a k distribuci [modulu plug-in Microsoft Enterprise SSO pro zařízení Apple](apple-sso-plugin.md).

## <a name="create-a-shared-device-mode-app"></a>Vytvoření aplikace režimu sdíleného zařízení

Pokud chcete vytvořit aplikaci pro režim sdíleného zařízení, vývojáři a správci cloudových zařízení spolupracují:

1. **Vývojáři aplikací** píší aplikaci s jedním účtem (aplikace s více účtůmi nejsou podporovány v režimu sdíleného zařízení) a zapisují kód pro zpracování věcí, jako je odhlášení sdíleného zařízení.

1. **Správci zařízení** připravují zařízení ke sdílení pomocí poskytovatele správy mobilních zařízení (MDM), jako je Microsoft Intune, ke správě zařízení v jejich organizaci. MDM odešle aplikaci Microsoft Authenticator do zařízení a zapne "Sdílený režim" pro každé zařízení prostřednictvím aktualizace profilu do zařízení. Toto nastavení sdíleného režimu mění chování podporovaných aplikací v zařízení. Tato konfigurace od poskytovatele MDM nastaví režim sdíleného zařízení pro zařízení a povolí [modul plug-in Microsoft Enterprise SSO pro zařízení Apple,](apple-sso-plugin.md) který je vyžadován pro režim sdíleného zařízení.

1. [**Povinné pouze během veřejného náhledu**] Uživatel s rolí [Správce cloudových zařízení](../users-groups-roles/directory-assign-admin-roles.md#cloud-device-administrator) pak musí spustit [aplikaci Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) a připojit své zařízení k organizaci.

    Konfigurace členství v organizačních rolích na webu Azure Portal: Role **Služby** > Azure a**správce cloudových zařízení** **správci služby** > Azure

Následující části vám pomohou aktualizovat aplikaci tak, aby podporovala režim sdíleného zařízení.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Povolení režimu sdíleného zařízení & rozšíření montovna SSO pomocí Intune

> [!NOTE]
> Následující krok je vyžadován pouze během verze Public Preview.

Zařízení musí být nakonfigurováno tak, aby podporovalo režim sdíleného zařízení. Musí mít nainstalovaný iOS 13+ a musí být zaregistrován. Konfigurace MDM také potřebuje povolit [modul plug-in Microsoft Enterprise SSO pro zařízení Apple](apple-sso-plugin.md). Další informace o rozšířeních s přisytím služby SSO najdete ve [videu Apple](https://developer.apple.com/videos/play/tech-talks/301/).

1. Na konfiguračním portálu Intune sdělte zařízení, aby povolilo [modul plug-in Microsoft Enterprise SSO pro zařízení Apple](apple-sso-plugin.md) s následující konfigurací:

    - **Typ**: Přesměrování
    - **ID rozšíření:** com.microsoft.azureauthenticator.ssoextension
    - **ID týmu**: SGGM6D27TK
    - **Adresy URL**:https://login.microsoftonline.com
    - Další data ke konfiguraci:
      - Klíč: sharedDeviceMode
      - Typ: Logická hodnota
      - Hodnota: True

    Další informace o konfiguraci pomocí Intune najdete v [dokumentaci ke konfiguraci Intune](https://docs.microsoft.com/intune/configuration/ios-device-features-settings).

1. Dále nakonfigurujte mdm tak, aby aplikace Microsoft Authenticator do zařízení prostřednictvím profilu MDM.

    Nastavte následující možnosti konfigurace pro zapnutí režimu sdíleného zařízení:

    - Konfigurace 1:
      - Klíč: sharedDeviceMode
      - Typ: Logická hodnota
      - Hodnota: True

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>Úprava aplikace pro iOS tak, aby podporovala režim sdíleného zařízení

Uživatelé jsou na vás závislí, aby zajistili, že jejich data neprosakují jinému uživateli. Následující části poskytují užitečné signály, které vaší aplikaci oznamují, že došlo ke změně a měla by být zpracována.

Jste zodpovědní za kontrolu stavu uživatele na zařízení při každém použití aplikace a následné vymazání dat předchozího uživatele. To zahrnuje, pokud je znovu načten z pozadí v multi-tasking.

Při změně uživatele byste měli zajistit, aby byla vymazána data předchozího uživatele a aby byla odebrána všechna data uložená v mezipaměti zobrazená v aplikaci. Důrazně doporučujeme vám a vaší společnosti, abyste po aktualizaci aplikace provedli proces kontroly zabezpečení, aby podporovali režim sdíleného zařízení.

### <a name="detect-shared-device-mode"></a>Detekce režimu sdíleného zařízení

Detekce režimu sdíleného zařízení je pro vaši aplikaci důležitá. Mnoho aplikací bude vyžadovat změnu v jejich uživatelské zkušenosti (UX) při použití aplikace na sdíleném zařízení. Vaše aplikace může mít například funkci Registrace, která není vhodná pro pracovníka první linie, protože pravděpodobně již má účet. Můžete také přidat další zabezpečení pro vaše aplikace zpracování dat, pokud je v režimu sdíleného zařízení.

Pomocí `getDeviceInformationWithParameters:completionBlock:` rozhraní API `MSALPublicClientApplication` v aplikaci určete, jestli aplikace běží na zařízení v režimu sdíleného zařízení.

Následující fragmenty kódu zobrazují příklady použití `getDeviceInformationWithParameters:completionBlock:` rozhraní API.

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

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Získání přihlášeného uživatele a zjištění, zda se uživatel v zařízení změnil

Další důležitou součástí podpory režimu sdíleného zařízení je určení stavu uživatele v zařízení a vymazání dat aplikace, pokud se uživatel změnil nebo pokud v zařízení není vůbec žádný uživatel. Jste zodpovědní za zajištění, že data nebudou prozrazena jinému uživateli.

Pomocí rozhraní `getCurrentAccountWithParameters:completionBlock:` API můžete zadat dotaz na aktuálně přihlášený účet v zařízení.

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

### <a name="globally-sign-in-a-user"></a>Globální přihlášení uživatele

Když je zařízení nakonfigurováno jako sdílené zařízení, může vaše aplikace volat `acquireTokenWithParameters:completionBlock:` rozhraní API pro přihlášení k účtu. Účet bude k dispozici globálně pro všechny způsobilé aplikace na zařízení po prvním přihlášení aplikace v účtu.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>Globální odhlášení uživatele

Následující kód odebere přihlášený účet a vymaže tokeny uložené v mezipaměti nejen z aplikace, ale také ze zařízení, které je v režimu sdíleného zařízení. Nečistí však *data* z vaší aplikace. Je nutné vymazat data z vaší aplikace, stejně jako vymazat všechna data uložená v mezipaměti aplikace může být zobrazena uživateli.

#### <a name="clear-browser-state"></a>Vymazat stav prohlížeče

> [!NOTE]
> Následující krok je vyžadován pouze během verze Public Preview.

V této verzi verze ver preview [modul plug-in Microsoft Enterprise SSO pro zařízení Apple](apple-sso-plugin.md) vymaže stav pouze pro aplikace. V prohlížeči Safari není jasný stav. Doporučujeme ručně vymazat relaci prohlížeče, abyste zajistili, že nezůstanou žádné stopy stavu uživatele. Pomocí níže uvedené `signoutFromBrowser` volitelné vlastnosti můžete vymazat všechny soubory cookie. To způsobí, že se prohlížeč krátce spustí v zařízení.

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

Chcete-li zobrazit režim sdíleného zařízení v akci, následující ukázka kódu na GitHubu obsahuje příklad spuštění aplikace Firstline Worker na zařízení se systémem iOS v režimu sdíleného zařízení:

[Ukázka rozhraní API rozhraní Microsoft Graph API msal iOS Swift](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
