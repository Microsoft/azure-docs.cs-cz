---
title: Vytvoření prostředí Azure Time Series Insights | Microsoft Docs
description: Tento článek popisuje, jak pomocí Azure Portal vytvořit nové prostředí Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 1956fd9eaaa537d7ffa992070fc5cffd567954ce
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274812"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Vytvoření nového prostředí Time Series Insights v Azure Portal

Tento článek popisuje, jak vytvořit nové prostředí Time Series Insights pomocí Azure Portal.

Time Series Insights vám umožňuje začít vizualizovat a dotazovat tok dat do služby Azure IoT Hub a Event Hubs v řádu minut, což vám umožní dotazovat se na velké objemy dat časových řad v řádu sekund.  Byla navržena pro škálování na internetu věcí (IoT) a může zpracovávat terabajty dat.

## <a name="steps-to-create-the-environment"></a>Postup vytvoření prostředí

Pomocí těchto kroků vytvořte prostředí:

1. Přihlaste se k [Azure Portal](https://portal.azure.com).

1. Vyberte tlačítko **+ vytvořit prostředek** .

1. Vyberte kategorii **Internet věcí** a vyberte možnost **Time Series Insights**.

   [@no__t – 1Create Time Series Insights prostředí](media/time-series-insights-get-started/1-new-tsi.png)](media/time-series-insights-get-started/1-new-tsi.png#lightbox))

1. Na stránce **Time Series Insights** vyberte **vytvořit**.

1. Vyplňte požadované parametry. Jednotlivé parametry jsou vysvětleny v následující tabulce:
   
   [@no__t – 1Create skupiny prostředků Time Series Insights](media/time-series-insights-get-started/2-create-tsi.png)](media/time-series-insights-get-started/2-create-tsi.png#lightbox)
   
   Nastavením|Navrhovaná hodnota|Popis
   ---|---|---
   Název prostředí | Jedinečný název | Tento název představuje prostředí v [Průzkumníkovi časových řad](https://insights.timeseries.azure.com) .
   Formě | Vaše předplatné | Máte-li více předplatných, vyberte předplatné, které obsahuje váš zdroj událostí nejlépe. Time Series Insights může automaticky detekovat prostředky služby Azure IoT Hub a centra událostí existující v rámci stejného předplatného.
   Skupina prostředků | Vytvořit nový nebo použít existující | Skupina prostředků je kolekce prostředků Azure, které se používají společně. Můžete vybrat existující skupinu prostředků, například tu, která obsahuje vaše centrum událostí nebo IoT Hub. Nebo můžete vytvořit nový, pokud tento prostředek nesouvisí s ostatními prostředky.
   Umístění | Nejbližší zdroji události | V takovém případě můžete zvolit stejné umístění datového centra, které obsahuje vaše data zdroje událostí, a vyhnout se tak nákladům na šířku pásma mezi oblastí a mezi zónami a přidáním latence při přesunu dat mimo oblast.
   Cenová úroveň | S1 | Vyberte požadovanou propustnost. Pro nejnižší náklady a počáteční kapacitu vyberte S1.
   klíčivost | první | Kapacita je násobitel, který se vztahuje na rychlost příchozího přenosu dat, kapacitu úložiště a náklady spojené s vybranou SKU.  Po vytvoření můžete změnit kapacitu prostředí. Pro nejnižší náklady vyberte kapacitu 1. 
  
1. Vyberte **vytvořit** a zahajte proces zřizování. Může to trvat několik minut.

1. Pokud chcete monitorovat proces nasazení, vyberte symbol **oznámení** (ikona zvonku).

   [@no__t – 1Watch oznámení](media/time-series-insights-get-started/3-notifications.png)](media/time-series-insights-get-started/3-notifications.png#lightbox)

    Po úspěšném nasazení můžete vybrat **Přejít k prostředku** a nakonfigurovat další vlastnosti, nastavit zabezpečení pomocí zásad přístupu k datům, přidat zdroje událostí a další akce.

1. V **přehledu**prostředků v pravém horním rohu vyberte **ikonu připnutí** , abyste mohli v budoucnu snadno přistupovat k vašemu Time Series Insights prostředí.

   [@no__t – 1Create Time Series Insights připnout na řídicí panel](media/time-series-insights-get-started/4-pin-create.png)](media/time-series-insights-get-started/4-pin-create.png#lightbox)

## <a name="next-steps"></a>Další kroky

* [Definujte zásady přístupu k datům](time-series-insights-data-access.md) pro zabezpečení vašeho prostředí.

* [Přidejte zdroj události centra událostí](time-series-insights-how-to-add-an-event-source-eventhub.md) do prostředí Azure Time Series Insights.

* [Odešle události](time-series-insights-send-events.md) do zdroje událostí.

* Zobrazte si prostředí v [průzkumníkovi Time Series Insights](https://insights.timeseries.azure.com).
