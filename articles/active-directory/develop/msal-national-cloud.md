---
title: Použít Microsoft Authentication Library (MSAL) v národních cloudech – platforma identit Microsoft
description: Microsoft Authentication Library (MSAL) vývojářům aplikací umožňuje získat tokeny pro volání zabezpečená webová rozhraní API. Tyto webové rozhraní API může být Microsoft Graphu, jiné APIs Microsoft, partnerskému webovému rozhraní API nebo vlastního webového rozhraní API. Knihovna MSAL podporuje více aplikačních architektur a platformy.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9958356cae3c486ecf68e280f33d63c6a537b14
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235269"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Použití MSAL v prostředí národních cloudů

[Národní cloudy](authentication-national-cloud.md) jsou fyzicky izolované instance Azure. Tyto oblasti Azure pomoct, ujistěte se, že požadavky na rezidenci a suverenitu, dodržování předpisů dat jsou zachované v zeměpisné oblasti.

Kromě po celém světě cloudu společnosti Microsoft Microsoft Authentication Library (MSAL) umožňuje vývojářům aplikací v národních cloudech získat tokeny, aby bylo možné ověřovat a zabezpečená webová rozhraní API volat. Tyto webové rozhraní API může být Microsoft Graphu nebo další APIs Microsoft.

Včetně globální cloudové služby Azure Active Directory (Azure AD) nasazené v následujících národních cloudů:  

- Azure Government
- Azure China 21Vianet
- Azure Germany

Tato příručka ukazuje, jak se přihlásit k pracovní a školní účty, získat přístupový token a volání rozhraní Microsoft Graph API [cloudu Azure Government](https://azure.microsoft.com/global-infrastructure/government/) prostředí.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jsou splněny tyto požadavky.

### <a name="choose-the-appropriate-identities"></a>Zvolte odpovídající identit

[Azure Government](https://docs.microsoft.com/azure/azure-government/) aplikací můžete použít identit Azure AD Government a Azure AD veřejné identity k ověřování uživatelů. Vzhledem k tomu můžete použít některý z těchto identit, je potřeba rozhodnout, které koncového bodu autority, měli byste zvolit pro váš scénář:

- Veřejné Azure AD: Běžně používá, pokud už vaše organizace má Azure AD veřejné tenanta služby podpory Office 365 (veřejné nebo GCC) nebo v jiné aplikaci.
- Azure AD Government: Běžně používá, pokud vaše organizace už má Azure AD Government tenanta služby podpory Office 365 (GCC vysokou nebo Military, DoD) nebo ve službě Azure AD Government je vytvoření nového tenanta.

Až se rozhodnete, je zvláštní pozornost, kde provádíte registrace vaší aplikace. Pokud se rozhodnete veřejný Azure AD identity pro vaše aplikace Azure Government, musí zaregistrovat aplikaci v tenantovi Azure AD veřejné.

### <a name="get-an-azure-government-subscription"></a>Získání předplatného Azure Government

Získání předplatného Azure Government, najdete v článku [Správa a připojení k vašemu předplatnému Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).

Pokud ještě nemáte předplatné Azure Government, vytvořte [bezplatný účet](https://azure.microsoft.com/global-infrastructure/government/request/) předtím, než začnete.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>Krok 1: Registrace vaší aplikace

1. Přihlaste se k webu [Azure Portal](https://portal.azure.us/).
    
   Koncové body Azure portal pro další národní cloudy, najdete v tématu [App registrace koncových bodů](authentication-national-cloud.md#app-registration-endpoints).

1. Pokud váš účet poskytuje přístup k více než jednoho tenanta, v pravém horním rohu vyberte svůj účet a nastavení portálu relace k požadované službě Azure AD tenanta.
1. Přejděte [registrace aplikací](https://aka.ms/ra/ff) stránky na platformě Microsoft identity pro vývojáře.
1. Když **zaregistrovat aplikaci** se zobrazí stránka, zadejte název pro vaši aplikaci.
1. V části **podporovaných typů účtu**vyberte **účty v libovolném adresáři organizace**.
1. V **identifikátor URI pro přesměrování** vyberte **webové** platformy a nastavte hodnotu na adresu URL aplikace založené na vašem webovém serveru. Najdete v dalších částech pokyny o tom, jak nastavit a získat adresu URL pro přesměrování ve Visual Studiu a uzel.
1. Vyberte **Zaregistrovat**.
1. V aplikaci **přehled** stránce si poznamenejte **ID aplikace (klient)** hodnotu.
1. Tento kurz vyžaduje, abyste zapnuli [implicitní grant tok](v2-oauth2-implicit-grant-flow.md). V levém podokně zaregistrovanou aplikaci, vyberte **ověřování**.
1. V **upřesňující nastavení**v části **implicitní grant**, vyberte **tokeny typu ID** a **přístupové tokeny** zaškrtávací políčka. Tokeny typu ID a přístupové tokeny jsou povinné, protože tato aplikace potřebuje přihlásit uživatele a volat rozhraní API.
1. Vyberte **Uložit**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Krok 2:  Nastavení webového serveru nebo projektu

- [Stáhnout soubory projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) pro místní webový server, jako je například uzel.

  nebo

- [Stáhněte si Visual Studio projekt](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Potom přejděte ke [konfigurovat vaše JavaScript SPA](#step-4-configure-your-javascript-spa) konfigurace vzorový kód před jejím spuštěním.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Krok 3: Knihovna Microsoft Authentication Library použít k přihlášení uživatele

Postupujte podle kroků v [kurz jazyka JavaScript](tutorial-v2-javascript-spa.md#create-your-project) k vytvoření projektu a integrovat MSAL k přihlášení uživatele.

### <a name="step-4-configure-your-javascript-spa"></a>Krok 4: Konfigurace vaší aplikace SPA v JavaScriptu

V `index.html` soubor vytvořen během instalace projektu, přidejte informace o registraci aplikace. Přidejte následující kód v horní části v rámci `<script></script>` značky v těle vaše `index.html` souboru:

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

V kódu:

- `Enter_the_Application_Id_here` je **ID aplikace (klient)** hodnotu této aplikace, které jste zaregistrovali.
- `Enter_the_Tenant_Info_Here` je nastavená na jednu z následujících možností:
    - Pokud vaše aplikace podporuje **účty v tomto adresáři organizace**, tuto hodnotu nahraďte ID tenanta nebo název (například contoso.microsoft.com) pro tenanta.
    - Pokud vaše aplikace podporuje **účty v libovolném adresáři organizace**, nahradí tato hodnota se `organizations`.
    
    Koncové body ověřování pro národní cloudy, najdete v tématu [koncové body ověřování Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Osobní účty Microsoft se nepodporují v národních cloudech.
  
- `graphEndpoint` je koncový bod Microsoft Graph pro Microsoft cloud pro státní správu USA.

   Microsoft Graph koncové body pro národní cloudy, najdete v tématu [Microsoft Graphu koncových bodů v národních cloudech](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="net"></a>.NET

MSAL.NET slouží k přihlašování uživatelů, získání tokenů a volání rozhraní Microsoft Graph API v národních cloudech.

Následující kurzy ukazují, jak sestavit aplikaci .NET Core 2.2 MVC Web. Aplikace používá k přihlášení uživatelů s pracovním a školním účtem v organizaci, který patří do národních cloudů OpenID Connect.

- Chcete-li přihlásit uživatele a získat tokeny, postupujte podle [v tomto kurzu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Volání rozhraní Microsoft Graph API, postupujte podle [v tomto kurzu](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="next-steps"></a>Další postup

Další informace:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)