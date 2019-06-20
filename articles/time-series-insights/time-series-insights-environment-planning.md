---
title: Plánování škálování vašeho prostředí Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje, jak řídit se doporučenými postupy při plánování prostředí Azure Time Series Insights. Oblasti, které jsou pokryty zahrnují kapacitu úložiště, uchovávání dat, kapacita příchozího přenosu dat, monitorování a obchodní kontinuity podnikových procesů a zotavení po havárii (BCDR).
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 2c11e3f623817894cea801173239cc386c6c3313
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165841"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Plánování prostředí Azure čas Series Insights GA

Tento článek popisuje postup plánování prostředí Azure Time Series Insights všeobecné dostupnosti (GA), na základě vaší rychlost očekávané příchozího přenosu dat a požadavků na uchovávání dat.

## <a name="video"></a>Video

**Podívejte se na toto video a další informace o uchovávání dat v Azure Time Series Insights a jak ji plánovat**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Osvědčené postupy

Abyste mohli začít s Time Series Insights, je vhodné, pokud víte, kolik dat očekáváte, že tak, aby nabízel minutu a jak dlouho je nutné ukládat data.  

Další informace o kapacitě a uchovávání dat pro obě čas Series Insights SKU najdete v tématu [Time Series Insights ceny](https://azure.microsoft.com/pricing/details/time-series-insights/).

Chcete-li nejlépe plánování prostředí Time Series Insights pro dlouhodobá úspěšnost, zvažte následující atributy:

- <a href="#storage-capacity">Kapacita úložiště</a>
- <a href="#data-retention">Období uchovávání dat</a>
- <a href="#ingress-capacity">Kapacita příchozího přenosu dat</a>
- <a href="#shape-your-events">Strukturování události</a>
- <a href="#ensure-that-you-have-reference-data">Ověříte, že máte referenční data na místě</a>

## <a name="storage-capacity"></a>Kapacita úložiště

Ve výchozím nastavení, služby Time Series Insights uchovává data na základě objemu úložiště zřídíte (jednotky &#215; velikost úložiště na jednotku) a příchozího přenosu.

## <a name="data-retention"></a>Uchovávání dat

Můžete změnit **doby uchování dat** nastavení ve vašem prostředí Time Series Insights. Můžete povolit až po dobu 400 dnů uchovávání informací. 

Time Series Insights má dva režimy. Jeden režim optimalizuje pro zajištění, že vaše prostředí obsahuje nejnovější data. Tento režim je, ve výchozím nastavení. 

V režimu optimalizuje pro zajištění, že jsou splněny limity uchování. V druhém režimu příchozího přenosu dat se pozastaví při splnění celkové kapacity úložiště prostředí. 

Můžete upravit, udržení zákazníků a přepínání mezi těmito dvěma režimy na stránce konfigurace prostředí na webu Azure Portal.

Nakonfigurovat až po dobu 400 dnů uchovávání dat ve vašem prostředí Time Series Insights.

### <a name="configure-data-retention"></a>Konfigurace uchovávání dat

1. V [webu Azure portal](https://portal.azure.com), vyberte vaše prostředí Time Series Insights.

1. V **prostředí Time Series Insights** podokně v části **nastavení**vyberte **konfigurovat**.

1. V **doba uchovávání dat (ve dnech)** zadejte hodnotu mezi 1 a 400.

   [![Konfigurace uchovávání](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> Další informace o tom, jak implementovat zásady uchovávání informací příslušná data, najdete v článku [konfigurace uchovávání](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Kapacita příchozího přenosu dat

Druhé oblasti zaměřit na plánování prostředí Time Series Insights je kapacita příchozího přenosu. Kapacita příchozího přenosu dat je odvozený přidělování za minutu.

Z hlediska omezování ingressed datový paket, který má velikost paketu 32 kB považuje událostí s 32, každý o velikosti 1 KB. Události maximální povolená velikost je 32 KB. Datových paketů větší než 32 KB se oříznou.

Následující tabulka shrnuje kapacita příchozího přenosu dat na jednotku pro každý skladové Položce Time Series Insights:

|Skladová jednotka (SKU)  |Počet událostí za měsíc  |Velikost událostí za měsíc  |Počet událostí za minutu  |Velikost událostí za minutu  |
|---------|---------|---------|---------|---------|
|S1     |   30 milionů     |  30 GB     |  720    |  720 KB   |
|S2     |   300 milionů    |   300 GB   | 7,200   | 7200 KB  |

Můžete zvýšit kapacitu S1 nebo S2 SKU na 10 jednotek v jednom prostředí. Nemůžete migrovat z prostředí S1 na S2. Můžete nelze migrovat z prostředí S2 S1.

Kapacita příchozího přenosu dat nejprve určete celkové příchozí přenos dat podle vašich požadavků na základě za měsíc. V dalším kroku Zjišťovali co vaše minutách potřeby. 

Omezování a latence hrají roli v kapacitě za minutu. Pokud máte prudký nárůst ve vaší příchozího přenosu dat, která trvá méně než 24 hodin, Time Series Insights může "dohnat" příchozího přenosu dat sazbou dvakrát sazeb uvedených v předchozí tabulce.

Například pokud máte jeden S1 SKU, příchozího přenosu dat ve výši 720 událostí za minutu, a špičky přenosovou rychlost pro méně než hodinu s rychlostí 1 440 událostí nebo méně, neexistuje žádné znatelné latence ve vašem prostředí. Ale pokud překročíte 1 440 událostí za minutu po dobu více než jednu hodinu, pravděpodobně nastane latence v datech, která je k dispozici pro dotaz ve vašem prostředí a vizualizovaných.

Možná nevíte, předem kolik dat očekáváte, push. V takovém případě můžete najít data telemetrie pro [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) a [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) ve vašem předplatném Azure portal. Telemetrii můžete určit, jak zřídit prostředí. Použití **metriky** podokně webu Azure Portal pro zdroj příslušné události k zobrazení jeho telemetrická data. Pokud jste se seznámili vaši metriky zdroje událostí, efektivněji naplánovat a zřídit prostředí Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Výpočet požadavků na příchozího přenosu dat

Výpočet požadavků na příchozího přenosu dat:

- Ověřte, že kapacitu příchozího přenosu dat nad vaše průměrná rychlost za minutu a že je vaše prostředí dostatečně velký pro zpracování váš předpokládaný příchozího přenosu dat odpovídá dvojnásobku kapacitu menší než jedna hodina.

- Pokud se generují hroty příchozího přenosu dat, které naposledy po dobu delší než 1 hodina, použít jako vaše průměrná rychlost (špičky). Zřízení prostředí s kapacitou pro zpracování frekvence (špičky).

### <a name="mitigate-throttling-and-latency"></a>Zmírnění omezování a latence

Informace o tom, aby se zabránilo omezení šířky pásma a čekací doba najdete v tématu [snížit latenci a omezování](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Obrazce události

Je důležité zajistit, aby tak, jak odesílat události do služby Time Series Insights podporuje velikost prostředí, ve kterém jsou zřizování. (A naopak, můžete namapovat velikost prostředí tak, aby počet událostí Time Series Insights přečte a velikost každé události.) Je také důležité zvážit atributy, které můžete chtít použít k vyfiltrování a filtrovat podle při dotazování na data.

> [!TIP]
> Zkontrolujte JSON tvarování dokumentaci v [odesílání událostí](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-that-you-have-reference-data"></a>Ujistěte se, že máte referenční data

A *odkazovat na datové sadě* je kolekce položek, které rozšiřují události ze zdroje událostí. Modul příchozího přenosu dat Time Series Insights spojí každé události ze zdroje událostí s odpovídající řádek dat v datové sadě odkaz. Rozšířená událost je pak k dispozici pro dotaz. Je na základě spojení **primární klíč** sloupce, které jsou definovány v odkaz datové sady.

> [!NOTE]
> Referenční data není připojený zpětně. Pouze aktuální a budoucí příchozího přenosu dat se odpovídající a po jeho má nakonfigurovaný a připojený k referenční datová sada. Pokud chcete odesílat velké množství historických dat Time Series Insights a není prvním odeslání nebo vytvoření referenčních dat v Time Series Insights, bude pravděpodobně nutné znovu svou práci (pomocný parametr: není fun).  

Další informace o tom, jak vytvořit, odesílat a spravovat vaše referenčních dat v Time Series Insights, najdete v našich [referenční datová sada dokumentace](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Další postup

- Začněte vytvořením [nového prostředí Time Series Insights na webu Azure Portal](time-series-insights-get-started.md).

- Zjistěte, jak [přidání zdroje událostí služby Event Hubs](time-series-insights-how-to-add-an-event-source-eventhub.md) pro Time Series Insights.

- Přečtěte si informace o tom, jak [konfigurovat zdroje událostí IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md).
