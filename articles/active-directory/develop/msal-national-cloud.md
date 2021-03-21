---
title: Použití MSAL v národní cloudové aplikaci | Azure
titleSuffix: Microsoft identity platform
description: Knihovna Microsoft Authentication Library (MSAL) umožňuje vývojářům aplikací získat tokeny, aby mohli volat zabezpečená webová rozhraní API. Tato webová rozhraní API můžou být Microsoft Graph, jiná rozhraní API Microsoftu, partnerská webová rozhraní API nebo vlastní webové rozhraní API. MSAL podporuje více architektur aplikací a platforem.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: marsma, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 09c4dadd7a6560bd5163d623dd8a7f247b57860e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100102491"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Použití MSAL v národním cloudovém prostředí

[Národní cloudy](authentication-national-cloud.md), označované také jako svrchované cloudy, jsou fyzicky izolované instancemi Azure. Tyto oblasti Azure vám pomůžou zajistit, aby se požadavky na data, svrchovanost a dodržování předpisů dodržovaly v rámci zeměpisných hranic.

Kromě celosvětového cloudu společnosti Microsoft Microsoft Authentication Library (MSAL) umožňuje vývojářům aplikací v národních cloudech získat tokeny, aby mohli ověřovat a volat zabezpečená webová rozhraní API. Tato webová rozhraní API můžou být Microsoft Graph nebo jiná rozhraní API Microsoftu.

Zahrnutí globálního cloudu Azure Active Directory (Azure AD) je nasazeno v následujících národních cloudech:  

- Azure Government
- Azure (Čína) 21Vianet
- Azure (Německo)

