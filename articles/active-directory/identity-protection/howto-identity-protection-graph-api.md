---
title: Rozhraní API microsoft graphu pro ochranu identity služby Azure Active Directory
description: Přečtěte si, jak se dotazovat zjišťování rizik v Microsoft Graphu a souvisejících informací z Azure Active Directory
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
ms.openlocfilehash: f6fd62ccab4d6e32b23835d280732797e133ada8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671626"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Začínáme s Azure Active Directory Identity Protection a Microsoft Graph

Microsoft Graph je koncový bod sjednoceného rozhraní API microsoftu a domov rozhraní API [ochrany identity služby Azure Active Directory.](../active-directory-identityprotection.md) Existují čtyři api, která zveřejňují informace o rizikových uživatelích a přihlášeních. První rozhraní API **riskDetection**umožňuje dotaz ovat v aplikaci Microsoft Graph na seznam detekcí propojených rizik pro uživatele i přihlášení a související informace o zjišťování. Druhé rozhraní **API, riskyUsers**, umožňuje dotaz ovat v aplikaci Microsoft Graph pro informace o uživatelích Identity Protection zjištěné jako riziko. Třetí rozhraní API, **signIn**, umožňuje dotaz microsoft graphu na informace o přihlášení azure ad s konkrétní vlastnosti související se stavem rizika, podrobnosti a úroveň. Čtvrté rozhraní API, **identityRiskEvents**, umožňuje dotaz ovat v [aplikaci](../reports-monitoring/concept-risk-events.md) Microsoft Graph pro seznam zjišťování rizik a souvisejících informací. Rozhraní identityRiskEvents API bude zastaralé 10. doporučujeme použít **rozhraní api riskDetections.** Tento článek vás nastartuje s připojením k Microsoft Graphu a dotazováním na tato rozhraní API. Podrobné informace, úplnou dokumentaci a přístup k průzkumníku grafů naleznete na [webu Microsoft Graph](https://graph.microsoft.io/) nebo v konkrétní referenční dokumentaci pro tato rozhraní API:

* [rizikoDetonkovat API](/graph/api/resources/riskdetection?view=graph-rest-beta)
* [rozhraní API pro rizikové uživatele](/graph/api/resources/riskyuser?view=graph-rest-beta)
* [rozhraní API přihlášení](/graph/api/resources/signin?view=graph-rest-beta)
* [identityRiskEvents API](/graph/api/resources/identityriskevent?view=graph-rest-beta) *Bude zastaralé 10.1.2020*

## <a name="connect-to-microsoft-graph"></a>Připojení k microsoftgraphu

Existují čtyři kroky pro přístup k datům identity protection prostřednictvím aplikace Microsoft Graph:

1. Načtěte název domény.
2. Vytvořte novou registraci aplikace. 
3. Tento tajný klíč a několik dalších informací k ověření v aplikaci Microsoft Graph, kde obdržíte ověřovací token. 
4. Tento token slouží k vyžádání koncového bodu rozhraní API a získat data ochrany identity zpět.

Než začnete, budete potřebovat:

* Oprávnění správce k vytvoření aplikace ve službě Azure AD
* Název domény klienta (například contoso.onmicrosoft.com)

## <a name="retrieve-your-domain-name"></a>Načtení názvu domény 

1. [Přihlaste](https://portal.azure.com) se na svůj portál Azure jako správce. 
1. V levém navigačním podokně klepněte na **položku Active Directory**. 

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/41.png)

1. V části **Spravovat** klikněte na **položku Vlastnosti**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/42.png)

1. Zkopírujte název domény.

## <a name="create-a-new-app-registration"></a>Vytvoření nové registrace aplikace

1. Na stránce **Služba Active Directory** klikněte v části **Manage** na **App registrations**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/42.png)

1. V nabídce nahoře klikněte na **Nová registrace aplikace**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/43.png)

1. Na stránce **Vytvořit** proveďte následující kroky:

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/44.png)

   1. Do textového pole **Název** zadejte název aplikace (například: Aplikace rozhraní API pro zjišťování rizik Azure AD).

   1. **Jako typ**vyberte webové aplikace a / nebo webové **rozhraní API**.

   1. Do textového pole Přihlašovací adresa `http://localhost` **URL** zadejte .

   1. Klikněte na **Vytvořit**.
1. Pokud chcete otevřít stránku **Nastavení,** klikněte v seznamu aplikací na nově vytvořenou registraci aplikace. 
1. Zkopírujte **ID aplikace**.

## <a name="grant-your-application-permission-to-use-the-api"></a>Udělit aplikaci oprávnění k používání rozhraní API

1. Na stránce **Nastavení** klepněte na **položku Požadovaná oprávnění**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/15.png)

1. Na stránce **Požadovaná oprávnění** klikněte na panelu nástrojů v horní části na **Přidat**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/16.png)

1. Na stránce **Přidat přístup k rozhraní API** klikněte na Vybrat rozhraní **API**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/17.png)

1. Na stránce **Vybrat rozhraní API** vyberte Microsoft **Graph**a klepněte na tlačítko **Vybrat**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/18.png)

