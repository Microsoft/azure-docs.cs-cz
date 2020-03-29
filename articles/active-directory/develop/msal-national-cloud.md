---
title: Použití msal v národní cloudové aplikace | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library (MSAL) umožňuje vývojářům aplikací získat tokeny za účelem volání zabezpečených webových rozhraní API. Tato webová rozhraní API mohou být Microsoft Graph, další rozhraní API společnosti Microsoft, webová rozhraní API pro partnery nebo vlastní webové rozhraní API. MSAL podporuje více architektur aplikací a platforem.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: dfca2b1311f1b55f19d5709f7c9ca7c3e366769c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695734"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Použití MSAL v národním cloudovém prostředí

[Národní cloudy](authentication-national-cloud.md), označované také jako suverénní cloudy, jsou fyzicky izolované instance Azure. Tyto oblasti Azure pomáhají zajistit, aby byly požadavky na rezidenci dat, suverenitu a dodržování předpisů dodržovány v rámci geografických hranic.

Kromě celosvětového cloudu společnosti Microsoft umožňuje Knihovna ověřování Microsoftu (MSAL) vývojářům aplikací v národních cloudech získávat tokeny za účelem ověření a volání zabezpečených webových rozhraní API. Tato webová rozhraní API mohou být Microsoft Graph nebo jiná rozhraní API společnosti Microsoft.

Včetně globálního cloudu se Azure Active Directory (Azure AD) nasazuje v následujících národních cloudech:  

- Azure Government
- Azure China 21Vianet
- Azure Germany

