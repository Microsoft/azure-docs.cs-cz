---
title: Vytvoření prostředí – Přehledy Azure Time Series | Dokumenty společnosti Microsoft
description: Přečtěte si, jak pomocí portálu Azure vytvořit nové prostředí Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: seodec18
ms.openlocfilehash: 7459c6afc775aa0df43d6f9285191c4c7e1b8cb8
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81602362"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Vytvoření nového prostředí Time Series Insights na webu Azure Portal

Tento článek popisuje, jak vytvořit nové prostředí Time Series Insights pomocí portálu Azure.

Time Series Insights vám umožní začít vizualizovat a dotazovat data toku do Azure IoT Hubs a Event Hubs během několika minut, což vám umožní dotazovat velké objemy dat časových řad během několika sekund.  Byl navržen pro měřítko internetu věcí (IoT) a dokáže zpracovat terabajty dat.

## <a name="steps-to-create-the-environment"></a>Postup vytvoření prostředí

Chcete-li vytvořit prostředí, postupujte takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyberte tlačítko **+ Vytvořit zdroj.**

1. Vyberte kategorii **Internet věcí** a vyberte **Přehledy časových řad**.

   [![Vytvoření prostředí Time Series Insights](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. Na stránce **Přehledy časových řad** vyberte **Vytvořit**.

1. Vyplňte požadované parametry. Následující tabulka vysvětluje každý parametr:

   [![Vytvoření skupiny prostředků Time Series Insights](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)

   Nastavení|Navrhovaná hodnota|Popis
   ---|---|---
   Název prostředí | Jedinečný název | Tento název představuje prostředí v [průzkumníku časových řad](https://insights.timeseries.azure.com)
   Předplatné | Vaše předplatné | Pokud máte více předplatných, zvolte předplatné, které obsahuje zdroj událostí nejlépe. Time Series Insights můžete automaticky rozpoznat Azure IoT Hub a Event Hub prostředky existující ve stejném předplatném.
   Skupina prostředků | Vytvoření nového nebo použití existujícího | Skupina prostředků je kolekce společně používaných prostředků Azure. Můžete zvolit existující skupinu prostředků, například tu, která obsahuje centrum událostí nebo IoT Hub. Nebo můžete vytvořit nový, pokud tento zdroj nesouvisí s jinými zdroji.
   Umístění | Nejbližší zdroj události | Pokud možno zvolte stejné umístění datového centra, které obsahuje data zdroje událostí, ve snaze vyhnout se přidaným nákladům na šířku pásma napříč oblastmi a mezi zónami a přidané latenci při přesunu dat mimo oblast.
   Cenová úroveň | S1 | Vyberte potřebnou propustnost. Chcete-li nejnižší náklady a kapacitu startéru, vyberte Možnost S1.
   Kapacita | 1 | Kapacita je násobitel se vztahuje na rychlost příchozího přenosu dat, kapacitu úložiště a náklady spojené s vybranou skladovou položkou.  Kapacitu prostředí můžete po vytvoření změnit. Pro nejnižší náklady vyberte kapacitu 1.
  
1. Vyberte **Vytvořit,** chcete-li zahájit proces zřizování. Může to trvat několik minut.

1. Chcete-li sledovat proces nasazení, vyberte symbol **Oznámení** (ikona zvonku).

   [![Podívejte se na oznámení](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. V **přehledu**prostředků ověřte nastavení konfigurace nasazení.

   [![Vytvoření služby Time Series Insights – připnutí na řídicí panel](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(Nepovinné)** Vyberte **ikonu špendlíku** v pravém horním rohu, abyste v budoucnu měli snadný přístup k prostředí Time Series Insights.

## <a name="next-steps"></a>Další kroky

* [Definujte zásady přístupu k datům](time-series-insights-data-access.md) pro zabezpečení vašeho prostředí.

* [Přidejte zdroj událostí centra událostí](time-series-insights-how-to-add-an-event-source-eventhub.md) do prostředí Azure Time Series Insights.

* [Odešlete události](time-series-insights-send-events.md) do zdroje událostí.

* Zobrazení prostředí v [průzkumníku Time Series Insights](https://insights.timeseries.azure.com).
