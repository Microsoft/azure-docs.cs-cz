---
title: Ukládání dat a příchozí přenosy do verze Preview – Azure Time Series Insights | Microsoft Docs
description: Přečtěte si o ukládání dat a příchozím přenosu v Azure Time Series Insights ve verzi Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/31/2019
ms.custom: seodec18
ms.openlocfilehash: 1deca696ba576849701eb8719de7fbaa7895a26a
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861400"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Ukládání dat a příchozí přenosy v Azure Time Series Insights ve verzi Preview

Tento článek popisuje aktualizace úložiště dat a příchozí Azure Time Series Insights ve verzi Preview. Pokrývá základní strukturu úložiště, formát souboru a ID časové řady. Popisuje také základní proces příchozího přenosu dat, osvědčené postupy a aktuální omezení verze Preview.

## <a name="data-ingress"></a>Příchozí datové přenosy

Vaše Azure Time Series Insights prostředí obsahuje modul ingestování, který umožňuje shromažďovat, zpracovávat a ukládat data časových řad. Při plánování prostředí je potřeba vzít v úvahu několik důležitých informací, aby se zajistilo, že se zpracují všechna příchozí data a aby se dosáhlo vysokého rozsahu příchozího přenosu dat a minimalizovala latence příjmu (doba potřebná pro čtení a zpracování dat z události v TSI). zdroj). 

V Time Series Insights ve verzi Preview určují zásady příchozího přenosu dat, ze kterých se dají data nacházet, a jaký formát mají mít data.

### <a name="ingress-policies"></a>Zásady příchozího přenosu dat

Time Series Insights Preview podporuje následující zdroje událostí:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Time Series Insights Preview podporuje maximálně dva zdroje událostí na instanci. Azure Time Series Insights podporuje JSON odeslaný prostřednictvím Azure IoT Hub nebo Azure Event Hubs.

> [!WARNING] 
> * Při připojování zdroje událostí k prostředí Preview se může vyskytnout vysoká počáteční latence. 
> Latence zdroje událostí závisí na počtu událostí, které jsou aktuálně ve IoT Hub nebo v centru událostí.
> * Po prvním ingestování zdrojových dat událostí se tato vysoká latence bude nacházet. Pokud chcete pokračovat v vysoké latenci, kontaktujte nás odesláním lístku podpory prostřednictvím Azure Portal.

## <a name="ingress-best-practices"></a>Osvědčené postupy pro příchozí přenosy

Doporučujeme, abyste využívali následující osvědčené postupy:

* Nakonfigurujte Time Series Insights a centrum IoT nebo centrum událostí ve stejné oblasti. Tím se sníží latence přijímání, která vzniká v důsledku sítě.
* Vypočítejte předpokládané požadavky na škálování tak, že vypočítáte očekávanou rychlost příjmu a ověříte, že spadá do podporované sazby uvedené níže.
* Seznamte se s tím, jak optimalizovat a natvarovat data JSON, jakož i aktuální omezení ve verzi Preview. Přečtěte si, jak můžete naformátovat [JSON pro příchozí a dotaz](./time-series-insights-update-how-to-shape-events.md).

### <a name="ingress-scale-and-limitations-in-preview"></a>Škálování a omezení příchozího přenosu dat ve verzi Preview

Ve výchozím nastavení podporují prostředí pro příchozí přenosy až **1 megabajt za sekundu (MB/s) na prostředí**. Zákazníci můžou v případě potřeby škálovat v prostředích ve verzi Preview až o **16 MB/s** .
K dispozici je také omezení počtu **0,5 MB/s**na oddíly. 

Omezení na oddíly má pro zákazníky, kteří používají IoT Hub, důsledky. Konkrétně vzhledem k spřažení mezi IoT Hub zařízením a oddílem. Ve scénářích, kdy jedno zařízení brány předává zprávy do centra pomocí vlastního ID zařízení a připojovacího řetězce, je nebezpečí dosažení 0,5 MB/s, že se zprávy dostanou do jednoho oddílu, a to i v případě, že datová část události Určuje různá ID časových řad. 

Míry příchozího přenosu dat se zobrazují jako faktor počtu zařízení, která jsou ve vaší organizaci, četnosti emisí událostí a velikosti jednotlivých událostí:

