---
title: Plánování prostředí GA – Azure Time Series Insights | Dokumenty společnosti Microsoft
description: Doporučené postupy pro přípravu, konfiguraci a nasazení prostředí Ga Azure Time Series Insights.
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/13/2020
ms.custom: seodec18
ms.openlocfilehash: 85910ee5467ecc9f4fe3c1a8bc13110b6f218e5c
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272708"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Plánování prostředí Azure Time Series Insights GA

Tento článek popisuje, jak naplánovat prostředí obecné dostupnosti Azure Time Series Insights na základě očekávané míry příchozího přenosu dat a požadavků na uchovávání dat.

## <a name="video"></a>Video

**V tomto videu se dozvíte další informace o uchovávání dat v Azure Time Series Insights a o tom, jak ho naplánovat:**<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Osvědčené postupy

Chcete-li začít s Azure Time Series Insights, je nejlepší, pokud víte, kolik dat očekáváte, že bude tlačit minutu a jak dlouho budete muset ukládat data.  

Další informace o kapacitě a uchovávání informací pro obě sloky Time Series Insights najdete v [cenách Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Chcete-li nejlépe naplánovat prostředí Time Series Insights pro dlouhodobý úspěch, zvažte následující atributy:

- [Kapacita úložiště](#storage-capacity)
- [Doba uchování dat](#data-retention)
- [Kapacita příchozího přenosu dat](#ingress-capacity)
- [Formování vašich akcí](#shape-your-events)
- [Zajištění toho, abyste měli referenční data na místě](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>Kapacita úložiště

Ve výchozím nastavení time series insights uchovává data na základě množství úložiště, které zřídíte (jednotky &#215; velikost úložiště na jednotku) a příchozí ho.

## <a name="data-retention"></a>Uchovávání dat

Nastavení času **uchovávání dat** můžete změnit v prostředí Azure Time Series Insights. Můžete povolit až 400 dní uchovávání. 

Azure Time Series Insights má dva režimy:

* Jeden režim optimalizuje pro nejaktuálnější data. Vynucuje zásady **vymazat stará data,** takže poslední data jsou k dispozici s instancí. Tento režim je ve výchozím nastavení zapnutý. 
* Ostatní optimalizuje data tak, aby zůstala pod nakonfigurovanými limity uchovávání informací. **Pozastavení příchozího přenosu dat** zabrání vniknutí nových dat, když je vybráno jako **chování limitu úložiště**.

Můžete upravit uchovávání informací a přepínat mezi dvěma režimy na konfigurační stránce prostředí na webu Azure Portal.

> [!IMPORTANT]
> V prostředí Ga Azure Time Series Insights můžete nakonfigurovat maximálně 400 dní uchovávání dat.

### <a name="configure-data-retention"></a>Konfigurace uchovávání dat

1. Na [webu Azure Portal](https://portal.azure.com)vyberte prostředí Time Series Insights.

1. V podokně **prostředí Time Series Insights** vyberte v části **Nastavení** **konfiguraci úložiště**.

1. Do pole **Doba uchovávání dat (ve dnech)** zadejte hodnotu mezi 1 a 400.

   [![Konfigurace uchovávání informací](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

> [!TIP]
> Další informace o implementaci příslušných zásad uchovávání dat naleznete v zásadách [konfigurace uchovávání informací](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Kapacita příchozího přenosu dat

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

### <a name="environment-planning"></a>Plánování prostředí

Druhou oblastí, na kterou se zaměřit při plánování prostředí Time Series Insights je kapacita příchozího přenosu dat. Denní příchozí přenos dat úložiště a kapacita událostí se měří za minutu, v blocích 1 KB. Maximální povolená velikost paketu je 32 kB. Datové pakety větší než 32 KB jsou zkráceny.

Můžete zvýšit kapacitu S1 nebo S2 Skladové jednotky na 10 jednotek v jednom prostředí. Z prostředí S1 nelze migrovat do prostředí S2. Z prostředí S2 nelze migrovat na S1.

Pro příchozí kapacity nejprve určete celkový příchozí přenos dat, který požadujete na základě měsíce. Dále určete, jaké jsou vaše potřeby za minutu. 

Omezení a latence hrají roli v kapacitě za minutu. Pokud máte špičku v příchozích dat, která trvá méně než 24 hodin, Time Series Insights můžete "dohnat" rychlostí příchozího přenosu dat dvakrát sazby uvedené v předchozí tabulce.

Například pokud máte jednu skladovou položku S1, příchozí přenos dat rychlostí 720 událostí za minutu a rychlost přenosu dat špičky pro méně než jednu hodinu rychlostí 1 440 událostí nebo méně, neexistuje žádná znatelná latence ve vašem prostředí. Pokud však překročíte 1 440 událostí za minutu po dobu delší než jednu hodinu, pravděpodobně zaznamenáte latenci v datech, která jsou vizualizována a k dispozici pro dotaz ve vašem prostředí.

Možná předem nevíte, kolik dat očekáváte, že budete tlačit. V takovém případě můžete najít telemetrii dat pro [Azure IoT Hub](../iot-hub/iot-hub-metrics.md) a [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) v předplatném portálu Azure. Telemetrie vám může pomoci určit, jak zřídit vaše prostředí. Pomocí podokna **Metriky** na portálu Azure pro příslušný zdroj událostí zobrazit jeho telemetrie. Pokud rozumíte metrikám zdroje událostí, můžete efektivněji plánovat a zřčovat prostředí Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Výpočet požadavků na příchozí přenos dat

Výpočet požadavků na příchozí přenos dat:

- Ověřte, zda je kapacita příchozího přenosu dat vyšší než průměrná rychlost za minutu a zda je vaše prostředí dostatečně velké, aby zvládlo očekávaný příchozí přenos dat, což odpovídá dvojnásobné kapacitě po dobu kratší než jednu hodinu.

- Pokud dojde k ingress hroty, které trvají déle než 1 hodinu, použijte rychlost špičky jako průměr. Zřízení prostředí s kapacitou pro zpracování rychlost i pro špičku.

### <a name="mitigate-throttling-and-latency"></a>Zmírnění omezení a latence

Informace o tom, jak zabránit omezení a latence, [přečtěte si zmírnění latence a omezení](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Utvářejte své události

Je důležité zajistit, aby způsob odesílání událostí do Time Series Insights podporoval velikost prostředí, které zřizujete. (Naopak můžete mapovat velikost prostředí na počet událostí, které time series insights přečte a velikost každé události.) Je také důležité přemýšlet o atributy, které můžete chtít použít k řezu a filtrování při dotazování dat.

> [!TIP]
> Projděte si dokumentaci k tvarování JSON v [odesílání událostí](time-series-insights-send-events.md).

## <a name="ensure-that-you-have-reference-data"></a>Ujistěte se, že máte referenční data

*Referenční datová sada* je kolekce položek, které rozšiřují události ze zdroje událostí. Modul příchozího přenosu dat Time Series Insights připojí každou událost ze zdroje událostí s odpovídajícím datovým řádkem v referenční datové sadě. Rozšířená událost je pak k dispozici pro dotaz. Spojení je založeno na sloupcích **primárního klíče,** které jsou definovány ve vaší referenční datové sadě.

> [!NOTE]
> Referenční data nejsou spojena zpětně. Pouze aktuální a budoucí příchozí přenos dat je spárována a připojena k referenční datové sadě po nakonfigurované a nahrané. Pokud plánujete odeslat velké množství historických dat do přehledů Time Series Insights a nenahrávejte nejprve ani nevytvářejte referenční data v Přehledech časových řad, možná budete muset svou práci zrekonstruovat (nápověda: není zábavná).  

Další informace o vytváření, nahrávání a správě referenčních dat v přehledech časové řady si přečtěte v [dokumentaci k referenční datové sadě](time-series-insights-add-reference-data-set.md).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Další kroky

- Můžete začít vytvořením [nového prostředí Time Series Insights na webu Azure Portal](time-series-insights-get-started.md).

- Přečtěte si, jak [přidat zdroj událostí Centra událostí](time-series-insights-how-to-add-an-event-source-eventhub.md) do přehledů time series.

- Přečtěte si, jak [nakonfigurovat zdroj událostí služby IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md).
