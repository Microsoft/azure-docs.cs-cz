---
title: Odesílání událostí do prostředí Azure Time Series Insights | Dokumentace Microsoftu
description: Tento kurz vysvětluje, jak vytvořit a nakonfigurovat Centrum událostí a spustit ukázkovou aplikaci pro odesílání událostí v Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 64fb9f72cfd7edef18d56f15cbcce726dd33b50d
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847265"
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>Odesílání událostí do prostředí Time Series Insights pomocí centra událostí

Tento článek vysvětluje, jak vytvořit a nakonfigurovat Centrum událostí a spustit ukázkovou aplikaci pro odesílání událostí. Pokud máte existující centrum událostí s událostmi ve formátu JSON, přeskočte tento kurz a zobrazte své prostředí v [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Konfigurace centra událostí

1. Chcete-li vytvořit Centrum událostí, postupujte podle pokynů z centra událostí [dokumentaci](https://docs.microsoft.com/azure/event-hubs/).
1. Na panelu hledání vyhledejte centra událostí. Klikněte na tlačítko **Event Hubs** ve vráceném seznamu.
1. Vyberte Centrum událostí kliknutím na jeho název.
1. V části **entity** klikněte v okně Konfigurace střední **Event Hubs** znovu.
1. Vyberte název centra událostí, abyste ho nakonfigurovali.

    ![Skupina uživatelů][1]

1. V části **entity**vyberte **skupiny příjemců**.
1. Ujistěte se, že vytváříte skupinu příjemců, kterou používá výhradně váš zdroj událostí TSI.

> [!IMPORTANT]
> Zajistěte, aby že tuto skupinu příjemců není používán jinou službu (například úloha Stream Analytics nebo jiné prostředí TSI). Pokud skupinu příjemců používají další služby, operace čtení bude negativně ovlivněna pro toto prostředí i ostatní služby. Pokud používáte `$Default` jako skupinu příjemců, by mohlo vést k potenciálním opakované použití jinými čtenáři.

1. V části **nastavení** záhlaví, vyberte **zásady přístupu pro sdílenou složku**.
1. V Centru událostí vytvořte zásadu **zásady MySendPolicy** , který slouží k odesílání událostí v C# vzorku.

    ![sdílený přístup: 1 =][2]

    ![sdílet dvěma přístup][3]

## <a name="add-time-series-insights-instances"></a>Přidání instancí služby Time Series Insights

Aktualizace služby TSI používá k přidání kontextové údaje do příchozí telemetrická data instance. Data se připojí na pomocí dotazu **ID řady času**. **ID řady času** pro windmills ukázkový projekt je `Id`. Další informace o času instancí řady a **čas řady ID**, najdete v tématu [čas řady modely](./time-series-insights-update-tsm.md).

### <a name="create-time-series-insights-event-source"></a>Vytvoření zdroje událostí Time Series Insights

1. Pokud jste ještě nevytvořili zdroj událostí, postupujte podle těchto pokynů k vytvoření zdroje událostí.
1. Zadejte `timeSeriesId` – odkazovat [čas řady modely](./time-series-insights-update-tsm.md) Další informace o **čas řady ID**.

### <a name="push-events-sample-windmills"></a>Odesílání událostí (ukázka windmills)

1. Vyhledání centra událostí v panelu vyhledávání. Klikněte na tlačítko **Event Hubs** ve vráceném seznamu.
1. Vyberte Centrum událostí kliknutím na jeho název.
1. Přejděte na **sdílené zásady přístupu** a potom **RootManageSharedAccessKey**. Kopírovat **připojení stingu – primární klíč**

   ![connection-string][4]

1. Přejděte do části https://tsiclientsample.azurewebsites.net/windFarmGen.html (Soubor > Nový > Jiné). Toto řešení běží windmill simulované zařízení.
1. Vložte připojovací řetězec zkopírovali v kroku 3 **připojovací řetězec centra událostí**
1. Klikněte na **klikněte na tlačítko Start**
1. Vraťte se do vašeho centra událostí. Měli byste vidět nové události se přijaté službou:

   ![telemetrie][5]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zobrazte své prostředí v Průzkumníku Time Series Insights](https://insights.timeseries.azure.com).

<!-- Images -->
[1]: media/send-events/consumer-group.png
[2]: media/send-events/shared-access-policy.png
[3]: media/send-events/shared-access-policy-2.png
[4]: media/send-events/sample-code-connection-string.png
[5]: media/send-events/telemetry.png