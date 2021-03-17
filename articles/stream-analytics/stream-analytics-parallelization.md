---
title: Použití paralelního dotazování a škálování v Azure Stream Analytics
description: Tento článek popisuje, jak škálovat úlohy Stream Analytics konfigurací vstupních oddílů, vyladěním definice dotazu a nastavením jednotek streamování úloh.
author: JSeb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 9149413d070bbb5eb8d0f8d0c99fe5ff705bcefb
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012321"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Využití paralelismu dotazů v Azure Stream Analytics
V tomto článku se dozvíte, jak využít paralelismus v Azure Stream Analytics. Naučíte se, jak škálovat Stream Analytics úlohy konfigurací vstupních oddílů a optimalizací definice analytického dotazu.
Je možné, že budete chtít být obeznámeni s pojmem jednotky streamování popsané v tématu [pochopení a úprava jednotek streamování](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Jaké jsou části Stream Analytics úlohy?
Definice úlohy Stream Analytics zahrnuje aspoň jeden vstup streamování, dotaz a výstup. Vstupy jsou místo, odkud úloha čte datový proud z. Dotaz slouží k transformaci vstupního datového proudu a výstup je, kde úloha odešle výsledky úlohy do.

## <a name="partitions-in-inputs-and-outputs"></a>Oddíly ve vstupech a výstupech
Dělení umožňuje rozdělit data na podmnožiny na základě [klíče oddílu](../event-hubs/event-hubs-scalability.md#partitions). Pokud je vaše zadání (například Event Hubs) rozdělené podle klíče, důrazně doporučujeme tento klíč oddílu zadat při přidávání vstupu do úlohy Stream Analytics. Škálování Stream Analytics úlohy využívá oddíly ve vstupu a výstupu. Stream Analytics úloha může spotřebovávat a zapisovat různé oddíly paralelně, což zvyšuje propustnost. 

### <a name="inputs"></a>Vstupy
Všechny vstupy Azure Stream Analytics můžou využít dělení na oddíly:
-   EventHub (nutnost nastavení klíče oddílu explicitně pomocí klíčového slova PARTITION BY, pokud se používá úroveň kompatibility 1,1 nebo nižší)
-   IoT Hub (je potřeba nastavit klíč oddílu explicitně pomocí klíčového slova PARTITION BY, pokud se používá úroveň kompatibility 1,1 nebo nižší)
-   Blob Storage

### <a name="outputs"></a>Výstupy

Při práci s Stream Analytics můžete využívat dělení ve výstupech:
-   Azure Data Lake Storage
-   Azure Functions
-   Tabulka Azure
-   Úložiště objektů BLOB (klíč oddílu můžete explicitně nastavit)
-   Cosmos DB (je potřeba nastavit klíč oddílu explicitně)
-   Event Hubs (je potřeba nastavit klíč oddílu explicitně)
-   IoT Hub (je potřeba nastavit klíč oddílu explicitně)
-   Service Bus
- SQL a Azure synapse Analytics s volitelným rozdělením na oddíly: Další informace najdete na [stránce výstup do Azure SQL Database](./stream-analytics-sql-output-perf.md).

Power BI nepodporuje dělení. Můžete však stále rozdělit vstup, jak je popsáno v [této části](#multi-step-query-with-different-partition-by-values) . 

Další informace o oddílech najdete v následujících článcích:

* [Přehled funkcí Event Hubs](../event-hubs/event-hubs-features.md#partitions)
* [Dělení dat](/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Paralelní úlohy zpracovatelné
*Zpracovatelné paralelní* úloha je nejškálovatelný scénář v Azure Stream Analytics. Připojí jeden oddíl vstupu k jedné instanci dotazu k jednomu oddílu výstupu. Tento paralelismus má následující požadavky:

1. Pokud vaše logika dotazu závisí na stejném klíči, který je zpracováván stejnou instancí dotazu, je nutné zajistit, aby události přešly do stejného oddílu vašeho vstupu. Pro Event Hubs nebo IoT Hub to znamená, že data události musí mít nastavenou hodnotu **PartitionKey** . Alternativně můžete použít rozdělené odesílatele. Pro úložiště objektů blob to znamená, že se události odesílají do stejné složky oddílu. Příkladem může být instance dotazu, která agreguje data na ID uživatele, kde je vstupní centrum událostí dělené jako klíč oddílu pomocí userID. Pokud však logika dotazu nevyžaduje, aby se stejný klíč zpracoval pomocí stejné instance dotazu, můžete tento požadavek ignorovat. Příkladem této logiky je jednoduchý dotaz SELECT-Project-Filter.  

2. Dalším krokem je vytvoření oddílů dotazu. Pro úlohy s úrovní kompatibility 1,2 nebo vyšší (doporučeno) je možné zadat vlastní sloupec jako klíč oddílu ve vstupním nastavení a úloha bude paralellized automaticky. Úlohy s úrovní kompatibility 1,0 nebo 1,1 vyžadují, abyste v všech krocích dotazu používali **partition by PartitionID** . Je povoleno více kroků, ale všechny musí být rozděleny stejným klíčem. 

3. Většina výstupů podporovaných v Stream Analytics může využít dělení. Pokud použijete typ výstupu, který nepodporuje vytváření oddílů vaší úlohy, nebude *zpracovatelné paralelní*. Pro výstupy centra událostí se ujistěte, že je **sloupec klíč oddílu** nastavený na stejný klíč oddílu, který se používá v dotazu. Další podrobnosti najdete v [části s výstupem](#outputs) .

4. Počet vstupních oddílů musí být stejný jako počet výstupních oddílů. Výstup služby Blob Storage může podporovat oddíly a zdědí schéma dělení nadřazeného dotazu. Když je zadaný klíč oddílu pro úložiště objektů blob, budou se data rozdělit na oddíly na vstupním oddílu, takže výsledek bude pořád plně paralelní. Tady jsou příklady hodnot oddílů, které umožňují plně paralelní úlohu:

   * 8 vstupních oddílů centra událostí a 8 výstupních oddílů centra událostí
   * 8 vstupních oddílů centra událostí a výstupu služby Blob Storage
   * 8 vstupních oddílů centra událostí a výstupu služby Blob Storage dělené vlastním polem s libovolnou mohutnosti
   * 8 vstupních oddílů služby Blob Storage a výstupu služby Blob Storage
   * 8 vstupních oddílů služby Blob Storage a 8 výstupních oddílů centra událostí

Následující části popisují některé příklady scénářů, které jsou zpracovatelné paralelní.

### <a name="simple-query"></a>Jednoduchý dotaz

* Vstup: centrum událostí s 8 oddíly
* Výstup: centrum událostí s 8 oddíly ("sloupec klíče oddílu" musí být nastaven na použití "PartitionId")

Dotaz:

```SQL
    --Using compatibility level 1.2 or above
    SELECT TollBoothId
    FROM Input1
    WHERE TollBoothId > 100
    
    --Using compatibility level 1.0 or 1.1
    SELECT TollBoothId
    FROM Input1 PARTITION BY PartitionId
    WHERE TollBoothId > 100
```

Tento dotaz je jednoduchým filtrem. Proto se nemusíte starat o dělení vstupu, který se odesílá do centra událostí. Všimněte si, že úlohy s úrovní kompatibility před 1,2 musí zahrnovat **oddíl podle identifikátoru PartitionID** , takže splní #2 požadavku ze starší verze. Pro výstup musíme v úloze nakonfigurovat výstup centra událostí tak, aby byl klíč oddílu nastavený na **PartitionID**. Od poslední kontroly se ujistěte, že počet vstupních oddílů je stejný jako počet výstupních oddílů.

### <a name="query-with-a-grouping-key"></a>Dotaz s klíčem seskupení

* Vstup: centrum událostí s 8 oddíly
* Výstup: BLOB Storage

Dotaz:

```SQL
    --Using compatibility level 1.2 or above
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    
    --Using compatibility level 1.0 or 1.1
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Tento dotaz obsahuje klíč seskupení. Proto se události seskupené dohromady musí odeslat do stejného oddílu centra událostí. Vzhledem k tomu, že v tomto příkladu budeme seskupovat podle TollBoothID, měli byste se ujistit, že se při posílání událostí do centra událostí používá jako klíč oddílu TollBoothID. Potom můžeme v ASA použít **oddíl podle identifikátoru PartitionID** k dědění z tohoto schématu oddílu a povolení úplného paralelismu. Vzhledem k tomu, že výstupem je úložiště objektů blob, nemusíte si dělat starosti s konfigurací hodnoty klíče oddílu, jak #4 podle požadavků.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Příklady scénářů, které *nejsou zpracovatelné paralelní*

V předchozí části jsme ukázali, že jsme zpracovatelné paralelní scénáře. V této části se podíváme na scénáře, které nesplňují všechny požadavky zpracovatelné Parallel. 

### <a name="mismatched-partition-count"></a>Počet neodpovídajících oddílů
* Vstup: centrum událostí s 8 oddíly
* Výstup: centrum událostí s 32 oddíly

Pokud se počet vstupních oddílů neshoduje s počtem výstupních oddílů, topologie nebude zpracovatelné paralelně bez ohledu na dotaz. Pořád ale můžeme získat nějakou úroveň nebo paralelní zpracování.

### <a name="query-using-non-partitioned-output"></a>Dotazování pomocí nerozděleného výstupu
* Vstup: centrum událostí s 8 oddíly
* Výstup: Power BI

Výstup Power BI v současné době nepodporuje dělení. Proto tento scénář není zpracovatelné paralelně.

### <a name="multi-step-query-with-different-partition-by-values"></a>Dotaz na více kroků s různými hodnotami oddílů
* Vstup: centrum událostí s 8 oddíly
* Výstup: centrum událostí s 8 oddíly
* Úroveň kompatibility: 1,0 nebo 1,1

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

Jak vidíte, druhý krok používá **TollBoothId** jako klíč rozdělení do oddílů. Tento krok není stejný jako první krok, a proto je pro nás potřeba provést náhodné provedení. 

### <a name="multi-step-query-with-different-partition-by-values"></a>Dotaz na více kroků s různými hodnotami oddílů
* Vstup: centrum událostí s 8 oddíly
* Výstup: centrum událostí s 8 oddíly ("sloupec klíče oddílu" musí být nastaven na použití "TollBoothId")
* Úroveň kompatibility – 1,2 nebo vyšší

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

Úroveň kompatibility 1,2 nebo vyšší umožňuje spuštění paralelního dotazu ve výchozím nastavení. Například dotaz z předchozí části bude rozdělený tak dlouho, dokud je sloupec "TollBoothId" nastaven jako klíč vstupního oddílu. Klauzule PARTITION BY PartitionId není povinná.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Vypočítat maximální počet jednotek streamování úlohy
Celkový počet jednotek streamování, které může úloha Stream Analytics použít, závisí na počtu kroků v dotazu definovaném pro úlohu a na počtu oddílů pro každý krok.

### <a name="steps-in-a-query"></a>Kroky v dotazu
Dotaz může mít jeden nebo několik kroků. Každý krok je poddotaz definovaný pomocí klíčového slova **with** . Dotaz, který je mimo klíčové slovo **with** (pouze jeden dotaz), se také počítá jako krok, například příkaz **Select** v následujícím dotazu:

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
> Tento dotaz se podrobněji popisuje dále v článku.
>  

### <a name="partition-a-step"></a>Oddíl a krok
Rozdělení kroku na oddíly vyžaduje tyto podmínky:

* Vstupní zdroj musí být rozdělený na oddíly. 
* Příkaz **Select** dotazu musí číst z rozděleného vstupního zdroje.
* Dotaz v kroku musí mít **oddíl podle** klíčového slova.

Když je dotaz rozdělený na oddíly, vstupní události se zpracují a agreguje v samostatných skupinách oddílů a pro každou skupinu se vygenerují události s výstupem. Pokud chcete kombinovat agregaci, musíte pro agregaci vytvořit druhý krok bez oddílů.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Vypočítat maximální počet jednotek streamování pro úlohu
Všechny kroky, které nejsou rozdělené do oddílů, můžou společně škálovat až šest jednotek streamování (SUs) pro úlohu Stream Analytics. Kromě toho můžete přidat 6 služby SUs pro každý oddíl do děleného kroku.
V následující tabulce vidíte některé **Příklady** .

| Dotaz                                               | Maximální služba SUs pro úlohu |
| --------------------------------------------------- | ------------------- |
| <ul><li>Dotaz obsahuje jeden krok.</li><li>Tento krok není rozdělený.</li></ul> | 6 |
| <ul><li>Vstupní datový proud je rozdělen o 16.</li><li>Dotaz obsahuje jeden krok.</li><li>Tento krok je rozdělený na oddíly.</li></ul> | 96 (6 × 16 oddílů) |
| <ul><li>Dotaz obsahuje dva kroky.</li><li>Ani jeden z kroků není rozdělený.</li></ul> | 6 |
| <ul><li>Vstupní datový proud je rozdělen podle 3.</li><li>Dotaz obsahuje dva kroky. Vstupní krok je rozdělený na oddíly a druhý krok ne.</li><li>Příkaz <strong>Select</strong> načte z rozděleného vstupu.</li></ul> | 24 (18 pro dělené kroky + 6 pro kroky bez oddílů) |

### <a name="examples-of-scaling"></a>Příklady škálování

Následující dotaz vypočítá počet vozidel v rámci tří minut, který prochází telefonní stanicí, která má tři tollbooths. Tento dotaz se dá škálovat až na šest SUs.

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Aby bylo možné použít pro dotaz více SUs, musí být vstupní datový proud i dotaz rozděleny na oddíly. Vzhledem k tomu, že oddíl datového proudu je nastavený na hodnotu 3, může se škálovat následující upravený dotaz až o 18 SUs:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Když je dotaz rozdělený na oddíly, vstupní události se zpracují a agreguje do samostatných skupin oddílů. Výstupní události jsou také generovány pro každou skupinu. Dělení může způsobit neočekávané výsledky, když pole **Seskupit podle** není klíč oddílu ve vstupním datovém proudu. Například pole **TollBoothId** v předchozím dotazu není klíčem oddílu **Input1**. Výsledkem je, že data z TollBooth #1 lze rozložit do více oddílů.

Každý z oddílů **Input1** se zpracuje samostatně pomocí Stream Analytics. Výsledkem je, že se vytvoří několik záznamů o počtu automobilů pro stejné tollboothy ve stejném Bubnovém okně. Pokud se klíč vstupního oddílu nedá změnit, můžete tento problém vyřešit přidáním kroku, který není rozdělený na oddíly pro agregaci hodnot napříč oddíly, jako v následujícím příkladu:

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

Tento dotaz lze škálovat na 24 SUs.

> [!NOTE]
> Pokud se připojujete ke dvěma datovým proudům, ujistěte se, že jsou datové proudy rozdělené podle klíče oddílu sloupce, který používáte k vytvoření spojení. Také se ujistěte, že v obou datových proudech máte stejný počet oddílů.
> 
> 

## <a name="achieving-higher-throughputs-at-scale"></a>Dosažení vyšších propustností ve velkém měřítku

[Zpracovatelné paralelní](#embarrassingly-parallel-jobs) úloha je nutná, ale není dostatečná pro udržení vyšší propustnosti ve velkém měřítku. Každý systém úložiště a příslušný výstup Stream Analytics obsahuje variace toho, jak dosáhnout nejlepší možné propustnosti zápisu. Stejně jako u všech scénářů ve velkém měřítku se dají vyřešit některé výzvy, které je možné vyřešit pomocí správných konfigurací. Tato část pojednává o konfiguracích pro několik běžných výstupů a obsahuje ukázky pro udržení sazeb ingestování 1 tisíc, 5K a 10 000 událostí za sekundu.

Následující poznámky používají úlohu Stream Analytics s dotazem bez stavu (Passthrough), základní jazyk JavaScript UDF, který zapisuje do centra událostí, Azure SQL DB nebo Cosmos DB.

#### <a name="event-hub"></a>Centrum událostí

|Rychlost přijímání zpráv (události za sekundu) | Jednotky streamování | Výstupní prostředky  |
|--------|---------|---------|
| 1 tis.     |    1    |  2 TU   |
| 5K     |    6    |  6.   |
| 10K    |    12   |  10 Z  |

Řešení [centra událostí](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-eventhubs) se škáluje lineárně v podobě jednotek streamování (SU) a propustnosti, takže je nejúčinnější a výkonný způsob analýzy a streamování dat z Stream Analytics. Úlohy je možné škálovat až do 192 SU, což zhruba souvisí se zpracováním až 200 MB/s, nebo 19 000 000 000 000 událostí za den.

#### <a name="azure-sql"></a>Azure SQL
|Rychlost přijímání zpráv (události za sekundu) | Jednotky streamování | Výstupní prostředky  |
|---------|------|-------|
|    1 tis.   |   3  |  S3   |
|    5K   |   18 |  P4   |
|    10K  |   36 |  P6   |

[Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-azuresql)  podporuje zapisování paralelně, označované jako dědění oddílů, ale není ve výchozím nastavení povolené. Povolení dědění rozdělení na oddíly, společně s plně paralelním dotazem, ale nemusí být dostačující pro dosažení vyšší propustnosti. Propustnost zápisu SQL závisí významně na konfiguraci databáze a schématu tabulek. Článek o [výkonu SQL Output](./stream-analytics-sql-output-perf.md) obsahuje další podrobnosti o parametrech, které můžou maximalizovat propustnost zápisu. Jak je uvedeno ve [výstupu Azure Stream Analytics Azure SQL Database](./stream-analytics-sql-output-perf.md#azure-stream-analytics) článku, toto řešení se neškáluje lineárně jako plně paralelní kanál nad rámec 8 oddílů a může vyžadovat přerozdělení do výstupu SQL (viz [do](/stream-analytics-query/into-azure-stream-analytics#into-shard-count)). Skladové jednotky úrovně Premium se potřebují pro udržení vysokého vstupně-výstupních operací spolu se režiemi ze záloh protokolů při každém několika minutách.

#### <a name="cosmos-db"></a>Databáze Cosmos
|Rychlost přijímání zpráv (události za sekundu) | Jednotky streamování | Výstupní prostředky  |
|-------|-------|---------|
|  1 tis.   |  3    | 20 TISÍC RU  |
|  5K   |  24   | 60K RU  |
|  10K  |  48   | 120K RU |

[Cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-cosmosdb) výstup z Stream Analytics byl aktualizován tak, aby používal nativní integraci do [úrovně kompatibility 1,2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12). Úroveň kompatibility 1,2 umožňuje významně vyšší propustnost a snižuje spotřebu RU v porovnání s 1,1, což je výchozí úroveň kompatibility pro nové úlohy. Toto řešení využívá kontejnery CosmosDB rozdělené na/deviceId a zbytek řešení je identický nakonfigurované.

Všechny [datové proudy ve zkušebních ukázkách Azure](https://github.com/Azure-Samples/streaming-at-scale) používají jako vstup centrum událostí jako vstup, který je dodaný zátěží pro simulaci testovacích klientů. Každá vstupní událost je dokument 1 KB JSON, který překládá nakonfigurovanou rychlost přijímání do propustnosti (1 MB/s, 5 MB/s a 10 MB/s) snadno. Události simulují zařízení IoT odesílající následující data JSON (ve zkrácené formě) až do zařízení 1 tisíc:

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

Pokud chcete získat další pomoc, vyzkoušejte si naši [stránku Microsoft Q&Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Další kroky
* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](/rest/api/streamanalytics/)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/