---
title: Ukládání dat a příchozí přenosy v Azure Time Series Insights Preview | Microsoft Docs
description: Principy úložiště a příchozího přenosu dat ve verzi Azure Time Series Insights Preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/20/2019
ms.custom: seodec18
ms.openlocfilehash: a37021d11de86fc0958f330f4f594e25e3aa00bd
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68618195"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Ukládání dat a příchozí přenosy v Azure Time Series Insights ve verzi Preview

Tento článek popisuje změny datového úložiště a příchozího přenosu dat z Azure Time Series Insights ve verzi Preview. Pokrývá základní strukturu úložiště, formát souboru a ID časové řady. Tento článek také popisuje základní proces příchozího přenosu dat, propustnost a omezení.

## <a name="data-ingress"></a>Příchozí datové přenosy

Azure Time Series Insights zásady příchozího přenosu dat určují, odkud se dají data nacházet z a v jakém formátu.

[![Přehled modelu časové řady](media/v2-update-storage-ingress/tsi-data-ingress.png)](media/v2-update-storage-ingress/tsi-data-ingress.png#lightbox)

### <a name="ingress-policies"></a>Zásady příchozího přenosu dat

Time Series Insights Preview podporuje stejné zdroje událostí a typy souborů, které Time Series Insights aktuálně podporuje:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)
  
