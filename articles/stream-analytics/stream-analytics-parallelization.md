---
title: Použít využitím paralelizace dotazů a škálování ve službě Azure Stream Analytics
description: Tento článek popisuje, jak škálovat úlohy Stream Analytics pomocí konfigurace vstupního oddíly, ladění definice dotazu a nastavení úlohu jednotek streamování.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: d1afb6037b5fc290de93faba405982ebd1fb68ea
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364550"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Využití paralelizace dotazů ve službě Azure Stream Analytics
Tento článek ukazuje, jak využít výhod paralelního zpracování v Azure Stream Analytics. Zjistíte, jak škálovat úlohy Stream Analytics pomocí konfigurace vstupního oddíly a ladění definice dotazu analytics.
Je možné, že budete chtít být obeznámeni s pojmem jednotky streamování popsané v tématu [pochopení a úprava jednotek streamování](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Jaké jsou součástí úlohy Stream Analytics?
Definice úlohy Stream Analytics zahrnuje vstupů, dotaz a výstup. Vstupy jsou, kde úloha načte datový proud z. Dotaz je používána pro vstupní datový proud a výstup je, kde úloha odesílá výsledky úlohy.

Úlohu streamování dat vyžaduje alespoň jeden vstupní zdroj. Vstupní zdroj dat datový proud může být uložená v Centru událostí Azure nebo ve službě Azure blob storage. Další informace najdete v tématu [Úvod do Azure Stream Analytics](stream-analytics-introduction.md) a [Začínáme s používáním Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md).

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
-   Cosmos DB (je potřeba nastavit klíč oddílu explicitně)
-   Event Hubs (je potřeba nastavit klíč oddílu explicitně)
-   IoT Hub (je potřeba explicitně nastavit klíč oddílu)
-   Service Bus
- SQL a SQL Data Warehouse s volitelným dělením oddílů: Další informace najdete na [stránce výstup do Azure SQL Database](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf).

Power BI nepodporuje dělení. Můžete však stále rozdělit vstup, jak je popsáno v [této části](#multi-step-query-with-different-partition-by-values) . 

Další informace o oddílech najdete v následujících článcích:

* [Přehled funkcí Event Hubs](../event-hubs/event-hubs-features.md#partitions)
* [Dělení dat](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Jednoduše paralelně zpracovatelné úlohy
*Zpracovatelné paralelní* úloha je Nejškálovatelnější scénář, který je v Azure Stream Analytics. Připojí jeden oddíl vstup do jedné instance dotazu do jednoho oddílu výstupu. Tato paralelismu má následující požadavky:

1. Pokud svoji logiku dotazu závisí na stejnou instanci dotazu zpracovává stejným klíčem, musí se ujistěte, že událostí přejděte do stejného oddílu váš vstup. Pro Event Hubs nebo IoT Hub to znamená, že data události musí mít nastavenou hodnotu **PartitionKey** . Alternativně můžete použít dělené odesílatelů. Pro úložiště objektů blob to znamená odeslání události do stejné složky oddílu. Pokud svoji logiku dotazu nevyžaduje stejný klíč zpracovat stejnou instanci dotazu, můžete ignorovat tento požadavek. Příkladem této logiky může být jednoduchý dotaz filtru vybrat projektu.  

2. Po dat je rozloží na straně vstupní, ujistěte se, že je váš dotaz dělený. To vyžaduje, abyste v rámci všech kroků použili **oddíl** . Jsou povoleny několik kroků, ale všechny musí být dělené podle stejný klíč. V části úroveň kompatibility 1,0 a 1,1 musí být klíč rozdělení nastavený na **PartitionID** , aby byla úloha plně paralelní. Pro úlohy s úrovní compatility 1,2 a vyšší lze vlastní sloupec zadat jako klíč oddílu ve vstupním nastavení a úloha bude paralellized automaticky i bez klauzule PARTITION BY. Pro výstup centra událostí musí být vlastnost "sloupec klíče oddílu" nastavena na použití "PartitionId".

3. Většina našich výstupu můžete využít výhod dělení, ale pokud použijete výstupní typ, který nepodporuje vytváření oddílů nebudou plně paralelní úlohy. Další podrobnosti najdete v [části s výstupem](#outputs) .

4. Počet vstupních oddílů musí být roven počtu oddílů výstup. Výstup úložiště objektů BLOB podporují oddíly a dědí schéma rozdělení oddílů nadřazeného dotazu. Když klíč oddílu pro objekt Blob úložiště není zadána, data je rozdělený na vstupní oddíl tedy i nadále plně paralelní výsledek. Tady jsou příklady hodnot oddílu, které umožňují plně paralelní úlohy:

   * 8 vstupní oddílů centra událostí a Centrum událostí 8 výstup oddílů
   * 8 vstupní oddílů centra událostí a výstup úložiště objektů blob
   * 8 vstupní oddílů centra událostí a výstup úložiště objektů blob dělené podle vlastního pole libovolného Kardinalita
   * 8 oddílů vstupní úložiště objektů blob a výstup úložiště objektů blob
   * 8 blob oddílů pro úložiště a 8 výstup oddílů centra událostí

Následující části popisují některé ukázkové scénáře, které jsou jednoduše paralelně zpracovatelné.

### <a name="simple-query"></a>Jednoduchý dotaz

* Vstup: Centrum událostí s 8 oddíly
* Výstup: centrum událostí s 8 oddíly ("sloupec klíče oddílu" musí být nastaven na použití "PartitionId")

Dotaz:

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

Tento dotaz je jednoduchý filtr. Proto jsme nemusíte se starat o dělení vstup odeslaná do centra událostí. Všimněte si, že úlohy s úrovní kompatibility před 1,2 musí zahrnovat **oddíl podle identifikátoru PartitionID** , takže splní #2 požadavku ze starší verze. Pro výstup musíme v úloze nakonfigurovat výstup centra událostí tak, aby byl klíč oddílu nastavený na **PartitionID**. Jeden poslední se ujistěte se, že počet vstupních oddílů je roven počtu oddílů výstup.

### <a name="query-with-a-grouping-key"></a>Dotazování s klíčem seskupení

* Vstup: Centrum událostí s 8 oddíly
* Výstup: Úložiště objektů Blob

Dotaz:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Tento dotaz obsahuje seskupení klíč. Události seskupené dohromady musí proto odešlou do stejného oddílu centra událostí. Protože v tomto příkladu jsme Seskupit podle TollBoothID, jsme měli jistotu, že TollBoothID slouží jako klíč oddílu, při odesílání událostí do centra událostí. Potom můžeme v ASA použít **oddíl podle identifikátoru PartitionID** k dědění z tohoto schématu oddílu a povolení úplného paralelismu. Protože výstupem je úložiště objektů blob, jsme nemusíte se starat o konfiguraci hodnotu klíče oddílu, podle požadavků #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Příklady scénářů, které *nejsou zpracovatelné paralelní*

V předchozí části jsme vám ukázali některé jednoduše paralelně zpracovatelné scénáře. V této části se podíváme na scénáře, které nesplňují všechny požadavky, které budou jednoduše paralelně zpracovatelné. 

### <a name="mismatched-partition-count"></a>Počet oddílů neodpovídající
* Vstup: Centrum událostí s 8 oddíly
* Výstup: Centrum událostí s 32 oddíly

V takovém případě nebude vadit, co je dotaz. Pokud počet oddílů vstupní neodpovídá počtu oddílů výstup, topologie není zpracovatelné paralelně. + ale jsme stále získáte některé úroveň nebo paralelního zpracování.

### <a name="query-using-non-partitioned-output"></a>Dotazování pomocí výstupu bez oddílů
* Vstup: Centrum událostí s 8 oddíly
* Výstup: Power BI

Výstup Power BI v současné době nepodporuje dělení. Proto tento scénář není jednoduše paralelně zpracovatelné.

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

Jak vidíte, druhý krok používá **TollBoothId** jako klíč rozdělení do oddílů. Tento krok není stejný jako v prvním kroku, a proto vyžaduje, abychom náhodně. 

Předchozí příklady ukazují některé úlohy Stream Analytics, které odpovídají (nebo nemusíte) jednoduše paralelně zpracovatelné topologie. Pokud jsou v souladu, mají potenciál pro maximální škálování. Pro úlohy, které se nehodí jeden z těchto profilů škálování doprovodné materiály bude k dispozici v budoucích aktualizací. Prozatím použijte obecné pokyny v následujících částech.

### <a name="compatibility-level-12---multi-step-query-with-different-partition-by-values"></a>Úroveň kompatibility 1,2 – více kroků dotaz s různými oddíly podle hodnot 
* Vstup: Centrum událostí s 8 oddíly
* Výstup: centrum událostí s 8 oddíly ("sloupec klíče oddílu" musí být nastaven na použití "TollBoothId")

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

Úroveň kompatibility 1,2 umožňuje spuštění paralelního dotazu ve výchozím nastavení. Například dotaz z předchozí části bude parttioned, pokud je sloupec "TollBoothId" nastaven jako klíč vstupního oddílu. Klauzule PARTITION BY ParttionId není povinná.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Vypočítat maximální počet jednotek úlohy streamování
Celkový počet jednotek streamování, které je možné úlohu Stream Analytics, závisí na počtu kroků v dotazu definovaném pro úlohy a počet oddílů pro jednotlivé kroky.

### <a name="steps-in-a-query"></a>Kroky v dotazu
Dotaz může mít jeden nebo více kroků. Každý krok je poddotaz definovaný pomocí klíčového slova **with** . Dotaz, který je mimo klíčové slovo **with** (pouze jeden dotaz), se také počítá jako krok, například příkaz **Select** v následujícím dotazu:

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
* Příkaz **Select** dotazu musí číst z rozděleného vstupního zdroje.
* Dotaz v kroku musí mít **oddíl podle** klíčového slova.

Při dotazu je rozdělená na oddíly, vstupní události jsou skupiny, zpracovaných a agregované v samostatném oddílu a výstupy události se generují pro každou skupinu. Pokud chcete, aby kombinované agregace, je třeba vytvořit druhý krok bez oddílů k agregaci.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Vypočítat maximální počet jednotek pro úlohu streamování
Všechny kroky bez oddílů můžete společně škálovat až šest jednotky streamování (su) pro úlohu Stream Analytics. Kromě toho můžete přidat su 6 pro každý oddíl v dělené kroku.
V následující tabulce vidíte některé **Příklady** .

| Dotaz                                               | Služba SUs Max pro úlohu |
| --------------------------------------------------- | ------------------- |
| <ul><li>Dotaz obsahuje jeden krok.</li><li>V kroku není rozdělena na oddíly.</li></ul> | 6 |
| <ul><li>Vstupní datový proud je rozdělený podle 16.</li><li>Dotaz obsahuje jeden krok.</li><li>V kroku je rozdělit na oddíly.</li></ul> | 96 (oddíly 6 * 16) |
| <ul><li>Dotaz obsahuje dva kroky.</li><li>Ani jeden z kroků je rozdělit na oddíly.</li></ul> | 6 |
| <ul><li>Vstupní datový proud je rozdělit na oddíly ve 3.</li><li>Dotaz obsahuje dva kroky. Vstupní kroku je rozdělit na oddíly a v druhém kroku není.</li><li>Příkaz <strong>Select</strong> načte z rozděleného vstupu.</li></ul> | 24 (18 dělené postup + 6 pokyny bez oddílů |

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

Pokud je dotaz rozdělený, vstupní události zpracování a agregovat v samostatném oddílu skupiny. Výstupní události jsou také generovány pro každou skupinu. Dělení může způsobit neočekávané výsledky, když pole **Seskupit podle** není klíč oddílu ve vstupním datovém proudu. Například pole **TollBoothId** v předchozím dotazu není klíčem oddílu **Input1**. Výsledkem je, že data z TollBooth č. 1 možné rozdělit do několika oddílů.

Každý z oddílů **Input1** se zpracuje samostatně pomocí Stream Analytics. V důsledku toho se vytvoří více záznamů car počet pro stejný tollbooth ve stejném aktivační událost pro Přeskakující okno. Pokud klíč vstupního oddílu nelze změnit, lze tento problém napravit tak, že přidáte krok mimo oddíl můžete agregovat hodnoty napříč oddíly, jako v následujícím příkladu:

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

## <a name="achieving-higher-throughputs-at-scale"></a>Dosažení vyšších propustností ve velkém měřítku

[Zpracovatelné paralelní](#embarrassingly-parallel-jobs) úloha je nutná, ale není dostatečná pro udržení vyšší propustnosti ve velkém měřítku. Každý systém úložiště a příslušný výstup Stream Analytics obsahuje variace toho, jak dosáhnout nejlepší možné propustnosti zápisu. Stejně jako u všech scénářů ve velkém měřítku se dají vyřešit některé výzvy, které je možné vyřešit pomocí správných konfigurací. Tato část pojednává o konfiguracích pro několik běžných výstupů a obsahuje ukázky pro udržení sazeb ingestování 1 tisíc, 5K a 10 000 událostí za sekundu.

Následující poznámky používají úlohu Stream Analytics s dotazem bez stavu (Passthrough), základní jazyk JavaScript UDF, který zapisuje do centra událostí, Azure SQL DB nebo Cosmos DB.

#### <a name="event-hub"></a>Centrum událostí

|Rychlost přijímání zpráv (události za sekundu) | Jednotky streamování | Výstupní prostředky  |
|--------|---------|---------|
| 1 tis.     |    1    |  2 TU   |
| 5 000     |    6    |  6\.   |
| 10 000    |    12   |  10 Z  |

Řešení [centra událostí](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs) se škáluje lineárně v podobě jednotek streamování (SU) a propustnosti, takže je nejúčinnější a výkonný způsob analýzy a streamování dat z Stream Analytics. Úlohy je možné škálovat až do 192 SU, což zhruba souvisí se zpracováním až 200 MB/s, nebo 19 000 000 000 000 událostí za den.

#### <a name="azure-sql"></a>Azure SQL
|Rychlost přijímání zpráv (události za sekundu) | Jednotky streamování | Výstupní prostředky  |
|---------|------|-------|
|    1 tis.   |   3  |  S3   |
|    5 000   |   18 |  P4   |
|    10 000  |   36 |  P6   |

[Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql) podporuje zapisování paralelně, označované jako dědění oddílů, ale není ve výchozím nastavení povolené. Povolení dědění rozdělení na oddíly, společně s plně paralelním dotazem, ale nemusí být dostačující pro dosažení vyšší propustnosti. Propustnost zápisu SQL závisí významně na konfiguraci SQL Azure databáze a schématu tabulek. Článek o [výkonu SQL Output](./stream-analytics-sql-output-perf.md) obsahuje další podrobnosti o parametrech, které můžou maximalizovat propustnost zápisu. Jak je uvedeno ve [výstupu Azure Stream Analytics Azure SQL Database](./stream-analytics-sql-output-perf.md#azure-stream-analytics) článku, toto řešení se neškáluje lineárně jako plně paralelní kanál nad rámec 8 oddílů a může vyžadovat přerozdělení do výstupu SQL (viz [do](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)). Skladové jednotky úrovně Premium se potřebují pro udržení vysokého vstupně-výstupních operací spolu se režiemi ze záloh protokolů při každém několika minutách.

#### <a name="cosmos-db"></a>Databáze Cosmos
|Rychlost přijímání zpráv (události za sekundu) | Jednotky streamování | Výstupní prostředky  |
|-------|-------|---------|
|  1 tis.   |  3    | 20 TISÍC RU  |
|  5 000   |  24   | 60K RU  |
|  10 000  |  48   | 120K RU |

[Cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) výstup z Stream Analytics byl aktualizován tak, aby používal nativní integraci do [úrovně kompatibility 1,2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12). Úroveň kompatibility 1,2 umožňuje významně vyšší propustnost a snižuje spotřebu RU v porovnání s 1,1, což je výchozí úroveň kompatibility pro nové úlohy. Toto řešení využívá kontejnery CosmosDB rozdělené na/deviceId a zbytek řešení je identický nakonfigurované.

Veškeré [streamování ve zkušebních ukázkách Azure](https://github.com/Azure-Samples/streaming-at-scale) používá ke vstupnímu zatížení simulaci centra událostí. Každá vstupní událost je dokument 1 KB JSON, který překládá nakonfigurovanou rychlost přijímání do propustnosti (1 MB/s, 5 MB/s a 10 MB/s) snadno. Události simulují zařízení IoT odesílající následující data JSON (ve zkrácené formě) až do zařízení 1 tisíc:

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
> Konfigurace se mohou měnit v důsledku různých komponent používaných v řešení. Pokud chcete přesnější odhad, přizpůsobte si ukázky podle svého scénáře.

### <a name="identifying-bottlenecks"></a>Identifikace kritických bodů

Pomocí podokna metrik v Azure Stream Analytics úlohy můžete identifikovat kritická místa ve vašem kanálu. Zkontrolujte **vstupní/výstupní události** pro propustnost a ["zpoždění vodoznaku"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) nebo **nevyřízené události** , abyste viděli, jestli úloha nepracuje se vstupní sazbou. V případě metrik centra událostí vyhledejte **omezené požadavky** a odpovídajícím způsobem upravte prahové jednotky. V případě Cosmos DB metriky si přečtěte **maximální počet spotřebovaných ru/s na rozsah klíče oddílu** propustnost, abyste zajistili, že rozsahy klíčů oddílu budou jednotně spotřebovány. V případě služby Azure SQL DB Sledujte **protokol IO** a **CPU**.

## <a name="get-help"></a>Podpora

Pokud potřebujete další pomoc, vyzkoušejte naši [Azure Stream Analytics Fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
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

