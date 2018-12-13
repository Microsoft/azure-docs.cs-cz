---
title: Řídicí panel aplikace
titleSuffix: Language Understanding - Azure Cognitive Services
description: Další informace o řídicím panelu aplikací, vizualizovaných nástrojů pro generování sestav, která umožňuje monitorování aplikací na jeden první pohled.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 6f6de278e802b83623413b0bf70e556d083818c8
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140604"
---
# <a name="model-and-usage-statistics-in-the-dashboard"></a>Statistika modelu a využití na řídicím panelu
Řídicí panel aplikací umožňuje monitorovat aplikace jeden pohled. **Řídicí panel** zobrazí při otevření aplikace po kliknutí na název aplikace **Moje aplikace** stránce vyberte **řídicí panel** v horním panelu. 

> [!CAUTION]
> Pokud chcete nejaktuálnější metriky pro LUIS, budete muset:
> * Použít LUIS [klíče koncového bodu](luis-how-to-azure-subscription.md) vytvořené v Azure
> * Použití klíče koncového bodu služby LUIS u všech žádostí koncového bodu, včetně služby LUIS [API](https://aka.ms/luis-endpoint-apis) a robota
> * Použít klíč jiný koncový bod pro každou aplikaci LUIS. Nepoužívejte klíč jeden koncový bod pro všechny aplikace. Klíč koncového bodu se sleduje v klíčových úrovni, nikoliv na úrovni aplikace.  

**Řídicí panel** stránka poskytuje přehled o aplikaci LUIS, včetně současného modelu stavu i [koncový bod](luis-glossary.md#endpoint) využití v čase. 
  
## <a name="app-status"></a>Stav aplikace
Na řídicím panelu zobrazí aplikace školení a stav, včetně datum a čas, kdy byly aplikace naposledy publikování trénují a publikovaná.  

![Řídicí panel – stav aplikace](./media/luis-how-to-use-dashboard/app-state.png)

## <a name="model-data-statistics"></a>Statistika modelu dat
Na řídicím panelu zobrazí celkový počet záměrů, entit a s popiskem projevy existující v aplikaci. 

![Statistiky dat aplikací](./media/luis-how-to-use-dashboard/app-model-count.png)

## <a name="endpoint-hits"></a>Přístupy do koncového bodu
Na řídicím panelu zobrazí celkový počet koncových bodů přístupů, které aplikace LUIS obdrží a umožňuje zobrazit volání v době, kterou jste zadat. Celkový počet přístupů, zobrazí se součet koncový bod přístupů, které používají [klíče koncového bodu](./luis-concept-keys.md#endpoint-key) a koncový bod volání, které používají [klíč pro tvorbu](./luis-concept-keys.md#authoring-key).

![Přístupy do koncového bodu](./media/luis-how-to-use-dashboard/dashboard-endpointhits.png)

> [!NOTE] 
> Počet průchodů aktuální koncový bod je na webu Azure Portal v přehledu služby LUIS. 
 
### <a name="total-endpoint-hits"></a>Celkový počet koncových bodů přístupů
Celkový počet koncových bodů přístupů do své aplikace od vytvoření aplikace až do aktuálního data.

### <a name="endpoint-hits-per-period"></a>Koncový bod přístupů za období
Počet přístupů během posledních období přijat, zobrazí za den. Body mezi počátečním a koncovým datem představují dny v tomto období. Ukazatel myši nad každý bod zobrazíte počet návštěv za každý den ve lhůtě. 

Chcete-li vybrat období pro zobrazení v grafu:
 
1. Klikněte na tlačítko **další nastavení** ![tlačítko Další nastavení](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) pro přístup k seznamu období. Můžete vybrat období od jednoho týdne až na jeden rok. 

    ![Koncový bod přístupů za období](./media/luis-how-to-use-dashboard/timerange.png)

2. Vyberte období ze seznamu a potom klikněte na šipku zpět ![Šipka vzad](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) Chcete-li zobrazit v grafu.

### <a name="key-usage"></a>Použití klíče
Počet přístupů spotřebované klíče koncového bodu aplikace. Další informace o klíčích koncový bod, najdete v části [klíče v LUIS](luis-concept-keys.md). 
  
## <a name="intent-breakdown"></a>Rozpis záměru
**Záměr rozpis** zobrazí rozpis záměry na základě s popiskem projevy nebo koncový bod přístupů. Toto souhrnné grafy zobrazují relativní význam jednotlivých záměr v aplikaci. Když myší najedete myší určitý řez, uvidíte název záměru a, kterou představuje procentuální podíl celkového počtu přístupů s popiskem projevy nebo koncového bodu. 

![Rozpis záměru](./media/luis-how-to-use-dashboard/intent-breakdown.png)

Chcete-li řídit, jestli rozdělení je založena na označené projevy nebo koncový bod přístupů:

1. Klikněte na tlačítko **další nastavení** ![tlačítka Další nastavení do seznamu přístupu](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) pro přístup k seznamu stejně jako na následujícím obrázku:

    ![Seznam rozdělení záměru](./media/luis-how-to-use-dashboard/intent-breakdown-based-on.png)
2. Vyberte hodnotu ze seznamu a potom klikněte na šipku zpět ![Šipka vzad](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) Chcete-li zobrazit v grafu.

## <a name="entity-breakdown"></a>Rozpis entity
Na řídicím panelu zobrazí rozpis entit na základě s popiskem projevy nebo koncový bod přístupů. Toto souhrnné grafy zobrazují relativní důležitost Každá entita v aplikaci. Když myší najedete myší určitý řez, uvidíte název entity a procento v přístupy s popiskem projevy nebo koncového bodu. 

![Rozpis entity](./media/luis-how-to-use-dashboard/entity-breakdown.png)

Chcete-li řídit, jestli rozdělení je založena na označené projevy nebo koncový bod přístupů:

1. Klikněte na tlačítko **další nastavení** ![tlačítko Další nastavení a získání seznamu](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) pro přístup k seznamu stejně jako na následujícím obrázku:

    ![Seznam rozdělení entit](./media/luis-how-to-use-dashboard/entity-breakdown-based-on.png)
2. Vyberte hodnotu ze seznamu a potom klikněte na šipku zpět ![Šipka vzad](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) Chcete-li zobrazit graf odpovídajícím způsobem.
