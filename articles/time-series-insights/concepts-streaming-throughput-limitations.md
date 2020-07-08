---
title: Omezení propustnosti příjmu streamování – Azure Time Series Insights | Microsoft Docs
description: Přečtěte si o omezeních propustnosti příchozích dat v Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: d5a82aa3aca55622308cae5816c95df9766048da
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049822"
---
# <a name="streaming-ingestion-throughput-limits"></a>Omezení propustnosti příjmu streamování

Omezení příchozího přenosu dat Azure Time Series Insights streamování jsou popsány níže.

> [!TIP]
> Podrobný seznam všech omezení pro verzi Preview najdete v tématu [Naplánování prostředí Preview](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) . **TODO** odstraní slovo "Preview", jakmile se nadpis článku aktualizoval.

## <a name="per-environment-limitations"></a>Omezení podle prostředí

Míry příchozího přenosu dat se zobrazují jako faktor počtu zařízení, která jsou ve vaší organizaci, četnosti emisí událostí a velikosti jednotlivých událostí:

*  **Počet zařízení** × **četnost měření událostí** × **Velikost každé události**.

Ve výchozím nastavení Time Series Insights může ingestovat příchozí data rychlostí **až 1 MB za sekundu (MB/s) na Time Series Insights prostředí**. Existují další omezení [na oddíl centra](concepts-streaming-throughput-limitations.md#hub-partitions-and-per-partition-limits).

> [!TIP]
>
> * Podpora prostředí pro přijímání rychlostí až 16 MB/s může být zajištěna žádostí.
> * Pokud potřebujete vyšší propustnost odesláním lístku podpory prostřednictvím Azure Portal, kontaktujte nás.
 
* **Příklad 1:**

    Při expedici společnosti Contoso je 100 000 zařízení, která generují události třikrát za minutu. Velikost události je 200 bajtů. Používají IoT Hub se čtyřmi oddíly jako zdroj události Time Series Insights.

    * Rychlost příjmu pro své Time Series Insights prostředí by byla: **100 000 zařízení * 200 bajtů/událost * (3/60 události/s) = 1 MB/s**.
    * Frekvence přijímání zpráv na oddíl by byla 0,25 MB/s.
    * Míra ingestování společnosti Contoso bude v rámci omezení škálování.

* **Příklad 2:**

    Analýza loďstva společnosti Contoso má 60 000 zařízení, která každou sekundu emitují událost. V případě Time Series Insightsho zdroje událostí používají centrum událostí s počtem oddílů 4. Velikost události je 200 bajtů.

    * Frekvence ingestování prostředí by byla: **60 000 zařízení × 200 bajtů/událost * 1 událost/s = 12 MB/** s.
    * Frekvence za oddíl by byla 3 MB/s.
    * Míra ingestování infrastruktury společnosti Contoso je nad limity prostředí a oddílů. Můžou odeslat žádost o Time Series Insights přes Azure Portal, aby se zvýšila rychlost přijímání zpráv pro své prostředí, a vytvořit centrum událostí s více oddíly, které budou v rámci limitů verze Preview.

## <a name="hub-partitions-and-per-partition-limits"></a>Omezení oddílů centra a na oddíly

Při plánování Time Series Insightsho prostředí je důležité zvážit konfiguraci zdrojů událostí, ke kterým se budete připojovat Time Series Insights. Azure IoT Hub i Event Hubs využívají oddíly k povolení horizontálního škálování pro zpracování událostí. 

*Oddíl* je seřazená posloupnost událostí, která je držena v centru. Počet oddílů se nastaví během fáze vytváření centra a nedá se změnit.

[Informace o tom, kolik oddílů](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need) potřebuji pro Event Hubs Doporučené postupy, najdete v tématu.

> [!NOTE]
> Většina rozbočovačů IoT používaných s Azure Time Series Insights potřebuje jenom čtyři oddíly.

Bez ohledu na to, jestli vytváříte nové centrum pro Time Series Insights prostředí nebo už použijete stávající, budete muset vypočítat sazbu ingestování na oddíly, abyste zjistili, jestli jsou v rámci omezení. 

Verze Preview Azure Time Series Insights aktuálně má **omezení 0,5 MB/s na oddíly**.

### <a name="iot-hub-specific-considerations"></a>Předpoklady týkající se IoT Hub

Při vytvoření zařízení v IoT Hub se trvale přiřadí k oddílu. V takovém případě IoT Hub moci zaručit řazení událostí (protože se přiřazení nikdy nemění).

Pevné přiřazení oddílu také ovlivňuje Time Series Insights instance, které ingestují data odesílaná z IoT Hub pro příjem dat. Když se zprávy z více zařízení předají do centra pomocí stejného ID zařízení brány, můžou dorazit do stejného oddílu ve stejnou dobu, než je omezení škálování na oddíly.

**Dopad**:

* Pokud se v jednom oddílu udržuje nepřetržitá míra přijímání v rámci limitu verze Preview, je možné, že Time Series Insights nebude synchronizovat všechny telemetrie zařízení před tím, než se překročí doba uchování dat IoT Hub. V důsledku toho může dojít ke ztrátě odeslaných dat v případě, že dojde k trvalému překročení limitů pro přijímání.

Pro zmírnění této situace doporučujeme následující osvědčené postupy:

* Před nasazením řešení Vypočítejte sazby pro ingestování na jednotlivé prostředí a na oddíly.
* Ujistěte se, že vaše zařízení IoT Hub jsou vyvážená z hlediska zatížení co nejdáleního rozsahu.

> [!IMPORTANT]
> Pro prostředí, která používají IoT Hub jako zdroj události, vypočítejte rychlost příjmu pomocí počtu používaných zařízení centra, aby se zajistilo, že frekvence klesne pod omezení 0,5 MB/s na jeden oddíl.
>
> * I v případě, že několik událostí dorazí současně, limit verze Preview nebude překročen.

  ![Diagram IoT Hubho oddílu](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Další informace o optimalizaci propustnosti a oddílů centra najdete v následujících zdrojích informací:

* [Škálování IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Škálování centra událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Oddíly centra událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [úložišti](concepts-storage.md) dat