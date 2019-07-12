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
ms.openlocfilehash: 5eba5601a50640261fa1b488d959f606d4514737
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612217"
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

2. Po dat je rozloží na straně vstupní, ujistěte se, že je váš dotaz dělený. To vyžaduje, abyste použili **PARTITION BY** ve všech krocích. Jsou povoleny několik kroků, ale všechny musí být dělené podle stejný klíč. V části úroveň kompatibility 1.0 a 1.1, musí být nastaveno na klíč rozdělení **PartitionId** mohl být plně paralelní úlohy. Pro úlohy s úrovní compatility 1.2 nebo vyšší vlastní sloupec se dá nastavit jako klíč oddílu v nastavení vstupu a úlohy budou paralellized automoatically i bez klauzuli PARTITION BY.

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

Tento dotaz je jednoduchý filtr. Proto jsme nemusíte se starat o dělení vstup odeslaná do centra událostí. Všimněte si, že před 1.2 musí obsahovat úlohy s úrovní kompatibility **oddíl podle PartitionId** tak splňuje požadavek #2 z předchozí klauzuli. Pro výstup, musíme nakonfigurovat výstup centra událostí v projektu na sadu klíče oddílu pro **PartitionId**. Jeden poslední se ujistěte se, že počet vstupních oddílů je roven počtu oddílů výstup.

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

### <a name="compatibility-level-12---multi-step-query-with-different-partition-by-values"></a>Úroveň kompatibility 1.2 – vícekrokového dotazu s různými hodnotami PARTITION BY 
* Vstup: Centrum událostí s 8 oddíly
* Výstup: Centrum událostí s 8 oddíly

Dotaz:

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Úroveň kompatibility 1.2 ve výchozím nastavení povoluje provádění paralelního dotazu. Například dotaz z předchozí části bude parttioned tak dlouho, dokud "TollBoothId" sloupec je nastaven jako vstupní klíč oddílu. Klauzule PARTITION podle ParttionId se nevyžaduje.

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

## <a name="achieving-higher-throughputs-at-scale"></a>Dosáhnout vyšší propustnosti ve velkém měřítku