Tato příručka ukazuje, jak se přihlásit k pracovním a školním účtům, získat přístupový token a volat rozhraní API Microsoft Graph v [cloudovém prostředí Azure Government](https://azure.microsoft.com/global-infrastructure/government/) .

## <a name="prerequisites"></a>Předpoklady

Než začnete, ujistěte se, že splňujete tyto požadavky.

### <a name="choose-the-appropriate-identities"></a>Zvolit odpovídající identity

Aplikace [Azure Government](../../azure-government/index.yml) můžou k ověřování uživatelů používat identity státní správy Azure AD a veřejné identity Azure AD. Vzhledem k tomu, že můžete použít některou z těchto identit, určete koncový bod autority, který byste měli zvolit pro váš scénář:

- Azure AD Public: běžně se používá v případě, že vaše organizace už má veřejného tenanta Azure AD, aby podporoval Microsoft 365 (veřejný nebo RSZ) nebo jinou aplikaci.
- Azure AD vláda: běžně se používá v případě, že vaše organizace už má tenanta státní správy Azure AD podporovat Office 365 (na úrovni RSZ nebo DoD) nebo vytváří nového tenanta ve službě Azure AD.

V případě, že se rozhodnete, že provádíte registraci vaší aplikace, budete mít zvláštní pozornost. Pokud pro svou aplikaci Azure Government zvolíte veřejné identity Azure AD, musíte aplikaci zaregistrovat ve veřejném tenantovi Azure AD.

### <a name="get-an-azure-government-subscription"></a>Získat Azure Government předplatné

Pokud chcete získat Azure Government předplatné, přečtěte si téma [Správa a připojení k předplatnému v Azure Government](../../azure-government/compare-azure-government-global-azure.md).

Pokud nemáte předplatné Azure Government, vytvořte si [bezplatný účet](https://azure.microsoft.com/global-infrastructure/government/request/) před tím, než začnete.

Podrobnosti o používání národního cloudu s určitým programovacím jazykem získáte, když vyberete kartu, která odpovídá vašemu jazyku:

## <a name="net"></a>[.NET](#tab/dotnet)

MSAL.NET můžete použít k přihlašování uživatelů, získání tokenů a volání rozhraní API Microsoft Graph v národních cloudech.

Následující kurzy ukazují, jak vytvořit webovou aplikaci .NET Core 2,2 MVC. Aplikace používá OpenID Connect k přihlašování uživatelů pomocí pracovního a školního účtu v organizaci, která patří do národního cloudu.

- Pokud se chcete přihlásit k uživatelům a získat tokeny, postupujte podle tohoto kurzu: [sestavení webové aplikace ASP.NET Core přihlašování uživatelů v rámci svrchovaných cloudů pomocí platformy Microsoft Identity Platform](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Chcete-li volat rozhraní API Microsoft Graph, postupujte podle tohoto kurzu: [použití platformy Microsoft identity k volání rozhraní api Microsoft Graph z webové aplikace ASP.NET Core 2. x, jménem uživatele přihlašování pomocí pracovního a školního účtu v národním cloudu společnosti Microsoft](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="javascript"></a>[JavaScript](#tab/javascript)

Povolení aplikace MSAL.js pro cloudy svrchovaného prostředí:

### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace

1. Přihlaste se na <a href="https://portal.azure.us/" target="_blank">Azure Portal</a>.

   Pokud chcete najít Azure Portal koncových bodů pro jiné národní cloudy, přečtěte si téma [koncové body registrace aplikace](authentication-national-cloud.md#app-registration-endpoints).

1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
1. Zadejte **název** vaší aplikace. Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
1. V části **podporované typy účtů** vyberte **účty v libovolném organizačním adresáři**.
1. V části **identifikátor URI pro přesměrování** vyberte **webovou** platformu a nastavte hodnotu na adresu URL aplikace na základě vašeho webového serveru. V dalších částech najdete pokyny, jak nastavit a získat adresu URL pro přesměrování v sadě Visual Studio a uzlu.
1. Vyberte **Zaregistrovat**.
1. Na stránce **Přehled** si poznamenejte hodnotu **ID aplikace (klienta)** pro pozdější použití.
    Tento kurz vyžaduje, abyste povolili [postup implicitního udělení](v2-oauth2-implicit-grant-flow.md). 
1. V části **Spravovat** vyberte **ověřování**.
1. V části **implicitní udělení a hybridní toky** vyberte **tokeny ID** a **přístupové tokeny**. Tokeny ID a přístupové tokeny jsou povinné, protože tato aplikace musí přihlašovat uživatele a volat rozhraní API.
1. Vyberte **Uložit**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Krok 2: nastavení webového serveru nebo projektu

- [Stáhněte si soubory projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) pro místní webový server, jako je například Node.

  nebo

- [Stáhněte si projekt sady Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Pak přejděte ke [konfiguraci vašeho JavaScriptu](#step-4-configure-your-javascript-spa) , abyste před spuštěním nakonfigurovali ukázku kódu.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Krok 3: k přihlášení uživatele použijte Microsoft Authentication Library.

Použijte postup v [kurzu JavaScriptu](tutorial-v2-javascript-spa.md#create-your-project) k vytvoření projektu a integraci s MSAL pro přihlášení uživatele.

### <a name="step-4-configure-your-javascript-spa"></a>Krok 4: konfigurace vašeho JavaScriptu SPA

V `index.html` souboru vytvořeném během nastavení projektu přidejte informace o registraci aplikace. Do `<script></script>` značek v těle souboru přidejte následující kód `index.html` :

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

- `Enter_the_Application_Id_here` je hodnota **ID aplikace (klienta)** pro aplikaci, kterou jste zaregistrovali.
- `Enter_the_Tenant_Info_Here` je nastavená na jednu z následujících možností:
    - Pokud vaše aplikace podporuje **účty v tomto organizačním adresáři**, nahraďte tuto hodnotu ID tenanta nebo názvem tenanta (například contoso.Microsoft.com).
    - Pokud vaše aplikace podporuje **účty v jakémkoli organizačním adresáři**, nahraďte tuto hodnotu hodnotou `organizations` .

    Pokud chcete najít koncové body ověřování pro všechny národní cloudy, přečtěte si téma [koncové body ověřování Azure AD](./authentication-national-cloud.md#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Osobní účty Microsoft se v národních cloudech nepodporují.

- `graphEndpoint` je Microsoft Graphm koncovým bodem cloudu Microsoftu pro státní správu USA.

   Pokud chcete najít Microsoft Graph koncové body pro všechny národní cloudy, přečtěte si téma [Microsoft Graph koncových bodů v národních cloudech](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="python"></a>[Python](#tab/python)

Povolení aplikace Python MSAL pro cloudy svrchovaného prostředí:

- Zaregistrujte svou aplikaci na konkrétním portálu v závislosti na cloudu. Další informace o tom, jak zvolit portál, najdete v tématu [koncové body registrace aplikace](authentication-national-cloud.md#app-registration-endpoints) .
- V závislosti na cloudu, který je uveden dále, použijte kteroukoli z těchto [ukázek](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) úložiště s několika změnami v konfiguraci.
- Použijte konkrétní autoritu v závislosti na cloudu, ve kterém jste zaregistrovali aplikaci. Další informace o autoritách pro různé cloudy najdete v tématu [koncové body ověřování Azure AD](authentication-national-cloud.md#azure-ad-authentication-endpoints).

    Tady je příklad autority:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```

- Volání rozhraní Microsoft Graph API vyžaduje adresu URL koncového bodu specifickou pro Cloud, který používáte. Pokud chcete najít Microsoft Graph koncových bodů pro všechny národní cloudy, přečtěte si téma [Microsoft Graph a kořenové koncové body služby Graph Exploreru](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

    Tady je příklad koncového bodu Microsoft Graph s oborem:

    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```

## <a name="java"></a>[Java](#tab/java)

Povolení aplikace MSAL for Java pro cloudy z svrchovaného prostředí:

- Zaregistrujte svou aplikaci na konkrétním portálu v závislosti na cloudu. Další informace o tom, jak zvolit portál, najdete v tématu [koncové body registrace aplikace](authentication-national-cloud.md#app-registration-endpoints) .
- V závislosti na cloudu, které jsou uvedené dál, použijte kteroukoli z těchto [ukázek](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) úložiště s několika změnami v konfiguraci.
- Použijte konkrétní autoritu v závislosti na cloudu, ve kterém jste zaregistrovali aplikaci. Další informace o autoritách pro různé cloudy najdete v tématu [koncové body ověřování Azure AD](authentication-national-cloud.md#azure-ad-authentication-endpoints).

Tady je příklad autority:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- Volání rozhraní Microsoft Graph API vyžaduje adresu URL koncového bodu specifickou pro Cloud, který používáte. Pokud chcete najít Microsoft Graph koncových bodů pro všechny národní cloudy, přečtěte si téma [Microsoft Graph a kořenové koncové body služby Graph Exploreru](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

Tady je příklad koncového bodu grafu s rozsahem:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

MSAL pro iOS a macOS se dají použít k získání tokenů v národních cloudech, ale při vytváření vyžaduje další konfiguraci `MSALPublicClientApplication` .

Například pokud chcete, aby vaše aplikace byla víceklientské aplikace v národním cloudu (zde USA – státní správa), můžete napsat:

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

MSAL pro iOS a macOS se dají použít k získání tokenů v národních cloudech, ale při vytváření vyžaduje další konfiguraci `MSALPublicClientApplication` .

Například pokud chcete, aby vaše aplikace byla víceklientské aplikace v národním cloudu (zde USA – státní správa), můžete napsat:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>Další kroky

Seznam koncových bodů URL Azure Portal a koncových bodů tokenu pro každý Cloud najdete v tématu věnovaném [národním koncovým bodům cloudového ověřování](authentication-national-cloud.md) .

Dokumentace k národnímu cloudu:

- [Azure Government](../../azure-government/index.yml)
- [Azure (Čína) 21Vianet](/azure/china/)
- [Azure (Německo)](../../germany/index.yml)