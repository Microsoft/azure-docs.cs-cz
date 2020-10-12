---
title: Přehled úložiště – Azure Time Series Insights Gen2 | Microsoft Docs
description: Seznamte se s úložištěm dat v Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/28/2020
ms.custom: seodec18
ms.openlocfilehash: b186c2d2c4b5efc8e1e052a63505549e860b5619
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91460824"
---
# <a name="data-storage"></a>Úložiště dat

Tento článek popisuje úložiště dat v Azure Time Series Insights Gen2. Zabývá se teplou a studenou, dostupností dat a osvědčenými postupy.

## <a name="provisioning"></a>Zřizování

Když vytvoříte prostředí Azure Time Series Insights Gen2, máte následující možnosti:

* Úložiště studených dat:
  * Vytvořte nový prostředek Azure Storage v předplatném a oblasti, kterou jste si zvolili pro vaše prostředí.
  * Připojte již existující účet Azure Storage. Tato možnost je k dispozici pouze při nasazení ze [šablony](https://docs.microsoft.com/azure/templates/microsoft.timeseriesinsights/allversions)Azure Resource Manager a není viditelná v Azure Portal.
* Úložiště dat s teplou:
  * Záložní úložiště je volitelné a může být povoleno nebo zakázáno během zřizování nebo po jeho spuštění. Pokud se rozhodnete povolit teplé úložiště později a data v chladírenských skladech jsou již k dispozici, [Přečtěte si následující část](concepts-storage.md#warm-store-behavior) , abyste pochopili očekávané chování. Doba uchovávání dat v úložišti teplého úložiště se dá nakonfigurovat na 7 až 31 dnů a můžete ji také upravit podle potřeby.

Při příjmu události je tato událost indexována v teplém úložišti (Pokud je povoleno) a v chladírenském úložišti.

[![Přehled služby Storage](media/concepts-storage/pipeline-to-storage.png)](media/concepts-storage/pipeline-to-storage.png#lightbox)

> [!WARNING]
> Jako vlastník účtu služby Azure Blob Storage, ve kterém jsou uložená data, máte plný přístup ke všem datům v účtu. Tento přístup zahrnuje oprávnění k zápisu a odstraňování. Neupravujte ani neodstraňujte data, která Azure Time Series Insights Gen2 zápisy, protože to může způsobit ztrátu dat.

## <a name="data-availability"></a>Dostupnost dat

Azure Time Series Insights Gen2 oddíly a data indexů pro optimální výkon dotazů. Data budou k dispozici pro dotazy z teplého (Pokud povoleného) a studeného úložiště po jeho indexování. Množství dat, která se ingestují, a četnost propustnosti pro jednotlivé oddíly můžou mít vliv na dostupnost. Zkontrolujte [omezení propustnosti](./concepts-streaming-ingress-throughput-limits.md) zdroje událostí a [osvědčené postupy](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) pro dosažení optimálního výkonu. Můžete také nakonfigurovat [Upozornění](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency#monitor-latency-and-throttling-with-alerts) na prodlevu, které bude upozorněno na to, jestli vaše prostředí má problémy zpracovávající data.

> [!IMPORTANT]
> Až budou data k dispozici, může docházet k období až 60 sekund. Pokud se setkáte s významnou latencí delší než 60 sekund, odešlete prosím lístek podpory prostřednictvím Azure Portal.

## <a name="warm-store"></a>Záložní úložiště

Data v teplém úložišti jsou k dispozici pouze prostřednictvím [rozhraní API pro dotazování časové řady](./time-series-insights-update-tsq.md), nástroje [Azure Time Series Insights Explorer](./time-series-insights-update-explorer.md)nebo [konektoru Power BI](./how-to-connect-power-bi.md). Dotazy na záložní úložiště jsou bezplatné a není k dispozici žádná kvóta, ale [limit 30](https://docs.microsoft.com/rest/api/time-series-insights/reference-api-limits#query-apis---limits) souběžných požadavků.

### <a name="warm-store-behavior"></a>Chování teplého úložiště

* Pokud je tato možnost povolená, všechna data zasílaná do vašeho prostředí se budou směrovat do vašeho úložiště s teplem bez ohledu na časové razítko události. Mějte na paměti, že kanál pro příjem dat do streamování je sestavený pro streamování téměř v reálném čase a historické události se [nepodporují](./concepts-streaming-ingestion-event-sources.md#historical-data-ingestion).
* Doba uchování se vypočítá na základě toho, kdy byla událost indexována v teplém úložišti, nikoli v časovém razítku události. To znamená, že data již nejsou v teplém úložišti k dispozici po uplynutí doby uchování, a to i v případě, že je časové razítko události pro budoucnost.
  * Příklad: událost s 10 hodinami předpověď počasí je ingestovaná a indexovaná v kontejneru teplého úložiště nakonfigurovaném s dobou uchování 7 dní. Po 7 dnech je předpověď už v záložním úložišti přístupná, ale dá se k ní dotazovat z studeného provozu.
* Pokud zapnete úložiště na stávajícím prostředí, které už má v studeném úložišti poslední data, je potřeba si uvědomit, že vaše teplé úložiště nebude s těmito daty zase vyplněné.
* Pokud jste právě povolili služby teplého úložiště a dochází k problémům s prohlížením vašich nejnovějších dat v Průzkumníkovi, můžete dočasně přepnout dotazy služby tepl Store mimo:

   [![Zakázat teplé dotazy](media/concepts-storage/toggle-warm.png)](media/concepts-storage/toggle-warm.png#lightbox)

## <a name="cold-store"></a>Chladírenský sklad

Tato část popisuje Azure Storage detailů, které jsou relevantní pro Azure Time Series Insights Gen2.

Podrobný popis úložiště objektů BLOB v Azure najdete v [úvodu do objektů BLOB úložiště](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-cold-storage-account"></a>Účet studeného úložiště

Azure Time Series Insights Gen2 zachovává až dvě kopie každé události v účtu Azure Storage. Jedna kopie ukládá události seřazené podle času příjmu, vždy umožňuje přístup k událostem v posloupnosti seřazené podle času. V průběhu času Azure Time Series Insights Gen2 také vytvoří znovu rozdělenou kopii dat, která se mají optimalizovat pro provádění dotazů.

Všechna vaše data se ve vašem účtu Azure Storage ukládají na neomezenou dobu.

#### <a name="writing-and-editing-blobs"></a>Zápis a úpravy objektů BLOB

Aby se zajistil výkon dotazů a dostupnost dat, neupravujte ani neodstraňujte žádné objekty blob, které Azure Time Series Insights Gen2 vytvoří.

#### <a name="accessing-cold-store-data"></a>Přístup k datům z chladírenského úložiště

Kromě přístupu k datům z rozhraní API [Azure Time Series Insights Exploreru](./time-series-insights-update-explorer.md) a [dotazů časových řad](./time-series-insights-update-tsq.md)můžete také chtít získat přístup k datům přímo ze souborů Parquet uložených v chladírenském úložišti. Můžete například číst, transformovat a čistit data v Jupyter poznámkovém bloku a pak je použít ke školení modelu Azure Machine Learning ve stejném pracovním postupu Spark.

Pokud chcete získat přístup k datům přímo z účtu Azure Storage, potřebujete přístup pro čtení k účtu, který se používá k ukládání dat Azure Time Series Insights Gen2. Následně si můžete přečíst vybraná data na základě doby vytvoření souboru Parquet ve `PT=Time` složce popsané níže v části [Formát souboru Parquet](#parquet-file-format-and-folder-structure) .  Další informace o povolení přístupu pro čtení k vašemu účtu úložiště najdete v tématu [Správa přístupu k prostředkům účtu úložiště](../storage/blobs/storage-manage-access-to-resources.md).

#### <a name="data-deletion"></a>Odstranění dat

Neodstraňujte soubory Gen2 Azure Time Series Insights. Spravujte související data jenom z Azure Time Series Insights jenom v Gen2.

### <a name="parquet-file-format-and-folder-structure"></a>Formát souboru a struktura složek Parquet

Parquet je open source formát sloupcového souboru navržený pro efektivní úložiště a výkon. Azure Time Series Insights Gen2 používá Parquet k povolení výkonu dotazů na základě ID časových řad.  

Další informace o typu souboru Parquet najdete v [dokumentaci k Parquet](https://parquet.apache.org/documentation/latest/).

Azure Time Series Insights Gen2 ukládá kopie vašich dat následujícím způsobem:

* První, počáteční kopie je dělená časem ingestování a ukládá data přibližně v pořadí doručení. Tato data se nachází ve `PT=Time` složce:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* Druhá, znovu rozdělená kopie je seskupená podle ID časových řad a nachází se ve `PT=TsId` složce:

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

Časové razítko v názvech objektů BLOB ve `PT=Time` složce odpovídá času příchodu dat, Azure Time Series Insights Gen2 a nikoliv k časovému razítku událostí.

Data ve `PT=TsId` složce budou optimalizována pro dotazy v průběhu času a nejsou statická. Během změny oddílů můžou být některé události přítomné ve více objektech blob. Pojmenování objektů BLOB v této složce není zaručeno, aby zůstalo stejné.

Obecně platí, že pokud potřebujete získat přístup k datům přímo pomocí souborů Parquet, použijte `PT=Time` složku.  Budoucí funkce umožní efektivní přístup ke `PT=TsId` složce.

> [!NOTE]
>
> * `<YYYY>` provede mapování na vyjádření roku se čtyřmi číslicemi.
> * `<MM>` provede mapování na vyjádření měsíčních číslic.
> * `<YYYYMMDDHHMMSSfff>` mapuje se na časovou reprezentaci se čtyřmi číslicemi (), měsíčním číslem (), dvěma číslicemi `YYYY` (), dvěma číslicemi (), dvěma číslicemi (), dvěma číslicemi `MM` `DD` () a třemi číslicemi `HH` `MM` `SS` milisekund ( `fff` ).

Azure Time Series Insights události Gen2 jsou namapovány na obsah souboru Parquet následujícím způsobem:

* Každá událost je mapována na jeden řádek.
* Každý řádek obsahuje sloupec **časového razítka** s časovým razítkem události. Vlastnost časového razítka není nikdy null. Ve výchozím nastavení je **čas** zařazený do fronty, pokud není ve zdroji událostí zadaná vlastnost časového razítka. Uložené časové razítko je vždy ve formátu UTC.
* Každý řádek obsahuje sloupce s ID časové řady (TSID), jak je definováno při vytváření prostředí Azure Time Series Insights Gen2. Název vlastnosti TSID zahrnuje `_string` příponu.
* Všechny ostatní vlastnosti odeslané jako data telemetrie jsou mapovány na názvy sloupců, které končí `_bool` (Boolean), `_datetime` (časové razítko), `_long` (Long), ( `_double` Double), `_string` (řetězec) nebo `dynamic` (dynamické), v závislosti na typu vlastnosti.  Další informace najdete v článku o [podporovaných datových typech](./concepts-supported-data-types.md).
* Toto mapování schématu se vztahuje na první verzi formátu souboru, na kterou odkazuje **v = 1** a je uloženo v základní složce se stejným názvem. Vzhledem k tomu, že se tato funkce vyvíjí, může se toto mapování schématu změnit a zvýší se název odkazu.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o [modelování dat](./time-series-insights-update-tsm.md).

* Plánování [Azure Time Series Insightsho prostředí Gen2](./time-series-insights-update-plan.md).
