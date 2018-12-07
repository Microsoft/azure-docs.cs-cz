---
title: Datové úložiště a příchozího přenosu dat v Azure Time Series Insights (Preview) | Dokumentace Microsoftu
description: Principy datové úložiště a příchozího přenosu dat v Azure Time Series Insights (Preview)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/05/2018
ms.openlocfilehash: e2440f6aa32710730e8b015bef1e7c583f7063e2
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001867"
---
# <a name="data-storage-and-ingress-in-the-azure-time-series-insights-preview"></a>Datové úložiště a příchozího přenosu dat v Azure Time Series Insights (Preview)

Tento článek popisuje změny úložiště dat a příchozího přenosu dat z Azure Time Series Insights (TSI) ve verzi Preview. Zahrnuje základní strukturu úložiště, formát souboru a **ID řady času** vlastnost. Také popisuje proces základního příchozího přenosu dat, propustnosti a omezení.

## <a name="data-storage"></a>Úložiště dat

Při vytváření Azure TSI Preview (**PAYG SKU**) při vytváření prostředí, dva prostředky:

* Prostředí Azure TSI.
* Účet služby Azure Storage pro obecné účely V1 ukládat data.

TSI (Preview) používá úložiště objektů Blob v Azure s typem souboru Parquet. Azure TSI spravuje všechny datové operace, včetně vytváření objektů BLOB, indexování a dělení dat v účtu Azure Storage. Tyto objekty BLOB jsou vytvořené pomocí účtu služby Azure Storage.

Stejně jako ostatní služby Azure Storage blob může číst a zapisovat do objektů BLOB Azure TSI vytvořené pro podporu různých integrační scénáře.

> [!TIP]
> TSI výkon může negativně ovlivněn čtením nebo zápisem k objektům BLOB příliš často.

