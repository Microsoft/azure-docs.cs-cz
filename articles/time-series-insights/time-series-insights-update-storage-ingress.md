---
title: Ukládání dat a příchozí přenosy v Azure Time Series Insights Preview | Microsoft Docs
description: Principy úložiště a příchozího přenosu dat ve verzi Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.custom: seodec18
ms.openlocfilehash: 0b61e194bdea5fd8272ffc0fc9e16a2d80d3cf60
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989710"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Ukládání dat a příchozí přenosy v Azure Time Series Insights ve verzi Preview

Tento článek popisuje aktualizace úložiště dat a příchozí Azure Time Series Insights ve verzi Preview. Pokrývá základní strukturu úložiště, formát souboru a ID časové řady. Popisuje také základní proces příchozího přenosu dat, propustnost a omezení.

## <a name="data-ingress"></a>Příchozí datové přenosy

V Time Series Insights ve verzi Preview určují zásady příchozího přenosu dat, ze kterých se dají data nacházet, a jaký formát mají mít data.

[Přehled modelu časové řady ![](media/v2-update-storage-ingress/tsi-data-ingress.png)](media/v2-update-storage-ingress/tsi-data-ingress.png#lightbox)

### <a name="ingress-policies"></a>Zásady příchozího přenosu dat

Time Series Insights Preview podporuje stejné zdroje událostí, které Time Series Insights aktuálně podporuje:

- [Azure IoT Hub](../iot-hub/about-iot-hub.md)
- [Azure Event Hubs](../event-hubs/event-hubs-about.md)

Time Series Insights Preview podporuje maximálně dva zdroje událostí na instanci.
  
Azure Time Series Insights podporuje JSON odeslaný prostřednictvím Azure IoT Hub nebo Azure Event Hubs. Pokud chcete optimalizovat data ve službě IoT JSON, přečtěte si, [jak můžete tvarovat JSON](./time-series-insights-send-events.md#supported-json-shapes).

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
> Vydaná verze Time Series Insights pro obecné dostupnosti zpřístupňuje data v 60 sekund po jejich načtení ze zdroje událostí. Během období Preview může docházet k delší době, než budou data k dispozici. Pokud se setkáte s významnou latencí delší než 60 sekund, kontaktujte nás prosím.

### <a name="scale"></a>Měřítko

Ve výchozím nastavení podporuje Time Series Insights Preview počáteční škálu příchozího přenosu dat až 1 megabajt za sekundu (MB/s) na prostředí. V případě potřeby je k dispozici propustnost až 16 MB/s. Pokud potřebujete podporu rozšířeného škálování, kontaktujte nás prosím.

Pro zdroj událostí můžete získat další možnosti příchozího přenosu dat a škálování:

* [IoT Hub](../iot-hub/iot-hub-scaling.md)
* [Event Hubs](../event-hubs/event-hubs-scalability.md)

## <a name="azure-storage"></a>Azure Storage

Tato část popisuje Azure Storage detailů týkajících se Azure Time Series Insights verze Preview.

Podrobný popis úložiště objektů BLOB v Azure najdete v [úvodu do objektů BLOB úložiště](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Váš účet úložiště

Když vytvoříte prostředí s průběžnými platbami Time Series Insights Preview, vytvoří se účet blob Azure Storage pro obecné účely v1 jako váš dlouhodobý chladírenský sklad.  

Time Series Insights Preview publikuje až dvě kopie každé události v účtu služby Azure Storage. Počáteční kopie má události seřazené podle času ingestování a je vždycky zachovaná, takže k ní můžete použít jiné služby. Pro zpracování nezpracovaných souborů Parquet můžete použít Spark, Hadoop a další známé nástroje. 

Time Series Insights Preview přerozdělení souborů Parquet na optimalizaci pro Time Series Insights dotaz. Tato kopie dat se také ukládá do oddílů.

Během veřejné verze Preview se data ukládají na účet služby Azure Storage po neomezenou dobu.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Zápis a úpravy objektů BLOB Time Series Insights

Aby se zajistil výkon dotazů a dostupnost dat, neupravujte ani neodstraňujte žádné objekty blob, které Time Series Insights vytváření Preview.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Přístup k datům z Time Series Insights Preview a jejich export

Můžete chtít získat přístup k datům zobrazeným v Průzkumníkovi služby Time Series Insights Preview pro použití ve spojení s jinými službami. Data můžete například použít k sestavení sestavy v Power BI nebo ke školení modelu strojového učení pomocí Azure Machine Learning Studio. Nebo můžete data použít k transformaci, vizualizaci a modelování v Jupyter poznámkových blocích.

K datům můžete získat přístup třemi obecnými způsoby:

* Z Průzkumníka služby Time Series Insights Preview. Data můžete z Průzkumníka exportovat jako soubor CSV. Další informace najdete v tématu [Time Series Insights Průzkumníku Preview](./time-series-insights-update-explorer.md).
* Z rozhraní API služby Time Series Insights Preview. Ke koncovému bodu rozhraní API se můžete dostat na `/getRecorded`. Další informace o tomto rozhraní API najdete v tématu [dotazování časových řad](./time-series-insights-update-tsq.md).
* Přímo z účtu úložiště Azure. Potřebujete přístup pro čtení k libovolnému účtu, který používáte pro přístup k datům ve verzi Preview Time Series Insights. Další informace najdete v tématu [Správa přístupu k prostředkům účtu úložiště](../storage/blobs/storage-manage-access-to-resources.md).

### <a name="data-deletion"></a>Odstranění dat

Neodstraňujte soubory ve verzi Preview Time Series Insights. Související data byste měli spravovat jenom z Time Series Insights jenom ve verzi Preview.

## <a name="parquet-file-format-and-folder-structure"></a>Formát souboru a struktura složek Parquet

Parquet je open source formát sloupcového souboru, který byl navržen pro efektivní úložiště a výkon. Time Series Insights Preview v těchto případech používá Parquet. Rozdělí data podle ID časové řady pro výkon dotazů ve velkém měřítku.  

Další informace o typu souboru Parquet naleznete v [dokumentaci Parquet](https://parquet.apache.org/documentation/latest/).

Time Series Insights Preview ukládá kopie vašich dat následujícím způsobem:

* První, počáteční kopie je dělená časem ingestování a ukládá data přibližně v pořadí doručení. Data se nachází ve složce `PT=Time`:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Druhá, znovu rozdělená kopie je rozdělená na oddíly s ID časových řad a nachází se ve složce `PT=TsId`:

  `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

V obou případech časové hodnoty odpovídají času vytvoření objektu BLOB. Data ve složce `PT=Time` jsou zachována. Data ve složce `PT=TsId` budou optimalizována pro dotazy v průběhu času a nebudou zůstat statická.

> [!NOTE]
> * `<YYYY>` se mapuje na vyjádření čtyřmístného roku.
> * `<MM>` se mapuje na dvouciferné měsíční vyjádření.
> * `<YYYYMMDDHHMMSSfff>` se mapuje na vyjádření časového razítka se čtyřmi číslicemi (`YYYY`), dvoumístný měsíc (`MM`), dvěma číslicemi (`DD`), dvěma číslicemi (`HH`), dvěma číslicemi (`MM`), dvěma číslicemi (`SS`) a třemi číslicemi milisekund (`fff`).

Události ve verzi Preview Time Series Insights jsou namapovány na obsah souboru Parquet následujícím způsobem:

* Každá událost je mapována na jeden řádek.
* Každý řádek obsahuje sloupec **časového razítka** s časovým razítkem události. Vlastnost časového razítka není nikdy null. Ve výchozím nastavení je to čas zařazený do **fronty** , pokud není ve zdroji událostí zadána vlastnost časového razítka. Časové razítko je vždy v UTC.
* Každý řádek obsahuje sloupec ID časové řady definovaný při vytvoření prostředí Time Series Insights. Název vlastnosti zahrnuje příponu `_string`.
* Všechny ostatní vlastnosti odeslané jako data telemetrie jsou namapovány na názvy sloupců, které končí na `_string` (String), `_bool` (Boolean), `_datetime` (DateTime) nebo `_double` (Double) v závislosti na typu vlastnosti.
* Toto schéma mapování platí pro první verzi formátu souboru, na kterou se odkazuje jako **V = 1**. Jak se tato funkce vyvíjí, může se tento název zvýšit.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [Azure Time Series Insights a příchozí úložiště ve verzi Preview](./time-series-insights-update-storage-ingress.md).

- Přečtěte si o nových [datových modelování](./time-series-insights-update-tsm.md).
