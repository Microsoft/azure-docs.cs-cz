---
title: Konfigurace zprostředkovatelů identity (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Naučte se používat různé autority, jako jsou B2C, svrchované cloudy a uživatele typu Host, s MSAL pro iOS a macOS.
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
ms.openlocfilehash: d8a176fff0da932d0fafd40b9ab895b635acc5f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96169439"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Postupy: Konfigurace MSAL pro iOS a macOS pro používání různých zprostředkovatelů identity

V tomto článku se dozvíte, jak nakonfigurovat aplikaci knihovny Microsoft Authentication Library pro iOS a macOS (MSAL) pro různé úřady, jako je Azure Active Directory (Azure AD), Business-to-Consumer (B2C), cloudy a uživatelé typu Host.  V celém tomto článku se můžete obvykle domnívat, že se jedná o autoritu jako poskytovatel identity.

## <a name="default-authority-configuration"></a>Výchozí konfigurace autority

`MSALPublicClientApplication` má nakonfigurovanou adresu URL s výchozí autoritou `https://login.microsoftonline.com/common` , která je vhodná pro většinu Azure Active Directorych scénářů (AAD). Pokud neimplementujete pokročilé scénáře, jako jsou národní cloudy nebo pracujete s B2C, nebudete je muset měnit.

> [!NOTE]
> Moderní ověřování pomocí Active Directory Federation Services (AD FS) jako zprostředkovatele identity (ADFS) není podporované (podrobnosti najdete v tématu [ADFS pro vývojáře](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) ). Služba ADFS je podporovaná prostřednictvím federace.

## <a name="change-the-default-authority"></a>Změna výchozího úřadu

V některých scénářích, jako je například B2C (Business-to-Consumer), může být potřeba změnit výchozí autoritu.

### <a name="b2c"></a>B2C

Aby bylo možné pracovat s B2C, musí [Knihovna MSAL (Microsoft Authentication Library)](reference-v2-libraries.md) vyžadovat odlišnou konfiguraci autority. MSAL rozpoznává jeden formát adresy URL autority jako B2C sám sebe. Rozpoznaný formát autority B2C je `https://<host>/tfp/<tenant>/<policy>` například `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy` . Můžete ale také použít jiné podporované adresy URL autority B2C a deklarovat autoritu jako B2C autoritu explicitně.

Pro podporu formátu libovolných adres URL pro B2C se `MSALB2CAuthority` dá nastavit libovolná adresa URL, například:

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

Všechny B2C autority, které nepoužívají výchozí formát B2C autority, musí být deklarovány jako známé autority.

Přidejte jednotlivé B2C autority do seznamu známých autorit i v případě, že se úřady liší pouze v zásadě.

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

Když vaše aplikace požaduje novou zásadu, adresa URL autority musí být změněna, protože adresa URL autority je pro každou zásadu odlišná. 

Chcete-li nakonfigurovat aplikaci B2C, nastavte `@property MSALAuthority *authority` s instancí `MSALB2CAuthority` v v `MSALPublicClientApplicationConfig` před vytvořením `MSALPublicClientApplication` podobným způsobem:

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

### <a name="sovereign-clouds"></a>Suverénní cloudy

Pokud je vaše aplikace spuštěná v rámci svrchovaného cloudu, možná budete muset změnit adresu URL autority v `MSALPublicClientApplication` . Následující příklad nastaví adresu URL autority pro práci s německým cloudem AAD:

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

Je možné, že budete muset do každého svrchovaného cloudu předat různé obory. Které obory k odeslání závisí na prostředku, který používáte. Můžete například použít `"https://graph.microsoft.com/user.read"` v celosvětovém cloudu a `"https://graph.microsoft.de/user.read"` v německém cloudu.

### <a name="signing-a-user-into-a-specific-tenant"></a>Přihlášení uživatele do konkrétního tenanta

Když je adresa URL autority nastavená na `"login.microsoftonline.com/common"` , uživatel se přihlásí do svého domovského tenanta. Některé aplikace ale můžou potřebovat podepsat uživatele do jiného tenanta a některé aplikace fungují jenom s jedním klientem.

Pokud chcete uživatele podepsat do konkrétního tenanta, nakonfigurujte ho `MSALPublicClientApplication` pomocí konkrétní autority. Například:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

Následující příklad ukazuje, jak podepsat uživatele do konkrétního tenanta:

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

## <a name="supported-authorities"></a>Podporované autority

### <a name="msalauthority"></a>MSALAuthority

`MSALAuthority`Třída je základní abstraktní třída pro třídy MSAL autority. Nepokoušejte se vytvořit instanci této instance pomocí `alloc` nebo `new` . Místo toho buď vytvořte jednu z jejích podtříd přímo ( `MSALAADAuthority` , `MSALB2CAuthority` ), nebo použijte metodu Factory `authorityWithURL:error:` k vytvoření podtříd pomocí adresy URL autority.

`url`K získání normalizované adresy URL autority použijte vlastnost. Další parametry a součásti cesty nebo fragmenty, které nejsou součástí autority, nebudou v vrácené normalizované adrese URL autority.

Níže jsou uvedené podtřídy `MSALAuthority` , které můžete vytvořit v závislosti na tom, jakou autoritu chcete použít.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority` představuje autoritu AAD. Adresa URL autority by měla být v následujícím formátu, kde `<port>` je volitelná: `https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority` představuje autoritu B2C. Adresa URL autority B2C by měla být ve výchozím nastavení v následujícím formátu, kde `<port>` je volitelná: `https://<host>:<port>/tfp/<tenant>/<policy>` . MSAL však podporuje i další libovolné formáty B2C autority.

## <a name="next-steps"></a>Další kroky

Další informace o [tocích ověřování a scénářích aplikací](authentication-flows-app-scenarios.md)