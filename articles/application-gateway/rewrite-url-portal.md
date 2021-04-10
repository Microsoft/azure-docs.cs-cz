---
title: Přepsání adresy URL a řetězce dotazu pomocí Azure Application Gateway-Azure Portal
description: Naučte se, jak pomocí Azure Portal nakonfigurovat adresu URL a řetězec dotazu pro Azure Application Gateway.
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: how-to
ms.date: 4/05/2021
ms.author: azhussai
ms.openlocfilehash: b8ddc5e57b9ce56d6bce7e220bc840ba0fa43ae2
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384753"
---
# <a name="rewrite-url-with-azure-application-gateway---azure-portal"></a>Přepište adresu URL pomocí Azure Application Gateway-Azure Portal

Tento článek popisuje, jak pomocí Azure Portal nakonfigurovat instanci [SKU Application Gateway v2](application-gateway-autoscaling-zone-redundant.md) , aby se přepsala adresa URL.

>[!NOTE]
> Funkce pro přepsání adresy URL je k dispozici pouze pro Standard_v2 a WAF_v2 SKU Application Gateway. Když je u brány s povoleným WAF nakonfigurovaná možnost přepsat adresu URL, provedou se vyhodnocení WAF na přepsaných hlavičkách a adrese URL. [Další informace](rewrite-http-headers-url.md#using-url-rewrite-or-host-header-rewrite-with-web-application-firewall-waf_v2-sku).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Než začnete

Abyste mohli dokončit kroky v tomto článku, musíte mít instanci SKU Application Gateway v2. Přepsání adresy URL není v SKU v1 podporováno. Pokud nemáte SKU verze 2, vytvořte před zahájením instanci [sku Application Gateway v2](tutorial-autoscale-ps.md) .

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí svého účtu Azure.

## <a name="configure-url-rewrite"></a>Konfigurace přepsání adresy URL

V následujícím příkladu pokaždé, když adresa URL požadavku obsahuje */article*, přepíší se cesta URL a řetězec dotazu URL.

`contoso.com/article/123/fabrikam` -> `contoso.com/article.aspx?id=123&title=fabrikam`

1. Vyberte **všechny prostředky** a pak vyberte svoji Aplikační bránu.

2. V levém podokně vyberte **přepsat** .

3. Vyberte **sadu přepsání**:

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-1.png" alt-text="Přidat sadu přepsání":::

4. Zadejte název pro sadu přepsání a přidružte ji k pravidlu směrování:

    a. Do pole **název** zadejte název sady přepsaného zápisu.
    
    b. Vyberte jedno nebo více pravidel uvedených v seznamu **přidružená pravidla směrování** . Slouží k přidružení konfigurace přepisování ke zdroji naslouchacího procesu prostřednictvím pravidla směrování. Můžete vybrat pouze pravidla směrování, která nebyla přidružena k ostatním přepsaným sadám. Pravidla, která už jsou přidružená k jiným sadám přepsání, jsou šedá.
    
    c. Vyberte **Další**.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-2.png" alt-text="Přidružit k pravidlu":::

5. Vytvořit pravidlo přepsání:

    a. Vyberte **Přidat pravidlo přepsání**.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-3.png" alt-text="Snímek obrazovky, který zvýrazní pravidlo přidat přepsání":::
    
    b. Do pole **název pravidla přepsání** zadejte název pravidla pro přepsání. Zadejte číslo do pole **pořadí pravidel** .

6. V tomto příkladu přepíšeme cestu URL a řetězec dotazu URL pouze v případě, že cesta obsahuje */article*. Provedete to tak, že přidáte podmínku pro vyhodnocení, jestli cesta URL obsahuje */article* .

    a. Vyberte **Přidat podmínku** a potom vyberte pole **obsahující pokyny pro jeho rozšíření** .
    
    b. Vzhledem k tomu, že v tomto příkladu chceme v cestě URL kontrolovat vzor */article* , vyberte v seznamu **typ proměnné pro kontrolu** možnost **Serverová proměnná**.
    
    c. V seznamu **proměnné serveru** vyberte uri_path
    
    d. V části rozlišovat **velikost písmen** vyberte možnost **ne**.
    
    e. V seznamu **operátor** vyberte **EQUAL (=)**.
    
    f. Zadejte vzor regulárního výrazu. V tomto příkladu použijeme vzor. `.*article/(.*)/(.*)`
    
      () slouží k zachycení podřetězce pro pozdější použití v sestavování výrazu pro přepis cesty URL. Další informace najdete [tady](rewrite-http-headers-url.md#capturing).

    například Vyberte **OK**.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-4.png" alt-text="Condition"::: (Podmínka)

 

7. Přidejte akci pro přepsání adresy URL a cesty URL

   a. V seznamu **typ přepsání** vyberte možnost **Adresa URL**.

   b. V seznamu **typ akce** vyberte možnost **nastavit**.

   c. V části **součásti** vyberte možnost **cesta URL a řetězec dotazu URL** .

   d. Do pole **Adresa URL** zadejte novou hodnotu v cestě. V tomto příkladu použijeme **/article.aspx** . 

   e. Do pole **hodnota řetězce dotazu URL** zadejte novou hodnotu řetězce dotazu URL. V tomto příkladu použijeme **ID = {var_uri_path_1} &title = {var_uri_path_2}** .
    
    `{var_uri_path_1}` a `{var_uri_path_1}` slouží k načtení podřetězců zachycených při vyhodnocování podmínky v tomto výrazu `.*article/(.*)/(.*)`
    
   f. Vyberte **OK**.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-5.png" alt-text="Akce":::

8. Kliknutím na **vytvořit** vytvořte sadu přepsání.

9. Ověřte, že se nová sada přepisování zobrazuje v seznamu přepsaných sad.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-6.png" alt-text="Přidat pravidlo pro přepsání":::

## <a name="verify-url-rewrite-through-access-logs"></a>Ověření přepsání adresy URL prostřednictvím protokolů přístupu

Podívejte se na následující pole v protokolech Access a ověřte, jestli se k přepsání adresy URL nestalo podle vašeho očekávání.

* **originalRequestUriWithArgs**: Toto pole obsahuje původní adresu URL požadavku.
* **RequestUri**: Toto pole obsahuje adresu URL po operaci přepsání u Application Gateway

Další informace o všech polích v protokolech přístupu najdete [tady](application-gateway-diagnostics.md#for-application-gateway-and-waf-v2-sku).

##  <a name="next-steps"></a>Další kroky

Další informace o tom, jak nastavit přepsání pro některé běžné případy použití, najdete v tématu [běžné scénáře přepsání](rewrite-http-headers.md).
