---
title: Rozhraní Microsoft Graph API pro Azure Active Directory Identity Protection
description: Naučte se, jak zadávat dotazy na Microsoft Graph detekce rizik a související informace z Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 10/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e16f33cb8aa7c6ceeb1398dd23ccba31b5f936b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776132"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Začínáme s Azure Active Directory Identity Protection a Microsoft Graph

Microsoft Graph je koncový bod rozhraní Microsoft Unified API a jeho Domovská stránka rozhraní API pro [Azure Active Directory Identity Protection](./overview-identity-protection.md) . Existují tři rozhraní API, která zveřejňují informace o rizikových uživatelích a přihlášeních. První rozhraní API, **riskDetection**, umožňuje zadat dotaz na Microsoft Graph seznam uživatelů a jejich přihlašování a související informace o detekci. Druhé rozhraní API, **riskyUsers**, vám umožní dotazovat se na Microsoft Graph informace o ochraně identity uživatelů zjištěné jako rizika. Třetí rozhraní API vám umožní dotazovat **se na**Microsoft Graph pro informace o přihlášeních k Azure AD s konkrétními vlastnostmi, které se týkají stavu rizika, podrobností a úrovně. 

Tento článek vám pomůže začít s připojením k Microsoft Graph a dotazování na tato rozhraní API. Podrobné informace o tom, úplnou dokumentaci a přístup k Průzkumníku graphu, najdete na [webu Microsoft Graph](https://graph.microsoft.io/) nebo v konkrétní referenční dokumentaci pro tato rozhraní API:

* [Rozhraní API riskDetection](/graph/api/resources/riskdetection?view=graph-rest-v1.0)
* [Rozhraní API riskyUsers](/graph/api/resources/riskyuser?view=graph-rest-v1.0)
* [Rozhraní API signIn](/graph/api/resources/signin?view=graph-rest-v1.0)

## <a name="connect-to-microsoft-graph"></a>Připojit k Microsoft graphu

Existují čtyři kroky pro přístup k datům Identity Protection prostřednictvím Microsoft Graph:

- [Načíst název domény](#retrieve-your-domain-name)
- [Vytvořit novou registraci aplikace](#create-a-new-app-registration)
- [Konfigurace oprávnění rozhraní API](#configure-api-permissions)
- [Konfigurace platného pověření](#configure-a-valid-credential)

### <a name="retrieve-your-domain-name"></a>Načíst název domény 

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).  
1. Vyhledejte **Azure Active Directory**  >  **vlastní názvy domén**. 
1. Poznamenejte si `.onmicrosoft.com` doménu, budete potřebovat tyto informace v pozdějším kroku.

### <a name="create-a-new-app-registration"></a>Vytvořit novou registraci aplikace

1. V Azure Portal přejděte do **Azure Active Directory**  >  **Registrace aplikací**.
1. Vyberte **Nová registrace**.
1. Na stránce **vytvořit** proveďte následující kroky:
   1. Do textového pole **název** zadejte název vaší aplikace (například: rozhraní API pro detekci rizik Azure AD).
   1. V části **podporované typy účtů**vyberte typ účtů, které budou používat rozhraní API.
   1. Vyberte **Zaregistrovat**.
1. Zkopírujte **ID aplikace**.

### <a name="configure-api-permissions"></a>Konfigurace oprávnění rozhraní API

1. V **aplikaci** , kterou jste vytvořili, vyberte **oprávnění rozhraní API**.
1. Na stránce **konfigurovaná oprávnění** klikněte na panelu nástrojů v horní části na **Přidat oprávnění**.
1. Na stránce **Přidat přístup přes rozhraní API** klikněte na **Vybrat rozhraní API**.
1. Na stránce **Vyberte rozhraní API** vyberte **Microsoft Graph**a pak klikněte na **Vybrat**.
1. Na stránce **oprávnění API žádosti** : 
   1. Vyberte **oprávnění aplikace**.
   1. Zaškrtněte políčka vedle `IdentityRiskEvent.Read.All` a `IdentityRiskyUser.Read.All` .
   1. Vyberte **Přidat oprávnění**.
1. Vyberte **pro doménu udělit souhlas správce** . 

### <a name="configure-a-valid-credential"></a>Konfigurace platného pověření

1. V **aplikaci** , kterou jste vytvořili, vyberte **certifikáty & tajných**kódů.
1. V části **tajné klíče klienta**vyberte **nový tajný klíč klienta**.
   1. Zadejte tajný klíč klienta **Popis** a nastavte časový interval vypršení platnosti podle vašich zásad vaší organizace.
   1. Vyberte **Přidat**.

   > [!NOTE]
   > Pokud tento klíč ztratíte, budete se muset vrátit do této části a vytvořit nový klíč. Zachovat tento klíč jako tajný klíč: kdokoli, kdo má přístup k vašim datům.

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Ověřování pro Microsoft Graph a dotazování rozhraní API detekce rizik identity

V tomto okamžiku byste měli mít:

- Název domény tenanta
- ID aplikace (klienta) 
- Tajný klíč nebo certifikát klienta 

K ověření odešlete požadavek post `https://login.microsoft.com` s následujícími parametry v těle:

- grant_type: "**client_credentials**"
- partner `https://graph.microsoft.com`
- client_id: \<your client ID\>
- client_secret: \<your key\>

V případě úspěchu vrátí tato žádost ověřovací token.  
Chcete-li volat rozhraní API, vytvořte hlavičku s následujícím parametrem:

```
`Authorization`="<token_type> <access_token>"
```

Při ověřování můžete v vráceném tokenu najít typ tokenu a přístupový token.

Poslat tuto hlavičku jako požadavek na následující adresu URL API: `https://graph.microsoft.com/v1.0/identityProtection/riskDetections`

Odpověď, pokud je úspěšná, je kolekce detekcí rizik identity a přidružených dat ve formátu JSON OData, který se dá analyzovat a zpracovat podle potřeby.

### <a name="sample"></a>Ukázka

V této ukázce se zobrazuje použití sdíleného tajného klíče k ověření. V produkčním prostředí, které ukládá tajné klíče do kódu, je obecně nelibě neseno. Organizace můžou k zabezpečení těchto přihlašovacích údajů využít spravované identity pro prostředky Azure. Další informace o spravovaných identitách najdete v článku [co jsou spravované identity pro prostředky Azure](../managed-identities-azure-resources/overview.md).

Tady je ukázkový kód pro ověřování a volání rozhraní API pomocí PowerShellu.  
Stačí přidat ID klienta, tajný klíč a doménu tenanta.

```PowerShell
    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/v1.0/identityProtection/riskDetections"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>Dotazování rozhraní API

Tato tři rozhraní API poskytují řadu příležitostí k načtení informací o rizikových uživatelích a přihlášeních ve vaší organizaci. Níže jsou uvedeny některé běžné případy použití pro tato rozhraní API a přidružené ukázkové požadavky. Tyto dotazy můžete spustit pomocí výše uvedeného ukázkového kódu nebo pomocí [Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Získání všech zjistitelných rizik v režimu offline (riskDetection API)

V případě rizikových zásad přihlašování pomocí Identity Protection můžete podmínky v případě zjištění rizika v reálném čase použít. Ale jaká zjištění se zjistila v režimu offline? Chcete-li pochopit, k jakým detekcím došlo offline, a proto by neaktivovaly zásady rizik přihlašování, můžete zadat dotaz na rozhraní riskDetection API.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Získat všechny uživatele, kteří úspěšně prošli výzvou MFA aktivovanou zásadou pro rizikové přihlášení (riskyUsers API)

Aby bylo možné pochopit, jaký dopad má vaše organizace zásady na rizika ochrany identity, můžete zadat dotaz na všechny uživatele, kteří úspěšně prošli výzvou MFA aktivovanými zásadami rizikových přihlášení. Tyto informace vám můžou porozumět tomu, kteří uživatelé Identity Protection možná nepravdivě zjistili riziko a že legitimní uživatelé můžou provádět akce, které AI považuje za rizikové.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

## <a name="next-steps"></a>Další kroky

Blahopřejeme, právě jste nastavili své první volání do Microsoft Graph!  
Nyní se můžete dotazovat na detekci rizik identity a používat data, která se ale budou zobrazovat.

Další informace o Microsoft Graph a o tom, jak sestavovat aplikace pomocí Graph API, najdete v [dokumentaci](/graph/overview) a mnohem víc na [webu Microsoft Graph](https://developer.microsoft.com/graph). 

Související informace najdete v těchto tématech:

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
- [Typy detekce rizik zjištěné Azure Active Directory Identity Protection](./overview-identity-protection.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Přehled Microsoft Graphu](https://developer.microsoft.com/graph/docs)
- [Kořenová služba Azure AD Identity Protection](/graph/api/resources/identityprotectionroot?view=graph-rest-1.0)