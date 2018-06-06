---
title: Microsoft Graph pro ochrany identit Azure Active Directory | Microsoft Docs
description: Zjistěte, jak dotazovat Microsoft Graph seznam rizikových událostech a přidružené informace ze služby Azure Active Directory.
services: active-directory
keywords: ochrany identit Azure active directory, riziko událostí, ohrožení zabezpečení, zásady zabezpečení, Microsoft Graph
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.custom: seohack1
ms.openlocfilehash: c6c59285fbb2c81e5e20d28171abfe806bd51122
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713197"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Začínáme s Azure Active Directory Identity Protection a Microsoft Graph
Microsoft Graph je Microsoft unified koncový bod rozhraní API a součástí [Azure Active Directory Identity Protection](active-directory-identityprotection.md) rozhraní API. Rozhraní API first **identityRiskEvents**, umožňuje dotazování Microsoft Graph seznam [rizik události](active-directory-identityprotection-risk-events-types.md) a související informace. Tento článek vám pomůže začít dotazování toto rozhraní API. Podrobný úvod, úplnou dokumentaci a přístup do Průzkumníka grafu naleznete v tématu [Microsoft Graph lokality](https://graph.microsoft.io/).


Přístup k datům ochrany identit pomocí Microsoft Graph čtyři kroky:

1. Načíst název vaší domény.
2. Vytvořte novou registraci aplikace. 
2. Tento tajný klíč a několik další požadované informace můžete použijte k ověření do aplikace Microsoft Graph, kterou budete dostávat ověřovací token. 
3. Pomocí tohoto tokenu provádět požadavky na koncový bod rozhraní API a vrátit data pro ochranu Identity.

Než začnete, budete potřebovat:

* Oprávnění správce k vytvoření aplikace ve službě Azure AD
* Název domény vašeho klienta (například contoso.onmicrosoft.com)


## <a name="retrieve-your-domain-name"></a>Načtení názvu domény 

1. [Přihlaste se](https://portal.azure.com) na portálu Azure jako správce. 

2. V levém navigačním podokně klikněte na tlačítko **služby Active Directory**. 
   
    ![Vytváření aplikací](./media/active-directory-identityprotection-graph-getting-started/41.png)


3. V **spravovat** klikněte na tlačítko **vlastnosti**.

    ![Vytváření aplikací](./media/active-directory-identityprotection-graph-getting-started/42.png)

4. Zkopírujte název vaší domény.


## <a name="create-a-new-app-registration"></a>Vytvořit novou registraci aplikace

1. Na **služby Active Directory** stránky v **spravovat** klikněte na tlačítko **registrace aplikace**.

    ![Vytváření aplikací](./media/active-directory-identityprotection-graph-getting-started/42.png)


2. V nabídce v horní části, klikněte na tlačítko **nové registrace aplikace**.
   
    ![Vytváření aplikací](./media/active-directory-identityprotection-graph-getting-started/43.png)

3. Na **vytvořit** proveďte následující kroky:
   
    ![Vytváření aplikací](./media/active-directory-identityprotection-graph-getting-started/44.png)

    a. V **název** textovému poli, zadejte název vaší aplikace (např: aplikace AADIP riziko událostí rozhraní API).
   
    b. Jako **typ**, vyberte **webové aplikace nebo webové rozhraní API**.
   
    c. V **přihlašovací adresa URL** textovému poli, typ `http://localhost`.

    d. Klikněte na možnost **Vytvořit**.

4. Chcete-li otevřít **nastavení** stránky, v seznamu aplikací, klikněte na možnost registrace nově vytvořené aplikace. 

5. Kopírování **ID aplikace**.


## <a name="grant-your-application-permission-to-use-the-api"></a>Udělit oprávnění vaše aplikace k používání rozhraní API

1. Na **nastavení** klikněte na tlačítko **požadovaná oprávnění**.
   
    ![Vytváření aplikací](./media/active-directory-identityprotection-graph-getting-started/15.png)

2. Na **požadovaná oprávnění** stránky, na panelu nástrojů v horní části klikněte na tlačítko **přidat**.
   
    ![Vytváření aplikací](./media/active-directory-identityprotection-graph-getting-started/16.png)
   
3. Na **přidat rozhraní API pro přístup** klikněte na tlačítko **vybrat rozhraní API**.
   
    ![Vytváření aplikací](./media/active-directory-identityprotection-graph-getting-started/17.png)

4. Na **vybrat rozhraní API** vyberte **Microsoft Graph**a potom klikněte na **vyberte**.
   
    ![Vytváření aplikací](./media/active-directory-identityprotection-graph-getting-started/18.png)

5. Na **přidat rozhraní API pro přístup** klikněte na tlačítko **vyberte oprávnění**.
   
    ![Vytváření aplikací](./media/active-directory-identityprotection-graph-getting-started/19.png)

6. Na **povolit přístup** klikněte na tlačítko **číst všechny informace o identitě riziko**a potom klikněte na **vyberte**.
   
    ![Vytváření aplikací](./media/active-directory-identityprotection-graph-getting-started/20.png)

7. Na **přidat rozhraní API pro přístup** klikněte na tlačítko **provést**.
   
    ![Vytváření aplikací](./media/active-directory-identityprotection-graph-getting-started/21.png)

8. Na **požadovaných oprávnění** klikněte na tlačítko **udělit oprávnění**a potom klikněte na **Ano**.
   
    ![Vytváření aplikací](./media/active-directory-identityprotection-graph-getting-started/22.png)



## <a name="get-an-access-key"></a>Získání přístupového klíče

1. Na **nastavení** klikněte na tlačítko **klíče**.
   
    ![Vytváření aplikací](./media/active-directory-identityprotection-graph-getting-started/23.png)

2. Na **klíče** proveďte následující kroky:
   
    ![Vytváření aplikací](./media/active-directory-identityprotection-graph-getting-started/24.png)

    a. V **klíče popis** textovému poli, zadejte popis (například *AADIP riziko událostí*).
    
    b. Jako **doba trvání**, vyberte **v 1 rok**.

    c. Klikněte na **Uložit**.
   
    d. Hodnota klíče zkopírujte a vložte jej do bezpečného umístění.   
   
   > [!NOTE]
   > Pokud tento klíč ztratíte, budete muset vraťte do této části a vytvořte nový klíč. Zachovat tento klíč tajný klíč: každý, kdo je přístup ke svým datům.
   > 
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Ověřování do aplikace Microsoft Graph a dotaz rozhraní API Identity riziko události
V tomto okamžiku byste měli mít:

- Název domény vašeho klienta

- ID klienta 

- Klíč 


K ověření, odeslat požadavek post do `https://login.microsoft.com` s následujícími parametry v textu:

- grant_type: "**client_credentials**"

-  prostředek: "**https://graph.microsoft.com**"

- client_id: \<vaše ID klienta\>

- tajný klíč client_secret: \<klíč\>


V případě úspěchu vrátí ověřovací token.  
Pro volání rozhraní API, vytvoří hlavičku s následující parametr:

    `Authorization`=”<token_type> <access_token>"


Při ověřování, můžete najít typ tokenu a přístupový token v vrácený tokenu.

Odeslat tuto hlavičku jako požadavek na následující adresu URL rozhraní API: `https://graph.microsoft.com/beta/identityRiskEvents`

Odpověď, a to v případě úspěchu je kolekce identity rizikových událostech a přidružená data ve formátu OData JSON, který lze analyzovat a zpracovávají podle svých potřeb.

Tady je ukázkový kód pro ověřování a volání rozhraní API pomocí prostředí PowerShell.  
Stačí přidáte vaše ID klienta a tajný klíč, doména klienta.

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

Blahopřejeme, jste právě provedli vaše první volání Microsoft Graph.  
Nyní můžete dotaz identity rizikových událostech a použije data, ale svých potřeb.

Další informace o Microsoft Graph a jak vytvářet aplikace, které používají rozhraní Graph API, podívejte se [dokumentace](https://graph.microsoft.io/docs) zdaleka na [Microsoft Graph lokality](https://graph.microsoft.io/). Také si nezapomeňte bookmark [Azure AD Identity Protection API](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) stránky, která obsahuje seznam všech dostupných v grafu Identity ochrany pro rozhraní API. Jak jsme přidat nové způsoby, jak pracovat s ochranou Identity prostřednictvím rozhraní API, uvidíte je na této stránce.

Související informace najdete v tématu:

-  [Ochrany identit Azure Active Directory](active-directory-identityprotection.md)

-  [Typy rizikových událostí detekovaných službou Azure Active Directory Identity Protection](active-directory-identityprotection-risk-events-types.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [Přehled Microsoft Graph](https://graph.microsoft.io/docs)

- [Kořenovém adresáři služby Azure AD Identity Protection](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