*  **Počet zařízení** × **četnost měření událostí** × **Velikost každé události**.

> [!TIP]
> Pro prostředí, která používají IoT Hub jako zdroj události, vypočítejte rychlost příjmu pomocí počtu používaných připojení centra, nikoli podle toho, jaká zařízení v organizaci používáte nebo v organizaci.

Další informace o jednotkách propustnosti, omezeních a oddílech:

* [Škálování IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Škálování centra událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Oddíly centra událostí](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

### <a name="data-storage"></a>Úložiště dat

Když vytvoříte prostředí Time Series Insights Preview s průběžnými platbami podle aktuálního využití, vytvoříte dva prostředky Azure:

* Prostředí Time Series Insights ve verzi Preview, které může volitelně zahrnovat možnosti pro teplé úložiště.
* Účet blob Azure Storage pro obecné účely V1 pro úložiště studených dat.

Data v teplém úložišti jsou k dispozici pouze v [dotazech Time Series](./time-series-insights-update-tsq.md) a v [Průzkumníku služby Azure Time Series Insights Preview](./time-series-insights-update-explorer.md). 

Time Series Insights Preview uloží data z chladírny do úložiště objektů BLOB v Azure ve [formátu souboru Parquet](#parquet-file-format-and-folder-structure). Time Series Insights Preview spravuje tato data studeného úložiště výhradně, ale je k dispozici pro čtení přímo jako standardních souborů Parquet.

> [!WARNING]
> Jako vlastník účtu služby Azure Blob Storage, ve kterém jsou uložená data, máte plný přístup ke všem datům v účtu. Tento přístup zahrnuje oprávnění k zápisu a odstraňování. Neupravujte ani neodstraňujte data, která Time Series Insights náhled zapisuje, protože to může způsobit ztrátu dat.

### <a name="data-availability"></a>Dostupnost dat

Time Series Insights zobrazovat náhled oddílů a data indexů pro optimální výkon dotazů. Data budou k dispozici pro dotaz po jejím indexování. Množství dat, která se ingestují, můžou ovlivnit tuto dostupnost.

> [!IMPORTANT]
> Nadcházející verze Time Series Insights, která je k dispozici pro obecné dostupnosti, zpřístupní data po uplynutí 60 sekund od jejího čtení ze zdroje událostí. Během období Preview může docházet k delší době, než budou data k dispozici. Pokud se setkáte s významnou latencí delší než 60 sekund, odešlete prosím lístek podpory prostřednictvím Azure Portal.

## <a name="azure-storage"></a>Azure Storage

Tato část popisuje Azure Storage detailů týkajících se Azure Time Series Insights verze Preview.

Podrobný popis úložiště objektů BLOB v Azure najdete v [úvodu do objektů BLOB úložiště](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Váš účet úložiště

Když vytvoříte prostředí s průběžnými platbami Time Series Insights Preview, vytvoří se účet blob Azure Storage pro obecné účely v1 jako váš dlouhodobý chladírenský sklad.  

Time Series Insights Preview publikuje až dvě kopie každé události v účtu Azure Storage. Počáteční kopie má události seřazené podle času ingestování a je vždycky zachovaná, takže k ní můžete použít jiné služby. Pro zpracování nezpracovaných souborů Parquet můžete použít Spark, Hadoop a další známé nástroje. 

Time Series Insights Preview přerozdělení souborů Parquet na optimalizaci pro Time Series Insights dotaz. Tato kopie dat se také ukládá do oddílů.

Během veřejné verze Preview jsou data ve vašem účtu Azure Storage ukládána po neomezenou dobu.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Zápis a úpravy objektů BLOB Time Series Insights

Aby se zajistil výkon dotazů a dostupnost dat, neupravujte ani neodstraňujte žádné objekty blob, které Time Series Insights vytváření Preview.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Přístup k datům z Time Series Insights Preview a jejich export

Můžete chtít získat přístup k datům zobrazeným v Průzkumníkovi služby Time Series Insights Preview pro použití ve spojení s jinými službami. Data můžete například použít k sestavení sestavy v Power BI nebo ke školení modelu strojového učení pomocí Azure Machine Learning Studio. Nebo můžete data použít k transformaci, vizualizaci a modelování v Jupyter poznámkových blocích.

K datům můžete získat přístup třemi obecnými způsoby:

* Z Průzkumníka služby Time Series Insights Preview. Data můžete z Průzkumníka exportovat jako soubor CSV. Další informace najdete v tématu [Time Series Insights Exploreru pro náhled](./time-series-insights-update-explorer.md).
* Z rozhraní API služby Time Series Insights Preview. Ke koncovému bodu rozhraní API se můžete dostat na `/getRecorded`. Pokud chcete získat další informace o tomto rozhraní API, dotaz pro čtení [časové řady](./time-series-insights-update-tsq.md).
* Přímo z účtu Azure Storage. Potřebujete přístup pro čtení k libovolnému účtu, který používáte pro přístup k datům ve verzi Preview Time Series Insights. Další informace najdete v článku [Správa přístupu k prostředkům účtu úložiště](../storage/blobs/storage-manage-access-to-resources.md).

### <a name="data-deletion"></a>Odstranění dat

Neodstraňujte soubory ve verzi Preview Time Series Insights. Spravujte související data jenom z Time Series Insights jenom ve verzi Preview.

## <a name="parquet-file-format-and-folder-structure"></a>Formát souboru a struktura složek Parquet

Parquet je open source formát sloupcového souboru, který byl navržen pro efektivní úložiště a výkon. Time Series Insights Preview v těchto případech používá Parquet. Rozdělí data podle ID časové řady pro výkon dotazů ve velkém měřítku.  

Další informace o typu souboru Parquet najdete v [dokumentaci k Parquet](https://parquet.apache.org/documentation/latest/).

Time Series Insights Preview ukládá kopie vašich dat následujícím způsobem:

* První, počáteční kopie je dělená časem ingestování a ukládá data přibližně v pořadí doručení. Data se nachází ve složce `PT=Time`:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Druhá, znovu rozdělená kopie je rozdělená na oddíly s ID časových řad a nachází se ve složce `PT=TsId`:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

V obou případech časové hodnoty odpovídají času vytvoření objektu BLOB. Data ve složce `PT=Time` jsou zachována. Data ve složce `PT=TsId` budou optimalizována pro dotazy v průběhu času a nebudou zůstat statická.

> [!NOTE]
> * `<YYYY>` se mapuje na vyjádření čtyřmístného roku.
> * `<MM>` se mapuje na dvouciferné měsíční vyjádření.
> * `<YYYYMMDDHHMMSSfff>` se mapuje na časovou reprezentaci se čtyřmi číslicemi (`YYYY`), dvoumístný měsíc (`MM`), dvoumístný den (`DD`), dvě číslice (`HH`), dvě číslice (`MM`), dvě číslice (`SS`) a tři číslice milisekundy (`fff`).

Události ve verzi Preview Time Series Insights jsou namapovány na obsah souboru Parquet následujícím způsobem:

* Každá událost je mapována na jeden řádek.
* Každý řádek obsahuje sloupec **časového razítka** s časovým razítkem události. Vlastnost časového razítka není nikdy null. Ve výchozím nastavení je to čas zařazený do **fronty** , pokud není ve zdroji událostí zadána vlastnost časového razítka. Časové razítko je vždy v UTC.
* Každý řádek obsahuje sloupec ID časové řady definovaný při vytvoření prostředí Time Series Insights. Název vlastnosti zahrnuje příponu `_string`.
* Všechny ostatní vlastnosti odeslané jako data telemetrie jsou namapovány na názvy sloupců, které končí na `_string` (String), `_bool` (Boolean), `_datetime` (DateTime) nebo `_double` (Double) v závislosti na typu vlastnosti.
* Toto schéma mapování platí pro první verzi formátu souboru, na kterou se odkazuje jako **V = 1**. Jak se tato funkce vyvíjí, může se tento název zvýšit.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [Azure Time Series Insights a příchozí úložiště ve verzi Preview](./time-series-insights-update-storage-ingress.md).

- Přečtěte si o nových [datových modelování](./time-series-insights-update-tsm.md).
