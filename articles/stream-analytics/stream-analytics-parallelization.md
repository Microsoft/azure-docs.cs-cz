---
title: Použít dotaz paralelizace a škálování v Azure Stream Analytics
description: Tento článek popisuje postup konfigurace vstupní oddíly, ladění definice dotazu a nastavení úlohu streamování jednotky škálování úlohy Stream Analytics.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 44a7c0721d8a0683162d2219bff0e4a4ecb117e6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Využít paralelizace dotazu v Azure Stream Analytics
Tento článek ukazuje, jak chcete využít výhod paralelního zpracování v Azure Stream Analytics. Zjistíte, jak se škálovat úlohy Stream Analytics nakonfigurováním vstupní oddíly a ladění analytics definice dotazu.
Předpokladem je, můžete chtít znát představu o jednotek Streaming popsané v [Rady pro pochopení a upravte jednotek streamování](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Jaké jsou součástí úlohy Stream Analytics?
Definice úlohy Stream Analytics obsahuje vstupy, dotaz a výstup. Vstupní hodnoty jsou, kde úloha načte datový proud z. Dotaz se používá k transformaci dat vstupního datového proudu a výstup je, kde úloha odešle na výsledky úlohy.  

Úloha vyžaduje alespoň jeden vstupní zdroj pro streamování data. Vstupní zdroj dat datového proudu můžete ukládat v Centru událostí Azure nebo v Azure blob storage. Další informace najdete v tématu [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md) a [začít používat Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-sources-and-sinks"></a>Oddíly v zdroje a jímky
Škálování úloha Stream Analytics využívá oddílů v vstup nebo výstup. Vytváření oddílů umožňuje data rozdělíte do podmnožin podle klíč oddílu. Proces, který používá data (například úlohu streamování Analytics) může spotřebovávat a zápis různých oddílů souběžně, což zvyšuje propustnost. 

### <a name="inputs"></a>Vstupy
Veškerý vstup Azure Stream Analytics můžete využít výhod vytváření oddílů:
-   Centrum EventHub (třeba nastavit klíč oddílu explicitně s PARTITION BY – klíčové slovo)
-   IoT Hub (třeba nastavit klíč oddílu explicitně s PARTITION BY – klíčové slovo)
-   Blob Storage

### <a name="outputs"></a>Výstupy

Při práci s Stream Analytics můžete využít oddíly v výstupy:
-   Azure Data Lake Storage
-   Azure Functions
-   Tabulka Azure
-   Úložiště objektů BLOB (můžete nastavit klíč oddílu explicitně)
-   CosmosDB (třeba explicitně nastaven klíč oddílu)
-   Centrum EventHub (třeba explicitně nastaven klíč oddílu)
-   IoT Hub (třeba explicitně nastaven klíč oddílu)
-   Service Bus

Výstupy PowerBI, SQL a datový sklad SQL nepodporují dělení. Ale můžete můžete stále oddílu vstup jak je popsáno v [v této části](#multi-step-query-with-different-partition-by-values) 

Další informace o oddílech najdete v následujících článcích:

* [Přehled funkcí služby Event Hubs](../event-hubs/event-hubs-features.md#partitions)
* [Segmentace dat](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning#partitioning-azure-blob-storage)


## <a name="embarrassingly-parallel-jobs"></a>Trapně paralelní úlohy
*Paralelně zpracovatelné* úlohy je nejvíce škálovatelným scénář máme v Azure Stream Analytics. Připojí se jeden oddíl vstupu do jedné instance dotazu do jednoho oddílu výstupu. Tato paralelismus má následující požadavky:

1. Pokud logika dotazu závisí na stejný klíč zpracovávaných stejnou instanci dotazu, musí se ujistěte, že události přejděte do stejného oddílu váš vstup. Pro službu Event Hubs nebo IoT Hub, to znamená, že data události musí mít **PartitionKey** hodnotu sady. Alternativně můžete použít odesílatelé oddílů. Pro úložiště objektů blob to znamená, že události budou odeslány do stejné složky oddílu. Pokud dotaz logiky nevyžaduje stejný klíč zpracování na stejnou instanci dotazu, můžete ignorovat tento požadavek. Příkladem této logiky, může být jednoduchý dotaz vyberte filtr projektu.  

2. Jakmile data rozložená na vstupní straně, musí se ujistěte, že váš dotaz je rozdělený do oddílů. To vyžaduje, abyste použili **PARTITION BY** v všechny kroky. Jsou povoleny několik kroků, ale musí mít všechny oddíly pomocí stejného klíče. V současné době rozdělení klíč musí být nastavena na **PartitionId** v pořadí pro úlohu, která má být plně paralelní.  

3. Většina našich výstupu můžete využít výhod rozdělení do oddílů, ale pokud používáte výstupní typ, které nepodporují dělení úlohu nebudou plně paralelní. Odkazovat [výstup části](#outputs) další podrobnosti.

4. Počet vstupních oddílů musí být roven počtu oddílů výstup. Výstup úložiště objektů BLOB může podporovat oddíly a dědí schéma rozdělení oddílů nadřazeného dotazu. Pokud klíč oddílu pro objekt Blob úložiště je zadán, data rozdělena na oddíly vstupní tedy stále plně paralelní výsledek. Zde jsou příklady oddílu hodnot, které umožňují plně paralelní úlohy:

   * 8 oddíly vstupní centra událostí a Centrum událostí 8 výstupní oddíly
   * 8 oddíly vstupní centra událostí a výstup úložiště objektů blob
   * 8 vstupní oddíly centra událostí a výstup úložiště objektů blob rozdělena na oddíly pomocí vlastního pole libovolný mohutností
   * 8 oddíly vstupní úložiště objektů blob a výstup úložiště objektů blob
   * 8 blob oddílů pro úložiště a 8 oddíly výstupu centra událostí

Následující části popisují některé ukázkové scénáře, které jsou jednoduše paralelně zpracovatelné.

### <a name="simple-query"></a>Jednoduchý dotaz

* Vstup: Centra událostí s 8 oddíly
* Výstup: Centra událostí s 8 oddíly

Dotaz:

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

Dotaz je jednoduchý filtr. Proto jsme nemusíte starat o vytváření oddílů vstup odeslaný do centra událostí. Všimněte si, že dotaz obsahuje **oddílu podle PartitionId**, takže se splní požadavek #2 z dříve. Pro výstup, je potřeba nakonfigurovat výstupu centra událostí v projektu na sadu klíče oddílu na **PartitionId**. Jeden poslední kontrola je počet vstupních oddílů musí být roven počtu oddílů výstup.

### <a name="query-with-a-grouping-key"></a>Dotaz s klíčem seskupení

* Vstup: Centra událostí s 8 oddíly
* Výstup: Úložiště objektů Blob

Dotaz:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Tento dotaz obsahuje seskupení klíč. Proto události seskupeny dohromady, musí se poslat do stejného oddílu centra událostí. Vzhledem k tomu, že v tomto příkladu jsme Seskupit podle TollBoothID, jsme by měl být jisti, že TollBoothID slouží jako klíč oddílu, odeslání události do centra událostí. Potom v ASA, můžeme použít **oddílu podle PartitionId** dědit z této schéma oddílu a povolit úplné paralelizace. Vzhledem k tomu, že výstupem je úložiště objektů blob, jsme nemusíte si dělat starosti o konfiguraci hodnotu klíče oddílu, podle požadavků #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Příklad scénáře, které jsou *není* paralelně zpracovatelné

V předchozí části jsme vám ukázal některé paralelně zpracovatelné scénáře. V této části probereme scénáře, které nejsou splněny všechny požadavky být jednoduše paralelně zpracovatelné. 

### <a name="mismatched-partition-count"></a>Počet neodpovídající oddílů
* Vstup: Centra událostí s 8 oddíly
* Výstup: Centra událostí s 32 oddílů

V takovém případě nezávisle na tom, co je dotaz. Pokud počet vstupních oddílů se neshoduje se počet oddílů výstup, topologie není trapně paralelní. + ale jsme se můžete pořád dostat některé úroveň nebo paralelizace.

### <a name="query-using-non-partitioned-output"></a>Dotazování pomocí bez oddílů výstup
* Vstup: Centra událostí s 8 oddíly
* Výstup: PowerBI

Výstup PowerBI aktuálně nepodporuje vytváření oddílů. Proto tento scénář není paralelně zpracovatelné.

### <a name="multi-step-query-with-different-partition-by-values"></a>Vícekrokový dotazu s různými hodnotami PARTITION BY
* Vstup: Centra událostí s 8 oddíly
* Výstup: Centra událostí s 8 oddíly

Dotaz:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Jak vidíte, druhý krok používá **TollBoothId** jako klíč rozdělení. Tento krok není stejný jako v prvním kroku, a proto vyžaduje nám udělat náhodně. 

Některé úlohy Stream Analytics, které odpovídat (nebo nemusíte) paralelně zpracovatelné topologie v předchozích příkladech. Pokud jsou v souladu, mají potenciální pro maximální měřítko. Pro úlohy, které se nehodí jeden z těchto profilů škálování pokyny bude k dispozici v budoucích aktualizací. Prozatím použijte obecné pokyny v následujících částech.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Vypočítat maximální počet jednotek úlohy streamování
Celkový počet jednotek streamování, které lze použít v rámci úlohy Stream Analytics závisí na počtu kroků v dotazu definovány pro úlohy a počet oddílů pro každý krok.

### <a name="steps-in-a-query"></a>Kroky v dotazu
Dotaz může mít jeden nebo mnoho kroků. Každý krok je poddotaz definované **WITH** – klíčové slovo. Dotaz, který je mimo **WITH** – klíčové slovo (pouze jeden dotaz) také považován za krok, například **vyberte** příkaz v následujícím dotazu:

Dotaz:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

Tento dotaz má dva kroky.

> [!NOTE]
> Tento dotaz je podrobněji popsána dále v tomto článku.
>  

### <a name="partition-a-step"></a>Oddílu krok
Vytváření oddílů krok vyžaduje následující podmínky:

* Vstupní zdroj, musí mít oddíly. 
* **Vyberte** příkaz dotazu musí ke čtení z oddílů vstupní zdroj.
* Dotaz v rámci kroku, musí mít **PARTITION BY** – klíčové slovo.

Při dotazu je rozdělena na oddíly, vstupních událostech se zpracování a agregované v samostatném oddílu skupiny a výstupy události jsou generovány pro každou skupinu. Pokud chcete, aby součet agregace, musíte vytvořit druhý krok bez oddílů k agregaci.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Vypočítat maximální počet jednotek pro úlohu streamování
Všechny kroky bez oddílů společně můžete škálovat až šest jednotky streamování (SUs) u úlohy Stream Analytics. Kromě toho můžete přidat 6 služby SUs pro každý oddíl v oddílů kroku.
Zobrazí se některé **příklady** v následující tabulce.

| Dotaz                                               | Služba SUs Max pro úlohu |
| --------------------------------------------------- | ------------------- |
| <ul><li>Dotaz obsahuje jeden krok.</li><li>V kroku není rozdělena na oddíly.</li></ul> | 6 |
| <ul><li>Vstupní datový proud je rozdělena na oddíly pomocí 16.</li><li>Dotaz obsahuje jeden krok.</li><li>V kroku je rozdělena na oddíly.</li></ul> | 96 (6 * 16 oddíly) |
| <ul><li>Dotaz obsahuje dva kroky.</li><li>Ani jeden z kroků je rozdělena na oddíly.</li></ul> | 6 |
| <ul><li>Vstupní datový proud je rozdělena na oddíly 3.</li><li>Dotaz obsahuje dva kroky. Vstupní krok je rozdělena na oddíly a druhý krok není.</li><li><strong>Vyberte</strong> příkaz čte z oddílů vstup.</li></ul> | 24 (18 oddílů kroky + 6 pokyny bez oddílů |

### <a name="examples-of-scaling"></a>Příklady škálování

Následující dotaz vypočítá počet aut, v rámci průchodu přes projedou stanice, která má tři tollbooths časového období tři minuty. Tento dotaz je možné rozšířit až šest služby SUs.

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Pokud chcete používat další služby SUs pro dotaz, musí mít oddíly vstupní datový proud a dotazu. Vzhledem k tomu, že oddíl datový proud dat je nastaven na 3, tyto změny dotazu je možné rozšířit až 18 služby SUs:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Při dotazu je rozdělena na oddíly, vstupních událostech se zpracovat a agregovat v samostatném oddílu skupiny. Výstup události jsou také vytvářeny pro každou skupinu. Dělení může způsobit, že některé neočekávané výsledky při **GROUP BY** pole není klíč oddílu v vstupní datový proud. Například **TollBoothId** pole v předchozího dotazu není klíč oddílu **Input1**. Výsledkem je, že data z mýtná celnice č. 1 možné rozdělit do několika oddílů.

Každý z **Input1** oddíly se zpracovávají odděleně podle Stream Analytics. V důsledku toho bude vytvořen více záznamů počtu car pro stejné mýtná celnice ve stejném okně Přeskakující. Pokud klíč vstupní oddílu nelze změnit, můžete tento problém opravit tak, že přidáte krok jiný oddíl agregované hodnoty mezi oddílů, jako v následujícím příkladu:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Tento dotaz můžete škálovat na 24 služby SUs.

> [!NOTE]
> Pokud jsou připojení dvě datové proudy, ujistěte se, že datové proudy jsou rozdělena na oddíly pomocí klíče oddílu sloupce, který použijete při jejich vytváření. Ujistěte se také mít stejný počet oddílů v obou datových proudů.
> 
> 





## <a name="get-help"></a>Podpora
Pro další pomoc, vyzkoušejte naše [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

