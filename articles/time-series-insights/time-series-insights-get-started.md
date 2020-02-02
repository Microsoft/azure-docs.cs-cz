---
title: Vytvoření prostředí – Azure Time Series Insights | Microsoft Docs
description: Naučte se, jak pomocí Azure Portal vytvořit nové prostředí Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/31/2020
ms.custom: seodec18
ms.openlocfilehash: 2c946c49884ef0de6843028976d4ec00ccfbcdfe
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934854"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Vytvoření nového prostředí Time Series Insights na webu Azure Portal

Tento článek popisuje, jak vytvořit nové prostředí Time Series Insights pomocí Azure Portal.

Time Series Insights vám umožňuje začít vizualizovat a dotazovat tok dat do služby Azure IoT Hub a Event Hubs v řádu minut, což vám umožní dotazovat se na velké objemy dat časových řad v řádu sekund.  Byla navržena pro škálování na internetu věcí (IoT) a může zpracovávat terabajty dat.

## <a name="steps-to-create-the-environment"></a>Postup vytvoření prostředí

Pomocí těchto kroků vytvořte prostředí:

1. Přihlaste se k [Portálu Azure](https://portal.azure.com).

1. Vyberte tlačítko **+ vytvořit prostředek** .

1. Vyberte kategorii **Internet věcí** a vyberte možnost **Time Series Insights**.

   [![vytvoření prostředí Time Series Insights](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. Na stránce **Time Series Insights** vyberte **vytvořit**.

1. Vyplňte požadované parametry. Jednotlivé parametry jsou vysvětleny v následující tabulce:
   
   [![vytvořit skupinu prostředků Time Series Insights](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)
   
   Nastavení|Navrhovaná hodnota|Popis
   ---|---|---
   Název prostředí | Jedinečný název | Tento název představuje prostředí v [Průzkumníkovi časových řad](https://insights.timeseries.azure.com) .
   Předplatné | Vaše předplatné | Máte-li více předplatných, vyberte předplatné, které obsahuje váš zdroj událostí nejlépe. Time Series Insights může automaticky zjišťovat prostředky Azure IoT Hub a centra událostí existující ve stejném předplatném.
   Skupina prostředků | Vytvořit nový nebo použít existující | Skupina prostředků je kolekce společně používaných prostředků Azure. Můžete vybrat existující skupinu prostředků, například tu, která obsahuje vaše centrum událostí nebo IoT Hub. Nebo můžete vytvořit nový, pokud tento prostředek nesouvisí s ostatními prostředky.
   Umístění | Nejbližší zdroji události | V takovém případě můžete zvolit stejné umístění datového centra, které obsahuje vaše data zdroje událostí, a vyhnout se tak nákladům na šířku pásma mezi oblastí a mezi zónami a přidáním latence při přesunu dat mimo oblast.
   Cenová úroveň | S1 | Vyberte potřebnou propustnost. Pro nejnižší náklady a počáteční kapacitu vyberte S1.
   Kapacita | 1\. místo | Kapacita je násobitel, který se vztahuje na rychlost příchozího přenosu dat, kapacitu úložiště a náklady spojené s vybranou SKU.  Kapacitu prostředí můžete po vytvoření změnit. Pro nejnižší náklady vyberte kapacitu 1. 
  
1. Vyberte **vytvořit** a zahajte proces zřizování. Může to trvat několik minut.

1. Pokud chcete monitorovat proces nasazení, vyberte symbol **oznámení** (ikona zvonku).

   [![sledovat oznámení](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. V **přehledu**prostředků ověřte nastavení konfigurace nasazení.

   [![vytvořit Time Series Insights připnout na řídicí panel](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(Volitelné)** Výběrem **ikony připnutí** v pravém horním rohu snadno získáte přístup k prostředí Time Series Insights v budoucnu.

## <a name="next-steps"></a>Další kroky

* [Definujte zásady přístupu k datům](time-series-insights-data-access.md) pro zabezpečení vašeho prostředí.

* [Přidejte zdroj události centra událostí](time-series-insights-how-to-add-an-event-source-eventhub.md) do prostředí Azure Time Series Insights.

* [Odesílání událostí](time-series-insights-send-events.md) ke zdroji události.

* Zobrazte si prostředí v [průzkumníkovi Time Series Insights](https://insights.timeseries.azure.com).
