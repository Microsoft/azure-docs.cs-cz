---
title: Použití paralelizace a škálování dotazů v Azure Stream Analytics
description: Tento článek popisuje, jak škálovat úlohy Stream Analytics konfigurací vstupních oddílů, laděním definice dotazu a nastavením jednotek streamování úloh.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 31ac43ec796d305b8a8f4b62ea09481e262b6b3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256976"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Využití paralelizace dotazů ve službě Azure Stream Analytics
Tento článek ukazuje, jak využít výhod paralelizace v Azure Stream Analytics. Dozvíte se, jak škálovat úlohy Stream Analytics konfigurací vstupních oddílů a laděním definice analytického dotazu.
Jako předpoklad můžete chtít být obeznámeni s pojmem jednotky streamování popsané v [pochopit a upravit jednotky streamování](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Jaké jsou součásti úlohy Stream Analytics?
Definice úlohy Stream Analytics zahrnuje vstupy, dotaz a výstup. Vstupy jsou, kde úloha čte datový proud z. Dotaz se používá k transformaci vstupnídatový proud dat a výstup je místo, kam úloha odešle výsledky úlohy.

Úloha vyžaduje alespoň jeden vstupní zdroj pro streamování dat. Vstupní zdroj datového proudu se dá uložit v centru událostí Azure nebo v úložišti objektů blob Azure. Další informace najdete [v tématu Úvod do Azure Stream Analytics](stream-analytics-introduction.md) a [začínáme používat Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-sources-and-sinks"></a>Oddíly ve zdrojích a propadech
Škálování úlohy Stream Analytics využívá oddíly ve vstupu nebo výstupu. Dělení umožňuje rozdělit data do podmnožiny na základě klíče oddílu. Proces, který spotřebovává data (například úlohu Analýzy streamování) může využívat a zapisovat různé oddíly paralelně, což zvyšuje propustnost. 

### <a name="inputs"></a>Vstupy
Všechny vstupy Azure Stream Analytics můžou využít rozdělení:
-   EventHub (je třeba explicitně nastavit klíč oddílu pomocí klíčového slova PARTITION BY)
-   IoT Hub (je třeba explicitně nastavit klíč oddílu pomocí klíčového slova PARTITION BY)
-   Blob Storage

### <a name="outputs"></a>Výstupy

Při práci s Stream Analytics můžete využít rozdělení výstupů:
-   Azure Data Lake Storage
-   Azure Functions
-   Tabulka Azure
-   Úložiště objektů blob (explicitně můžete nastavit klíč oddílu)
-   Cosmos DB (je třeba explicitně nastavit klíč oddílu)
-   Rozbočovače událostí (je třeba explicitně nastavit klíč oddílu)
-   IoT Hub (je třeba explicitně nastavit klíč oddílu)
-   Service Bus
- SQL a SQL Datový sklad s volitelným dělením: další informace [najdete](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf)na stránce Output to Azure SQL Database .

Power BI nepodporuje dělení. Vstup však stále můžete rozdělit, jak je popsáno v [této části.](#multi-step-query-with-different-partition-by-values) 

Další informace o oddílech naleznete v následujících článcích:

* [Přehled funkcí Event Hubs](../event-hubs/event-hubs-features.md#partitions)
* [Dělení dat](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Trapně paralelní úlohy
*Trapně paralelní* úloha je nejškálovatelnější scénář, který máme ve službě Azure Stream Analytics. Připojí jeden oddíl vstupu k jedné instanci dotazu k jednomu oddílu výstupu. Tento paralelismus má následující požadavky:

1. Pokud logika dotazu závisí na stejném klíči zpracovávaném stejnou instancí dotazu, musíte se ujistit, že události přejdou do stejného oddílu vašeho vstupu. Pro centra událostí nebo IoT Hub to znamená, že data události musí mít nastavenou hodnotu **PartitionKey.** Případně můžete použít dělené odesílatele. Pro úložiště objektů blob to znamená, že události jsou odesílány do stejné složky oddílu. Pokud logika dotazu nevyžaduje stejný klíč, který má být zpracován stejnou instancí dotazu, můžete tento požadavek ignorovat. Příkladem této logiky by byl jednoduchý dotaz select-project-filter.  

2. Jakmile jsou data rozložena na vstupní straně, musíte se ujistit, že dotaz je rozdělen na oddíly. To vyžaduje, abyste ve všech krocích používali **PARTITION BY.** Více kroků jsou povoleny, ale všechny musí být rozděleny stejným klíčem. Podle úrovně kompatibility 1.0 a 1.1 musí být klíč dělení nastaven na **PartitionId,** aby úloha byla plně rovnoběžná. Pro úlohy s úrovní kompatibility 1.2 a vyšší, vlastní sloupec lze zadat jako klíč oddílu ve vstupním nastavení a úloha bude paralellized automaticky i bez partition by klauzule. Pro výstup rozbočovače událostí musí být vlastnost "Sloupec klíče oddílu" nastavena na použití "PartitionId".

3. Většina našeho výstupu může využít dělení, ale pokud používáte typ výstupu, který nepodporuje dělení úlohy nebude zcela paralelní. Pro výstupy centra událostí se ujistěte, že **sloupec klíče oddílu** je nastaven stejně jako klíč oddílu dotazu. Další podrobnosti naleznete v [části s výstupem.](#outputs)

4. Počet vstupních oddílů se musí rovnat počtu výstupních oddílů. Výstup úložiště objektů blob může podporovat oddíly a dědí schéma dělení upstream dotazu. Pokud je zadán klíč oddílu pro úložiště objektů Blob, data jsou rozdělena na jeden vstupní oddíl, takže výsledek je stále plně paralelní. Zde jsou příklady hodnot oddílů, které umožňují plně paralelní úlohu:

   * 8 vstupních oddílů rozbočovače událostí a 8 výstupních oddílů centra událostí
   * 8 vstupních oddílů centra událostí a výstup úložiště objektů blob
   * 8 vstupních oddílů centra událostí a výstup úložiště objektů blob rozdělený podle vlastního pole s libovolnou mohutností
   * 8 vstupních oddílů úložiště blob a výstup úložiště objektů blob
   * 8 vstupních oddílů úložiště objektů blob a 8 výstupních oddílů centra událostí

Následující části popisují některé příklady scénářů, které jsou trapně paralelní.

### <a name="simple-query"></a>Jednoduchý dotaz

* Vstup: Centrum událostí s 8 oddíly
* Výstup: Rozbočovač událostí s 8 oddíly ("sloupec klíče oddílu" musí být nastaven na použití "PartitionId")

Dotaz:

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

Tento dotaz je jednoduchý filtr. Proto se nemusíme starat o rozdělení vstupu, který je odesílán do centra událostí. Všimněte si, že úlohy s úrovní kompatibility před 1.2 musí obsahovat **klauzuli PARTITION BY PartitionId,** takže splňuje požadavek #2 z dřívějších. Pro výstup musíme nakonfigurovat výstup rozbočovače událostí v úloze, aby byl klíč oddílu nastaven na **PartitionId**. Poslední kontrola je ujistěte se, že počet vstupních oddílů se rovná počtu výstupních oddílů.

### <a name="query-with-a-grouping-key"></a>Dotaz pomocí klíče seskupení

* Vstup: Centrum událostí s 8 oddíly
* Výstup: Úložiště objektů blob

Dotaz:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Tento dotaz má klíč seskupení. Proto události seskupené dohromady musí být odeslány do stejného oddílu centra událostí. Vzhledem k tomu, že v tomto příkladu jsme seskupí tollBoothID, měli bychom si být jisti, že TollBoothID se používá jako klíč oddílu při odeslání událostí do Centra událostí. Pak v ASA, můžeme použít **PARTITION BY PartitionId** dědit z tohoto schématu oddílu a povolit úplnou paralelizaci. Vzhledem k tomu, že výstup je úložiště objektů blob, nemusíme se starat o konfiguraci hodnoty klíče oddílu podle požadavku #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Příklad scénářů, které *nejsou* trapně paralelní

V předchozí části jsme ukázali některé trapně paralelní scénáře. V této části probereme scénáře, které nesplňují všechny požadavky, které mají být trapně paralelní. 

### <a name="mismatched-partition-count"></a>Počet neodpovídajících oddílů
* Vstup: Centrum událostí s 8 oddíly
* Výstup: Rozbočovač událostí s 32 oddíly

V tomto případě nezáleží na tom, co je dotaz. Pokud počet vstupních oddílů neodpovídá počtu výstupních oddílů, topologie není trapně paralelní.+ Přesto můžeme získat určitou úroveň nebo paralelizaci.

### <a name="query-using-non-partitioned-output"></a>Dotaz pomocí výstupu bez oddílů
* Vstup: Centrum událostí s 8 oddíly
* Výstup: Power BI

Výstup Power BI momentálně nepodporuje dělení. Proto tento scénář není trapně paralelní.

### <a name="multi-step-query-with-different-partition-by-values"></a>Vícekrokový dotaz s různými hodnotami PARTITION BY
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

Jak můžete vidět, druhý krok používá **TollBoothId** jako klíč dělení. Tento krok není stejný jako první krok, a proto vyžaduje, abychom udělali shuffle. 

Předchozí příklady ukazují některé úlohy Služby Stream Analytics, které odpovídají (nebo neodpovídají) trapně paralelní topologii. Pokud se vsouladu, mají potenciál pro maximální měřítko. Pro úlohy, které se nevejdou jeden z těchto profilů, škálování pokyny budou k dispozici v budoucích aktualizacích. Prozatím použijte obecné pokyny v následujících částech.

### <a name="compatibility-level-12---multi-step-query-with-different-partition-by-values"></a>Úroveň kompatibility 1.2 – vícekrokový dotaz s různými hodnotami PARTITION BY 
* Vstup: Centrum událostí s 8 oddíly
* Výstup: Rozbočovač událostí s 8 oddíly ("sloupec klíče oddílu" musí být nastaven na použití "TollBoothId")

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

Úroveň kompatibility 1.2 umožňuje paralelní spuštění dotazu ve výchozím nastavení. Například dotaz z předchozí části bude rozdělen a to tak dlouho, dokud "TollBoothId" sloupec je nastaven jako vstupní klíč oddílu. Klauzule PARTITION BY PartitionId není vyžadována.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Výpočet maximálního počtu jednotek streamování úlohy
Celkový počet jednotek streamování, které lze použít úlohu Stream Analytics, závisí na počtu kroků v dotazu definovaném pro úlohu a na počtu oddílů pro každý krok.

### <a name="steps-in-a-query"></a>Kroky v dotazu
Dotaz může mít jeden nebo více kroků. Každý krok je poddotaz definovaný klíčovým slovem **WITH.** Dotaz, který je mimo klíčové slovo **WITH** (pouze jeden dotaz) se také počítá jako krok, jako je například **příkaz SELECT** v následujícím dotazu:

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
> Tento dotaz je popsán podrobněji dále v článku.
>  

### <a name="partition-a-step"></a>Rozdělení kroku
Rozdělení kroku vyžaduje následující podmínky:

* Vstupní zdroj musí být rozdělen na oddíly. 
* **Příkaz SELECT** dotazu musí číst z rozděleného vstupního zdroje.
* Dotaz v rámci kroku musí mít klíčové slovo **PARTITION BY.**

Při dělení dotazu jsou vstupní události zpracovány a agregovány v samostatných skupinách oddílů a pro každou skupinu jsou generovány výstupní události. Pokud chcete kombinovaný agregace, musíte vytvořit druhý krok bez oddílů agregovat.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Výpočet maximálních jednotek streamování pro úlohu
Všechny kroky bez oddílů společně můžete škálovat až šest jednotek streamování (SU) pro úlohu Stream Analytics. Kromě toho můžete přidat 6 su pro každý oddíl v dělený krok.
Některé **příklady** si můžete prohlédnout v následující tabulce.

| Dotaz                                               | Maximální su pro tuto práci |
| --------------------------------------------------- | ------------------- |
| <ul><li>Dotaz obsahuje jeden krok.</li><li>Krok není rozdělen a oddíly.</li></ul> | 6 |
| <ul><li>Vstupní datový proud je rozdělen na 16.</li><li>Dotaz obsahuje jeden krok.</li><li>Krok je rozdělen na oddíly.</li></ul> | 96 (6 * 16 oddílů) |
| <ul><li>Dotaz obsahuje dva kroky.</li><li>Ani jeden z kroků je rozdělena.</li></ul> | 6 |
| <ul><li>Vstupní datový proud je rozdělen na 3.</li><li>Dotaz obsahuje dva kroky. Vstupní krok je rozdělen a druhý krok není.</li><li>Příkaz <strong>SELECT</strong> čte z rozděleného vstupu.</li></ul> | 24 (18 pro kroky rozdělené na oddíly + 6 pro kroky bez rozdělení |

### <a name="examples-of-scaling"></a>Příklady změny měřítka

Následující dotaz vypočítá počet vozů v rámci tříminutového okna procházejícího mýtnou stanicí, která má tři mýtné budky. Tento dotaz lze škálovat až na šest su.

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Chcete-li použít více su pro dotaz, vstupní datový proud a dotaz musí být rozděleny do oddílů. Vzhledem k tomu, že oddíl datového proudu je nastavena na 3, následující upravený dotaz lze škálovat až na 18 su:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Při rozdělení dotazu jsou vstupní události zpracovány a agregovány v samostatných skupinách oddílů. Výstupní události jsou také generovány pro každou ze skupin. Dělení může způsobit některé neočekávané výsledky, pokud pole **GROUP BY** není klíč oddílu ve vstupním datovém proudu. Například **TollBoothId** pole v předchozím dotazu není klíč oddílu **Input1**. Výsledkem je, že data z Toll #1Booth mohou být rozložena do více oddílů.

Každý oddíl **YIn1** bude zpracován samostatně službou Stream Analytics. V důsledku toho bude vytvořeno více záznamů o počtu automobilů pro stejnou mýtnou budku ve stejném okně Omílání. Pokud klíč vstupního oddílu nelze změnit, tento problém lze vyřešit přidáním kroku bez oddílu k agregaci hodnot napříč oddíly, jako v následujícím příkladu:

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

Tento dotaz lze škálovat na 24 su.

> [!NOTE]
> Pokud připojujete dva datové proudy, ujistěte se, že datové proudy jsou rozděleny podle klíče oddílu sloupce, který používáte k vytvoření spojení. Také se ujistěte, že máte stejný počet oddílů v obou datových proudech.
> 
> 

## <a name="achieving-higher-throughputs-at-scale"></a>Dosažení vyšších propustnosti ve velkém měřítku

[Trapně paralelní](#embarrassingly-parallel-jobs) práce je nezbytná, ale ne dostatečná k udržení vyšší propustnosti ve velkém měřítku. Každý úložný systém a jeho odpovídající výstup Stream Analytics má variace na to, jak dosáhnout nejlepší možné propustnost zápisu. Stejně jako u každého scénáře ve velkém měřítku existují některé problémy, které lze vyřešit pomocí správných konfigurací. Tato část popisuje konfigurace pro několik běžných výstupů a poskytuje ukázky pro udržení rychlosti ingestování událostí 1 kB, 5 kB a 10 kB za sekundu.

Následující pozorování používají úlohu Stream Analytics s bezstavovým (průchozím) dotazem, což je základní UDF JavaScript, který zapisuje do centra událostí, Azure SQL DB nebo Cosmos DB.

#### <a name="event-hub"></a>Centrum událostí

|Rychlost požití (události za sekundu) | Jednotky streamování | Výstupní zdroje  |
|--------|---------|---------|
| 1 tis.     |    1    |  2 TU   |
| 5K     |    6    |  6 TU   |
| 10K    |    12   |  10 TU  |

Řešení [Event Hub](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs) se lineárně škáluje z hlediska jednotek streamování (SU) a propustnosti, což z něj činí nejefektivnější a nejvýkonnější způsob analýzy a streamování dat z Stream Analytics. Úlohy lze rozšířit až na 192 SU, což zhruba znamená zpracování až 200 MB/s nebo 19 bilionů událostí za den.

#### <a name="azure-sql"></a>Azure SQL
|Rychlost požití (události za sekundu) | Jednotky streamování | Výstupní zdroje  |
|---------|------|-------|
|    1 tis.   |   3  |  S3   |
|    5K   |   18 |  P4   |
|    10K  |   36 |  P6   |

[Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql) podporuje zápis paralelně, s názvem Inherit Partitioning, ale není ve výchozím nastavení povolen. Povolení Inherit Partitioning spolu s plně paralelní dotaz, však nemusí být dostatečná k dosažení vyšší propustnosti. Propustnosti zápisu SQL výrazně závisí na konfiguraci databáze SQL Azure a schématu tabulek. Článek [výkonu výstupu SQL](./stream-analytics-sql-output-perf.md) obsahuje další podrobnosti o parametrech, které mohou maximalizovat propustnost zápisu. Jak je uvedeno ve [výstupu Azure Stream Analytics do](./stream-analytics-sql-output-perf.md#azure-stream-analytics) Azure SQL Database článku, toto řešení není škálovat lineárně jako plně paralelní kanál nad 8 oddílů a může potřebovat přerozdělení před výstupem SQL (viz [INTO).](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) Prémiové skladové položky jsou potřebné k udržení vysoké sazby vi spolu s režií ze záloh protokolu děje každých pár minut.

#### <a name="cosmos-db"></a>Cosmos DB
|Rychlost požití (události za sekundu) | Jednotky streamování | Výstupní zdroje  |
|-------|-------|---------|
|  1 tis.   |  3    | 20K RU  |
|  5K   |  24   | 60K RU  |
|  10K  |  48   | 120K RU |

[Výstup Cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) z Stream Analytics byl aktualizován tak, aby používal nativní integraci pod [úrovní kompatibility 1.2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12). Úroveň kompatibility 1.2 umožňuje výrazně vyšší propustnost a snižuje spotřebu ru ve srovnání s hodnotou 1.1, což je výchozí úroveň kompatibility pro nové úlohy. Řešení používá CosmosDB kontejnery rozdělené na /deviceId a zbytek řešení je identicky nakonfigurován.

Všechny [vzorky Azure streamování na škálování](https://github.com/Azure-Samples/streaming-at-scale) použít Centrum událostí krmené zatížením simulující testovací klienty jako vstup. Každá vstupní událost je dokument 1KB JSON, který snadno převádí nakonfigurované míry ingestování na propustnost (1 MB/s, 5 MB/s a 10 MB/s). Události simulují zařízení IoT odesílající následující data JSON (ve zkrácené podobě) až pro zařízení 1K:

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
> Konfigurace se mohou měnit v důsledku různých komponent používaných v roztoku. Chcete-li získat přesnější odhad, přizpůsobte vzorky tak, aby vyhovovaly vašemu scénáři.

### <a name="identifying-bottlenecks"></a>Identifikace kritických míst

Podokno Metriky v úloze Azure Stream Analytics slouží k identifikaci kritických míst ve vašem kanálu. Zkontrolujte **vstupní a výstupní události** pro propustnost a ["Zpoždění vodoznaku"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) nebo **Backlogged Události** a zjistěte, zda úloha drží krok se vstupní rychlostí. Pro metriky Event Hub **vyhledejte omezené požadavky** a odpovídajícím způsobem upravte jednotky prahových hodnot. Pro metriky Cosmos DB zkontrolujte **Maximální spotřebované RU/s na oddíl rozsah klíčů** v rámci propustnosti zajistit rozsahy klíče oddílu jsou jednotně spotřebované. V Azure SQL DB **monitorujte IO protokolu** a **procesor**.

## <a name="get-help"></a>Podpora

Další pomoc našlápneme na fórum [Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

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

