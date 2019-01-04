---
title: Použít využitím paralelizace dotazů a škálování ve službě Azure Stream Analytics
description: Tento článek popisuje, jak škálovat úlohy Stream Analytics pomocí konfigurace vstupního oddíly, ladění definice dotazu a nastavení úlohu jednotek streamování.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 0b68819ba032d7655433aadd30fe2852941096ce
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000542"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Využití paralelizace dotazů ve službě Azure Stream Analytics
Tento článek ukazuje, jak využít výhod paralelního zpracování v Azure Stream Analytics. Zjistíte, jak škálovat úlohy Stream Analytics pomocí konfigurace vstupního oddíly a ladění definice dotazu analytics.
Předpokladem je, můžete chtít znát pojem jednotka streamování je popsáno v [principy a úpravy jednotek streamování](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Jaké jsou součástí úlohy Stream Analytics?
Definice úlohy Stream Analytics zahrnuje vstupů, dotaz a výstup. Vstupy jsou, kde úloha načte datový proud z. Dotaz je používána pro vstupní datový proud a výstup je, kde úloha odesílá výsledky úlohy.  

Úlohu streamování dat vyžaduje alespoň jeden vstupní zdroj. Vstupní zdroj dat datový proud může být uložená v Centru událostí Azure nebo ve službě Azure blob storage. Další informace najdete v tématu [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md) a [začít používat Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-sources-and-sinks"></a>Na úrovni zdroje a jímky
Škálování úlohu Stream Analytics využívá oddílů v vstup nebo výstup. Dělení umožňuje rozdělit data do podmnožiny na základě klíče oddílu. Proces, který používá data (například úloha Stream Analytics) můžete využívat a zápisu různých oddílů současně, což zvyšuje propustnost. 

### <a name="inputs"></a>Vstupy
Veškerý vstup Azure Stream Analytics můžete využít výhod dělení:
-   Centra událostí (třeba nastavit explicitně s klíčovým slovem PARTITION BY klíč oddílu)
-   IoT Hub (je potřeba nastavit klíč oddílu explicitně s klíčovým slovem PARTITION BY)
-   Blob Storage

### <a name="outputs"></a>Výstupy

Při práci s Stream Analytics můžete využít dělení v výstupy:
-   Azure Data Lake Storage
-   Azure Functions
-   Tabulka Azure
-   Úložiště objektů BLOB (můžete nastavit klíč oddílu explicitně)
-   Cosmos DB (je potřeba explicitně nastavit klíč oddílu)
-   Event Hubs (je potřeba explicitně nastavit klíč oddílu)
-   IoT Hub (je potřeba explicitně nastavit klíč oddílu)
-   Service Bus
- SQL a SQL Data Warehouse pomocí volitelné dělení: Další informace o naleznete [výstup na stránku služby Azure SQL Database](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf).

Power BI nepodporuje vytváření oddílů. Ale můžete i nadále dělit vstup jak je popsáno v [v této části](#multi-step-query-with-different-partition-by-values) 

Další informace o oddílech najdete v následujících článcích:

* [Přehled funkcí Event Hubs](../event-hubs/event-hubs-features.md#partitions)
* [Dělení dat](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Jednoduše paralelně zpracovatelné úlohy
*Jednoduše paralelně zpracovatelné* úlohy je nejvíce škálovatelným scénáři máme ve službě Azure Stream Analytics. Připojí jeden oddíl vstup do jedné instance dotazu do jednoho oddílu výstupu. Tato paralelismu má následující požadavky:

1. Pokud svoji logiku dotazu závisí na stejnou instanci dotazu zpracovává stejným klíčem, musí se ujistěte, že událostí přejděte do stejného oddílu váš vstup. Pro službu IoT Hub nebo Event Hubs, to znamená, že se data události musí mít **PartitionKey** hodnota sady. Alternativně můžete použít dělené odesílatelů. Pro úložiště objektů blob to znamená odeslání události do stejné složky oddílu. Pokud svoji logiku dotazu nevyžaduje stejný klíč zpracovat stejnou instanci dotazu, můžete ignorovat tento požadavek. Příkladem této logiky může být jednoduchý dotaz filtru vybrat projektu.  

2. Po dat je rozloží na straně vstupní, ujistěte se, že je váš dotaz dělený. To vyžaduje, abyste použili **PARTITION BY** ve všech krocích. Jsou povoleny několik kroků, ale všechny musí být dělené podle stejný klíč. V současné době dělení klíč musí být nastaveno na **PartitionId** mohl být plně paralelní úlohy.  

3. Většina našich výstupu můžete využít výhod dělení, ale pokud použijete výstupní typ, který nepodporuje vytváření oddílů nebudou plně paralelní úlohy. Odkazovat [výstupní sekce](#outputs) další podrobnosti.

4. Počet vstupních oddílů musí být roven počtu oddílů výstup. Výstup úložiště objektů BLOB podporují oddíly a dědí schéma rozdělení oddílů nadřazeného dotazu. Když klíč oddílu pro objekt Blob úložiště není zadána, data je rozdělený na vstupní oddíl tedy i nadále plně paralelní výsledek. Tady jsou příklady hodnot oddílu, které umožňují plně paralelní úlohy:

   * 8 vstupní oddílů centra událostí a Centrum událostí 8 výstup oddílů
   * 8 vstupní oddílů centra událostí a výstup úložiště objektů blob
   * 8 vstupní oddílů centra událostí a výstup úložiště objektů blob dělené podle vlastního pole libovolného Kardinalita
   * 8 oddílů vstupní úložiště objektů blob a výstup úložiště objektů blob
   * 8 blob oddílů pro úložiště a 8 výstup oddílů centra událostí

Následující části popisují některé ukázkové scénáře, které jsou jednoduše paralelně zpracovatelné.

### <a name="simple-query"></a>Jednoduchý dotaz

* Vstup: Centrum událostí s 8 oddíly
* Výstup: Centrum událostí s 8 oddíly

Dotaz:

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

Tento dotaz je jednoduchý filtr. Proto jsme nemusíte se starat o dělení vstup odeslaná do centra událostí. Všimněte si, že dotaz obsahuje **oddíl podle PartitionId**, takže splňuje požadavek #2 z dříve. Pro výstup, musíme nakonfigurovat výstup centra událostí v projektu na sadu klíče oddílu pro **PartitionId**. Jeden poslední se ujistěte se, že počet vstupních oddílů je roven počtu oddílů výstup.

### <a name="query-with-a-grouping-key"></a>Dotazování s klíčem seskupení

* Vstup: Centrum událostí s 8 oddíly
* Výstup: Blob Storage

Dotaz:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Tento dotaz obsahuje seskupení klíč. Události seskupené dohromady musí proto odešlou do stejného oddílu centra událostí. Protože v tomto příkladu jsme Seskupit podle TollBoothID, jsme měli jistotu, že TollBoothID slouží jako klíč oddílu, při odesílání událostí do centra událostí. Potom v Azure Stream Analytics, můžete pomocí **oddíl podle PartitionId** dědit z tohoto schématu oddílu a povolit úplné paralelního zpracování. Protože výstupem je úložiště objektů blob, jsme nemusíte se starat o konfiguraci hodnotu klíče oddílu, podle požadavků #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Příkladem scénáře, které jsou *není* jednoduše paralelně zpracovatelné

V předchozí části jsme vám ukázali některé jednoduše paralelně zpracovatelné scénáře. V této části se podíváme na scénáře, které nesplňují všechny požadavky, které budou jednoduše paralelně zpracovatelné. 

### <a name="mismatched-partition-count"></a>Počet oddílů neodpovídající
* Vstup: Centrum událostí s 8 oddíly
* Výstup: Centrum událostí s 32 oddíly

V takovém případě nebude vadit, co je dotaz. Pokud počet oddílů vstupní neodpovídá počtu oddílů výstup, topologie není zpracovatelné paralelně. + ale jsme stále získáte některé úroveň nebo paralelního zpracování.

### <a name="query-using-non-partitioned-output"></a>Dotazování pomocí výstupu bez oddílů
* Vstup: Centrum událostí s 8 oddíly
* Výstup: Power BI

Výstup Power BI v současné době nepodporuje vytváření oddílů. Proto tento scénář není jednoduše paralelně zpracovatelné.

### <a name="multi-step-query-with-different-partition-by-values"></a>Vícekrokové dotazu s různými hodnotami PARTITION BY
* Vstup: Centrum událostí s 8 oddíly
* Výstup: Centrum událostí s 8 oddíly

Dotaz:

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Jak je vidět, druhý krok využívá **TollBoothId** jako klíč rozdělení. Tento krok není stejný jako v prvním kroku, a proto vyžaduje, abychom náhodně. 

Předchozí příklady ukazují některé úlohy Stream Analytics, které odpovídají (nebo nemusíte) jednoduše paralelně zpracovatelné topologie. Pokud jsou v souladu, mají potenciál pro maximální škálování. Pro úlohy, které se nehodí jeden z těchto profilů škálování doprovodné materiály bude k dispozici v budoucích aktualizací. Prozatím použijte obecné pokyny v následujících částech.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Vypočítat maximální počet jednotek úlohy streamování
Celkový počet jednotek streamování, které je možné úlohu Stream Analytics, závisí na počtu kroků v dotazu definovaném pro úlohy a počet oddílů pro jednotlivé kroky.

### <a name="steps-in-a-query"></a>Kroky v dotazu
Dotaz může mít jeden nebo více kroků. Každý krok je poddotaz určené **WITH** – klíčové slovo. Dotaz, který je mimo **WITH** – klíčové slovo (pouze jeden dotaz) také považován za krok, jako **vyberte** příkaz do ní následující dotaz:

Dotaz:

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId
```

Tento dotaz má dva kroky.

> [!NOTE]
> Tento dotaz je podrobněji popsány dále v tomto článku.
>  

### <a name="partition-a-step"></a>Rozdělit krok
Dělení krok vyžaduje následující podmínky:

* Vstupní zdroje musí mít oddíly. 
* **Vyberte** příkaz dotazu musí číst z oddílů vstupní zdroj.
* Dotaz v rámci kroku musí mít **PARTITION BY** – klíčové slovo.

Při dotazu je rozdělená na oddíly, vstupní události jsou skupiny, zpracovaných a agregované v samostatném oddílu a výstupy události se generují pro každou skupinu. Pokud chcete, aby kombinované agregace, je třeba vytvořit druhý krok bez oddílů k agregaci.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Vypočítat maximální počet jednotek pro úlohu streamování
Všechny kroky bez oddílů můžete společně škálovat až šest jednotky streamování (su) pro úlohu Stream Analytics. Kromě toho můžete přidat su 6 pro každý oddíl v dělené kroku.
Zobrazí se některé **příklady** v následující tabulce.

| Dotaz                                               | Služba SUs Max pro úlohu |
| --------------------------------------------------- | ------------------- |
| <ul><li>Dotaz obsahuje jeden krok.</li><li>V kroku není rozdělena na oddíly.</li></ul> | 6 |
| <ul><li>Vstupní datový proud je rozdělený podle 16.</li><li>Dotaz obsahuje jeden krok.</li><li>V kroku je rozdělit na oddíly.</li></ul> | 96 (oddíly 6 * 16) |
| <ul><li>Dotaz obsahuje dva kroky.</li><li>Ani jeden z kroků je rozdělit na oddíly.</li></ul> | 6 |
| <ul><li>Vstupní datový proud je rozdělit na oddíly ve 3.</li><li>Dotaz obsahuje dva kroky. Vstupní kroku je rozdělit na oddíly a v druhém kroku není.</li><li><strong>Vyberte</strong> příkaz čte z oddílů vstup.</li></ul> | 24 (18 dělené postup + 6 pokyny bez oddílů |

### <a name="examples-of-scaling"></a>Příklady škálování

Následující dotaz vypočítá počet aut probíhá linka stanice, která má tři tollbooths okna tři minuty. Tento dotaz je možné škálovat až šest su.

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Pro účely další služby SUs dotazu, musí být rozdělený vstupní datový proud a dotazu. Vzhledem k tomu, že oddíl datového proudu dat nastavená na 3, následující upravený dotaz je možné škálovat až 18 su:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Pokud je dotaz rozdělený, vstupní události zpracování a agregovat v samostatném oddílu skupiny. Výstupní události jsou také generovány pro každou skupinu. Dělení může způsobit nějaké neočekávané výsledky při **Group** pole není klíč oddílu ve vstupní datový proud. Například **TollBoothId** pole předchozí dotaz není klíč oddílu **vstup1**. Výsledkem je, že data z TollBooth č. 1 možné rozdělit do několika oddílů.

Každá z **vstup1** oddíly se zpracovávají odděleně podle Stream Analytics. V důsledku toho se vytvoří více záznamů car počet pro stejný tollbooth ve stejném aktivační událost pro Přeskakující okno. Pokud klíč vstupního oddílu nelze změnit, lze tento problém napravit tak, že přidáte krok mimo oddíl můžete agregovat hodnoty napříč oddíly, jako v následujícím příkladu:

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Tento dotaz je možné škálovat na 24 su.

> [!NOTE]
> Pokud jsou spojování dvou datových proudů, ujistěte se, že datové proudy dělí podle klíče oddílu sloupce, který použijete k vytvoření spojení. Také se ujistěte, že mají stejný počet oddílů v obou datových proudů.
> 
> 





## <a name="get-help"></a>Podpora
Potřebujete další pomoc, vyzkoušejte naše [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

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

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

