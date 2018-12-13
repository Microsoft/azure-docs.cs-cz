---
title: Správa prostředků Azure
titleSuffix: Language Understanding - Azure Cognitive Services
description: V tomto článku vytvoříte klíč monitorovaný koncový bod pro váš účet služby LUIS k poskytování neomezené přenosy do vašeho koncového bodu po platební plán.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 5d3e837cf644e0cb9b35a0cfc715a9dcace592e3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53082981"
---
# <a name="manage-azure-endpoint-subscription-keys"></a>Spravovat klíče předplatného koncový bod Azure

Pro účely testování a prototypu pouze úroveň free (F0) můžete použijte. Pro produkční systémy, používat [placené](https://aka.ms/luis-price-tier) vrstvy. 

> [!NOTE]
> Nepoužívejte [vytváření klíč](luis-concept-keys.md#authoring-key) za dotazy na koncový bod v produkčním prostředí.

<a name="create-luis-service"></a>
## <a name="create-luis-endpoint-key"></a>Vytvoření klíče koncového bodu služby LUIS

1. Přihlaste se k  **[Microsoft Azure](https://ms.portal.azure.com/)**. 
2. Klikněte na zelené **+** přihlášení v horní panel vlevo a vyhledejte položku "LUIS" na webu Marketplace a potom klikněte na **Language Understanding** a postupujte podle pokynů **vytvořit prostředí**  k vytvoření účtu předplatného služba LUIS. 

    ![Azure Search](./media/luis-azure-subscription/azure-search.png) 

3. Nakonfigurování odběru s nastavením název účtu, včetně cenové úrovně, atd. 

    ![Volbou Azure API](./media/luis-azure-subscription/azure-api-choice.png) 

4. Jakmile vytvoříte službu LUIS, můžete zobrazit přístupové klíče vygenerované v **správy prostředků -> klíče**.  

    ![Azure klíče](./media/luis-azure-subscription/azure-keys.png)

    > [!Note] 
    > Přihlaste se k vaší oblasti [LUIS](luis-reference-regions.md) webu a [přiřadit nový LUIS klíče koncového bodu](luis-how-to-manage-keys.md#assign-endpoint-key). Budete potřebovat název předplatného služby LUIS z kroku 3.

## <a name="change-luis-pricing-tier"></a>Změna LUIS cenová úroveň

1.  V [Azure](https://portal.azure.com), vyhledejte své předplatné služby LUIS. Klikněte na předplatné služby LUIS.
    ![Vyhledejte své předplatné služby LUIS](./media/luis-usage-tiers/find.png)
2.  Klikněte na tlačítko **cenová úroveň** Chcete-li zobrazit dostupné cenové úrovně. 
    ![Zobrazení cenové úrovně](./media/luis-usage-tiers/subscription.png)
3.  Vyberte požadovanou cenovou úroveň a klikněte na tlačítko **vyberte** změny se uloží. 
    ![Změnit úroveň služby LUIS platby](./media/luis-usage-tiers/plans.png)
4.  Po dokončení při změně cen se automaticky otevírané okno ověří novou cenovou úroveň. 
    ![Ověřte svou úroveň platby LUIS](./media/luis-usage-tiers/updated.png)
5. Nezapomeňte [přiřadit tento klíč koncového bodu](luis-how-to-manage-keys.md#assign-endpoint-key) na **publikovat** stránce a používat ho v všechny dotazy na koncový bod. 

## <a name="exceed-pricing-tier-usage"></a>Překročit využití cenové úrovně
Každá úroveň umožňuje koncový bod požadavků na účet služby LUIS určitou rychlostí. Pokud se o počet žádostí o je vyšší než povolený počet účtu Účtovaná podle objemu dat za minutu nebo za měsíc, požadavky obdrží chybu HTTP "429: příliš mnoho požadavků."

Každá úroveň umožňuje kumulativní žádostí za měsíc. Pokud celkový počet požadavků jsou vyšší než povolený počet souborů, požadavky obdrží chybu HTTP "403: zakázáno".  

## <a name="viewing-summary-usage"></a>Zobrazení souhrnu využití
Informace o použití LUIS můžete zobrazit v Azure. **Přehled** stránka zobrazuje poslední souhrnné informace, včetně volání a chyby. Pokud provedete požadavku koncového bodu LUIS, pak okamžitě podívejte se **stránka s přehledem**, povolit pro použití se zobrazí až pět minut.

![Zobrazení souhrnu využití](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Přizpůsobení využití grafů
Metriky poskytuje podrobnější pohled na data.

![Výchozí metriky](./media/luis-usage-tiers/metrics-default.png)

Grafy metrik můžete nakonfigurovat pro časové období a typu metrika. 

![Vlastní metriky](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Celkový počet transakcí prahová hodnota výstrahy
Pokud chcete vědět, kdy jste dosáhli určitou transakci prahovou hodnotu, například 10 000 transakcí, můžete vytvořit výstrahu. 

![Výchozí upozornění](./media/luis-usage-tiers/alert-default.png)

Přidat upozornění metriky pro **celkový počet volání** metriky pro určité časové období. Přidáte e-mailové adresy lidí, kteří měli obdržet upozornění. Přidání webhooků pro všechny systémy, které by měla přijímat oznámení. Aplikace logiky můžete spustit také při aktivaci tohoto upozornění. 

## <a name="next-steps"></a>Další postup

Další informace o použití [verze](luis-how-to-manage-versions.md) spravovat změny aplikace LUIS.