[Jednoduše paralelně zpracovatelné](#embarrassingly-parallel-jobs) úlohy je nezbytné, ale není dostatek tolerovat vyšší propustnost ve velkém měřítku. Každý systém úložiště a jeho odpovídající výstupní Stream Analytics má o tom, jak dosáhnout propustnosti nejlepší možný zápis variace. Jak se jakýkoli scénář ve velkém měřítku, jsou některé běžné problémy, které je možné řešit s použitím správné konfigurací. Tato část popisuje konfigurace pro několik běžných výstupy a obsahuje ukázky pro udržování ingestování sazby 1 kB, 5 kB a 10 tisíc událostí za sekundu.

Zjištění použijte úlohu Stream Analytics s dotazem bezstavové (průchozí), základní UDF JavaScriptu, který zapisuje do centra událostí, Azure SQL Database nebo Cosmos DB.

#### <a name="event-hub"></a>Centrum událostí

|Rychlost příjmu (událostí za sekundu) | Jednotky streamování | Výstup prostředky  |
|--------|---------|---------|
| 1 TISÍC     |    1    |  2 TU   |
| 5 KB     |    6    |  6 JEDNOTEK PROPUSTNOSTI   |
| 10 tisíc    |    12   |  10 JEDNOTEK PROPUSTNOSTI  |

[Centra událostí](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs) řešení se škáluje lineárně z hlediska streamování (SU) jednotky a propustnosti, díky tomu je nejúčinnější a nejefektivněji k analýze a Streamovat data ze Stream Analytics. Úlohy je možné škálovat až 192 SU, což obecně znamená zpracování až 200 MB/s nebo bilion 19 událostí za den.

#### <a name="azure-sql"></a>Azure SQL
|Rychlost příjmu (událostí za sekundu) | Jednotky streamování | Výstup prostředky  |
|---------|------|-------|
|    1 TISÍC   |   3  |  S3   |
|    5 KB   |   18 |  P4   |
|    10 tisíc  |   36 |  P6   |

[Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql) podporuje zápis paralelně, volaná dědit dělení, ale není povolená ve výchozím nastavení. Povolení dědit dělení, společně s plně paralelní dotaz, ale nemusí být dostatečné pro dosažení vyšší propustnost. Propustnost zápisu SQL podstatně závisí na schématu konfigurace a tabulky databáze SQL Azure. [Výstupní výkon SQL](./stream-analytics-sql-output-perf.md) článek obsahuje další podrobnosti o parametrech, které můžete maximalizovat propustnost zápisu. Jak je uvedeno v [výstupu Azure Stream Analytics ke službě Azure SQL Database](./stream-analytics-sql-output-perf.md#azure-stream-analytics) článku, toto řešení není se škálují lineárně jako plně paralelní kanál nad rámec 8 oddílů a může být nutné oddílů před výstupu SQL (viz [ DO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)). SKU úrovně Premium jsou potřeba k udržení vysoké míry vstupně-výstupních operací spolu s režijní náklady ze zálohy protokolu děje každých několik minut.

#### <a name="cosmos-db"></a>Databáze Cosmos
|Rychlost příjmu (událostí za sekundu) | Jednotky streamování | Výstup prostředky  |
|-------|-------|---------|
|  1 TISÍC   |  3    | 20 TISÍC RU  |
|  5 KB   |  24   | 60 TIS. RU  |
|  10 tisíc  |  48   | 120 TIS. RU |

[Cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) výstup ze Stream Analytics byla aktualizována na používání nativní integrace v rámci [úroveň kompatibility 1.2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12). Úroveň kompatibility 1.2 umožňuje výrazně vyšší výkon a snižuje spotřebu RU 1.1, což je výchozí úroveň kompatibility pro nové úlohy. Toto řešení využívá rozdělit na oddíly na /deviceId kontejnery služby cosmos DB a zbytek řešení je stejně nakonfigurovaná.

Všechny [streamování ve škálování azure ukázky](https://github.com/Azure-Samples/streaming-at-scale) použít dodáni podle zatížení simulaci testovacích klientů jako vstup Centrum událostí. Každá událost vstupu je 1KB dokumentu JSON, což znamená snadno konfigurované ingestování kurzy propustnosti (1MB/s, 5MB/s a 10MB/s). Události simulace zařízení IoT odesílání následující data JSON (v Zkrácený tvar) až 1 kB zařízení:

```
{
    "eventId": "b81d241f-5187-40b0-ab2a-940faf9757c0",
    "complexData": {
        "moreData0": 51.3068118685458,
        "moreData22": 45.34076957651598
    },
    "value": 49.02278128887753,
    "deviceId": "contoso://device-id-1554",
    "type": "CO2",
    "createdAt": "2019-05-16T17:16:40.000003Z"
}
```

> [!NOTE]
> Tyto konfigurace se může změnit z důvodu různých komponent používané v řešení. Pro přesnější odhad upravit ukázky, aby vyhovovala vašemu scénáři.

### <a name="identifying-bottlenecks"></a>Nalezení problémových míst

Použití pokokna metriky v úloze Azure Stream Analytics identifikovat problémová místa ve vašem kanálu. Kontrola **vstupní a výstupní události** propustnost a ["Vodoznak zpoždění"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) nebo **události v Backlogu** zobrazíte, pokud je úloha uchovávání vstupní sazba. Metriky Event Hub, vyhledejte **omezuje požadavky** a odpovídajícím způsobem upravit jednotky prahovou hodnotu. Metriky služby Cosmos DB najdete v tématu **maximální počet spotřebovaných RU/s na rozsah klíče oddílu** pod propustnost pro zajištění rozsahy klíčů oddílů se rovnoměrně spotřebuje. Pro službu Azure SQL DB, sledovat **PROTOKOLOVACÍ** a **procesoru**.

## <a name="get-help"></a>Podpora

Potřebujete další pomoc, vyzkoušejte naše [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
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