1. Na stránce **Přidat přístup k rozhraní API** klikněte na Vybrat **oprávnění**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/19.png)

1. Na stránce **Povolit přístup** klikněte na **číst všechny informace o riziku identity**a potom klikněte na **vybrat**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/20.png)

1. Na stránce **Přidat přístup k rozhraní API** klikněte na **Hotovo**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/21.png)

1. Na stránce **Požadovaná oprávnění** klikněte na **Udělit oprávnění**a potom klikněte na **Ano**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/22.png)

## <a name="get-an-access-key"></a>Získání přístupového klíče

1. Na stránce **Nastavení** klepněte na tlačítko **Klíče**.

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/23.png)

1. Na stránce **Klíče** proveďte následující kroky:

   ![Vytvoření aplikace](./media/howto-identity-protection-graph-api/24.png)

   1. Do textového pole **Popis klíče** zadejte popis (například *Azure AD Detekce rizik).*
   1. Jako **doba trvání**vyberte Možnost Do 1 **roku**.
   1. Klikněte na **Uložit**.
   1. Zkopírujte hodnotu klíče a vložte ji do bezpečného umístění.   
   
   > [!NOTE]
   > Pokud tento klíč ztratíte, budete se muset vrátit do této části a vytvořit nový klíč. Tento klíč uchovávejte v tajnosti: každý, kdo ho má, má přístup k vašim datům.
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Ověření v aplikaci Microsoft Graph a dotaz na rozhraní API pro zjišťování rizikidentity

V tomto okamžiku byste měli mít:

- Název domény klienta
- ID klienta 
- Klíč 

Chcete-li ověřit, odešlete požadavek na `https://login.microsoft.com` příspěvek s následujícími parametry v těle:

- grant_type: "**client_credentials**"
- Zdrojů:`https://graph.microsoft.com`
- client_id: \<ID klienta\>
- client_secret: \<váš klíč\>

Pokud je úspěšná, vrátí to ověřovací token.  
Chcete-li volat rozhraní API, vytvořte záhlaví s následujícím parametrem:

```
`Authorization`="<token_type> <access_token>"
```

Při ověřování můžete najít typ tokenu a přístupový token v vráceném tokenu.

Odešlete tuto hlavičku jako požadavek na následující adresu URL rozhraní API:`https://graph.microsoft.com/beta/identityRiskEvents`

Odpověď, pokud je úspěšná, je kolekce detekce rizik identity a přidružená data ve formátu OData JSON, které lze analyzovat a zpracovat, jak uznáte za vhodné.

Tady je ukázkový kód pro ověřování a volání rozhraní API pomocí PowerShellu.  
Stačí přidat ID klienta, tajný klíč a doménu klienta.

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

## <a name="query-the-apis"></a>Dotaz na api

Tato tři api poskytují velké množství příležitostí k načtení informací o rizikových uživatelů a přihlášení ve vaší organizaci. Níže jsou uvedeny některé běžné případy použití pro tato api a související ukázkové požadavky. Tyto dotazy můžete spustit pomocí ukázkového kódu výše nebo pomocí [aplikace Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Získejte všechny offline detekce rizik (riskDetection API)

Pomocí zásad rizika přihlášení identity protection můžete použít podmínky při zjištění rizika v reálném čase. Ale co detekce, které jsou objeveny offline? Chcete-li pochopit, jaké detekce došlo v offline, a proto by nespustily zásady rizika přihlášení, můžete dotaz ovat rozhraní riskDetection API.

```
GET https://graph.microsoft.com/beta/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Získejte všechny uživatele, kteří úspěšně prošli výzvou MFA aktivovanou rizikovými zásadami přihlášení (riskyUsers API)

Chcete-li pochopit dopad zásady ochrany identity založené na rizicích na vaši organizaci, můžete zadat dotaz všem uživatelům, kteří úspěšně prošli výzvou MFA aktivovanou riskantnízásadou přihlášení. Tyto informace vám mohou pomoci pochopit, kteří uživatelé identity Protection mohli být nepravdivě odhaleni jako riziko a kteří z vašich oprávněných uživatelů mohou provádět akce, které ai považuje za rizikové.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Získejte všechna riskantní přihlášení pro konkrétního uživatele (rozhraní API přihlášení)

Pokud se domníváte, že uživatel mohl být ohrožen, můžete lépe pochopit stav jeho rizika načtením všech jeho rizikových přihlášení. 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```
## <a name="next-steps"></a>Další kroky

Gratulujeme, právě jste udělali svůj první hovor na Microsoft Graph!  
Nyní můžete dotazovat detekce rizik identity a používat data, jak uznáte za vhodné.

Další informace o aplikaci Microsoft Graph a o tom, jak vytvářet aplikace pomocí rozhraní Graph API, naleznete v [dokumentaci](/graph/overview) a mnoho dalšího na [webu Microsoft Graph](https://developer.microsoft.com/graph). 

Související informace naleznete v:

- [Ochrana identit služby Azure Active Directory](../active-directory-identityprotection.md)
- [Typy zjišťování rizik zjištěné službou Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Přehled Microsoft Graphu](https://developer.microsoft.com/graph/docs)
- [Kořenová služba ochrany identity Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
