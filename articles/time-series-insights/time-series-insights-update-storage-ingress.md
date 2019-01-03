---
title: Datové úložiště a příchozího přenosu dat v Azure čas Series Insights ve verzi Preview | Dokumentace Microsoftu
description: Ke zjištění datového úložiště a příchozího přenosu dat v Azure čas Series Insights ve verzi Preview.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/05/2018
ms.custom: seodec18
ms.openlocfilehash: f0326e6f05f44b5f2c4416a1c1db09bc5816a297
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558307"
---
# <a name="data-storage-and-ingress-in-azure-time-series-insights-preview"></a>Datové úložiště a příchozího přenosu dat v Azure čas Series Insights ve verzi Preview

Tento článek popisuje změny úložiště dat a příchozího přenosu dat z Azure čas Series Insights ve verzi Preview. Zahrnuje základní strukturu úložiště, formátu a vlastnost ID řady času. Tento článek také popisuje proces základního příchozího přenosu dat, propustnosti a omezení.

## <a name="data-storage"></a>Úložiště dat

Při vytváření prostředí s průběžnými platbami SKU čas Series Insights ve verzi Preview, kterou vytváříte dva prostředky:

* Prostředí Time Series Insights.
* Účet služby Azure Storage pro obecné účely V1 ukládat data.

Čas Series Insights ve verzi Preview využívá úložiště objektů Blob v Azure s typem souboru Parquet. Time Series Insights spravuje všechny datové operace, včetně vytváření objektů BLOB, indexování a dělení dat v účtu úložiště Azure. Vytvoření těchto objektů BLOB pomocí účtu služby Azure storage.

Stejně jako ostatní objekty BLOB služby Azure Storage objekty BLOB vytvořené Time Series Insights vám umožní číst a zapisovat do nich podporují různé scénáře integrace.

> [!TIP]
> Čas Series Insights výkon může negativně ovlivněn, pokud číst nebo zapisovat do objektů BLOB příliš často.

