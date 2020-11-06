---
title: Přepis hlaviček požadavků a odpovědí HTTP na portálu – Azure Application Gateway
description: Naučte se, jak pomocí Azure Portal nakonfigurovat službu Azure Application Gateway, aby přepsala hlavičky HTTP v žádostech a odpovědích, které procházejí bránou.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 79314db13531f1fcf518c7931d4a1aa9158a172b
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397191"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Přepsání hlaviček požadavků a odpovědí HTTP pomocí Azure Application Gateway-Azure Portal

Tento článek popisuje, jak pomocí Azure Portal nakonfigurovat instanci [SKU Application Gateway v2](./application-gateway-autoscaling-zone-redundant.md) , aby v žádostech a odpovědích přepsala hlavičky HTTP.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Než začnete

Abyste mohli dokončit kroky v tomto článku, musíte mít instanci SKU Application Gateway v2. Přepisování hlaviček není v SKU v1 podporováno. Pokud nemáte SKU verze 2, vytvořte před zahájením instanci [sku Application Gateway v2](./tutorial-autoscale-ps.md) .

## <a name="create-required-objects"></a>Vytvořit požadované objekty

Chcete-li nakonfigurovat přepsání hlaviček protokolu HTTP, je nutné provést tyto kroky.

1. Vytvořte objekty, které jsou požadovány pro přepsání hlaviček protokolu HTTP:

   - **Akce přepisu** : slouží k zadání polí hlavičky Request a Request, která chcete přepsat, a nové hodnoty hlaviček. K akci přepsání můžete přidružit jednu nebo více podmínek přepsání.

   - **Podmínka přepisu** : volitelná konfigurace. Podmínky přepisu vyhodnocují obsah požadavků a odpovědí HTTP (S). Akce přepisu nastane, pokud požadavek nebo odpověď HTTP (S) odpovídá podmínce přepsání.

     Pokud k akci přiřadíte více než jednu podmínku, bude akce provedena pouze v případě, že jsou splněny všechny podmínky. Jinými slovy, operace je logická a operace.

   - **Pravidlo přepisu** : obsahuje více kombinací podmínka pro akci přepisu nebo přepisu.

   - **Pořadí pravidel** : pomáhá určit pořadí, ve kterém se pravidla přepsání spouštějí. Tato konfigurace je užitečná, když máte více pravidel pro přepsání v sadě přepsání. Jako první se spustí pravidlo pro přepsání s nižší hodnotou pořadí pravidel. Pokud přiřadíte stejnou hodnotu pořadí pravidel pro dvě pravidla přepisu, pořadí spuštění je nedeterministické.

   - **Sada přepsání** : obsahuje více pravidel pro přepis, která budou přidružena k pravidlu směrování požadavků.

2. Připojte sadu přepsání s pravidlem směrování. Konfigurace přepsání je připojena ke zdroji naslouchání prostřednictvím pravidla směrování. Použijete-li pravidlo základního směrování, je konfigurace přepsání hlaviček přidružena ke zdrojovému naslouchacího procesu a je přepsána globální hlavičkou. Když použijete pravidlo směrování na základě cesty, konfigurace opětovného zápisu hlaviček se definuje na mapě cesty URL. V takovém případě platí pouze pro konkrétní oblast cesty lokality.

Můžete vytvořit více sad pro přepsání hlaviček protokolu HTTP a použít každou sadu přepsání na více posluchačů. Můžete ale použít jenom jednu sadu přepsaného zápisu na konkrétní naslouchací proces.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí svého účtu Azure.

## <a name="configure-header-rewrite"></a>Konfigurace přepsání hlaviček

V tomto příkladu změníme adresu URL pro přesměrování přepsáním hlavičky umístění v odpovědi HTTP odesílané back-endové aplikaci.

1. Vyberte **všechny prostředky** a pak vyberte svoji Aplikační bránu.

2. V levém podokně vyberte **přepsat** .

3. Vyberte **sadu přepsání** :

   ![Přidat sadu přepsání](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Zadejte název pro sadu přepsání a přidružte ji k pravidlu směrování:

   - Do pole **název** zadejte název sady přepsaného zápisu.
   - Vyberte jedno nebo více pravidel uvedených v seznamu **přidružená pravidla směrování** . Můžete vybrat pouze pravidla, která nebyla přidružena k ostatním přepsaným sadám. Pravidla, která již byla přidružena k jiným sadám přepsání, jsou ztlumena.
   - Vyberte **Další**.
   
     ![Přidat název a přidružení](media/rewrite-http-headers-portal/name-and-association.png)

5. Vytvořit pravidlo přepsání:

   - Vyberte **Přidat pravidlo přepsání**.

     ![Přidat pravidlo pro přepsání](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Do pole **název pravidla přepsání** zadejte název pravidla pro přepsání. Zadejte číslo do pole **pořadí pravidel** .

     ![Přidat název pravidla přepsání](media/rewrite-http-headers-portal/rule-name.png)

6. V tomto příkladu přepíšeme hlavičku umístění pouze v případě, že obsahuje odkaz na azurewebsites.net. Chcete-li to provést, přidejte podmínku pro vyhodnocení, zda hlavička umístění v odpovědi obsahuje azurewebsites.net:

   - Vyberte **Přidat podmínku** a potom vyberte pole **obsahující pokyny pro jeho rozšíření** .

     ![Přidání podmínky](media/rewrite-http-headers-portal/add-condition.png)

   - V seznamu **typ proměnné pro kontrolu** vyberte **záhlaví HTTP**.

   - V seznamu **typ hlavičky** vyberte možnost **odpověď**.

   - Vzhledem k tomu, že v tomto příkladu vyhodnocujeme hlavičku Location, která je běžnou hlavičkou, vyberte v části **název záhlaví** možnost **společné záhlaví** .

   - V seznamu **Common Header (společné záhlaví** ) vyberte **umístění**.

   - V části rozlišovat **velikost písmen** vyberte možnost **ne**.

   - V seznamu **operátor** vyberte **EQUAL (=)**.

   - Zadejte vzor regulárního výrazu. V tomto příkladu použijeme vzor  `(https?):\/\/.*azurewebsites\.net(.*)$` .

   - Vyberte **OK**.

     ![Konfigurace podmínky if](media/rewrite-http-headers-portal/condition.png)

7. Přidejte akci pro přepsání hlavičky umístění:

   - V seznamu **typ akce** vyberte možnost **nastavit**.

   - V seznamu **typ hlavičky** vyberte možnost **odpověď**.

   - V části **název záhlaví** vyberte **společné záhlaví**.

   - V seznamu **Common Header (společné záhlaví** ) vyberte **umístění**.

   - Zadejte hodnotu hlavičky. V tomto příkladu použijeme `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako hodnotu hlavičky. Tato hodnota nahradí *azurewebsites.NET* hodnotou *contoso.com* v hlavičce umístění.

   - Vyberte **OK**.

     ![Přidání akce](media/rewrite-http-headers-portal/action.png)

8. Vyberte **vytvořit** a vytvořte sadu přepsání:

   ![Výběr možnosti Vytvořit](media/rewrite-http-headers-portal/create.png)

9. Otevře se zobrazení přepsané sady. Ověřte, že je nastavená sada přepsání, kterou jste vytvořili, v seznamu přepsaných sad:

   ![Přepište zobrazení sady](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak nastavit některé běžné případy použití, najdete v tématu [běžné scénáře přepisování hlaviček](./rewrite-http-headers.md).