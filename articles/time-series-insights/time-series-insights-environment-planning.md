---
title: Naplánování rozsahu Azure Time Series Insights prostředí | Microsoft Docs
description: Tento článek popisuje, jak postupovat podle osvědčených postupů při plánování Azure Time Series Insightsho prostředí. Mezi oblasti, které se týkají, patří kapacita úložiště, uchovávání dat, kapacita příchozího provozu, monitorování a provozní kontinuita a zotavení po havárii (BCDR).
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/05/2019
ms.custom: seodec18
ms.openlocfilehash: 1e0fee903372668d30db0686f6a23dd913428454
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828167"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Plánování prostředí Azure Time Series Insights GA

Tento článek popisuje, jak naplánovat Azure Time Series Insights prostředí pro obecné dostupnosti (GA) na základě očekávané míry příchozího přenosu dat a vašich požadavků na uchovávání dat.

## <a name="video"></a>Video

**Podívejte se na toto video, kde najdete další informace o uchovávání dat v Azure Time Series Insights a o tom, jak je naplánovat**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Osvědčené postupy

Pokud chcete začít s Time Series Insights, je nejlepší, pokud víte, kolik dat jste čekali po minutách a jak dlouho budete potřebovat ukládat data.  

Další informace o kapacitě a uchování obou Time Series Insights SKU najdete v tématu [Time Series Insights ceny](https://azure.microsoft.com/pricing/details/time-series-insights/).

Chcete-li nejlépe naplánovat Time Series Insights prostředí pro dlouhodobou úspěšnost, vezměte v úvahu následující atributy:

- <a href="#storage-capacity">Kapacita úložiště</a>
- <a href="#data-retention">Doba uchovávání dat</a>
- <a href="#ingress-capacity">Kapacita příchozího přenosu dat</a>
- <a href="#shape-your-events">Natvarování událostí</a>
- <a href="#ensure-that-you-have-reference-data">Zajištění, že máte referenční data na místě</a>

## <a name="storage-capacity"></a>Kapacita úložiště

Ve výchozím nastavení Time Series Insights uchovává data na základě velikosti úložiště, které zřizujete (jednotkové &#215; množství úložiště na jednotku) a příchozího přenosu dat.

## <a name="data-retention"></a>Uchovávání dat

Nastavení **doby uchovávání dat** můžete změnit v prostředí Time Series Insights. Můžete povolit až 400 dnů uchovávání. 

Time Series Insights má dva režimy. Jeden režim optimalizuje, aby se zajistilo, že vaše prostředí má nejaktuálnější data. Tento režim je ve výchozím nastavení zapnutý. 

Druhý režim optimalizuje, aby se zajistilo splnění limitů uchovávání. V druhém režimu se příchozí přenos dat pozastaví, pokud je dosažená celková kapacita úložiště prostředí. 

Můžete upravit dobu uchovávání a přepínání mezi oběma režimy na stránce konfigurace prostředí v Azure Portal.

V prostředí Time Series Insights můžete nakonfigurovat maximálně 400 dní uchovávání dat.

### <a name="configure-data-retention"></a>Konfigurace uchovávání dat

1. V [Azure Portal](https://portal.azure.com)vyberte své prostředí Time Series Insights.

1. V podokně **Time Series Insights prostředí** v části **Nastavení**vyberte **Konfigurovat**.

1. Do pole **Doba uchovávání dat (ve dnech)** zadejte hodnotu mezi 1 a 400.

   [![Konfigurace uchovávání](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> Další informace o tom, jak implementovat příslušné zásady uchovávání dat, najdete v tématu [Postup konfigurace uchovávání](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Kapacita příchozího přenosu dat

Druhá oblast, která se zaměřuje na plánování Time Series Insightsho prostředí, je kapacita pro příchozí přenosy. Kapacita příchozího přenosu dat je odvozená od přidělení po minutách.

V perspektivě omezování se příchozí datový paket, který má velikost paketu 32 KB, považuje za 32 událostí, velikost 1 KB. Maximální povolená velikost události je 32 KB. Datové pakety větší než 32 KB se zkrátí.

Následující tabulka shrnuje kapacitu příchozího přenosu dat na jednotku pro každý Time Series Insights SKU:

|SKU  |Počet událostí za měsíc  |Velikost události za měsíc  |Počet událostí za minutu  |Velikost události za minutu  |
|---------|---------|---------|---------|---------|
|S1     |   30 000 000     |  30 GB     |  720    |  720 KB   |
|S2     |   300 000 000    |   300 GB   | 7 200   | 7 200 KB  |

Kapacitu SKU S1 nebo S2 můžete zvýšit na 10 jednotek v jednom prostředí. Z prostředí S1 nemůžete migrovat na S2. Nemůžete migrovat z prostředí S2 na S1.

V případě kapacity příchozího přenosu dat nejprve určete celkový počet příchozích dat, který budete potřebovat měsíčně. V dalším kroku určete, jaké jsou požadavky na minuty. 

Omezování a latence hrají roli v rámci kapacity po minutách. Pokud máte špičku vstupních dat, která trvá méně než 24 hodin, Time Series Insights se dá zachytit na míru příchozího přenosu dvou časů, jakou jsou sazby uvedené v předchozí tabulce.

Pokud máte třeba jednu SKU S1, data příchozího přenosu dat jsou 720 událostí za minutu a rychlost přenosu dat se v tomto prostředí neshoduje po dobu kratší než jedna hodina, a to v poměru 1 440 událostí nebo méně. Pokud ale po dobu více než jedné hodiny překročíte 1 440 událostí za minutu, pravděpodobně budete mít k dispozici latenci v datech, která jsou vizuální a dostupná pro dotaz ve vašem prostředí.

Je možné, že předem nevíte, kolik dat jste čekali. V takovém případě můžete v předplatném Azure Portal najít telemetrii dat pro [azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) a [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) . Telemetrii vám pomůže určit, jak se vaše prostředí dá zřídit. K zobrazení telemetrie použijte podokno **metrik** v Azure Portal pro příslušný zdroj události. Pokud rozumíte metrikám zdroje událostí, můžete efektivněji naplánovat a zřídit Time Series Insights prostředí.

### <a name="calculate-ingress-requirements"></a>Vypočítat požadavky na příchozí přenosy

Výpočet požadavků na příchozí přenos dat:

- Ověřte, že vaše kapacita příchozího přenosu dat překračuje průměrnou sazbu za minutu a že vaše prostředí je dostatečně velké, aby bylo možné zpracovat předpokládaný ekvivalent vstupu do dvojnásobku kapacity za méně než jednu hodinu.

- Pokud se objeví špičky vstupu, které jsou poslední po dobu delší než 1 hodinu, použijte jako průměr sazbu za špičku. Zřídí prostředí s kapacitou pro zpracování rychlosti špičky.

### <a name="mitigate-throttling-and-latency"></a>Zmírnění omezení a latence

Informace o tom, jak zabránit omezování a latenci, najdete v tématu [zmírnění latence a omezování](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Natvarování událostí

Je důležité zajistit, aby způsob, jakým odesíláte události do Time Series Insights, podporoval velikost prostředí, které zřizujete. (Naopak, můžete namapovat velikost prostředí na kolik událostí Time Series Insights čtení a velikost každé události.) Je také důležité vzít v úvahu atributy, které můžete chtít použít k vytváření řezů a filtrování při dotazování na data.

> [!TIP]
> Projděte si dokumentaci ke tvarům JSON při [odesílání událostí](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-that-you-have-reference-data"></a>Ujistěte se, že máte referenční data

*Referenční datová sada* je kolekce položek, které rozšiřují události ze zdroje událostí. Modul příchozího přenosu Time Series Insights spojí každou událost ze zdroje události s odpovídajícím řádkem dat v referenční datové sadě. Rozšířená událost je pak k dispozici pro dotaz. Spojení je založeno na sloupcích **primárního klíče** , které jsou definovány ve vaší referenční datové sadě.

> [!NOTE]
> Referenční data nejsou připojená zpětně. Po konfiguraci a nahrání referenční datové sady se budou shodovat pouze aktuální a budoucí data příchozího přenosu dat. Pokud plánujete odeslat velké množství historických dat Time Series Insights a nemusíte nejdřív nahrávat nebo vytvářet referenční data v Time Series Insights, možná budete muset opakovat svou práci (pomocný parametr: ne zábavné).  

Další informace o tom, jak vytvářet, nahrávat a spravovat referenční data v Time Series Insights, najdete v [dokumentaci k referenční datové sadě](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Další postup

- Začněte vytvořením [nového Time Series Insightsho prostředí v Azure Portal](time-series-insights-get-started.md).

- Naučte se, jak [přidat Event Hubs zdroj události](time-series-insights-how-to-add-an-event-source-eventhub.md) do Time Series Insights.

- Přečtěte si o tom, jak [nakonfigurovat zdroj události IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md).
