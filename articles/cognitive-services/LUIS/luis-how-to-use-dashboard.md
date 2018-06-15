---
title: Řídicí panel aplikací pro aplikace LEOŠ | Microsoft Docs
description: Další informace o řídicí panel aplikací, nástroj vizualizovaných generování sestav, který umožňuje monitorovat aplikace v kostce jeden.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/01/2018
ms.author: v-geberr
ms.openlocfilehash: 4e117aa734c551f76c3602f81e3e2ebc6aa9f337
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "35343830"
---
# <a name="application-dashboard"></a>Řídicí panel aplikací
Řídicí panel aplikace umožňuje sledování aplikace na první pohled jeden. **Řídicí panel** zobrazí při otevření aplikace klepnutím na název aplikace v **Moje aplikace** stránce vyberte **řídicí panel** z horním panelu. 

> [!CAUTION]
> Pokud chcete nejaktuálnější metriky pro LEOŠ, budete muset:
> * Použít LEOŠ [klíč koncového bodu](luis-how-to-azure-subscription.md) vytvoří v Azure
> * Klíč koncového bodu LEOŠ použijte pro všechny požadavky koncový bod, včetně LEOŠ [rozhraní API](https://aka.ms/luis-endpoint-apis) a robota
> * Použít pro každou aplikaci LEOŠ klíč jinému koncovému bodu. Nepoužívejte klíč jeden koncový bod pro všechny aplikace. Klíč koncového bodu je sledovaný klíče úrovni není na úrovni aplikace.  

**Řídicí panel** stránka poskytuje přehled LEOŠ aplikaci, včetně aktuální model stavu a také [koncový bod](luis-glossary.md#endpoint) využití v čase. <!--The following image shows the **Dashboard** page.-->

<!-- TBD: Get a working screen shot
![The Dashboard](./media/luis-how-to-use-dashboard/dashboard.png)
-->

<!-- TBD: IS THIS STILL TRUE?
At the top of the **Dashboard** page, a contextual notification bar constantly displays notifications to update you on the required or recommended actions appropriate for the current state of your app. It also provides useful tips and alerts as needed. A detailed description of the data reported on the **Dashboard** page follows.
-->
  
## <a name="app-status"></a>Stav aplikace
Řídicí panel zobrazuje školení aplikace a stav, včetně datum a čas, kdy byl naposledy aplikace publikování vycvičena a publikován.  

![Řídicí panel – stavu aplikace](./media/luis-how-to-use-dashboard/app-state.png)

## <a name="model-data-statistics"></a>Statistiku modelu dat
Řídicí panel zobrazuje celkový počet tříd Intent entity a s popiskem utterances existující v aplikaci. 

![Statistiky Data aplikací](./media/luis-how-to-use-dashboard/app-model-count.png)

## <a name="endpoint-hits"></a>Koncový bod přístupů
Řídicí panel zobrazuje celkový počet koncový bod přístupů, které aplikace LEOŠ obdrží a umožňuje zobrazit přístupy v období, které určují. Celkový počet přístupů k zobrazí je součet hodnot koncový bod přístupů, které používají [klíč koncového bodu](./luis-concept-keys.md#endpoint-key) a koncový bod přístupy, které používají [vytváření klíč](./luis-concept-keys.md#authoring-key).

<!-- TBD: this image is old but I don't have a new one based on usage -->
![Koncový bod přístupů](./media/luis-how-to-use-dashboard/dashboard-endpointhits.png)

> [!NOTE] 
> Počet volání na aktuální koncový bod je na portálu Azure na Přehled služby LEOŠ. 
 
### <a name="total-endpoint-hits"></a>Koncový bod celkový počet přístupů
Celkový počet přístupů koncový bod přijatých do vaší aplikace od vytváření aplikace až do aktuálního data.

### <a name="endpoint-hits-per-period"></a>Koncový bod přístupů za období
Počet přístupů v tečku minulosti přijatá, zobrazí za den. Body mezi počátečním a koncovým datem představují dní, které spadají do tohoto období. Ukazatel myši nad každý bod zobrazíte počet návštěv za každý den během období. 

Výběr období, které chcete zobrazit v grafu:
 
1. Klikněte na tlačítko **další nastavení** ![tlačítko Další nastavení](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) pro přístup k seznamu tečky. Můžete vybrat období rozsahu z jednoho týdne až do jednoho roku. 

    ![Koncový bod přístupů za období](./media/luis-how-to-use-dashboard/timerange.png)

2. Vyberte období ze seznamu a pak klikněte na šipku zpět ![Šipku zpět](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) Chcete-li zobrazit graf.

### <a name="key-usage"></a>Použití klíče
Počet přístupů k používán z klíč předplatného aplikace. Další informace o klíčích předplatné, najdete v části [klíče v LEOŠ](luis-concept-keys.md). 
  
## <a name="intent-breakdown"></a>Záměrné rozdělení
**Záměr rozpis** zobrazí rozpis záměry na základě s popiskem utterances nebo koncový bod přístupů. Toto souhrnné grafy zobrazují relativní důležitost každý záměr v aplikaci. Po přesunutí ukazatele myši ukazatele myši řez, zobrazí název záměrné a procento, které představuje celkový počet přístupů k s popiskem utterances nebo koncového bodu. 

![Záměrné rozdělení](./media/luis-how-to-use-dashboard/intent-breakdown.png)

Chcete-li řídit, jestli rozdělení na základě s popiskem utterances nebo přístupů koncový bod:

1. Klikněte na tlačítko **další nastavení** ![tlačítko Další nastavení](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) pro přístup k seznamu jako na následujícím obrázku:

    ![Záměrné rozpis seznamu](./media/luis-how-to-use-dashboard/intent-breakdown-based-on.png)
2. Vyberte hodnotu ze seznamu a pak klikněte na šipku zpět ![Šipku zpět](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) Chcete-li zobrazit graf.

## <a name="entity-breakdown"></a>Rozdělení entity
Řídicí panel zobrazuje rozpis entit na základě s popiskem utterances nebo koncový bod přístupů. Toto souhrnné grafy zobrazují relativní důležitost každé entity v aplikaci. Po přesunutí ukazatele myši ukazatele myši řez, zobrazí se název entity a procento v přístupů s popiskem utterances nebo koncového bodu. 

![Rozdělení entity](./media/luis-how-to-use-dashboard/entity-breakdown.png)

Chcete-li řídit, jestli rozdělení na základě s popiskem utterances nebo přístupů koncový bod:

1. Klikněte na tlačítko **další nastavení** ![tlačítko Další nastavení](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png) pro přístup k seznamu jako na následujícím obrázku:

    ![Seznam rozpis entit](./media/luis-how-to-use-dashboard/entity-breakdown-based-on.png)
2. Vyberte hodnotu ze seznamu a pak klikněte na šipku zpět ![Šipku zpět](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) Chcete-li zobrazit graf odpovídajícím způsobem.
