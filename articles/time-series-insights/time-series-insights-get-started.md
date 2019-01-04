---
title: Vytvoření prostředí Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje, jak vytvořit nové prostředí Time Series Insights pomocí webu Azure portal.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.custom: seodec18
ms.openlocfilehash: 74cd56f5a8bfe8717927c13e6bf30eb27b43fbc9
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558518"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Vytvoření nového prostředí Time Series Insights na webu Azure Portal
Tento článek popisuje, jak vytvořit nové prostředí Time Series Insights pomocí webu Azure portal.

Time Series Insights umožňuje začít vizualizaci a dotazování dat odesílaných do služby Azure IoT Hubs a služby Event Hubs v řádu minut a umožňuje dotazování velkých objemů dat časových řad v řádu sekund.  Je navržená pro škálování internet of things (IoT) a může zpracovat terabajty dat.

## <a name="steps-to-create-the-environment"></a>Postup vytvoření prostředí
Postupujte podle těchto kroků a vytvořte prostředí:

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2.  Vyberte **+ nová** tlačítko.

3.  Vyberte **Internet of Things** kategorie a vyberte **Time Series Insights**.

   ![Vytvoření prostředí Time Series Insights](media/time-series-insights-get-started/1-new-tsi.png)

4.  Na **Time Series Insights** stránce **vytvořit**.

5. Vyplňte požadované parametry. Následující tabulka popisuje jednotlivé parametry:
   
   ![Vytvoření skupiny prostředků Time Series Insights](media/time-series-insights-get-started/2-create-tsi.png)
   
   Nastavení|Navrhovaná hodnota|Popis
   ---|---|---
   Název prostředí | Jedinečný název | Tento název představuje prostředí v [Průzkumníku time Series insights](https://insights.timeseries.azure.com)
   Předplatné | Vaše předplatné | Pokud máte více předplatných, zvolte předplatné, které obsahuje váš zdroj událostí nejlépe. Time Series Insights dokáže automaticky rozpoznat prostředky služby Azure IoT Hub a centra událostí, které existují v rámci stejného předplatného.
   Skupina prostředků | Vytvořte novou, nebo použít existující | Skupina prostředků je kolekce společně používaných prostředků Azure. Můžete vybrat existující skupinu prostředků, například ten, který obsahuje vaše Centrum událostí nebo službu IoT Hub. Nebo můžete provést novou, pokud tento prostředek není v relaci k jiným prostředkům.
   Umístění | Nejbližší váš zdroj událostí | Pokud možno vyberte stejné umístění System center data, která obsahuje zdrojová data událostí, v úsilí, aby se zabránilo přidali náklady na šířku pásma mezi oblastmi a mezi zónami a latence při přesunu dat mimo oblast.
   Cenová úroveň | S1 | Vyberte potřebnou propustnost. Nejnižší náklady a počáteční kapacitu vyberte položku S1.
   Kapacita | 1 | Kapacita je násobitel platí pro rychlost příchozího přenosu dat, kapacitu a náklady spojené s vybraná skladová položka.  Kapacitu prostředí můžete po vytvoření změnit. Nejnižší náklady vyberte kapacitou 1. 
  
6. Zkontrolujte **připnout na řídicí panel** nejlépe snadný přístup k vaše prostředí Time Series Insights v budoucnu.

   ![Vytvoření služby Time Series Insights – připnutí na řídicí panel](media/time-series-insights-get-started/3-pin-create.png)

7. Vyberte **vytvořit** zahájíte proces zřizování. To může trvat několik minut.

8. Pokud chcete monitorovat proces nasazení, vyberte **oznámení** symbol (ikona zvonku).

   ![Podívejte se na oznámení](media/time-series-insights-get-started/4-notifications.png)

Po úspěšném nasazení můžete vybrat **přejít k prostředku** ke konfiguraci dalších vlastností, nastavení zabezpečení se zásady přístupu k datům, přidání zdroje událostí a dalších akcí.

## <a name="next-steps"></a>Další postup
* [Definovat zásady přístupu k datům](time-series-insights-data-access.md) zabezpečit vaše prostředí.
* [Přidání zdroje událostí centra událostí](time-series-insights-how-to-add-an-event-source-eventhub.md) do prostředí Azure Time Series Insights. 
* [Odesílání událostí](time-series-insights-send-events.md) ke zdroji události.
* Zobrazte své prostředí v [Průzkumníka služby Time Series Insights](https://insights.timeseries.azure.com).
