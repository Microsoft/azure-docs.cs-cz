---
title: Přehled úložiště – Azure Time Series Insights Gen2 | Microsoft Docs
description: Seznamte se s úložištěm dat v Azure Time Series Insights Gen2.
author: esung22
ms.author: elsung
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/31/2020
ms.custom: seodec18
ms.openlocfilehash: c05de0462dde2b09e0e01919dfc691a85df153fa
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89483265"
---
# <a name="data-storage"></a>Úložiště dat

Když vytvoříte prostředí Azure Time Series Insights Gen2, vytvoříte dva prostředky Azure:

* Prostředí Azure Time Series Insights Gen2, které se dá nakonfigurovat pro úložiště dat s teplou.
* Účet Azure Storage pro úložiště studených dat.

Data v teplém úložišti jsou dostupná jenom prostřednictvím [rozhraní API pro dotazy Time Series](./time-series-insights-update-tsq.md) a [Azure Time Series Insights Exploreru](./time-series-insights-update-explorer.md). Vaše teplé úložiště bude obsahovat poslední data v rámci [doby uchování](./time-series-insights-update-plan.md#the-preview-environment) vybrané při vytváření prostředí Azure Time Series Insights Gen2.

Azure Time Series Insights Gen2 ukládá data z chladírny do úložiště objektů BLOB v Azure ve [formátu souboru Parquet](#parquet-file-format-and-folder-structure). Azure Time Series Insights Gen2 spravuje tato data studeného úložiště výhradně, ale je k dispozici pro čtení přímo jako standardních souborů Parquet.

> [!WARNING]
> Jako vlastník účtu služby Azure Blob Storage, ve kterém jsou uložená data, máte plný přístup ke všem datům v účtu. Tento přístup zahrnuje oprávnění k zápisu a odstraňování. Neupravujte ani neodstraňujte data, která Azure Time Series Insights Gen2 zápisy, protože to může způsobit ztrátu dat.

## <a name="data-availability"></a>Dostupnost dat

Azure Time Series Insights Gen2 oddíly a data indexů pro optimální výkon dotazů. Data budou k dispozici pro dotazy z teplého (Pokud povoleného) a studeného úložiště po jeho indexování. Množství dat, která se ingestují, můžou ovlivnit tuto dostupnost.

> [!IMPORTANT]
> Až budou data k dispozici, může docházet k období až 60 sekund. Pokud se setkáte s významnou latencí delší než 60 sekund, odešlete prosím lístek podpory prostřednictvím Azure Portal.

## <a name="azure-storage"></a>Azure Storage

Tato část popisuje Azure Storage detailů, které jsou relevantní pro Azure Time Series Insights Gen2.

Podrobný popis úložiště objektů BLOB v Azure najdete v [úvodu do objektů BLOB úložiště](../storage/blobs/storage-blobs-introduction.md).

### <a name="your-storage-account"></a>Váš účet úložiště

Když vytvoříte prostředí Azure Time Series Insights Gen2, vytvoří se účet Azure Storage jako dlouhodobý chladírenský sklad.  

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
