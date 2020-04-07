---
title: Úložiště dat a příchozí přenos dat ve verzi Preview – Přehledy azure časové řady | Dokumenty společnosti Microsoft
description: Další informace o ukládání dat a příchozím přenosu dat ve verzi Azure Time Series Insights Preview.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 95a579cacc339360295f5f25fa6415ab29cd68ff
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673905"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Úložiště dat a příchozí přenos dat ve verzi Azure Time Series Insights Preview

Tento článek popisuje aktualizace úložiště dat a příchozího přenosu dat pro Azure Time Series Insights Preview. Popisuje základní strukturu úložiště, formát souboru a vlastnost ID časové řady. Základní proces příchozího přenosu dat, doporučené postupy a aktuální omezení náhledu jsou také popsány.

## <a name="data-ingress"></a>Příchozí přenos dat

Vaše prostředí Azure Time Series Insights obsahuje *modul pro ingestování,* který shromažďuje, zpracovává a ukládá data časových řad. 

Při [plánování](time-series-insights-update-plan.md)prostředí je třeba mít na paměti, že všechna příchozí data jsou zpracována, aby bylo možné dosáhnout vysokého měřítka příchozího přenosu dat a minimalizovat *latenci přijímání* (čas, který time series insights používá ke čtení a zpracování dat ze zdroje událostí).

Zásady příchozího přenosu dat přehledů časové řady určují, odkud lze data pocházet a jaký formát by měla mít data.

### <a name="ingress-policies"></a>Zásady příchozího přenosu dat

*Příchozí přenos dat* zahrnuje způsob, jakým se data odesílají do prostředí Azure Time Series Insights Preview. 

Konfigurace klíče, formátování a doporučené postupy jsou shrnuty níže.

#### <a name="event-sources"></a>Zdroje událostí

Azure Time Series Insights Preview podporuje následující zdroje událostí:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Azure Time Series Insights Preview podporuje maximálně dva zdroje událostí na instanci. Když připojíte zdroj událostí, vaše prostředí TSI přečte všechny události, které jsou aktuálně uložené ve vašem centru Hodně nebo Události, počínaje nejstarší událostí. 

> [!IMPORTANT] 
> * Při připojování zdroje událostí k prostředí Preview může dojít k vysoké počáteční latenci. 
> Latence zdroje událostí závisí na počtu událostí, které jsou aktuálně ve vašem centru IoT Hub nebo Event Hub.
> * Vysoká latence odezní po prvním požití zdrojových dat události. Pokud se u vás setkáte s trvalou vysokou latencí, odešlete lístek podpory prostřednictvím portálu Azure.

#### <a name="supported-data-format-and-types"></a>Podporovaný formát a typy dat

Azure Time Series Insights podporuje JSON kódovaný UTF-8 odeslaný z Azure IoT Hub nebo Azure Event Hubs. 

Podporované datové typy jsou:

