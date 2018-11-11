---
title: Microsoft Graph pro Azure Active Directory Identity Protection | Dokumentace Microsoftu
description: Zjistěte, jak provádět dotazy Microsoft Graphu seznam rizikových událostech a přidružené informace ze služby Azure Active Directory.
services: active-directory
keywords: Azure active directory identity protection, rizikovou událost, ohrožení zabezpečení, zásady zabezpečení, Microsoft Graphu
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2018
ms.author: markvi
ms.reviewer: nigu
ms.custom: seohack1
ms.openlocfilehash: a415258b8127bb78d8a8d1b5ef228234b34b3e26
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51287502"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Začínáme s Azure Active Directory Identity Protection a Microsoft Graph
Microsoft Graph je Microsoft unified koncový bod rozhraní API a Domovská [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) rozhraní API. Prvního rozhraní API, **identityRiskEvents**, umožňuje dotazů Microsoft Graphu seznam [rizikových událostí](../reports-monitoring/concept-risk-events.md) a související informace. Tento článek vám pomůže začít dotazování toto rozhraní API. Důkladný úvod, úplnou dokumentaci a přístup ke Graph Exploreru, najdete v článku [webu Microsoft Graphu](https://developer.microsoft.com/graph/).


Přístup k Identity Protection datům prostřednictvím Microsoft Graphu čtyři kroky:

1. Načtení názvu domény.
2. Vytvoření registrace nové aplikace. 
2. Tento tajný kód a pár dalších informací použijte k ověření u Microsoft Graphu, kam budete dostávat ověřovací token. 
3. Pomocí tohoto tokenu Zkontrolujte požadavky na koncový bod rozhraní API a získat zpět data Identity Protection.

Než začnete, budete potřebovat:

- Klient služby Azure AD P2

- Oprávnění správce k vytvoření aplikace ve službě Azure AD

- Název domény vašeho tenanta (například contoso.onmicrosoft.com).


## <a name="retrieve-your-domain-name"></a>Načtení názvu domény 

1. [Přihlaste se](https://portal.azure.com) na webu Azure portal jako správce. 

2. V levém navigačním podokně klikněte na tlačítko **služby Active Directory**. 
   
    ![Vytvoření aplikace](./media/graph-get-started/41.png)

3. Klikněte na tlačítko **vlastní názvy domén**.

    ![Názvy vlastních domén](./media/graph-get-started/71.png)

4. Ze seznamu názvů domén zkopírujte je označený jako primární název domény.

    ![Názvy vlastních domén](./media/graph-get-started/72.png)



## <a name="create-a-new-app-registration"></a>Vytvoření registrace nové aplikace

1. Na **služby Active Directory** stránku, **spravovat** klikněte na tlačítko **registrace aplikací**.

    ![Vytvoření aplikace](./media/graph-get-started/42.png)


2. V nabídce v horní části klikněte na tlačítko **registrace nové aplikace**.
   
    ![Vytvoření aplikace](./media/graph-get-started/43.png)

3. Na **vytvořit** stránce, proveďte následující kroky:
   
    ![Vytvoření aplikace](./media/graph-get-started/44.png)

    a. V **název** textového pole zadejte název vaší aplikace (např: AADIP rizikové události rozhraní API aplikace).
   
    b. Jako **typ aplikace**vyberte **webové aplikace a / nebo webové rozhraní API**.
   
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

- Parametr grant_type: "**client_credentials**"

-  Zdroj: "**https://graph.microsoft.com**"

- client_id: \<ID klienta\>

- Hodnota client_secret: \<klíč\>


Pokud je úspěšná, vrátí ověřovací token.  
Pro volání rozhraní API, vytvořte hlavičku s následující parametr:

    `Authorization`="<token_type> <access_token>"


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


## <a name="next-steps"></a>Další postup

Blahopřejeme, právě provedli první volání Microsoft graphu.  
Teď můžete zadávat dotazy identity rizikové události a používat data, ale podle potřeby.

Další informace o tom, jak vytvářet aplikace využívající rozhraní Graph API a Microsoft Graph, podívejte se [dokumentaci](https://developer.microsoft.com/graph/docs) a mnohem více na [webu Microsoft Graphu](https://developer.microsoft.com/graph/). Také, ujistěte se, že do záložek [Azure AD Identity Protection API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root) stránka, která obsahuje seznam všech API ochrany identit, která je dostupná v grafu. Protože budeme přidávat nové způsoby, jak pracovat s Identity Protection přes rozhraní API, zobrazí se vám je na této stránce.

Související informace naleznete v tématu:

-  [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

-  [Typy rizikových událostí detekovaných službou Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)

- [Microsoft Graph](https://developer.microsoft.com/graph/)

- [Přehled Microsoft Graphu](https://developer.microsoft.com/graph/docs)

- [Kořenový adresář Azure AD Identity Protection](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)

