---
title: Rozhraní Microsoft Graph API pro Azure Active Directory Identity Protection
description: Naučte se, jak zadávat dotazy na Microsoft Graph detekce rizik a související informace z Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15b9bae1bd901325efdefeaa4db53df2d6b42b44
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275894"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Začínáme s Azure Active Directory Identity Protection a Microsoft Graph

Microsoft Graph je koncový bod rozhraní Microsoft Unified API a jeho Domovská stránka rozhraní API pro [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) . K dispozici jsou čtyři rozhraní API, která zveřejňují informace o rizikových uživatelích a přihlášeních. První rozhraní API, **riskDetection**, umožňuje zadat dotaz na Microsoft Graph seznam uživatelů a jejich přihlašování a související informace o detekci. Druhé rozhraní API, **riskyUsers**, vám umožní dotazovat se na Microsoft Graph informace o ochraně identity uživatelů zjištěné jako rizika. Třetí rozhraní API vám umožní dotazovat **se na**Microsoft Graph pro informace o přihlášeních k Azure AD s konkrétními vlastnostmi, které se týkají stavu rizika, podrobností a úrovně. Čtvrté rozhraní API **identityRiskEvents**umožňuje zadat dotaz na seznam [detekcí rizik](../reports-monitoring/concept-risk-events.md) a přidružených informací v Microsoft Graph. Rozhraní identityRiskEvents API bude zastaralé 10. ledna 2020; Doporučujeme místo toho použít rozhraní **riskDetections** API. Tento článek vám pomůže začít s připojením k Microsoft Graph a dotazování na tato rozhraní API. Podrobné informace o tom, úplnou dokumentaci a přístup k Průzkumníku graphu, najdete na [webu Microsoft Graph](https://graph.microsoft.io/) nebo v konkrétní referenční dokumentaci pro tato rozhraní API:

* [rozhraní API pro riskDetection](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)
* [rozhraní API pro riskyUsers](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)
* [Přihlášení k rozhraní API](https://docs.microsoft.com/graph/api/resources/signin?view=graph-rest-beta)
* [rozhraní identityRiskEvents API](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) *bude zastaralé 10. ledna 2020*

## <a name="connect-to-microsoft-graph"></a>Připojit k Microsoft graphu

Existují čtyři kroky pro přístup k datům Identity Protection prostřednictvím Microsoft Graph:

1. Načtěte název domény.
2. Vytvořte novou registraci aplikace. 
3. Pomocí tohoto tajného klíče a několika dalších informací můžete ověřit Microsoft Graph, kde obdržíte ověřovací token. 
4. Pomocí tohoto tokenu můžete vytvářet požadavky na koncový bod rozhraní API a znovu získat data o ochraně identit.

Než začnete, budete potřebovat:

* Oprávnění správce k vytvoření aplikace ve službě Azure AD
* Název domény tenanta (například contoso.onmicrosoft.com)

## <a name="retrieve-your-domain-name"></a>Načíst název domény 

1. [Přihlaste](https://portal.azure.com) se k vašemu Azure Portal jako správce. 
1. V levém navigačním podokně klikněte na **Active Directory**. 

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/41.png)

1. V části **Spravovat** klikněte na **vlastnosti**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/42.png)

1. Zkopírujte název domény.

## <a name="create-a-new-app-registration"></a>Vytvoření registrace nové aplikace

1. Na stránce **Active Directory** klikněte v části **Spravovat** na **Registrace aplikací**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/42.png)

1. V nabídce v horní části klikněte na **Registrace nové aplikace**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/43.png)

1. Na stránce **vytvořit** proveďte následující kroky:

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/44.png)

   1. Do textového pole **název** zadejte název vaší aplikace (například aplikace rozhraní API pro detekci rizik Azure AD).

   1. Jako **typ**vyberte **Webová aplikace nebo webové rozhraní API**.

   1. Do textového pole **přihlašovací adresa URL** zadejte `http://localhost`.

   1. Klikněte na **Vytvořit**.
1. Stránku **Nastavení** otevřete tak, že v seznamu aplikace kliknete na nově vytvořenou registraci aplikace. 
1. Zkopírujte **ID aplikace**.

## <a name="grant-your-application-permission-to-use-the-api"></a>Udělit aplikaci oprávnění používat rozhraní API

1. Na stránce **Nastavení** klikněte na **požadovaná oprávnění**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/15.png)