Přehled služby Azure Blob storage, naleznete v tématu [úložiště objektů BLOB ÚVOD](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

Další informace o typu souboru Parquet, naleznete v tématu [podporované typy souborů ve službě Azure Storage](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#Parquet-format).

## <a name="parquet-file-format"></a>Formát souborů parquet

Parquet je sloupcově orientovaná, data formátu, který je navržená pro:

* Vzájemná funkční spolupráce
* Efektivity místa
* Účinnost dotazu

Time Series Insights zvolili Parquet, protože poskytuje efektivní datové komprese a kódování schémata, s Vylepšený výkon, který dokáže zpracovat komplexní data hromadně.

Pro lepší přehled formát souborů Parquet, naleznete v tématu [Parquet dokumentaci](https://parquet.apache.org/documentation/latest/).

## <a name="event-structure-in-parquet"></a>Struktura události v Parquet

Time Series Insights vytvoří a uloží kopie objektů BLOB v následujících dvou formátů:

1. První, počáteční kopie je rozdělený podle čas doručení:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Čas vytvoření objektu BLOB pro objekty BLOB dělené podle čas doručení.

1. Druhý, rozdělovat kopírování je rozdělený pomocí dynamické seskupení řady ID čas:

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Časové razítko minimální události do objektu BLOB pro objekty BLOB dělené podle ID čas řady.

> [!NOTE]
> * `<YYYY>` mapuje se na reprezentaci rok 4 číslice.
> * `<MM>` mapuje se na reprezentaci číslice 2 měsíce.
> * `<YYYYMMDDHHMMSSfff>` mapuje na reprezentaci časové razítko s 4 dvoumístného čísla roku (`YYYY`), číslice 2 měsíce (`MM`), den 2 číslice (`DD`), číslice 2 hodiny (`HH`), číslice 2 minuty (`MM`), číslice 2 sekundy (`SS`) a 3 číslice milisekundy (`fff`).

Čas události Series Insights jsou mapovány do obsahu souboru Parquet následujícím způsobem:

* Každá událost mapuje na jeden řádek.
* Integrované **časové razítko** sloupec s časovým razítkem události. Vlastnost časového razítka není nikdy hodnotu null. Použije se výchozí **čas zařazení do fronty zdroj událostí** Pokud nezadáte vlastnost časového razítka zdroje událostí. Časové razítko je ve formátu UTC. 
* Všechny ostatní vlastnosti, které jsou mapované na sloupce končit `_string` (řetězec), `_bool` (logická hodnota), `_datetime` (datetime), a `_double` (double), v závislosti na typu vlastnosti.
* To je mapování schématu pro první verzi formátu souborů, které označujeme jako **V = 1**. Jak tuto funkci vyvíjí, názvu se zvýší na **V = 2**, **V = 3**, a tak dále.

## <a name="partitions"></a>Oddíly

Každý čas Series Insights ve verzi Preview prostředí musí mít vlastnost ID řady čas a vlastnost časového razítka, která ji pak jednoznačně identifikuje. Vaše ID řady čas funguje jako logický oddíl pro vaše data a poskytuje prostředí čas Series Insights ve verzi Preview přirozené hranice pro distribuci dat napříč fyzickými oddíly. Čas Series Insights ve verzi Preview v účtu služby Azure storage spravuje fyzický oddíl správy.

Time Series Insights používá dynamické dělení na oddíly pro optimalizaci úložiště a výkonu dotazování umožňujícímu vyřadit a znovu vytvořte oddíly. Čas Series Insights ve verzi Preview dynamického dělení algoritmu pokusí jeden fyzický oddíl zabránili data pro několik různých logické oddíly. Jinými slovy dělení algoritmus uchovává všechna data konkrétní jeden čas řady ID výhradně součástí soubory Parquet bez se proloženy jiné ID řady čas. Algoritmus dynamického dělení se rovněž snaží zachovat původní pořadí událostí v rámci jedné řady čas ID.

Na začátku během příchozího přenosu dat, data jsou rozdělená podle časového razítka tak, aby v daném časovém rozsahu jednoho logického oddílu je možné rozdělit do více fyzických oddílů. Jeden fyzický oddíl může obsahovat také mnoho nebo všechny logické oddíly. Kvůli omezení velikosti objektu blob, i s optimální dělení jednoho logického oddílu může zabírat více fyzických oddílů.

> [!NOTE]
> Výchozí hodnota časového razítka je zpráva *čas zařazení do fronty* ve zdroji nakonfigurovanou událost. 

Pokud jste nahrávání historická data nebo zprávy dávkových, přiřadíte hodnotu, kterou chcete uložit se svými daty a vlastnost časového razítka, která se mapuje na příslušnou časové razítko. Vlastnost časového razítka je velká a malá písmena. Další informace najdete v tématu [modelu časové řady](./time-series-insights-update-tsm.md).

## <a name="physical-partitions"></a>Fyzické oddíly

Fyzický oddíl je objekt blob bloku, který je uložen ve vašem účtu úložiště. Skutečná velikost objektů blob se může lišit, protože závisí na frekvenci nabízených oznámení. Předpokládáme, přes bloby až po mít velikost přibližně 20 MB až 50 MB. Tento očekávání vedla tým Time Series Insights, aby jako velikost k optimalizaci výkonu dotazů vyberte 20 MB. Tato velikost může v průběhu času, v závislosti na velikosti souboru a rychlost příchozího přenosu dat měnit.

> [!NOTE]
> * Objekty BLOB je nastavena na 20 MB.
> * Objekty BLOB Azure se příležitostně rozdělovat pro lepší výkon díky vyřadit a znovu vytvořit.
> * Stejná data služby Time Series Insights také může být k dispozici ve dvou nebo více objektů BLOB.

## <a name="logical-partitions"></a>Logické oddíly

Logický oddíl je oddíl v rámci fyzický oddíl, který ukládá všechna data přidružená k jednu hodnotu klíče oddílu. Čas Series Insights ve verzi Preview logicky oddíly každý objekt blob na základě dvou vlastností:

* **Time Series ID**: Klíč oddílu pro všechna data služby Time Series Insights v rámci datového proudu událostí a modelu.
* **Časové razítko**: Čas podle počáteční příchozího přenosu.

Čas Series Insights ve verzi Preview nabízí výkonné dotazy, které jsou založeny na tyto dvě vlastnosti. Tyto dvě vlastnosti také poskytují nejúčinnější způsob pro rychlé doručování dat Time Series Insights.

Je důležité vybrat příslušné ID řady času, protože je neměnné vlastnosti. Další informace najdete v tématu [zvolte čas řady ID](./time-series-insights-update-how-to-id.md).

## <a name="your-azure-storage-account"></a>Váš účet úložiště Azure

### <a name="storage"></a>Storage

Když vytvoříte s průběžnými platbami prostředí Time Series Insights, můžete vytvořit dva prostředky: prostředí Time Series Insights a účet služby Azure Storage pro obecné účely V1 ukládat data. Rozhodli jsme se provést výchozí prostředek služby Azure Storage pro obecné účely V1 z důvodu jeho vzájemná funkční spolupráce, ceny a výkonu. 

Time Series Insights publikuje až dvě kopie každé události ve vašem účtu úložiště Azure. Počáteční kopie se vždy zachovají tak, aby vám ho dotazovat performantly pomocí dalších služeb. Snadno můžete Spark, Hadoop a další dobře známých nástrojů v čase řady ID nezpracované soubory Parquet, protože tyto moduly podporují filtrování základní název souboru. Seskupení objektů BLOB podle roku a měsíce je užitečný způsob, jak výpis objektů BLOB v rámci konkrétní časové období pro vlastní úlohu. 

Kromě toho Time Series Insights repartitions soubory Parquet optimalizovat pro rozhraní API, doba Series Insights. Nedávno repartitioned souboru se také uloží.

Ve verzi public Preview se data uložená v účtu služby Azure storage po neomezenou dobu.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Psaní a úpravy objektů BLOB Time Series Insights

K zajištění výkonu dotazů a dostupnosti dat, není upravit nebo odstranit všechny objekty BLOB, které jsou vytvořeny pomocí služby Time Series Insights.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Přístup k a export dat z času Series Insights ve verzi Preview

Můžete chtít přístup k datům uloženým v Průzkumníku čas Series Insights ve verzi Preview použít ve spojení s jinými službami. Můžete například chtít používat vaše data do sestavy v Power BI pro strojové učení pomocí Azure Machine Learning Studio, nebo použít v aplikaci Poznámkový blok s poznámkovými bloky Jupyter.

Přistupovat k datům v tři hlavní způsoby:

* Z Průzkumníka čas Series Insights ve verzi Preview.
* Na rozhraní API čas Series Insights ve verzi Preview.
* Přímo z účtu služby Azure storage.

#### <a name="from-the-time-series-insights-preview-explorer"></a>Z Průzkumníka čas Series Insights ve verzi Preview

Data můžete exportovat jako soubor CSV z Průzkumníka čas Series Insights ve verzi Preview. Další informace najdete v tématu [čas Series Insights ve verzi Preview explorer](./time-series-insights-update-explorer.md).

#### <a name="from-the-time-series-insights-preview-apis"></a>Na rozhraní API čas Series Insights ve verzi Preview

Koncový bod rozhraní API se dá kontaktovat na `/getRecorded`. Další informace o tomto rozhraní API najdete v tématu [čas řady dotazů](./time-series-insights-update-tsq.md).

#### <a name="from-an-azure-storage-account"></a>Z účtu služby Azure storage

* Potřebujete přístup pro čtení k jakýkoli účet, který používáte pro přístup k datům služby Time Series Insights. Další informace najdete v tématu [spravovat přístup k vašim prostředkům účtu úložiště](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources).

* Další informace o přímé způsoby, jak přečíst data z úložiště objektů Blob v Azure najdete v tématu [přesun dat do a z vašeho účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

* Exportovat data z účtu služby Azure storage:

    * Nejprve se ujistěte, že váš účet splňuje nezbytné požadavky pro export dat. Další informace najdete v tématu [úložiště import a export požadavky](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements).

    * Další informace o dalších způsobech na export dat z účtu služby Azure storage, najdete v článku [Import a export dat z objektů blob](https://docs.microsoft.com/azure/storage/common/storage-import-export-data-from-blobs).

### <a name="data-deletion"></a>Odstranění dat

Neodstraňovat objekty BLOB, protože čas Series Insights ve verzi Preview uchovává metadata o objektech BLOB v něm.

## <a name="ingress"></a>Příchozí přenos dat

### <a name="time-series-insights-ingress-policies"></a>Čas Series Insights příchozího přenosu dat zásad

Čas Series Insights ve verzi Preview podporuje stejné zdroje událostí a typy souborů, které v současné době podporuje Time Series Insights.

Zdroje událostí podporovaných patří:

- Azure IoT Hub
- Azure Event Hubs
  
  > [!NOTE]
  > Azure Event Hub instance podporují Kafka.

Podporované typy souborů patří:

* JSON: Další informace o podporované tvary JSON jsme dokáže zpracovat, naleznete v tématu [jak tvaru JSON](./time-series-insights-send-events.md#json).

### <a name="data-availability"></a>Dostupnost dat

Čas Series Insights ve verzi Preview indexuje data s použitím strategie optimalizace velikost objektu blob. Zpřístupní data pro dotazování po se indexují, která je založena na tom, kolik dat se chystá v a na jaké rychlosti.

> [!IMPORTANT]
> * Time Series Insights verzi všeobecné dostupnosti (GA) se data zpřístupnit v rámci dosažení zdroje událostí 60 sekund. 
> * Ve verzi preview můžete očekávejte delší období před opakovaným dat je k dispozici. 
> * Pokud dochází k žádné významné latenci, ujistěte se, že jste nás kontaktovat.

### <a name="scale"></a>Měřítko

Čas Series Insights ve verzi Preview podporuje měřítku počáteční příchozí přenos dat až 6 megabity za sekundu (MB/s) pro každé prostředí. Probíhá škálování rozšířenou podporu. Plánujeme aktualizovat tak, aby odrážely těchto vylepšení naší dokumentaci.

## <a name="next-steps"></a>Další postup

Přečtěte si [úložiště a příchozího přenosu dat ve verzi Preview služby Azure Time Series Insights](./time-series-insights-update-storage-ingress.md).

Přečtěte si informace o novém [modelování dat](./time-series-insights-update-tsm.md).

<!-- Images -->
[1]: media/v2-update-storage-ingress/storage-architecture.png
[2]: media/v2-update-storage-ingress/parquet-files.png
[3]: media/v2-update-storage-ingress/blob-storage.png