Tato příručka ukazuje, jak se přihlásit k pracovním a školním účtům, získat přístupový token a volat rozhraní Microsoft Graph API v [cloudovém](https://azure.microsoft.com/global-infrastructure/government/) prostředí Azure Government.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že tyto požadavky splňujete.

### <a name="choose-the-appropriate-identities"></a>Zvolte příslušné identity

[Aplikace Azure Government](https://docs.microsoft.com/azure/azure-government/) můžou k ověřování uživatelů používat identity Azure AD Government a veřejné identity Azure AD. Vzhledem k tomu, že můžete použít některou z těchto identit, musíte se rozhodnout, který koncový bod autority byste měli zvolit pro váš scénář:

- Azure AD Public: Běžně se používá, pokud vaše organizace už má veřejného tenanta Azure AD pro podporu Office 365 (Public nebo GCC) nebo jiné aplikace.
- Azure AD Government: Běžně se používá, pokud vaše organizace už má klienta Azure AD Government pro podporu Office 365 (GCC High nebo DoD) nebo vytváří nového klienta ve službě Azure AD Government.

Po rozhodnutí je zvláštní pozornost, kde provedete registraci aplikace. Pokud pro vaši aplikaci Azure Government zvolíte veřejné identity Azure AD, musíte aplikaci zaregistrovat ve veřejném tenantovi Azure AD.

### <a name="get-an-azure-government-subscription"></a>Získejte předplatné Azure Government

Pokud chcete získat předplatné Azure Government, [přečtěte si, že najdete v tématu Správa a připojení k předplatnému ve službě Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).

Pokud nemáte předplatné Azure Government, vytvořte si [bezplatný účet,](https://azure.microsoft.com/global-infrastructure/government/request/) než začnete.

Podrobnosti o používání národního cloudu s určitým programovacím jazykem zvolte kartu odpovídající vašemu jazyku:

## <a name="net"></a>[.NET](#tab/donet)

Pomocí MSAL.NET můžete přihlásit uživatele, získat tokeny a volat rozhraní Microsoft Graph API v národních cloudech.

Následující kurzy ukazují, jak vytvořit webovou aplikaci .NET Core 2.2 MVC. Aplikace používá OpenID Connect k přihlášení uživatelů pomocí pracovního a školního účtu v organizaci, která patří do národního cloudu.

- Pokud chcete přihlásit uživatele a získat tokeny, postupujte podle tohoto kurzu: [Vytvoření ASP.NET uživatelů přihlášení na základní web v suverénních cloudech pomocí platformy microsoft identit .](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform)
- Chcete-li volat rozhraní Microsoft Graph API, postupujte podle tohoto kurzu: [Pomocí platformy identit microsoftu volat rozhraní Microsoft Graph API z ASP.NET Core 2.x Web App, jménem uživatele přihlášení pomocí jejich práce a školní účet v Microsoft National Cloud](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="javascript"></a>[Javascript](#tab/javascript)

Povolení aplikace MSAL.js pro suverénní cloudy:

### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace

1. Přihlaste se k [portálu Azure](https://portal.azure.us/).
    
   Pokud chcete najít koncové body portálu Azure pro jiné národní cloudy, najdete v [tématu koncové body registrace aplikací](authentication-national-cloud.md#app-registration-endpoints).

1. Pokud váš účet umožňuje přístup k více než jednomu tenantovi, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného klienta Azure AD.
1. Přejděte na stránku [Registrace aplikací](https://aka.ms/ra/ff) na platformě identit Microsoftu pro vývojáře.
1. Když se zobrazí stránka **Zaregistrovat aplikaci**, zadejte název pro vaši aplikaci.
1. V části **Podporované typy účtů**vyberte možnost Účty v **libovolném organizačním adresáři**.
1. V části **Identifikátor URI přesměrování** vyberte **webovou** platformu a nastavte hodnotu adresy URL aplikace na základě webového serveru. Pokyny k nastavení a získání adresy URL přesměrování v sadě Visual Studio a Uzlu najdete v dalších částech.
1. Vyberte **Zaregistrovat**.
1. Na stránce **Přehled** aplikace si poznamenejte hodnotu **ID aplikace (klienta).**
1. Tento kurz vyžaduje povolit [implicitní grantový tok](v2-oauth2-implicit-grant-flow.md). V levém podokně registrované aplikace vyberte **možnost Ověřování**.
1. V **rozšířenénastavení**, v části **Implicitní udělení**, zaškrtněte **políčka ID tokeny** a **tokeny aplikace Access.** ID tokeny a přístupové tokeny jsou povinné, protože tato aplikace potřebuje přihlásit uživatele a volat rozhraní API.
1. Vyberte **Uložit**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Krok 2: Nastavení webového serveru nebo projektu

- [Stáhněte soubory projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) pro místní webový server, například Uzel.

  – nebo –

- [Stáhněte si projekt sady Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Potom přeskočíte na [Konfigurace javascriptového spa](#step-4-configure-your-javascript-spa) a nakonfigurujete ukázku kódu před spuštěním.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Krok 3: Přihlášení uživatele pomocí knihovny Microsoft Authentication Library

Postupujte podle pokynů v [kurzu JavaScript](tutorial-v2-javascript-spa.md#create-your-project) vytvořit svůj projekt a integrovat s MSAL pro přihlášení uživatele.

### <a name="step-4-configure-your-javascript-spa"></a>Krok 4: Konfigurace javascriptové hospo-

Do `index.html` souboru vytvořeného během instalace projektu přidejte informace o registraci aplikace. V textu `<script></script>` `index.html` souboru přidejte nahoře následující kód:

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

V tomto kódu:

- `Enter_the_Application_Id_here`je hodnota **ID Aplikace (klienta)** pro aplikaci, kterou jste zaregistrovali.
- `Enter_the_Tenant_Info_Here`je nastavena na jednu z následujících možností:
    - Pokud vaše aplikace podporuje **účty v tomto organizačním adresáři**, nahraďte tuto hodnotu ID klienta nebo názvem klienta (například contoso.microsoft.com).
    - Pokud vaše aplikace podporuje **účty v libovolném organizačním adresáři**, nahraďte tuto hodnotu . `organizations`
    
    Pokud jde o hledání koncových bodů ověřování pro všechny národní cloudy, najdete v [tématu koncové body ověřování Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Osobní účty Microsoft nejsou podporovány v národních cloudech.
  
- `graphEndpoint`je koncový bod microsoft graphu pro cloud Microsoft pro vládu USA.

   Pokud chcete najít koncové body Microsoft Graphu pro všechny národní cloudy, přečtěte si [informace o koncových bodech Microsoft Graphu v národních cloudech](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="python"></a>[Python](#tab/python)

Povolení aplikace MSAL Python pro suverénní cloudy:

- Zaregistrujte svou aplikaci na konkrétním portálu, v závislosti na cloudu. Další informace o tom, jak si vybrat portál, najdete v cílové [body registrace aplikací.](authentication-national-cloud.md#app-registration-endpoints)
- Použijte některý z [ukázek](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) z repo s několika změnami konfigurace, v závislosti na cloudu, který je uveden dále.
- Použijte konkrétní autoritu v závislosti na cloudu, ve které jste aplikaci zaregistrovali. Další informace o autoritách pro různé cloudy najdete v části [Koncové body ověřování Azure AD](authentication-national-cloud.md#azure-ad-authentication-endpoints).

    Zde je příklad orgánu:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```
    
- Volání microsoft graphu vyžaduje konkrétní adresu URL koncového bodu grafu, která závisí na tom, který cloud používáte. Chcete-li najít koncové body aplikace Microsoft Graph pro všechny národní cloudy, naleznete [v kořenových koncových bodech služby Microsoft Graph a Graph Explorer](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

    Tady je příklad koncového bodu grafu s rozsahem:
    
    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```
    
## <a name="java"></a>[Java](#tab/java)

Povolení aplikace MSAL pro Java pro suverénní cloudy:

- Zaregistrujte svou aplikaci na konkrétním portálu, v závislosti na cloudu. Další informace o tom, jak si vybrat portál, najdete v cílové [body registrace aplikací.](authentication-national-cloud.md#app-registration-endpoints)
- Použijte některý z [ukázek](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) z repo s několika změnami konfigurace, v závislosti na cloudu, které jsou uvedeny dále.
- Použijte konkrétní autoritu v závislosti na cloudu, ve které jste aplikaci zaregistrovali. Další informace o autoritách pro různé cloudy najdete v části [Koncové body ověřování Azure AD](authentication-national-cloud.md#azure-ad-authentication-endpoints).

Zde je příklad orgánu:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- Volání microsoft graphu vyžaduje konkrétní adresu URL koncového bodu grafu, která závisí na tom, který cloud používáte. Chcete-li najít koncové body aplikace Microsoft Graph pro všechny národní cloudy, naleznete [v kořenových koncových bodech služby Microsoft Graph a Graph Explorer](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

Tady je příklad koncového bodu grafu s rozsahem:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

MSAL pro iOS a macOS lze použít k získání tokenů v `MSALPublicClientApplication`národních cloudech, ale vyžaduje další konfiguraci při vytváření .

Například pokud chcete, aby vaše aplikace byla víceklientskou aplikací v národním cloudu (zde vláda USA), můžete napsat:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];
                                                          
MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];
                                                                  
NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

## <a name="swift"></a>[Swift](#tab/swift)

MSAL pro iOS a macOS lze použít k získání tokenů v `MSALPublicClientApplication`národních cloudech, ale vyžaduje další konfiguraci při vytváření .

Například pokud chcete, aby vaše aplikace byla víceklientskou aplikací v národním cloudu (zde vláda USA), můžete napsat:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>Další kroky

Další informace:

- [Ověřování v národních cloudech](authentication-national-cloud.md)
- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure (Německo)](https://docs.microsoft.com/azure/germany/)