Přehled služby Azure Blob Storage najdete v článku [úložiště objektů BLOB ÚVOD](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

Další informace o typu souboru Parquet, naleznete v tématu [podporované typy souborů ve službě Azure Storage](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#Parquet-format).

## <a name="parquet-file-format"></a>Formát souborů parquet

Parquet je sloupcově orientovaná, dat, formát souboru, který je navržená pro:

* Vzájemná funkční spolupráce
* Efektivity místa
* Účinnost dotazu

Azure TSI zvolili Parquet, protože poskytuje efektivní datové komprese a schémata kódování s Vylepšený výkon pro zpracování komplexních datových hromadně.

Abyste získali lepší představu o Parquet formát souboru se točí kolem, přečtěte si [oficiální dokumentaci Parquet](https://parquet.apache.org/documentation/latest/).

## <a name="event-structure-in-parquet"></a>Struktura události v Parquet

Objekty BLOB vytvořené pomocí Azure TSI dvě kopie se uloží v následujících formátech:

1. První, počáteční kopie se dělené podle čas doručení:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Čas vytvoření objektu BLOB pro objekty BLOB dělené podle čas doručení.

1. Druhá repartitioned kopírování se dělené podle dynamické seskupení **ID řady času**:

    * `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`
    * Časové razítko minimální události do objektu BLOB pro objekty BLOB dělené podle **ID řady času**.

> [!NOTE]
> * `<YYYY>` mapuje se na reprezentaci rok 4 číslice.
> * `<MM>` mapuje se na reprezentaci číslice 2 měsíce.
> * `<YYYYMMDDHHMMSSfff>` mapuje na reprezentaci časové razítko s 4 dvoumístného čísla roku (`YYYY`), číslice 2 měsíce (`MM`), den 2 číslice (`DD`), číslice 2 hodiny (`HH`), číslice 2 minuty (`MM`), číslice 2 sekundy (`SS`) a 3 číslice milisekundy (`fff`).

Azure TSI události jsou mapovány do obsahu souboru Parquet následujícím způsobem:

* Každou událost mapuje na jeden řádek.
* Integrované **časové razítko** sloupec s časovým razítkem události. **Časové razítko** vlastnost nikdy má hodnotu null.  Použije se výchozí **čas zařazení do fronty zdroj událostí** Pokud **časové razítko** vlastnost není zadaný ve zdroji události.  **Časové razítko** je ve formátu UTC.  
* Všechny ostatní vlastnosti mapování sloupců bude končit `_string` (řetězec), `_bool` (logická hodnota), `_datetime` (datetime), a `_double` (double) v závislosti na typu vlastnosti.
* To je první verze formátu souboru a označujeme je jako **V = 1**.  Pokud tuto funkci vyvíjí názvu se zvýší odpovídajícím způsobem na **V = 2**, **V = 3**, a tak dále.

## <a name="partitions"></a>Oddíly

Každé prostředí Azure TSI (Preview) musí mít **ID řady času** vlastnost a **časové razítko** vlastnost, která ji pak jednoznačně identifikuje. Vaše **ID řady času** funguje jako logický oddíl pro vaše data a poskytuje prostředí Azure TSI (Preview) nabízí přirozené hranice pro distribuci dat napříč fyzickými oddíly. Fyzický oddíl správy se spravuje přes Azure TSI (Preview) v účtu služby Azure Storage.

Azure TSI používá k optimalizaci úložiště využití a výkonu dotazování umožňujícímu odstranění a opětovné vytvoření oddílů dynamické dělení na oddíly. Azure TSI (Preview), dynamické dělení algoritmus pokusí zabránit jeden fyzický oddíl obsahující data pro několik různých logické oddíly. Jinými slovy, dělení algoritmus uchovává všechna data konkrétního do jediné **ID řady času** výhradně součástí soubory Parquet bez se prokládané s jinými **čas řady ID**. Algoritmus dynamického dělení taky automatický pokus o zachovat původní pořadí událostí v rámci jediného **ID řady času**.

V době příchozího přenosu dat na začátku data jsou rozdělená podle **časové razítko** tak v daném časovém rozsahu jednoho logického oddílu může rozdělit mezi několik fyzických oddílů. Mnoho nebo všechny logické oddíly mohou obsahovat také jeden fyzický oddíl.  Kvůli omezení velikosti objektu blob i s optimální dělení jeden logický oddíl může zabírat více fyzických oddílů.

> [!NOTE]
> **Časové razítko** hodnota je zpráva **čas zařazení do fronty** ve zdroji událostí nakonfigurované ve výchozím nastavení.  

Pokud ukládáte historická data nebo zprávy dávkových, můžete určit **časové razítko** vlastnost ve vašich datech, která se mapuje na příslušnou **časové razítko** hodnoty, které chcete uložit s vašimi daty.  **Časové razítko** vlastnost je velká a malá písmena. Další informace, přečtěte si [modelu časové řady článku](./time-series-insights-update-tsm.md).

## <a name="physical-partitions"></a>Fyzické oddíly

Fyzický oddíl je objekt blob bloku, které jsou uložené ve službě Azure Storage. Skutečná velikost objektů BLOB budou lišit v závislosti na závisí na frekvenci nabízených oznámení, je však objekty BLOB byly velikost přibližně 20 – 50 MB. Z důvodu očekávání vybraný tým TSI 20 MB na velikost k optimalizaci výkonu dotazů. To může mění v čase na základě velikosti souborů a rychlost příchozího přenosu dat.

> [!NOTE]
> * Objekty BLOB je nastavena na 20MB.
> * Objekty BLOB Azure jsou někdy rozdělovat pro lepší výkon díky vyřadit a vytvořit znovu.
> * Všimněte si také, že stejná TSI data může být k dispozici v několika objektů BLOB.

## <a name="logical-partitions"></a>Logické oddíly

Logický oddíl je oddíl v rámci fyzický oddíl, který ukládá všechna data přidružená k jednu hodnotu klíče oddílu. TSI (Preview) se logicky oddílu každý objekt blob na základě dvou vlastností:

1. **Time Series ID** -klíčem oddílu pro všechna data TSI v rámci datového proudu událostí a modelu.
1. **Časové razítko** – podle počáteční příchozího přenosu.

TSI Azure (Preview) poskytuje výkonné dotazy, které jsou založeny na tyto dvě vlastnosti. Tyto dvě vlastnosti také poskytují nejúčinnější způsob pro rychlé doručování TSI data.

Je důležité vybrat odpovídající **ID řady času**, jako je neměnné vlastnosti.  Zobrazit [výběr času řady ID](./time-series-insights-update-how-to-id.md) Další informace.

## <a name="your-azure-storage-account"></a>Váš účet Azure Storage

### <a name="storage"></a>Úložiště

Když vytvoříte TSI **PAYG** prostředí, vytvořte dva prostředky – prostředí TSI a účtu služby Azure storage pro obecné účely V1 ukládat data. Jsme se rozhodli použít jako výchozí z důvodu jeho vzájemná funkční spolupráce, ceny a výkonu služby Azure Storage pro obecné účely V1.  

Azure TSI publikuje až dvě kopie každé události ve vašem účtu úložiště Azure. Počáteční kopie se vždy zachovají zajistit, že se můžete dotazovat performantly pomocí jiných služeb. To znamená, Spark, Hadoop nebo jiných známých nástrojů můžete snadno použít napříč **čas řady ID** přes nezpracovaná Parquet souboru soubory, protože tyto moduly, které podporují základní filtrování název. Seskupení objektů BLOB podle roku a měsíce je užitečné shromáždit seznam objektů blob v rámci konkrétní časové období pro vlastní úlohu.  

Kromě toho TSI bude oddíly soubory Parquet optimalizovat pro rozhraní API služby Azure TSI a nedávno repartitioned soubor bude také uloženo.

Ve verzi Public Preview se uloží data po neomezenou dobu ve vašem účtu úložiště Azure.

### <a name="writing-and-editing-time-series-insights-blobs"></a>Psaní a úpravy objektů BLOB Time Series Insights

K zajištění výkonu dotazů a dostupnosti dat, upravit ani odstranit všechny objekty BLOB vytvořené TSI.

### <a name="accessing-and-exporting-data-from-time-series-insights-preview"></a>Přístup k a export dat ze služby Time Series Insights (Preview)

Můžete chtít přístup k datům uloženým v Průzkumníku TSI Azure (Preview) pro použití ve spojení s jinými službami. Můžete například používat vaše data pro generování sestav v Power BI pro strojové učení pomocí Azure Machine Learning Studio nebo v aplikaci Poznámkový blok Jupyter Notebooks atd.

Existují tři hlavní cesty pro přístup k datům:

* V Průzkumníku TSI Azure (Preview).
* Rozhraní API Azure TSI (Preview).
* Přímo z účtu služby Azure Storage.

### <a name="from-the-time-series-insights-preview-explorer"></a>V Průzkumníku Time Series Insights (Preview)

Data můžete exportovat jako soubor CSV v Průzkumníku TSI (Preview). Další informace najdete [v Průzkumníku TSI (Preview)](./time-series-insights-update-explorer.md).

### <a name="from-the-time-series-insights-preview-apis"></a>Z rozhraní API čas Series Insights (Preview)

Koncový bod rozhraní API se dá kontaktovat na `/getRecorded`. Další informace o tomto rozhraní API, přečtěte si informace o [čas řady dotazů](./time-series-insights-update-tsq.md).

### <a name="from-an-azure-storage-account"></a>Z účtu služby Azure Storage

1. Musíte mít oprávnění ke čtení udělena na jakýkoli účet budete používat pro přístup k datům služby TSI. Další informace o udělení oprávnění ke čtení do Azure Blob Storage, [Správa přístupu k prostředkům úložiště](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources).

1. Další informace o přímé způsoby, jak přečíst data z úložiště objektů Blob v Azure najdete v článku [přesun dat do a z Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

1. Export dat z účtu služby Azure Storage:

    * Nejprve zkontrolujte, zda že má váš účet nezbytné požadavky splněny exportovat data. Čtení [úložiště import a export požadavky](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements) Další informace.
    * Další informace o dalších způsobech exportovat data z vašeho účtu úložiště Azure návštěvou [úložiště import a export dat z objektů BLOB](https://docs.microsoft.com/azure/storage/common/storage-import-export-data-from-blobs)

### <a name="data-deletion"></a>Odstranění dat

Objekty BLOB neodstraňujte, protože Azure TSI (Preview) uchovává metadata o objektech BLOB v rámci služby TSI aktualizace.

## <a name="ingress"></a>Příchozí přenos dat

### <a name="azure-time-series-insights-ingress-policies"></a>Zásady Azure příchozího přenosu dat Time Series Insights

TSI Azure (Preview) podporuje stejné zdroje událostí a typy souborů, které dnes.

Zdroje událostí podporovaných patří:

- Azure IoT Hub
- Azure Event Hubs
  
  > [!NOTE]
  > Azure Event Hub instance podporují Kafka.

Podporované typy souborů patří:

* JSON: Na podporované tvary JSON jsme dokáže zpracovat víc, najdete v článku [jak tvaru JSON](./time-series-insights-send-events.md#json) dokumentaci.

### <a name="data-availability"></a>Dostupnost dat

Azure TSI (Preview) ve verzi public preview, indexuje data použití strategie optimalizace velikost objektu blob. To znamená, že data budou k dispozici pro dotazy, jakmile se indexují (která je založená na tom, kolik dat se chystá v a na jaké rychlost).

> [!IMPORTANT]
> * TSI všeobecné dostupnosti bude zpřístupnění dat během 60 sekund jeho dosažení zdroje událostí.  
> * Během období preview Očekáváme, že zobrazíte delší období před opakovaným dat je k dispozici.  
>   * Pokud dochází k žádné významné latenci, kontaktujte nás.

### <a name="scale"></a>Měřítko

Azure TSI (Preview) podporuje měřítku počáteční příchozí přenos dat až 6 MB/s pro každé prostředí. Probíhá škálování rozšířenou podporu. Dokumentace ke službě se aktualizují tak, aby odrážely těmito vylepšeními.

## <a name="next-steps"></a>Další postup

Přečtěte si [Azure TSI (Preview) úložiště a příchozího přenosu dat](./time-series-insights-update-storage-ingress.md).

Přečtěte si informace o novém [modelování dat](./time-series-insights-update-tsm.md).

<!-- Images -->
[1]: media/v2-update-storage-ingress/storage-architecture.png
[2]: media/v2-update-storage-ingress/parquet-files.png
[3]: media/v2-update-storage-ingress/blob-storage.png