| Datový typ | Popis |
|---|---|
| **bool** | Datový typ s jedním ze `true` `false`dvou stavů: nebo . |
| **Datetime** | Představuje okamžik v čase, obvykle vyjádřený jako datum a čas dne. Vyjádřeno ve formátu [ISO 8601.](https://www.iso.org/iso-8601-date-and-time-format.html) |
| **double** | Dvojitá přesnost 64bitová plovoucí bod [IEEE 754.](https://ieeexplore.ieee.org/document/8766229) |
| **Řetězec** | Textové hodnoty složené ze znaků Unicode.          |

#### <a name="objects-and-arrays"></a>Objekty a pole

Můžete odesílat složité typy, jako jsou objekty a pole jako součást datové části události, ale vaše data budou podrobena procesu sloučení při uložení. 

Podrobné informace popisující, jak utvářet události JSON, odeslat složitý typ a sloučení objektů vnořených objektů, jsou k dispozici v části [Jak utvářet json pro příchozí přenos dat a dotaz,](./time-series-insights-update-how-to-shape-events.md) který vám pomůže s plánováním a optimalizací.

### <a name="ingress-best-practices"></a>Příchozí osvědčené postupy

Doporučujeme použít následující osvědčené postupy:

* Nakonfigurujte Azure Time Series Insights a jakékoli IoT Hub nebo Event Hub ve stejné oblasti, abyste snížili potenciální latenci.

* [Naplánujte si potřeby škálovacístupnice](time-series-insights-update-plan.md) výpočtem předpokládané míry požití a ověřením, zda spadá do níže uvedené podporované sazby.

* Zjistěte, jak optimalizovat a utvářet data JSON, stejně jako aktuální omezení ve verzi Preview, čtením způsobu [utváření json pro příchozí přenos dat a dotaz](./time-series-insights-update-how-to-shape-events.md).

### <a name="ingress-scale-and-preview-limitations"></a>Omezení měřítka příchozího přenosu dat a náhledu 

Omezení příchozího přenosu dat Azure Time Series Insights jsou popsána níže.

> [!TIP]
> Přečtěte si [článek Plánování prostředí preview](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) pro úplný seznam všech limitů náhledu.

#### <a name="per-environment-limitations"></a>Omezení pro životní prostředí

Obecně platí, že míry příchozího přenosu dat jsou považovány za faktor počtu zařízení, která jsou ve vaší organizaci, četnosti emisí událostí a velikosti každé události:

*  **Počet zařízení** × **četnost emisí událostí** × velikost každé **události**.

Ve výchozím nastavení může náhled Time Series Insights ingestovat příchozí data rychlostí **až 1 mb/s za sekundu (Mb/s) na prostředí Time Series Insights**. Existují další omezení [na oddíl rozbočovače](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#hub-partitions-and-per-partition-limits).

> [!TIP] 
> * Na požádání lze poskytnout podporu prostředí pro ingestování rychlostí až 16 Mb/s.
> * Pokud potřebujete vyšší propustnost, kontaktujte nás odesláním lístku podpory prostřednictvím portálu Azure.
 
* **Příklad 1:**

    Společnost Contoso Shipping má 100 000 zařízení, která vyzařují událost třikrát za minutu. Velikost události je 200 bajtů. Používají službu Iot Hub se čtyřmi oddíly jako zdroj událostí Time Series Insights.

    * Míra ingestování pro jejich prostředí Time Series Insights by byla: **100 000 zařízení * 200 bajtů/událost * (3/60 událostí/s) = 1 Mb/s**.
    * Rychlost ingestování na oddíl by byla 0,25 Mb/s.
    * Rychlost přihlašování společnosti Contoso Shipping by byla v rámci omezení měřítka náhledu.

* **Příklad 2:**

    Contoso Fleet Analytics má 60 000 zařízení, která vyzařují událost každou sekundu. Používají centrum událostí s počtem oddílů 4 jako zdroj událostí Time Series Insights. Velikost události je 200 bajtů.

    * Rychlost požití prostředí by byla: **60 000 zařízení * 200 bajtů/událost * 1 událost/s = 12 Mb/s**.
    * Rychlost na oddíl by byla 3 MB/s.
    * Míra přihlašování společnosti Contoso Fleet Analytics je nad limity prostředí a oddílů. Můžou odeslat žádost na Time Series Insights prostřednictvím portálu Azure, aby zvýšili rychlost ingestování pro své prostředí a vytvořili Centrum událostí s dalšími oddíly, které bude v rámci limitů náhledu.

#### <a name="hub-partitions-and-per-partition-limits"></a>Oddíly rozbočovače a omezení na oddíl

Při plánování prostředí Time Series Insights je důležité zvážit konfiguraci zdrojů událostí, které se budete připojovat k Time Series Insights. Azure IoT Hub a Event Hubs využívají oddíly k povolení horizontálníškálování pro zpracování událostí. 

*Oddíl* je uspořádaná posloupnost událostí držených v rozbočovači. Počet oddílů je nastaven během fáze vytváření rozbočovače a nelze jej změnit. 

V centru událostí, kde se rozdělují osvědčené postupy, přečtěte [si, kolik oddílů potřebuji?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> Většina ioT hubů používaných s Azure Time Series Insights potřebuje jenom čtyři oddíly.

Ať už vytváříte nové centrum pro prostředí Time Series Insights nebo používáte stávající, budete muset vypočítat rychlost přihlašování na oddíl, abyste zjistili, jestli je v mezích náhledu. 

Azure Time Series Insights Preview má aktuálně obecný **limit na oddíl 0,5 Mb/s**.

#### <a name="iot-hub-specific-considerations"></a>Důležité informace specifické pro ioT hub

Když je zařízení vytvořeno v centru IoT Hub, je trvale přiřazeno k oddílu. Přitom ioT Hub je schopen zaručit řazení událostí (protože přiřazení se nikdy nezmění).

Přiřazení pevného oddílu má také vliv na instance Time Series Insights, které příjem vajících dat odeslaných z centra IoT Hub pod proudem. Pokud jsou zprávy z více zařízení předávány do rozbočovače pomocí stejného ID zařízení brány, mohou být doručeny do stejného oddílu současně potenciálně překračující limity měřítka na oddíl. 

**Dopad**:

* Pokud jeden oddíl dojde k trvalé rychlosti ingestování přes limit náhledu, je možné, že Time Series Insights nebude synchronizovat všechny telemetrie zařízení před uplynutím doby uchovávání dat služby IoT Hub. V důsledku toho může dojít ke ztrátě odeslaných dat, pokud jsou konzistentně překročeny limity pro přihlašování.

Chcete-li tuto situaci zmírnit, doporučujeme následující doporučené postupy:

* Před nasazením řešení spočítejte rychlost i pro jedno prostředí a počet přijím na oddíl.
* Ujistěte se, že vaše zařízení IoT Hub jsou vyvážená zatížením v co nejdál.

> [!IMPORTANT]
> Pro prostředí, která používají službu IoT Hub jako zdroj událostí, vypočítejte rychlost ingestování pomocí počtu používaných zařízení rozbočovače, abyste se ujistili, že rychlost klesne pod omezení 0,5 Mb/s na oddíl ve verzi Preview.
> * I v případě, že několik událostí dorazí současně, limit náhledu nebude překročen.

  ![Diagram oddílu centra IoT](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Další informace o optimalizaci propustností a oddílů centra najdete v následujících zdrojích informací:

* [Měřítko ioT hubu](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Měřítko centra událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Oddíly centra událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Úložiště dat

Když vytvoříte prostředí sku *s placenou* položkou s průběžných platbami s průběžným platbou s časovou řadou Insights, vytvoříte dva prostředky Azure:

* Prostředí Azure Time Series Insights Preview, které lze nakonfigurovat pro teplé úložiště dat.
* Účet objektů blob pro azure storage pro obecné účely V1 pro úložiště studených dat.

Data ve vašem teplém obchodě jsou dostupná jenom přes [Time Series Query](./time-series-insights-update-tsq.md) a Průzkumník [náhledu Přehledy Azure Time Series](./time-series-insights-update-explorer.md). Vaše teplé úložiště bude obsahovat nedávná data v rámci [doby uchování](./time-series-insights-update-plan.md#the-preview-environment) vybrané při vytváření prostředí Time Series Insights.

Náhled Time Series Insights ukládá data studeného obchodu do úložiště objektů Blob Azure ve [formátu paručových souborů](#parquet-file-format-and-folder-structure). Time Series Insights Preview spravuje tato data cold store výhradně, ale je k dispozici pro čtení přímo jako standardní parketové soubory.

> [!WARNING]
> Jako vlastník účtu úložiště objektů blob Azure, kde se nacházejí data chladírenského úložiště, máte úplný přístup ke všem datům v účtu. Tento přístup zahrnuje oprávnění k zápisu a odstranění. Neupravujte ani neodstraňujte data, která zapisuje náhled přehledů časové řady, protože to může způsobit ztrátu dat.

### <a name="data-availability"></a>Dostupnost dat

Azure Time Series Insights Náhled oddíly a indexy dat pro optimální výkon dotazu. Data budou k dispozici pro dotaz z warm (pokud je povolena) a úložiště chladu po indexování. Množství dat, která je ingestována může ovlivnit tuto dostupnost.

> [!IMPORTANT]
> Během náhledu může dojít k období až 60 sekund, než budou data k dispozici. Pokud se setkáte s významnou latencí nad 60 sekund, odešlete lístek podpory prostřednictvím portálu Azure.

## <a name="azure-storage"></a>Azure Storage

Tato část popisuje podrobnosti úložiště Azure relevantní pro Azure Time Series Insights Preview.

Podrobný popis úložiště objektů blob Azure načtete [úvod k blobs úložiště](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Váš účet úložiště

Když vytvoříte prostředí Azure Time Series Insights Preview PAYG, vytvoří se jako dlouhodobé úložiště pro chlazení účet v oblasti objektů blob úložiště Azure Storage.  

Azure Time Series Insights Preview uchovává až dvě kopie každé události ve vašem účtu Azure Storage. Jedna kopie ukládá události objednané podle doby požití, vždy umožňuje přístup k událostem v pořadí časem. V průběhu času time series insights preview také vytvoří předělanou kopii dat, která se optimalizuje pro výkonný dotaz Time Series Insights. 

Během veřejné verze Preview se data ukládají neomezeně dlouho ve vašem účtu Azure Storage.

#### <a name="writing-and-editing-time-series-insights-blobs"></a>Psaní a editace objektů objektů Blob Time Series Insights

Chcete-li zajistit výkon dotazů a dostupnost dat, neupravujte ani neodstraňujte žádné objekty BLOB, které vytvoří náhled přehledů časové řady.

#### <a name="accessing-time-series-insights-preview-cold-store-data"></a>Přístup k datům chladírenského úložiště Time Series Insights Preview 

Kromě přístupu k datům z [průzkumníka Náhled time series insights](./time-series-insights-update-explorer.md) a časového [dotazu řady](./time-series-insights-update-tsq.md)můžete také chtít přistupovat k datům přímo ze souborů parket uložených v chladírenském úložišti. Například můžete číst, transformovat a čistit data v poznámkovém bloku Jupyter a pak je použít k trénování modelu Azure Machine Learning ve stejném pracovním postupu Spark.

Chcete-li získat přístup k datům přímo z účtu Azure Storage, potřebujete přístup pro čtení k účtu, který slouží k ukládání dat náhledu přehledů časové řady. Vybraná data pak můžete číst na základě doby vytvoření `PT=Time` souboru parket umístěných ve složce popsané níže v části [Formát parketového souboru.](#parquet-file-format-and-folder-structure)  Další informace o povolení přístupu pro čtení k účtu úložiště najdete v [tématu Správa přístupu k prostředkům účtu úložiště](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Odstranění dat

Neodstraňujte soubory náhledu přehledů časové řady. Spravujte související data pouze z náhledu přehledů časových řad.

### <a name="parquet-file-format-and-folder-structure"></a>Formát parketami a struktura složek

Parkety je open-source sloupcový formát souborů určený pro efektivní ukládání a výkon. Náhled přehledů časové řady používá parkety k povolení výkonu dotazů založených na ID časových řad ve velkém měřítku.  

Další informace o typu souboru parket y naleznete v [dokumentaci k parketám](https://parquet.apache.org/documentation/latest/).

Time Series Insights Preview ukládá kopie vašich dat takto:

* První počáteční kopie je rozdělena podle doby požití a ukládá data zhruba v pořadí příjezdu. Tato data jsou `PT=Time` uložena ve složce:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Druhá, přerozdělená kopie je seskupena podle ID časové `PT=TsId` řady a je umístěna ve složce:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

V obou případech čas vlastnost souboru parket odpovídá čas vytvoření objektu blob. Data ve `PT=Time` složce jsou po zápisu do souboru zachována beze změn. Data ve `PT=TsId` složce budou optimalizována pro dotaz v průběhu času a nejsou statická.

> [!NOTE]
> * `<YYYY>`na mapu čtyřmístné roční reprezentace.
> * `<MM>`mapuje na dvouciferné měsíční znázornění.
> * `<YYYYMMDDHHMMSSfff>`mapuje na reprezentaci časového razítka`YYYY`se čtyřmístným`MM`rokem ( ),`DD`dvoumístným měsícem ( ), dvoumístným dnem ( ), dvoumístnou hodinou`HH`( ), dvoumístnou minutou (`MM`), dvoumístnou druhou (`SS`) a třímístnou milisekundou (`fff`).

Události náhledu time series insights jsou mapovány na obsah parketového souboru následujícím způsobem:

* Každá událost se mapuje na jeden řádek.
* Každý řádek obsahuje sloupec **časového razítka** s časovým razítkem události. Vlastnost časové razítko není nikdy null. Výchozí hodnota je čas zařazen do **fronty události,** pokud vlastnost časového razítka není zadána ve zdroji událostí. Uložené časové razítko je vždy v UTC.
* Každý řádek obsahuje sloupce ID časové řady (TSID), jak je definováno při vytvoření prostředí Time Series Insights. Název vlastnosti TSID `_string` obsahuje příponu.
* Všechny ostatní vlastnosti odeslané jako telemetrická data `_string` jsou `_bool` mapovány na `_datetime` názvy sloupců, které končí (řetězec), (Logická hodnota), (datetime) nebo `_double` (double), v závislosti na typu vlastnosti.
* Toto schéma mapování se vztahuje na první verzi formátu souboru, odkazované jako **V =1** a uložené v základní složce se stejným názvem. Jak se tato funkce vyvíjí, toto schéma mapování se může změnit a název odkazu se zpřísňují.

## <a name="next-steps"></a>Další kroky

- Přečtěte [si, jak utvářet JSON pro příchozí přenos dat a dotaz](./time-series-insights-update-how-to-shape-events.md).

- Přečtěte si o novém [modelování dat](./time-series-insights-update-tsm.md).