Azure Time Series Insights podporuje JSON odeslaný prostřednictvím Azure IoT Hub nebo Azure Event Hubs. Pokud chcete optimalizovat data ve službě IoT JSON, přečtěte si, [jak můžete tvarovat JSON](./time-series-insights-send-events.md#json).

### <a name="data-storage"></a>Úložiště dat

Když vytvoříte prostředí Time Series Insights Preview s průběžnými platbami podle aktuálního využití, vytvoříte dva prostředky:

* Time Series Insights prostředí.
* Azure Storage účet pro obecné účely V1, ve kterém se budou data ukládat.

Time Series Insights Preview používá úložiště objektů BLOB v Azure s typem souboru Parquet. Time Series Insights spravuje všechny datové operace, včetně vytváření objektů blob, indexování a dělení dat v účtu úložiště Azure. Tyto objekty blob vytvoříte pomocí účtu úložiště Azure.

Podobně jako u jiných objektů blob Azure Storage vám Time Series Insights vytvořené objekty blob umožňují čtení a zápis do nich pro podporu různých integračních scénářů.

### <a name="data-availability"></a>Dostupnost dat

Time Series Insights ve verzi Preview indexuje data pomocí strategie Optimalizace velikosti objektů BLOB. Data budou k dispozici pro dotaz po jejím indexování, což vychází z toho, kolik dat přichází a v jaké rychlosti.

> [!IMPORTANT]
> * Vydaná verze Time Series Insights General Availability (GA) zpřístupňuje data do 60 sekund po doručení do zdroje událostí.
> * Během období Preview je před zpřístupněním dat očekávána delší doba.
> * Pokud se setkáte s významnou latencí, kontaktujte nás.

### <a name="scale"></a>Měřítko

Time Series Insights Preview podporuje počáteční škálu příchozího přenosu dat až 1 megabajtů bajtů za sekundu (MB/s) na jedno prostředí. Podpora škálování na více serverů pokračuje. Plánujeme aktualizovat naši dokumentaci, aby odrážela tato vylepšení.

## <a name="parquet-file-format"></a>Formát souboru Parquet

Parquet je formát datového souboru orientovaný na sloupce, který byl navržen pro:

* Vzájemná funkční spolupráce
* Efektivita prostoru
* Efektivita dotazů

Time Series Insights zvolit Parquet, protože poskytuje efektivní schémata komprese a kódování dat s vyšším výkonem, který dokáže hromadně zpracovávat složitá data.

Další informace o typu souboru Parquet najdete v [dokumentaci k Parquet](https://parquet.apache.org/documentation/latest/).

Další informace o formátu souboru Parquet v Azure najdete v tématu [podporované typy souborů v Azure Storage](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#parquet-format).

### <a name="event-structure-in-parquet"></a>Struktura události v Parquet

Time Series Insights vytváří a ukládá kopie objektů BLOB v následujících dvou formátech:

1. První, počáteční kopie je rozdělená podle času doručení:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Čas vytvoření objektu BLOB pro objekty blob dělený časem doručení

1. Druhá, znovu rozdělené kopírování je rozdělené do oddílů pomocí dynamického seskupení ID časové řady:

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Minimální časové razítko události v objektu BLOB pro objekty blob rozdělené podle ID časové řady

> [!NOTE]
> * `<YYYY>`provede mapování na vyjádření čtyřmístného roku.
> * `<MM>`provede mapování na vyjádření v měsíci se dvěma číslicemi.
> * `<YYYYMMDDHHMMSSfff>`mapuje se na reprezentaci časového razítka`YYYY`s 4 číslicemi (), 2 číslice měsíc (`MM`), 2-číslice dne`DD`(), 2-číslice Hour`HH`(), 2-číslice minuty`MM`(), 2-číslice sekunda (`SS`) a 3 číslice. milisekunda`fff`().

Události Time Series Insights jsou namapovány na obsah souboru Parquet následujícím způsobem:

* Každá událost je mapována na jeden řádek.
* Vestavěný sloupec **časového razítka** s časovým razítkem události. Vlastnost časového razítka nemá nikdy hodnotu null. Ve výchozím nastavení je **zdrojová událost** ve frontě, pokud ve zdroji událostí není zadána vlastnost časového razítka. Časové razítko je v UTC. 
* Všechny ostatní vlastnosti, které jsou mapovány na sloupce `_string` končící na (String `_bool` ), (Boolean `_datetime` ), (DateTime) `_double` a (Double), v závislosti na typu vlastnosti.
* Toto je schéma mapování pro první verzi formátu souboru, na který odkazujeme jako **V = 1**. Jak se tato funkce vyvíjí, název se zvýší na **v = 2**, **v = 3**atd.

## <a name="azure-storage"></a>Azure Storage

Tato část popisuje Azure Storage podrobnosti, které jsou relevantní pro Azure Time Series Insights.

Podrobný popis služby Azure Blob Storage najdete v tématu Úvod do [objektů BLOB úložiště](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Váš účet úložiště

Když vytváříte Time Series Insights prostředí s průběžnými platbami, vytvoříte dva prostředky: Time Series Insights prostředí a účet Azure Storage pro obecné účely V1, kde budou data uložená. Zvolili jsme Azure Storage pro obecné účely v1 výchozí prostředek z důvodu jeho interoperability, ceny a výkonu.

Time Series Insights publikovat až dvě kopie každé události v účtu služby Azure Storage. Počáteční kopie se vždycky zachová, abyste se mohli rychle dotazovat na ni pomocí dalších služeb. Můžete snadno použít Spark, Hadoop a další známé nástroje pro ID časových řad v nezpracovaných Parquet souborech, protože tyto moduly podporují základní filtrování názvů souborů. Seskupení objektů BLOB podle roku a měsíce je užitečný způsob, jak vypsat objekty BLOB v určitém časovém rozsahu pro vlastní úlohu.

Dále Time Series Insights přerozdělit soubory Parquet tak, aby se optimalizoval pro Time Series Insights rozhraní API. Soubor naposledy znovu rozdělený do oddílů je také uložen.

Během veřejné verze Preview se data ukládají na účet služby Azure Storage po neomezenou dobu.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Zápis a úpravy objektů BLOB Time Series Insights

Chcete-li zajistit výkon dotazů a dostupnost dat, neupravujte ani neodstraňujte žádné objekty blob vytvořené nástrojem Time Series Insights.

> [!TIP]
> Time Series Insights výkon může negativně ovlivnit při příliš častém čtení nebo zápisu do objektů BLOB.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Přístup k datům z Time Series Insights Preview a jejich export

Můžete chtít získat přístup k datům uloženým v Průzkumníkovi služby Time Series Insights Preview pro použití ve spojení s jinými službami. Můžete například chtít data použít k hlášení Power BI, k provádění strojového učení pomocí Azure Machine Learning Studio nebo k použití v aplikaci poznámkového bloku s poznámkovým blokem Jupyter.

K datům můžete získat přístup třemi obecnými způsoby:

* Z Průzkumníka Time Series Insights Preview: data můžete exportovat jako soubor CSV z Průzkumníka Time Series Insights Preview. Další informace najdete v tématu [Time Series Insights Průzkumníku Preview](./time-series-insights-update-explorer.md).
* Z rozhraní API služby Time Series Insights Preview: koncový bod rozhraní API se dá `/getRecorded`kontaktovat na adrese. Další informace o tomto rozhraní API najdete v tématu [dotazování časových řad](./time-series-insights-update-tsq.md).
* Přímo z účtu služby Azure Storage (níže).

#### <a name="from-an-azure-storage-account"></a>Z účtu služby Azure Storage

* Potřebujete přístup pro čtení k libovolnému účtu, který používáte pro přístup k datům Time Series Insights. Další informace najdete v tématu [Správa přístupu k prostředkům účtu úložiště](../storage/blobs/storage-manage-access-to-resources.md).
* Další informace o přímých způsobech, jak číst data z úložiště objektů BLOB v Azure, najdete v tématu [Volba řešení Azure pro přenos dat](../storage/common/storage-choose-data-transfer-solution.md).
* Export dat z účtu služby Azure Storage:
    * Nejdřív se ujistěte, že váš účet splňuje nezbytné požadavky na export dat. Další informace najdete v tématu [požadavky na Import a export úložiště](../storage/common/storage-import-export-requirements.md).
    * Další informace o dalších způsobech exportu dat z účtu služby Azure Storage najdete v tématu [Import a export dat z objektů BLOB](../storage/common/storage-import-export-data-from-blobs.md).

### <a name="data-deletion"></a>Odstranění dat

Neodstraňujte objekty blob. Nejenom jsou užitečné k auditování a údržbě záznamů dat, Time Series Insights Preview udržuje metadata objektů BLOB v rámci každého objektu BLOB.

## <a name="partitions"></a>Oddíly

Každé prostředí Time Series Insights ve verzi Preview musí mít vlastnost **ID časové řady** a vlastnost **časového razítka** , která ji jednoznačně identifikuje. Vaše ID časové řady funguje jako logický oddíl pro vaše data a poskytuje prostředí Time Series Insights Preview přirozené hranice pro distribuci dat mezi fyzickými oddíly. Fyzické oddíly jsou spravované nástrojem Time Series Insights Preview v účtu Azure Storage.

Time Series Insights používá dynamické dělení k optimalizaci výkonu úložiště a dotazů tím, že vyřadí a znovu vytvoří oddíly. Algoritmus dynamického dělení Time Series Insights Preview se snaží zabránit tomu, aby jeden fyzický oddíl mohl mít data pro několik odlišných logických oddílů. Jinými slovy, algoritmus dělení udržuje všechna data specifická pro jedno ID časové řady, které je exkluzivně přítomné v souborech Parquet, bez nutnosti jejich ponechání s jinými ID časových řad. Algoritmus dynamického dělení se také pokouší zachovat původní pořadí událostí v rámci jediného ID časové řady.

Zpočátku jsou data rozdělená do oddílů pomocí časového razítka tak, aby jeden logický oddíl v daném časovém rozsahu mohl být rozložen mezi několik fyzických oddílů. Jeden fyzický oddíl může obsahovat také mnoho nebo všechny logické oddíly. Vzhledem k omezením velikosti objektů blob, a to i s optimálním rozdělením na oddíly, může jeden logický oddíl zabírat několik fyzických oddílů.

> [!NOTE]
> Ve výchozím nastavení je hodnota časového razítka v nakonfigurovaném zdroji událostí ve frontě.

Pokud odesíláte historická data nebo zprávy služby Batch, přiřaďte hodnotu, kterou chcete uložit s daty do vlastnosti timestamp, která se mapuje na příslušné časové razítko. Vlastnost timestamp rozlišuje velká a malá písmena. Další informace najdete v tématu [model časové řady](./time-series-insights-update-tsm.md).

### <a name="physical-partitions"></a>Fyzické oddíly

Fyzický oddíl je objekt blob bloku, který je uložený ve vašem účtu úložiště. Skutečná velikost objektů BLOB se může lišit, protože velikost závisí na sazbách nabízených oznámení. Očekává se ale velikost objektů BLOB o velikosti přibližně 20 MB až 50 MB. Toto očekávání vedlo Time Series Insights týmu k výběru 20 MB jako velikosti pro optimalizaci výkonu dotazů. Tato velikost se může v průběhu času měnit v závislosti na velikosti souboru a rychlosti příchozího přenosu dat.

> [!NOTE]
> * Velikost objektů BLOB je 20 MB.
> * V případě, že se vynechává a znovu vytvoří, jsou objekty BLOB v Azure občas znovu rozdělené pro lepší výkon.
> * Stejná Time Series Insightsová data mohou být také přítomna ve dvou nebo více objektech blob.

### <a name="logical-partitions"></a>Logické oddíly

Logický oddíl je oddíl v rámci fyzického oddílu, ve kterém jsou uložena všechna data přidružená k hodnotě klíče s jedním oddílem. Time Series Insights Preview logicky rozdělí každý objekt blob na základě dvou vlastností:

* **ID časové řady**: Klíč oddílu pro všechna Time Series Insights data v datovém proudu událostí a modelu.
* **Časové razítko**: Čas na základě počátečního příchozího přenosu dat.

Time Series Insights Preview poskytuje výkonné dotazy, které jsou založeny na těchto dvou vlastnostech. Tyto dvě vlastnosti také poskytují nejúčinnější metodu pro rychlé doručování Time Series Insights dat.

Je důležité vybrat odpovídající ID časové řady, protože se jedná o neměnné vlastnosti. Další informace najdete v tématu [Výběr ID časových řad](./time-series-insights-update-how-to-id.md).

## <a name="next-steps"></a>Další postup

- Přečtěte si [Azure Time Series Insights a příchozí úložiště ve verzi Preview](./time-series-insights-update-storage-ingress.md).

- Přečtěte si o nových [datových modelování](./time-series-insights-update-tsm.md).
