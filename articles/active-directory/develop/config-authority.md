---
title: Konfigurace zprostředkovatelů identit (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Naučte se používat různé autority, jako jsou B2C, suverénní cloudy a uživatelé hosta, s MSAL pro iOS a macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 4810de772e44be22ee5bd4a9fb6ef0ef756e62f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085204"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Postup: Konfigurace služby MSAL pro iOS a macOS tak, aby používala různé zprostředkovatele identit

V tomto článku se zobrazí postup konfigurace aplikace knihovny ověřování Microsoftu pro iOS a macOS (MSAL) pro různé autority, jako je Azure Active Directory (Azure AD), Business-to-Consumer (B2C), suverénní cloudy a uživatelé typu Host.  V tomto článku si obecně můžete myslet autority jako zprostředkovatele identity.

## <a name="default-authority-configuration"></a>Výchozí konfigurace autority

`MSALPublicClientApplication`je nakonfigurován s výchozí `https://login.microsoftonline.com/common`autoritou URL aplikace , která je vhodná pro většinu scénářů služby Azure Active Directory (AAD). Pokud neimplementujete pokročilé scénáře, jako jsou národní cloudy, nebo nepracujete s B2C, nebudete je muset měnit.

> [!NOTE]
> Moderní ověřování se službou ADFS jako zprostředkovatelem identity (ADFS) není podporováno (podrobnosti naleznete v tématu [ADFS for Developers).](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) Služba ADFS je podporována prostřednictvím federace.

## <a name="change-the-default-authority"></a>Změna výchozího oprávnění

V některých scénářích, jako je například business-to-consumer (B2C), může být nutné změnit výchozí autoritu.

### <a name="b2c"></a>B2C

Pro práci s B2C vyžaduje [Knihovna ověřování Společnosti Microsoft (MSAL)](reference-v2-libraries.md) jinou konfiguraci autority. MSAL rozpozná jeden formát adresy URL autority jako B2C sám o sobě. Uznaný formát autority `https://<host>/tfp/<tenant>/<policy>`B2C je například `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy`. Můžete však také použít jakékoli jiné podporované adresy URL oprávnění B2C tím, že deklarujete autoritu jako autoritu B2C explicitně.

Pro podporu libovolného formátu URL pro `MSALB2CAuthority` B2C lze nastavit pomocí libovolné adresy URL, například takto:

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

Všechny orgány B2C, které nepoužívají výchozí formát autority B2C, musí být deklarovány jako známé autority.

Přidejte každý jiný orgán B2C do seznamu známých orgánů, i když se orgány liší pouze v politice.

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

Když vaše aplikace požaduje novou zásadu, je třeba změnit adresu URL autority, protože adresa URL autority se pro každou zásadu liší. 

Chcete-li nakonfigurovat aplikaci `@property MSALAuthority *authority` B2C, nastavte před vytvořením instanci `MSALB2CAuthority` instanci instanci instanci `MSALPublicClientApplicationConfig` instanci `MSALPublicClientApplication`instanci instrebule , například takto:

Objective-C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>Suverénní mraky

Pokud vaše aplikace běží v suverénním cloudu, možná budete `MSALPublicClientApplication`muset změnit adresu URL autority v . Následující příklad nastaví adresu URL autority pro práci s německým cloudem AAD:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

Možná budete muset předat různé obory pro každý suverénní cloud. Které obory, které chcete odeslat, závisí na prostředku, který používáte. Můžete například použít `"https://graph.microsoft.com/user.read"` v celosvětovém `"https://graph.microsoft.de/user.read"` cloudu a v německém cloudu.

### <a name="signing-a-user-into-a-specific-tenant"></a>Přihlášení uživatele k určitému klientovi

Pokud je adresa URL `"login.microsoftonline.com/common"`autority nastavena na , bude uživatel přihlášen ke svému domovskému tenantovi. Některé aplikace však může být nutné přihlásit uživatele do jiného klienta a některé aplikace pracovat pouze s jedním tenantem.

Chcete-li uživatele přihlásit do `MSALPublicClientApplication` konkrétního klienta, nakonfigurujte s konkrétní autoritou. Například:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

Následující ukazuje, jak přihlásit uživatele do konkrétního klienta:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>Podporované orgány

### <a name="msalauthority"></a>Orgán MSALA

Třída `MSALAuthority` je základní abstraktní třída pro třídy autority MSAL. Nepokoušejte se vytvořit její `alloc` instanci pomocí nebo `new`. Místo toho buď vytvořte jednu`MSALAADAuthority` `MSALB2CAuthority`z jeho podtříd `authorityWithURL:error:` přímo ( , nebo použijte metodu výroby k vytvoření podtříd pomocí adresy URL autority.

Pomocí `url` vlastnosti získáte adresu URL normalizované autority. Další parametry a součásti cesty nebo fragmenty, které nejsou součástí autority, nebudou v adrese URL vrácené normalizované autority.

Níže jsou uvedeny `MSALAuthority` podtřídy, které můžete vytvořit instanci v závislosti na orgánu, který chcete použít.

### <a name="msalaadauthority"></a>MSALAADA úřad

`MSALAADAuthority`zastupuje orgán AAD. Adresa URL autority by měla být `<port>` v následujícím formátu, kde je nepovinná:`https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CA úřad

`MSALB2CAuthority`zastupuje orgán B2C. Ve výchozím nastavení by měla být adresa URL autority `<port>` B2C v následujícím formátu, kde je volitelné: `https://<host>:<port>/tfp/<tenant>/<policy>`. MSAL však také podporuje jiné libovolné formáty autority B2C.

## <a name="next-steps"></a>Další kroky

Další informace o [tocích ověřování a scénářích aplikací](authentication-flows-app-scenarios.md)
