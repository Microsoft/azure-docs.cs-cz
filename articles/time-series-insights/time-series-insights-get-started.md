---
title: Vytvoření prostředí Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje, jak pomocí portálu Azure k vytvoření nového prostředí Statistika časové řady.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: ef5c194aa462a83cd982adab0a818f0aa095ffa0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654432"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Vytvoření nového prostředí Time Series Insights na webu Azure Portal
Tento článek popisuje postup vytvoření nového prostředí časové řady statistika pomocí portálu Azure.

Časové řady Insights umožňuje Začínáme vizualizace a dotazování dat odesílaných do služby Azure IoT Hubs a Event Hubs v minutách, umožňuje dotazu velkých objemů dat řady čas v sekundách.  Byla navržena pro internet věcí (IoT) měřítka a dokáže zpracovat terabajtů data.

## <a name="steps-to-create-the-environment"></a>Postup vytvoření prostředí
Postupujte podle těchto kroků můžete vytvořit prostředí:

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2.  Vyberte **+ nový** tlačítko.

3.  Vyberte **Internet věcí** kategorie a vyberte **časové řady Insights**.

   ![Vytvoření prostředí Time Series Insights](media/time-series-insights-get-started/1-new-tsi.png)

4.  Na **časové řady Insights** vyberte **vytvořit**.

5. Vyplňte požadované parametry. Následující tabulka popisuje jednotlivé parametry:
   
   ![Vytvoření skupiny prostředků Time Series Insights](media/time-series-insights-get-started/2-create-tsi.png)
   
   Nastavení|Navrhovaná hodnota|Popis
   ---|---|---
   Název prostředí | Jedinečný název | Tento název představuje prostředí v [explorer časové řady](https://insights.timeseries.azure.com)
   Předplatné | Vaše předplatné | Pokud máte více předplatných, vyberte odběr, který obsahuje vaše zdroj události nejlépe. Time Series Insights dokáže automaticky rozpoznat prostředky služby Azure IoT Hub a centra událostí, které existují v rámci stejného předplatného.
   Skupina prostředků | Vytvořit novou nebo použít existující | Skupina prostředků je kolekce společně používaných prostředků Azure. Můžete použít existující skupinu prostředků, například ten, který obsahuje centra událostí nebo IoT Hub. Nebo můžete použít novou, pokud tento prostředek nemá relaci k dalším prostředkům.
   Umístění | Nejbližší zdroj události | Pokud možno vyberte stejné umístění center dat, které obsahuje zdrojová data událostí, v úsilí, aby se zabránilo přidány náklady na šířku pásma mezi oblastmi a mezi zóny a přidány latence při přesunu dat mimo oblast.
   Cenová úroveň | S1 | Zvolte propustnost potřeby. Nejnižší náklady a starter kapacit vyberte S1.
   Kapacita | 1 | Kapacita je, že násobitel platí pro příjem příchozích dat míry, kapacity úložiště a náklady spojené s vybranou SKU.  Kapacitu prostředí můžete po vytvoření změnit. Nejnižší náklady vyberte kapacitou 1. 
  
6. Zkontrolujte **připnout na řídicí panel** nejlépe snadný přístup k prostředí řady čas v budoucnosti.

   ![Vytvoření služby Time Series Insights – připnutí na řídicí panel](media/time-series-insights-get-started/3-pin-create.png)

7. Vyberte **vytvořit** zahájíte proces zřizování. To může trvat několik minut.

8. Chcete-li monitorovat proces nasazení, vyberte **oznámení** symbol (ikonu zvonku).

   ![Podívejte se na oznámení](media/time-series-insights-get-started/4-notifications.png)

Při nasazení úspěšné, můžete vybrat **přejděte k prostředku** můžete nakonfigurovat další vlastnosti, nastavení zabezpečení se zásady přístupu k datům, přidejte zdroje událostí a dalších akcí.

## <a name="next-steps"></a>Další postup
* [Definovat zásady přístupu k datům](time-series-insights-data-access.md) zabezpečit vaše prostředí.
* [Přidat zdroje událostí centra událostí](time-series-insights-how-to-add-an-event-source-eventhub.md) do prostředí Azure časové řady statistiky. 
* [Odesílání událostí](time-series-insights-send-events.md) ke zdroji událostí.
* Zobrazit prostředí v [explorer časové řady Insights](https://insights.timeseries.azure.com).