1. Na stránce **požadovaná oprávnění** klikněte na panelu nástrojů v horní části na **Přidat**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/16.png)

1. Na stránce **Přidat přístup přes rozhraní API** klikněte na **Vybrat rozhraní API**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/17.png)

1. Na stránce **Vyberte rozhraní API** vyberte **Microsoft Graph**a pak klikněte na **Vybrat**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/18.png)

1. Na stránce **Přidat přístup přes rozhraní API** klikněte na **vybrat oprávnění**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/19.png)

1. Na stránce **Povolit přístup** klikněte na **číst všechny informace o rizikech identity**a pak klikněte na **Vybrat**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/20.png)

1. Na stránce **Přidat přístup přes rozhraní API** klikněte na **Hotovo**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/21.png)

1. Na stránce **požadovaná oprávnění** klikněte na **udělit oprávnění**a potom klikněte na **Ano**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/22.png)

## <a name="get-an-access-key"></a>Získání přístupového klíče

1. Na stránce **Nastavení** klikněte na **klíče**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/23.png)

1. Na stránce **klíče** proveďte následující kroky:

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/24.png)

   1. Do textového pole **Popis klíče** zadejte popis (například *zjišťování rizik Azure AD*).
   1. Jako **dobu trvání**vyberte **v 1 roce**.
   1. Klikněte na možnost **Uložit**.
   1. Zkopírujte hodnotu klíče a vložte ji do bezpečného umístění.   
   
   > [!NOTE]
   > Pokud tento klíč ztratíte, budete se muset vrátit do této části a vytvořit nový klíč. Zachovat tento klíč jako tajný klíč: kdokoli, kdo má přístup k vašim datům.
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Ověřování pro Microsoft Graph a dotazování rozhraní API detekce rizik identity

V tomto okamžiku byste měli mít:

- Název domény tenanta
- ID klienta 
- Klíč 

K ověření odešlete požadavek post, který `https://login.microsoft.com` s následujícími parametry v těle:

- grant_type: "**client_credentials**"
- prostředek: `https://graph.microsoft.com`
- client_id: \<ID klienta\>
- client_secret: \<klíčového\>

V případě úspěchu tato akce vrátí ověřovací token.  
Chcete-li volat rozhraní API, vytvořte hlavičku s následujícím parametrem:

```
`Authorization`="<token_type> <access_token>"
```

Při ověřování můžete v vráceném tokenu najít typ tokenu a přístupový token.

Odeslat tuto hlavičku jako požadavek na následující adresu URL rozhraní API: `https://graph.microsoft.com/beta/identityRiskEvents`

Odpověď, pokud je úspěšná, je kolekce detekcí rizik identity a přidružených dat ve formátu JSON OData, který se dá analyzovat a zpracovat podle potřeby.

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

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
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
GET https://graph.microsoft.com/beta/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Získat všechny uživatele, kteří úspěšně prošli výzvou MFA aktivovanou zásadou pro rizikové přihlášení (riskyUsers API)

Aby bylo možné pochopit, jaký dopad má vaše organizace zásady na rizika ochrany identity, můžete zadat dotaz na všechny uživatele, kteří úspěšně prošli výzvou MFA aktivovanými zásadami rizikových přihlášení. Tyto informace vám můžou porozumět tomu, kteří uživatelé Identity Protection možná nepravdivě zjistili riziko a že legitimní uživatelé můžou provádět akce, které AI považuje za rizikové.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Získání všech rizikových přihlášení pro konkrétního uživatele (přihlášení k rozhraní API)

Pokud se domníváte, že došlo k ohrožení uživatele, můžete lépe pochopit stav jejich rizik tím, že načtete všechna jejich riziková přihlášení. 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```
## <a name="next-steps"></a>Další kroky

Blahopřejeme, právě jste nastavili své první volání do Microsoft Graph!  
Nyní se můžete dotazovat na detekci rizik identity a používat data, která se ale budou zobrazovat.

Další informace o Microsoft Graph a o tom, jak sestavovat aplikace pomocí Graph API, najdete v [dokumentaci](https://docs.microsoft.com/graph/overview) a mnohem víc na [webu Microsoft Graph](https://developer.microsoft.com/graph). 

Související informace najdete v těchto tématech:

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
- [Typy detekce rizik zjištěné Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Přehled Microsoft Graphu](https://developer.microsoft.com/graph/docs)
- [Kořenová služba Azure AD Identity Protection](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
