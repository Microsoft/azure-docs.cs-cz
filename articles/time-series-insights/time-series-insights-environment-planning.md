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
ms.date: 11/15/2017
ms.custom: seodec18
ms.openlocfilehash: ce31b22dcbf3fbe79192647e9a21983911e77a9c
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556029"
---
# <a name="plan-your-azure-time-series-insights-environment"></a>Plánování prostředí Azure Time Series Insights

Tento článek popisuje postup plánování prostředí Azure Time Series Insights na základě vaší rychlost očekávané příchozího přenosu dat a požadavků na uchovávání dat.

## <a name="video"></a>Video: 

### <a name="in-this-video-we-cover-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>V tomto videu se budeme zabývat uchovávání dat Time Series Insights a jak ji plánovat.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Osvědčené postupy

Abyste mohli začít s Time Series Insights, je vhodné, pokud víte, kolik dat očekáváte, že tak, aby nabízel po minutách a jak dlouho je nutné ukládat data.  

Další informace o kapacitě a uchovávání dat pro obě čas Series Insights SKU najdete v tématu [Time Series Insights ceny](https://azure.microsoft.com/pricing/details/time-series-insights/).

Vezměte v úvahu následující atributy nejvhodnější plán prostředí pro dlouhodobá úspěšnost: 
- Kapacita úložiště
- Doba uchování dat
- Kapacita příchozího přenosu dat 
- Strukturování události
- Ověříte, že máte referenční data na místě

## <a name="understand-storage-capacity"></a>Kapacita úložiště
Ve výchozím nastavení zachová Time Series Insights příchozího přenosu dat a dat na základě objemu úložiště, kterou jste zřídili (jednotky doby velikost úložiště na jednotku).

## <a name="understand-data-retention"></a>Vysvětlení uchovávání dat
Můžete nakonfigurovat prostředí Time Series Insights **doby uchování dat** nastavení povolení až po dobu 400 dnů uchovávání informací.  Time Series Insights má dva režimy, ten, který optimalizuje pro zajištění vašeho prostředí má nejaktuálnější data (na ve výchozím nastavení) a další vlastnost, která optimalizuje pro zajištění uchovávání omezení jsou splněny, kde je pozastaven příchozího přenosu dat, pokud celkové kapacity úložiště dosažení prostředí.  Můžete upravit, udržení zákazníků a přepínání mezi těmito dvěma režimy na stránce konfigurace prostředí na webu Azure Portal.

Nakonfigurovat až po dobu 400 dnů uchovávání dat ve vašem prostředí Time Series Insights.

## <a name="configure-data-retention"></a>Konfigurace uchovávání dat

1. V [webu Azure portal](https://portal.azure.com), vyberte vaše prostředí Time Series Insights.

2. Na **stránky prostředí Time Series Insights**v části **nastavení** záhlaví, vyberte **konfigurovat**. 

3. V **doba uchovávání dat (ve dnech)** pole, zadejte hodnotu od 1 do 400.

   ![Konfigurovat uchovávání](media/environment-mitigate-latency/configure-retention.png)

## <a name="understand-ingress-capacity"></a>Kapacita příchozího přenosu dat

Další oblasti a zaměřte se na plánování je kapacita příchozího přenosu, který je odvozený přidělování za minutu. 

Z hlediska omezení paket ingressed data s velikost paketu 32 kB považuje za 32 události, každý velikosti 1 KB. Události maximální povolená velikost je 32 KB; datových paketů větší než 32 KB se oříznou.

Následující tabulka shrnuje kapacita příchozího přenosu dat pro každý SKU:

|Skladová jednotka (SKU)  |Počet událostí za měsíc za jednotku  |Velikost událostí za měsíc za jednotku  |Počet událostí za minutu, za jednotku  | Velikost za minutu, za jednotku   |
|---------|---------|---------|---------|---------|
|S1     |   30 milionů     |  30 GB     |  720    |  720 KB   |
|S2     |   300 milionů    |   300 GB   | až 7 200   | 7200 KB  |

Můžete zvýšit kapacitu S1 nebo S2 SKU na 10 jednotek v jednom prostředí. Nelze migrovat z prostředí S1 na S2 nebo z prostředí S2 S1.

Za příchozí přenos dat kapacitu nejdřív byste měli zjistit celkový příchozí přenos dat podle vašich požadavků na základě za měsíc. V dalším kroku zjistěte, jaká vaše minutách potřeby jsou, protože se jedná, kde omezení šířky pásma a čekací doba hrají roli.

Pokud máte prudký nárůst ve vaší příchozího přenosu dat trvá méně než 24 hodin, můžete služby Time Series Insights "zachytávání" příchozího přenosu dat rychlostí 2 x uvedené podle sazeb uvedených nahoře. 

Například pokud máte jeden skladovou Položku S1 a příchozího přenosu dat sazbou 720 událostí za minutu a zásobníku pro menší než 1 hodina s rychlostí 1 440 událostí nebo méně, by existovat žádné znatelné latence pro vaše prostředí. Ale při překročení 1440 událostí za minutu po dobu více než jednu hodinu, by pravděpodobně setkáte s latencí k datům, která je k dispozici pro dotaz ve vašem prostředí a vizualizovaných. 

Nemusí vědět předem kolik dat očekáváte, push. V takovém případě můžete najít data telemetrie pro [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) a [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) portálu Azure portal. Tato telemetrie vám umožňují určit, jak zřídit prostředí. Použití **metriky** stránky na webu Azure Portal pro zdroj příslušné události k zobrazení jeho telemetrická data. Pokud jste se seznámili vaši metriky zdroje událostí, efektivněji naplánovat a zřídit prostředí Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Výpočet požadavků na příchozího přenosu dat

- Zkontrolujte kapacitu pro příchozí přenos dat je vyšší než vaše průměrná rychlost za minutu a že je vaše prostředí dostatečně velký pro zpracování váš předpokládaný příchozího přenosu dat odpovídá 2 x kapacitu menší než 1 hodina.

- Pokud příchozího přenosu dat generují hroty, který poslední po dobu delší než 1 hodina, použijte rychlost (špičky) jako vaše průměr a zřídit prostředí s kapacitou pro zpracování frekvence (špičky).
 
### <a name="mitigate-throttling-and-latency"></a>Zmírnění omezování a latence

Informace o tom, aby se zabránilo omezení šířky pásma a čekací doba najdete v tématu [snížit latenci a omezování](time-series-insights-environment-mitigate-latency.md).

## <a name="shaping-your-events"></a>Strukturování události
Je důležité pro zaručení způsob odesílání událostí do služby TSI podporuje velikost prostředí, které zřizujete (a naopak, můžete namapovat velikost prostředí tak, aby počet událostí TSI přečte a velikost každé události).  Podobně je důležité zvážit atributy, které můžete chtít rozdělit a filtrovat podle při zadávání dotazů na data.  S myslete na to, doporučujeme revize JSON tvarování část naší [odesílání událostí dokumentaci](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).  Je směrem k dolní části stránky.  

## <a name="ensuring-you-have-reference-data-in-place"></a>Ověříte, že máte referenční data na místě
Referenční sady dat je kolekce položek, které rozšiřují události ze zdroje událostí. Modul příchozího přenosu dat čas Series Insights spojí každé události ze zdroje událostí s odpovídající řádek dat v referenční sadě dat. Tato rozšířená událost je pak k dispozici pro dotaz. Toto připojení je založené na sloupců primárního klíče, které jsou definované v referenční sadě dat.

Mějte na paměti, referenčních dat není připojený zpětně. To znamená, že pouze aktuální a budoucí příchozího přenosu dat je odpovídající a připojený k sady referenční data, jakmile byl nakonfigurován a nahráli.  Pokud budete chtít poslat TSI velké množství historických dat a není nahrát nebo vytvořit referenční data ve službě TSI nejprve, pak bude pravděpodobně nutné znovu provedení práce (pomocného parametru, ne fun).  

Další informace o tom, jak vytvořit, odesílat a spravovat vaše referenčních dat ve službě TSI, přejděte na naše [referenční dokumentace sady dat](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

## <a name="business-disaster-recovery"></a>Zotavení po havárii firmy
Jako službu Azure Time Series Insights poskytuje vysokou dostupnost (HA) pomocí redundance na úrovni oblasti Azure, bez jakékoli další práce požadováno řešením. Platforma Microsoft Azure také zahrnuje funkce, které vám pomůžou vytvářet řešení s možností zotavení po havárii nebo dostupnosti mezi oblastmi. Pokud chcete zadat globální, mezi různými oblastmi vysoká dostupnost pro zařízení nebo uživatele, využijte tyto funkce zotavení po Havárii Azure. Tento článek [Azure obchodní kontinuity podnikových procesů technickou pomoc](../resiliency/resiliency-technical-guidance.md) popisuje předdefinované funkce v Azure pro provozní kontinuitu a zotavení po Havárii. [Zotavení po havárii a vysoká dostupnost pro aplikace Azure](https://docs.microsoft.com/azure/architecture/resiliency/index) dokument obsahuje pokyny k architektuře strategie pro aplikace v Azure zajistit vysokou dostupnost a zotavení po Havárii.

Azure Time Series Insights nemá integrované zotavení po havárii (BCDR).
Ve výchozím nastavení mají obnovení vytvořené Azure IoT Hub a Event Hubs.

Další informace o službě IoT Hub BCDR zásady, head [tady](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr).  

Další informace o zásadách BCDR centra událostí, head [tady](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr).

Zákazníci, kteří vyžadují BCDR však stále implementovat strategii zotavení pomocí následující metody.
Můžete vytvořit druhý prostředí Time Series Insights v zálohování Azure oblasti a odesílání událostí do tohoto sekundárního prostředí z zdroj primární události, využívat druhý vyhrazenou skupinu spotřebitelů a pokyny pro tento zdroj události BCDR.  

1.  Vytvořte prostředí v druhé oblasti.  Další informace o vytvoření prostředí Time Series Insights [tady](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
2.  Vytvoření druhého vyhrazenou skupinu spotřebitelů pro váš zdroj událostí a připojení tohoto zdroje událostí do nového prostředí.  Nezapomeňte určit druhé, vyhrazená skupina příjemců.  Další informace o tomto podle buď [dokumentace ke službě IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) nebo [dokumentace k centrům událostí](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access).
3.  Pokud primární oblast, přestanou fungovat během události po havárii, přepněte operací zálohování prostředí Time Series Insights.  

Je **důležité si uvědomit** během jakýkoli scénář převzetí služeb při selhání může docházet k prodlevám před TSI znovu spusťte zpracování zpráv: to může způsobit Špička při zpracování zpráv. Další informace prosím podívejte se na [tento dokument](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency)

## <a name="next-steps"></a>Další postup
- [Přidání zdroje událostí centra událostí](time-series-insights-how-to-add-an-event-source-eventhub.md)
- [Přidání zdroje událostí IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md)
