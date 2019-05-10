---
title: Plánování škálování vašeho prostředí Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje, jak řídit se doporučenými postupy při plánování prostředí Azure Time Series Insights, včetně kapacitu úložiště, uchovávání dat, kapacita příchozího přenosu dat, monitorování a provozní zotavení po havárii (BCDR).
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: bf1f570319370fab99e2f52086bc81df259e3d35
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236512"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Plánování prostředí Azure čas Series Insights GA

Tento článek popisuje postup plánování prostředí Azure Time Series Insights všeobecné dostupnosti (GA), na základě vaší rychlost očekávané příchozího přenosu dat a požadavků na uchovávání dat.

## <a name="video"></a>Video

### <a name="learn-more-about-data-retention-in-azuretime-series-insights-and-how-to-plan-for-itbr"></a>Další informace o uchovávání dat v AzureTime Series Insights a jak ji plánovat.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Osvědčené postupy

Abyste mohli začít s Time Series Insights, je vhodné, pokud víte, kolik dat očekáváte, že tak, aby nabízel po minutách a jak dlouho je nutné ukládat data.  

Další informace o kapacitě a uchovávání dat pro obě čas Series Insights SKU najdete v tématu [Time Series Insights ceny](https://azure.microsoft.com/pricing/details/time-series-insights/).

Vezměte v úvahu následující atributy nejvhodnější plán prostředí pro dlouhodobá úspěšnost:

- <a href="#understand-storage-capacity">Kapacita úložiště</a>
- <a href="#understand-data-retention">Období uchovávání dat</a>
- <a href="#understand-ingress-capacity">Kapacita příchozího přenosu dat</a>
- <a href="#shape-your-events">Strukturování události</a>
- <a href="#ensure-you-have-reference-data">Ověříte, že máte referenční data na místě</a>

## <a name="understand-storage-capacity"></a>Kapacita úložiště

Azure Time Series Insights ve výchozím nastavení, zachová data založená na velikost úložiště, kterou jste zřídili (jednotky doby velikost úložiště na jednotku) a příchozího přenosu dat.

## <a name="understand-data-retention"></a>Vysvětlení uchovávání dat

Můžete nakonfigurovat prostředí Time Series Insights **doby uchování dat** nastavení povolení až po dobu 400 dnů uchovávání informací. Time Series Insights má dva režimy, ten, který optimalizuje pro zajištění vašeho prostředí má nejaktuálnější data (na ve výchozím nastavení) a další vlastnost, která optimalizuje pro zajištění uchovávání omezení jsou splněny, kde je pozastaven příchozího přenosu dat, pokud celkové kapacity úložiště dosažení prostředí.  Můžete upravit, udržení zákazníků a přepínání mezi těmito dvěma režimy na stránce konfigurace prostředí na webu Azure Portal.

Nakonfigurovat až po dobu 400 dnů uchovávání dat ve vašem prostředí Time Series Insights.

### <a name="configure-data-retention"></a>Konfigurace uchovávání dat

1. V [webu Azure portal](https://portal.azure.com), vyberte vaše prostředí Time Series Insights.

1. Na **stránky prostředí Time Series Insights**v části **nastavení** záhlaví, vyberte **konfigurovat**.

1. V **doba uchovávání dat (ve dnech)** pole, zadejte hodnotu od 1 do 400.

   [![Konfigurace uchovávání](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> Další informace o implementaci zásadu uchovávání informací příslušná data kontrolou [konfigurace uchovávání](./time-series-insights-how-to-configure-retention.md).

## <a name="understand-ingress-capacity"></a>Kapacita příchozího přenosu dat

Další oblasti a zaměřte se na plánování je kapacita příchozího přenosu, který je odvozený přidělování za minutu.

Z hlediska omezení paket ingressed data s velikost paketu 32 kB považuje za 32 události, každý velikosti 1 KB. Události maximální povolená velikost je 32 KB; datových paketů větší než 32 KB se oříznou.

Následující tabulka shrnuje kapacita příchozího přenosu dat pro každý SKU:

|Skladová jednotka  |Počet událostí / měsíc / jednotka  |Velikost události / měsíc / jednotka  |Počet událostí za minutu / jednotka  | Velikost / min / jednotku   |
|---------|---------|---------|---------|---------|
|S1     |   30 milionů     |  30 GB     |  720    |  720 KB   |
|S2     |   300 milionů    |   300 GB   | 7,200   | 7200 KB  |

Můžete zvýšit kapacitu S1 nebo S2 SKU na 10 jednotek v jednom prostředí. Nelze migrovat z prostředí S1 na S2 nebo z prostředí S2 S1.

Za příchozí přenos dat kapacitu nejdřív byste měli zjistit celkový příchozí přenos dat podle vašich požadavků na základě za měsíc. V dalším kroku zjistěte, jaká vaše minutách potřeby jsou, protože se jedná, kde omezení šířky pásma a čekací doba hrají roli.

Pokud máte prudký nárůst ve vaší příchozího přenosu dat trvá méně než 24 hodin, můžete služby Time Series Insights "zachytávání" příchozího přenosu dat rychlostí 2 x uvedené podle sazeb uvedených nahoře.

Například pokud máte jeden skladovou Položku S1 a příchozího přenosu dat sazbou 720 událostí za minutu a zásobníku pro menší než 1 hodina s rychlostí 1 440 událostí nebo méně, by existovat žádné znatelné latence pro vaše prostředí. Ale při překročení 1440 událostí za minutu po dobu více než jednu hodinu, by pravděpodobně setkáte s latencí k datům, která je k dispozici pro dotaz ve vašem prostředí a vizualizovaných.

Nemusí vědět předem kolik dat očekáváte, push. V takovém případě můžete najít data telemetrie pro [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) a [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) portálu Azure portal. Tato telemetrie vám umožňují určit, jak zřídit prostředí. Použití **metriky** stránky na webu Azure Portal pro zdroj příslušné události k zobrazení jeho telemetrická data. Pokud jste se seznámili vaši metriky zdroje událostí, efektivněji naplánovat a zřídit prostředí Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Výpočet požadavků na příchozího přenosu dat

- Zkontrolujte kapacitu pro příchozí přenos dat je vyšší než vaše průměrná rychlost za minutu a že je vaše prostředí dostatečně velký pro zpracování váš předpokládaný příchozího přenosu dat odpovídá 2 x kapacitu menší než 1 hodina.

- Pokud příchozího přenosu dat generují hroty, který poslední po dobu delší než 1 hodina, použijte rychlost (špičky) jako vaše průměr a zřídit prostředí s kapacitou pro zpracování frekvence (špičky).

### <a name="mitigate-throttling-and-latency"></a>Zmírnění omezování a latence

Informace o tom, aby se zabránilo omezení šířky pásma a latence, přečtěte si informace o tom, jak [snížit latenci a omezování](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Obrazce události

Je důležité pro zaručení způsob odesílání událostí do služby TSI podporuje velikost prostředí, které zřizujete (a naopak, můžete namapovat velikost prostředí tak, aby počet událostí TSI přečte a velikost každé události). Podobně je důležité zvážit atributy, které můžete chtít rozdělit a filtrovat podle při zadávání dotazů na data.

> [!TIP]
> Zkontrolujte JSON tvarování dokumentaci v [odesílání událostí](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-you-have-reference-data"></a>Ujistěte se, že máte referenční data

A **referenční sady dat** je kolekce položek, které rozšiřují události ze zdroje událostí. Modul příchozího přenosu dat čas Series Insights spojí každé události ze zdroje událostí s odpovídající řádek dat v referenční sadě dat. Tato rozšířená událost je pak k dispozici pro dotaz. Toto připojení je založené na sloupců primárního klíče, které jsou definované v referenční sadě dat.

Mějte na paměti, referenčních dat není připojený zpětně. To znamená, že pouze aktuální a budoucí příchozího přenosu dat je odpovídající a připojený k sady referenční data, jakmile byl nakonfigurován a nahráli.  Pokud budete chtít poslat TSI velké množství historických dat a není nahrát nebo vytvořit referenční data ve službě TSI nejprve, pak bude pravděpodobně nutné znovu provedení práce (pomocného parametru, ne fun).  

Další informace o tom, jak vytvořit, odesílat a spravovat vaše referenčních dat ve službě TSI, přejděte na naše [referenční dokumentace sady dat](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Další postup

- Začněte vytvořením [nového prostředí Time Series Insights na webu Azure Portal](time-series-insights-get-started.md).

- Zjistěte, jak [přidání zdroje událostí centra událostí](time-series-insights-how-to-add-an-event-source-eventhub.md) pro Time Series Insights.

- Přečtěte si informace o tom, jak [konfigurovat zdroje událostí IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md).
