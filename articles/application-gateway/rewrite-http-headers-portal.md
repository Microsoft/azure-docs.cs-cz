---
title: Přepsání hlavičky požadavků http a odpovědí na portálu – Azure Application Gateway
description: Přečtěte si, jak pomocí portálu Azure nakonfigurovat aplikační bránu Azure k přepsání hlaviček HTTP v požadavcích a odpovědích procházejících bránou.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: b90736b3ed1c1f69488fde4a386cf215d751c362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74012859"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Přepis ovat hlavičky požadavků http a odpovědí pomocí Azure Application Gateway – portál Azure

Tento článek popisuje, jak pomocí portálu Azure nakonfigurovat [instanci sku souaplikací pro aplikační bránu v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) k přepsání hlaviček HTTP v požadavcích a odpovědích.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku musíte mít instanci sku brány aplikace v2. Přepisování záhlaví není podporováno ve skladové jednotce v1. Pokud nemáte skladovou položku v2, vytvořte před zahájením instanci [sku brány aplikace v2.](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps)

## <a name="create-required-objects"></a>Vytvoření požadovaných objektů

Chcete-li nakonfigurovat přepsání hlavičky HTTP, je třeba provést tyto kroky.

1. Vytvořte objekty, které jsou požadovány pro přepsání hlavičky HTTP:

   - **Akce přepsání**: Slouží k určení polí hlavičky požadavku a požadavku, která chcete přepsat, a nové hodnoty pro záhlaví. K akci přepsání můžete přidružit jednu nebo více podmínek přepsání.

   - **Podmínka přepsání**: Volitelná konfigurace. Podmínky přepsání vyhodnocují obsah požadavků http(S) a odpovědí. Akce přepsání dojde, pokud požadavek HTTP(S) nebo odpověď odpovídá podmínce přepsání.

     Pokud přidružíte více než jednu podmínku k akci, akce nastane pouze v případě, že jsou splněny všechny podmínky. Jinými slovy, operace je logická operace AND.

   - **Pravidlo přepisu**: Obsahuje více kombinací akcí přepsání / přepsání.

   - **Pořadí pravidel**: Pomáhá určit pořadí, ve kterém se pravidla přepisu spouští. Tato konfigurace je užitečná, pokud máte více pravidel pro přepsání v sadě přepsání. Pravidlo přepsání, které má nižší hodnotu pořadí pravidel, je spuštěno jako první. Pokud přiřadíte stejnou hodnotu pořadí pravidel dvěma pravidlům pro přepsání, pořadí provádění je nedeterministické.

   - **Přepsat sadu**: Obsahuje více pravidel přepsání, která budou přidružena k pravidlu směrování požadavků.

2. Připojte sadu přepisů k pravidlu směrování. Konfigurace přepsání je připojena ke zdrojovému naslouchací procesu prostřednictvím pravidla směrování. Při použití základního pravidla směrování je konfigurace přepsání záhlaví přidružena ke zdrojovému naslouchací procesu a je přepsáním globální hlavičky. Při použití pravidla směrování založeného na cestě je konfigurace přepsání záhlaví definována na mapě cesty URL. V takovém případě se vztahuje pouze na konkrétní oblast cesty lokality.

Můžete vytvořit více sad přepisů hlaviček PROTOKOLU HTTP a použít každou sadu přepsání na více naslouchacích procesech. Ale můžete použít pouze jeden přepsání nastavit na konkrétní naslouchací proces.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí svého účtu Azure.

## <a name="configure-header-rewrite"></a>Konfigurace přepsání záhlaví

V tomto příkladu změníme adresu URL přesměrování přepsáním hlavičky umístění v odpovědi HTTP odeslané back-endovou aplikací.

1. Vyberte **Všechny prostředky**a pak vyberte bránu aplikace.

2. V levém podokně vyberte **Přepíše.**

3. Vybrat **sadu přepisů**:

   ![Přidat sadu přepisů](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Zadejte název pro sadu přepisů a přidružte ji k pravidlu směrování:

   - Do pole **Název** zadejte název sady přepsání.
   - Vyberte jedno nebo více pravidel uvedených v seznamu **přidružených pravidel směrování.** Můžete vybrat pouze pravidla, která nebyla přidružena k jiným sadaem přepisů. Pravidla, která již byla přidružena k jiným množinám přepisů, jsou ztlumena.
   - Vyberte **další**.
   
     ![Přidání názvu a přidružení](media/rewrite-http-headers-portal/name-and-association.png)

5. Vytvořte pravidlo přepsání:

   - Vyberte **Přidat pravidlo přepsání**.

     ![Přidat pravidlo přepisu](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Do pole **Přepsat název pravidla** zadejte název pravidla přepsání. Zadejte číslo do pole **Pořadí pravidla.**

     ![Přidat název pravidla přepsání](media/rewrite-http-headers-portal/rule-name.png)

6. V tomto příkladu přepíšeme hlavičku umístění pouze v případě, že obsahuje odkaz na azurewebsites.net. Chcete-li to provést, přidejte podmínku k vyhodnocení, zda záhlaví umístění v odpovědi obsahuje azurewebsites.net:

   - Vyberte **Přidat podmínku** a pak vyberte pole obsahující pokyny **If,** které chcete rozbalit.

     ![Přidání podmínky](media/rewrite-http-headers-portal/add-condition.png)

   - V seznamu **Typ proměnné ke kontrole** vyberte záhlaví PROTOKOLU **HTTP**.

   - V seznamu **Typ záhlaví** vyberte **odpověď**.

   - Vzhledem k tomu, že v tomto příkladu vyhodnocujeme záhlaví umístění, což je běžné záhlaví, vyberte v části **Název záhlaví** **možnost Společné záhlaví** .

   - V seznamu **Společné záhlaví** vyberte **Umístění**.

   - V části **Rozlišování malých a**velkých písmen vyberte **Ne**.

   - V seznamu **Operátor** vyberte **equal (=)**.

   - Zadejte vzor regulárního výrazu. V tomto příkladu použijeme `(https?):\/\/.*azurewebsites\.net(.*)$`vzor .

   - Vyberte **OK**.

     ![Konfigurace podmínky If](media/rewrite-http-headers-portal/condition.png)

7. Přidejte akci pro přepsání záhlaví umístění:

   - V seznamu **Typ akce** vyberte **Nastavit**.

   - V seznamu **Typ záhlaví** vyberte **odpověď**.

   - V části **Název záhlaví**vyberte **Společné záhlaví**.

   - V seznamu **Společné záhlaví** vyberte **Umístění**.

   - Zadejte hodnotu záhlaví. V tomto příkladu použijeme `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako hodnotu záhlaví. Tato hodnota nahradí *azurewebsites.net* *contoso.com* v záhlaví umístění.

   - Vyberte **OK**.

     ![Přidání akce](media/rewrite-http-headers-portal/action.png)

8. Vyberte **Vytvořit,** chcete-li vytvořit sadu přepisů:

   ![Výběr možnosti Vytvořit](media/rewrite-http-headers-portal/create.png)

9. Otevře se zobrazení nastavení přepsání. Ověřte, zda je vytvořená sada přepisů v seznamu sad přepisů:

   ![Přepsání zobrazení sady](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Další kroky

Další informace o nastavení některých běžných případů použití naleznete v [běžných scénářích přepsání záhlaví](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).