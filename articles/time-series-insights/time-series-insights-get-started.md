---
title: Vytvoření prostředí – Azure Time Series Insights | Microsoft Docs
description: Naučte se, jak pomocí Azure Portal vytvořit nové prostředí Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 61af7922318514a7b86a349d1970c59d4d168d85
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023322"
---
# <a name="create-a-new-azure-time-series-insights-gen1-environment-in-the-azure-portal"></a>Vytvoření nového prostředí Azure Time Series Insights Gen1 v Azure Portal

> [!CAUTION]
> Toto je Gen1 článek.

Tento článek popisuje, jak vytvořit nové prostředí Azure Time Series Insights pomocí Azure Portal.

Azure Time Series Insights vám umožňuje začít vizualizovat a dotazovat tok dat do služby Azure IoT Hub a Event Hubs v řádu minut, což vám umožní dotazovat se na velké objemy dat časových řad v řádu sekund.  Byla navržena pro škálování na internetu věcí (IoT) a může zpracovávat terabajty dat.

## <a name="steps-to-create-the-environment"></a>Postup vytvoření prostředí

Pomocí těchto kroků vytvořte prostředí:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte tlačítko **+ vytvořit prostředek** .

1. Vyberte kategorii **Internet věcí** a vyberte možnost **Time Series Insights**.

   [![Vytvoření prostředí Azure Time Series Insights](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. Na stránce **Time Series Insights** vyberte **vytvořit**.

1. Vyplňte požadované parametry. Jednotlivé parametry jsou vysvětleny v následující tabulce:

   [![Vytvoření skupiny prostředků Azure Time Series Insights](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)

   Nastavení|Navrhovaná hodnota|Popis
   ---|---|---
   Název prostředí | Jedinečný název | Tento název představuje prostředí v [Průzkumníkovi časových řad](https://insights.timeseries.azure.com) .
   Předplatné | Vaše předplatné | Máte-li více předplatných, vyberte předplatné, které obsahuje váš zdroj událostí nejlépe. Azure Time Series Insights může automaticky zjišťovat prostředky Azure IoT Hub a centra událostí existující ve stejném předplatném.
   Skupina prostředků | Vytvořit nový nebo použít existující | Skupina prostředků je kolekce společně používaných prostředků Azure. Můžete vybrat existující skupinu prostředků, například tu, která obsahuje vaše centrum událostí nebo IoT Hub. Nebo můžete vytvořit nový, pokud tento prostředek nesouvisí s ostatními prostředky.
   Umístění | Nejbližší zdroji události | V takovém případě můžete zvolit stejné umístění datového centra, které obsahuje vaše data zdroje událostí, a vyhnout se tak nákladům na šířku pásma mezi oblastí a mezi zónami a přidáním latence při přesunu dat mimo oblast.
   Cenová úroveň | S1 | Vyberte potřebnou propustnost. Pro nejnižší náklady a počáteční kapacitu vyberte S1.
   Kapacita | 1 | Kapacita je násobitel, který se vztahuje na rychlost příchozího přenosu dat, kapacitu úložiště a náklady spojené s vybranou SKU.  Kapacitu prostředí můžete po vytvoření změnit. Pro nejnižší náklady vyberte kapacitu 1.
  
1. Vyberte **vytvořit** a zahajte proces zřizování. Může to trvat několik minut.

1. Pokud chcete monitorovat proces nasazení, vyberte symbol **oznámení** (ikona zvonku).

   [![Podívejte se na oznámení](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. V **přehledu** prostředků ověřte nastavení konfigurace nasazení.

   [![Vytvoření Azure Time Series Insights připnout na řídicí panel](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(Volitelné)** Výběrem **ikony připnutí** v pravém horním rohu snadno získáte přístup k prostředí Azure Time Series Insights v budoucnu.

## <a name="next-steps"></a>Další kroky

* [Definujte zásady přístupu k datům](./concepts-access-policies.md) pro zabezpečení vašeho prostředí.

* [Přidejte zdroj události centra událostí](./how-to-ingest-data-event-hub.md) do prostředí Azure Time Series Insights.

* [Odešle události](time-series-insights-send-events.md) do zdroje událostí.

* Zobrazte si prostředí v [průzkumníkovi Azure Time Series Insights](https://insights.timeseries.azure.com).