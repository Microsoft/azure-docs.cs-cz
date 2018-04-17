---
title: Plánování rozsahu prostředí Azure časové řady Insights | Microsoft Docs
description: Tento článek popisuje, jak postupovat podle osvědčených postupů při plánování prostředí Azure časové řady statistiky, včetně kapacitu úložiště, uchovávání dat, kapacity příjem příchozích dat a monitorování.
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 991db58db1bb07f338c0f80aa4db69ddb868dcab
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="plan-your-azure-time-series-insights-environment"></a>Plánování prostředí Statistika Azure časové řady

Tento článek popisuje, jak naplánovat prostředí Statistika Azure časové řady na základě vaší míry očekávaný příjem příchozích dat a požadavků na uchovávání dat.

## <a name="best-practices"></a>Osvědčené postupy

Začít s časové řady statistiky, je nejvhodnější Pokud víte, jaké množství dat byste měli push o minutu a jak dlouho je nutné ukládat data.  

Další informace o kapacitě a uchovávání dat pro obě položky čas řady statistiky najdete v tématu [ceny časové řady Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Zvažte následující atributy, které se nejlepší plán prostředí pro dlouhodobá úspěšnost: 
- Kapacita úložiště
- Doba uchování dat
- Příjem příchozích dat kapacity 
- Shaping události
- Ověříte, že máte referenční data na místě

## <a name="understand-storage-capacity"></a>Kapacita úložiště
Ve výchozím nastavení časové řady Statistika uchovává data v závislosti na množství úložiště, kterou jste zřídili (jednotky doby velikost úložiště na jednotku) a vstupní.

## <a name="understand-data-retention"></a>Vysvětlení uchovávání dat
Můžete nakonfigurovat prostředí časové řady Insights **doby uchování dat** nastavení, povolení až 400 dní uchovávání informací.  Statistika časové řady má dva režimy, ten, který optimalizuje pro zajištění prostředí má nejaktuálnější data (na ve výchozím nastavení) a druhou, která optimalizuje pro zajištění uchování omezení jsou splněny, kde je pozastaven příjem příchozích dat, pokud celkové kapacity úložiště prostředí je přístupů.  Můžete upravit uchovávání a přepínání mezi těmito dvěma režimy na stránce konfigurace v prostředí na portálu Azure.

Můžete nakonfigurovat maximálně 400 dní uchovávání dat ve vašem prostředí Statistika časové řady.

## <a name="configure-data-retention"></a>Konfigurace uchovávání dat

1. V [portál Azure](https://portal.azure.com), vyberte časové řady Statistika prostředí.

2. Na **stránku prostředí časové řady Statistika**v části **nastavení** záhlaví, vyberte **konfigurace**. 

3. V **doby uchování dat (ve dnech)** pole, zadejte hodnotu od 1 do 400.

   ![Konfigurace uchovávání](media/environment-mitigate-latency/configure-retention.png)

## <a name="understand-ingress-capacity"></a>Pochopení kapacity příjem příchozích dat

Další oblasti, ke které soustředit na plánování je kapacity příjem příchozích dat, který je odvozený ze přidělení za minutu. 

Z hlediska omezení paketu ingressed data s velikost paketu 32 kB je považován za 32 události, každý s nastavenou velikostí 1 KB. Událost maximální povolená velikost je 32 KB; se zkrátí datových paketů větší než 32 KB.

Následující tabulka shrnuje kapacity příjem příchozích dat pro každý SKU:

|Skladová jednotka (SKU)  |Počet událostí za měsíc za jednotku  |Velikost událostí za měsíc, za jednotku  |Počet událostí za minutu za jednotku  | Velikost za minutu za jednotku   |
|---------|---------|---------|---------|---------|
|S1     |   30 milionů     |  30 GB     |  700    |  700 KB   |
|S2     |   300 milionů    |   300 GB   | 7,000   | 7000 KB  |

Můžete zvýšit kapacitu S1 nebo S2 SKU pro 10 jednotek v jednom prostředí. Nemůžete migrovat z prostředí S1 S2 nebo z prostředí S2 S1. 

Pro příjem příchozích dat kapacitu nejdřív byste měli zjistit celkový příjem příchozích dat, které vyžadujete na základě za měsíc. V dalším kroku zjistěte, co vaše za minutu vyžaduje se, protože se jedná, kde omezení a latence hrají roli.

Pokud máte objeví se ve vaší vstupní data trvá méně než 24 hodin, můžete časové řady Insights "zjištěná" rychlostí příchozího 2 x výše uvedené sazby. 

Například pokud máte jeden S1 SKU a příchozí přenos dat s rychlostí 700 událostí za minutu a Špička pro kratší než 1 hodina s rychlostí 1400 událostí nebo méně, by žádné znatelné latence pro vaše prostředí. Ale pokud 1400 události za minutu po dobu více než jednu hodinu, by pravděpodobně setkáte latence pro data, která je k dispozici pro dotaz ve vašem prostředí a vizualizovaných. 

Nemusí vědět předem kolik dat byste měli push. V takovém případě můžete najít data telemetrie [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) a [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) na portálu Azure. Tuto telemetrii vám pomohou určit, jak zřídit prostředí. Použití **metriky** na portálu Azure ke zdroji příslušných událostí k zobrazení jeho telemetrie. Pokud budete rozumět tomu vaše metriky zdroje událostí, umožňuje efektivněji plánování a zřízení prostředí Statistika časové řady.

### <a name="calculate-ingress-requirements"></a>Výpočet požadavků na příjem příchozích dat

- Potvrďte vaše kapacita příjem příchozích dat je vyšší než vaše průměrná rychlost za minutu a že je vaše prostředí dostatečně velký pro zpracování vaše předpokládaného příchozího ekvivalentní 2 x vaší kapacitu menší než 1 hodina.

- Dojde-li špičky příjem příchozích dat, poslední po dobu delší než 1 hod., použijte rychlost Špička jako vaše průměr a zřízení prostředí s dostatečnou kapacitu pro zpracování Špička rychlost.
 
### <a name="mitigate-throttling-and-latency"></a>Zmírnění omezení a čekací doba

Informace o tom, jak zabránit omezení a latence najdete v tématu [zmírnění latence a omezování](time-series-insights-environment-mitigate-latency.md). 

## <a name="shaping-your-events"></a>Shaping události
Je důležité zajistit způsob, jak odesílat události TSI podporuje velikost prostředí jsou zřizování (naopak můžete namapovat velikost prostředí tak, aby počtu událostí TSI přečte a velikost jednotlivých událostí).  Podobně je důležité zvážit atributy, které můžete chtít řezu a filtrovat podle při dotazování na data.  Myslete na to, doporučujeme kontrola JSON shaping části našich *odesílat události* dokumentace [dokumentace] (https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-send-events).  Je směrem k dolní části stránky.  

## <a name="ensuring-you-have-reference-data-in-place"></a>Ověříte, že máte referenční data na místě
Referenční datové sady je kolekce položek, které posílení události ze zdroje událostí. Čas řady Statistika příchozího modul spojí všechny události ze zdroje událostí s odpovídající řádek dat v odkaz na sadu dat. Tato rozšířená událost je pak k dispozici pro dotaz. Toto připojení je založena na sloupce primární klíč v vaší referenční datové sadě definován.

Poznámka: referenční data není zpětně připojena. To znamená, že pouze aktuálních a budoucích příchozí přenos dat je shodná a připojený k sadu referenční data, jakmile byla nakonfigurována a nahrát.  Pokud budete chtít poslat TSI velký počet historických dat a nemáte nahrát nebo nejprve vytvořit referenční data v TSI pak budete muset znovu práci (pomocného parametru, není fun).  

Další informace o tom, jak vytvářet, odeslání a odkaz na data v TSI spravovat, přejděte na našem *referenční data* dokumentace [dokumentace] (https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-add-reference-data-set).


## <a name="next-steps"></a>Další postup
- [Postup přidání zdroje událostí centra událostí](time-series-insights-how-to-add-an-event-source-eventhub.md)
- [Postup přidání zdroje událostí služby IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md)
