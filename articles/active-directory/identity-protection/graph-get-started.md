---
title: Microsoft Graph pro Azure Active Directory Identity Protection | Dokumentace Microsoftu
description: Zjistěte, jak provádět dotazy Microsoft Graphu seznam rizikových událostech a přidružené informace ze služby Azure Active Directory.
services: active-directory
keywords: Azure active directory identity protection, rizikovou událost, ohrožení zabezpečení, zásady zabezpečení, Microsoft Graphu
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: markvi
ms.reviewer: nigu
ms.custom: seohack1
ms.openlocfilehash: 47786ac687272cc2a88c3fa885fece9c858a3c2b
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56099067"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Začínáme s Azure Active Directory Identity Protection a Microsoft Graph

Microsoft Graph je Microsoft unified koncový bod rozhraní API a Domovská [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) rozhraní API. Existují tři rozhraní API, které poskytují informace o rizikových uživatelů a přihlášení. Prvního rozhraní API, **identityRiskEvents**, umožňuje dotazů Microsoft Graphu seznam [rizikových událostí](../reports-monitoring/concept-risk-events.md) a související informace. Druhé rozhraní API, **riskyUsers**, informace o uživatelích Identity Protection zjistilo, že rizik vám umožní dotazů Microsoft Graphu. Rozhraní API třetí **signIn**, umožňuje dotazů Microsoft Graphu informace o službě Azure AD přihlášení mají určité vlastnosti související s stav rizika, podrobnosti a úroveň. Tento článek vám pomůže začít s [připojení k Microsoft Graphu](#Connect-to-Microsoft-Graph) a [dotazování těchto rozhraní API](#Query-the-APIs). Důkladný úvod, úplnou dokumentaci a přístup ke Graph Exploreru, najdete v článku [webu Microsoft Graphu](https://graph.microsoft.io/) nebo konkrétní referenční dokumentaci pro tato rozhraní API:

* [identityRiskEvents rozhraní API](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta)
* [riskyUsers rozhraní API](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)
* [signIn rozhraní API](https://docs.microsoft.com/graph/api/resources/signin?view=graph-rest-beta)


## <a name="connect-to-microsoft-graph"></a>Připojte se k Microsoft graphu

Přístup k Identity Protection datům prostřednictvím Microsoft Graphu čtyři kroky:

1. Načtení názvu domény.
2. Vytvoření registrace nové aplikace. 
3. Tento tajný kód a pár dalších informací použijte k ověření u Microsoft Graphu, kam budete dostávat ověřovací token. 
4. Pomocí tohoto tokenu Zkontrolujte požadavky na koncový bod rozhraní API a získat zpět data Identity Protection.

Než začnete, budete potřebovat:

* Oprávnění správce k vytvoření aplikace ve službě Azure AD
* Název domény vašeho tenanta (například contoso.onmicrosoft.com).


## <a name="retrieve-your-domain-name"></a>Načtení názvu domény 

1. [Přihlaste se](https://portal.azure.com) na webu Azure portal jako správce. 

2. V levém navigačním podokně klikněte na tlačítko **služby Active Directory**. 
   
    ![Vytvoření aplikace](./media/graph-get-started/41.png)


3. V **spravovat** klikněte na tlačítko **vlastnosti**.

    ![Vytvoření aplikace](./media/graph-get-started/42.png)

4. Zkopírujte název vaší domény.


## <a name="create-a-new-app-registration"></a>Vytvoření registrace nové aplikace

1. Na **služby Active Directory** stránku, **spravovat** klikněte na tlačítko **registrace aplikací**.

    ![Vytvoření aplikace](./media/graph-get-started/42.png)


2. V nabídce v horní části klikněte na tlačítko **registrace nové aplikace**.
   
    ![Vytvoření aplikace](./media/graph-get-started/43.png)

3. Na **vytvořit** stránce, proveďte následující kroky:
   
    ![Vytvoření aplikace](./media/graph-get-started/44.png)

    a. V **název** textového pole zadejte název pro vaši aplikaci (například: AADIP rizikové události rozhraní API aplikace).
   
    b. Jako **typ**vyberte **webové aplikace a / nebo webové rozhraní API**.
   
    c. V **přihlašovací adresa URL** textové pole, typ `http://localhost`.

    d. Klikněte na možnost **Vytvořit**.

4. Chcete-li otevřít **nastavení** stránky, v seznamu aplikací, klikněte na nově vytvořenou aplikaci registrace. 

5. Kopírovat **ID aplikace**.


## <a name="grant-your-application-permission-to-use-the-api"></a>Udělení oprávnění k vaší aplikaci k používání rozhraní API

1. Na **nastavení** klikněte na **požadovaná oprávnění**.
   
    ![Vytvoření aplikace](./media/graph-get-started/15.png)

2. Na **požadovaná oprávnění** klikněte na stránku, na panelu nástrojů v horní části **přidat**.
   
    ![Vytvoření aplikace](./media/graph-get-started/16.png)
   
3. Na **přístup přes rozhraní API přidat** klikněte na **vyberte rozhraní API**.
   
    ![Vytvoření aplikace](./media/graph-get-started/17.png)

4. Na **vyberte rozhraní API** stránce **Microsoft Graphu**a potom klikněte na tlačítko **vyberte**.
   
    ![Vytvoření aplikace](./media/graph-get-started/18.png)

5. Na **přístup přes rozhraní API přidat** klikněte na **vyberte oprávnění**.
   
    ![Vytvoření aplikace](./media/graph-get-started/19.png)

6. Na **povolit přístup z** klikněte na **přečíst všechny informace o identitě riziko**a potom klikněte na tlačítko **vyberte**.
   
    ![Vytvoření aplikace](./media/graph-get-started/20.png)

7. Na **přístup přes rozhraní API přidat** klikněte na **provádí**.
   
    ![Vytvoření aplikace](./media/graph-get-started/21.png)

8. Na **požadovaná oprávnění** klikněte na **udělit oprávnění**a potom klikněte na tlačítko **Ano**.
   
    ![Vytvoření aplikace](./media/graph-get-started/22.png)



## <a name="get-an-access-key"></a>Získání přístupového klíče

1. Na **nastavení** klikněte na **klíče**.
   
    ![Vytvoření aplikace](./media/graph-get-started/23.png)

2. Na **klíče** stránce, proveďte následující kroky:
   
    ![Vytvoření aplikace](./media/graph-get-started/24.png)

    a. V **Popis klíče** textového pole zadejte popis (například *AADIP riziková událost*).
    
    b. Jako **doba trvání**vyberte **v 1 rok**.

    c. Klikněte na **Uložit**.
   
    d. Hodnotu klíče si zkopírujte a vložte ho do bezpečného umístění.   
   
   > [!NOTE]
   > Pokud ztratíte tento klíč, budete muset vrátit do této části a vytvořte nový klíč. Zachovat tento klíč tajný klíč: každý, kdo to má přístup k vašim datům.
   > 
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Ověření u Microsoft Graphu a dotazování do rozhraní API události rizika Identity
V tomto okamžiku byste měli mít:

- Název domény vašeho tenanta

- ID klienta 

- Klíč 


Pokud chcete ověřit, odeslat požadavek post do `https://login.microsoft.com` s následujícími parametry v textu:

- grant_type: “**client_credentials**”

-  Zdroj: "**https://graph.microsoft.com**"

- client_id: \<ID klienta\>

- Hodnota client_secret: \<klíč\>


Pokud je úspěšná, vrátí ověřovací token.  
Pro volání rozhraní API, vytvořte hlavičku s následující parametr:

    `Authorization`=”<token_type> <access_token>"


Při ověřování, můžete najít typ tokenu a tokenu v vráceného tokenu.

Odešlete tuto hlavičku jako požadavek na následující adresu URL rozhraní API: `https://graph.microsoft.com/beta/identityRiskEvents`

Odpověď, a to v případě úspěchu je kolekce identity rizikové události a související data ve formátu OData JSON, který je možné analyzovat a zpracovat podle svých potřeb.

Tady je ukázkový kód pro ověření a volání rozhraní API pomocí Powershellu.  
Stačí přidáte doména tenanta, ID klienta a tajný klíč.

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

## <a name="query-the-apis"></a>Dotaz rozhraní API

Tyto tři API poskytují množství příležitostí k načtení informací o rizikových uživatelů a přihlášení ve vaší organizaci. Níže jsou uvedeny některé běžné případy použití pro tato rozhraní API a související ukázkové požadavky. Můžete spustit tyto dotazy pomocí ukázkového kódu nad nebo s použitím [Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-the-high-risk-and-medium-risk-events-identityriskevents-api"></a>Získat události (identityRiskEvents rozhraní API) s vysokým rizikem a středním rizikem

Střední a rizikové události představují těch, které můžou mít možnost aktivační události přihlášení k Identity Protection nebo zásady rizik uživatelů. Protože mají střední nebo vysoká pravděpodobnost, že pokus o přihlášení uživatele není vlastníkem oprávněné identity, oprava tyto události by měla být prioritu. 

```
GET https://graph.microsoft.com/beta/identityRiskEvents?`$filter=riskLevel eq 'high' or riskLevel eq 'medium'" 
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Získá všechny uživatele, kteří úspěšně prošli výzvu MFA aktivované zásady rizikových přihlášení (riskyUsers rozhraní API)

Na vědomí následky Identity Protection, že zásady mají na vaši organizaci můžete dotazovat, všichni uživatelé, kteří úspěšně prošli výzvu MFA aktivované zásadu riziková přihlášení. Tyto informace můžou pomoct pochopit, které uživatelé Identity Protection může mít falešně zjistí na rizika a která oprávněným uživatelům může provádět akce, které AI považuje za riziková.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Získat všechny rizikových přihlášení pro konkrétního uživatele (signIn rozhraní API)

Když budete přesvědčeni, že uživatel možná ohrožené, můžete lépe porozumět stavu svých rizika načítáním všech jejich rizikových přihlášení. 
```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```




# <a name="next-steps"></a>Další postup

Blahopřejeme, právě provedli první volání Microsoft graphu.  
Teď můžete zadávat dotazy identity rizikové události a používat data, ale podle potřeby.


Další informace o tom, jak vytvářet aplikace využívající rozhraní Graph API a Microsoft Graph, podívejte se [dokumentaci](https://docs.microsoft.com/graph/overview) a mnohem více na [webu Microsoft Graphu](https://developer.microsoft.com/graph). 


Související informace naleznete v tématu:

-  [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

-  [Typy rizikových událostí detekovaných službou Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)

- [Microsoft Graph](https://developer.microsoft.com/graph/)

- [Přehled Microsoft Graphu](https://developer.microsoft.com/graph/docs)

- [Azure AD Identity Protection Service Root](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
