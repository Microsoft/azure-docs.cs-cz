---
title: "Spark strukturovaná streamování v Azure HDInsight | Microsoft Docs"
description: "Jak používat aplikace strukturované datové proudy Spark v clusterech HDInsight Spark."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: maxluk
ms.openlocfilehash: aa56c1e2f1f506be51f449a1cf10b4f0bc57a152
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="overview-of-spark-structured-streaming"></a>Přehled Spark strukturovaná streamování

Strukturované datové proudy Spark umožňuje implementovat škálovatelné, vysokou propustností, odolný proti chybám aplikace pro zpracování datových proudů. Strukturované Streaming je založena na modul Spark SQL a vylepšuje konstrukce z datových rámců Spark SQL a datové sady, můžete napsat vysílání datového proudu dotazuje stejným způsobem, který jste by psát dotazy batch.  

Strukturované Streaming aplikace spustit na clustery HDInsight Spark a připojit k vysílání datového proudu data z Kafka, TCP soketů (pro účely ladění), Azure Storage nebo Azure Data Lake Store. Druhé dvě možnosti, které jsou závislé na službě externí úložiště, umožňují sledovat soubory přidané do úložiště a zpracovat jejich obsah, jako kdyby byly datovým proudem. 

Strukturované Streaming vytvoří dlouho běžící dotaz, během které použijete pro vstupních dat, například výběr, projekce, agregace, oddílová a připojení ke streamování DataFrame s odkazem na DataFrames operace. V dalším kroku vypsání výsledků do úložiště file (Azure Storage Blobs nebo Data Lake Store) nebo do jakékoli úložiště dat pomocí vlastní kód (například databáze SQL nebo Power BI). Strukturované Streaming také poskytuje výstup do konzoly nástroje pro ladění lokálně a na tabulku v paměti, takže se zobrazí data vygenerovaná pro ladění v HDInsight. 

![Zpracování s HDInsight a vysílání datového proudu strukturovaná Spark datového proudu ](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]
> Strukturované datové proudy Spark nahrazuje Spark streamování (DStreams). Do budoucna, strukturovaných streamování obdrží vylepšení a údržba, při DStreams bude v pouze v režimu údržby. Strukturované Streaming momentálně není jako funkce dokončování jako DStreams pro zdroje a jímky, že podporuje ihned, takže vyhodnotit požadavky na příslušné Spark zvolit možnost zpracování datového proudu. 

## <a name="streams-as-tables"></a>Datové proudy jako tabulky

Strukturované datové proudy Spark představuje datový proud jako tabulku, která je podrobněji bez vazby, který je nadále růst podle dorazí nová data v tabulce. To *vstupní tabulky* nepřetržitě zpracovává dlouho běžící dotaz a výsledky posílá *výstupní tabulku*:

![Strukturovaná streamování koncept](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

V streamování strukturovaných dat dorazí na systém a okamžitě nasávaného vstupní tabulku. Můžete psát dotazy (pomocí rozhraní API datovou sadu a DataFrame), které provádějí operace pro tento vstupní tabulka. Výstup dotazu dává jiné tabulky, *tabulce výsledků*. Tabulka výsledků obsahuje výsledky dotazu, ze kterého kreslení data pro externí úložiště, relační databáze. Načasování při zpracování dat ze vstupní tabulky řídí *interval aktivační událost*. Ve výchozím intervalu aktivační událost rovná nule, tak strukturovaných streamování pokusí zpracovat data při doručení. V praxi, to znamená, že jakmile strukturovaných streamování se provádí zpracování spuštění předchozího dotazu, začne spouštění na všech nově přijatých dat. Další zpracování. Aktivační událost spouštěla v intervalu, můžete nakonfigurovat tak, aby zpracování dat v dávkách založené na čase. 

Zpracování dat ve výsledcích tabulky mohou obsahovat pouze data, která je nového od poslední čas dotazu (*režim připojení*), nebo v tabulce může být zcela aktualizovat pokaždé, když není nová data, takže v tabulce jsou zahrnuty všechny výstupní data od začátku streamování dotazu (*dokončení režimu*).

### <a name="append-mode"></a>Režim připojení

V režimu připojení, jenom na řádky přidat do tabulky výsledky, protože poslední spuštění dotazu jsou k dispozici v tabulce výsledků a zapsaných na externí úložiště. Například nejjednodušší dotaz právě zkopíruje všechna data ze vstupní tabulky do tabulky výsledky v nezměněném stavu. Pokaždé, když uplyne intervalu aktivační události, je zpracován nová data a řádky, které představují nové data se zobrazí v tabulce výsledků. 

Vezměte v úvahu scénář, kde jsou zpracování telemetrie z teploty senzorů, jako je například termostatu. Předpokládejme, že první aktivační událost zpracovat jednu událost čas 00:01 zařízení 1 s teploty čtení 95 stupňů. V první aktivační událost dotazu zobrazí se pouze řádek s čas 00:01 v tabulce výsledků. Na dobu 00:02 když dorazí jinou událost pouze nový řádek je řádek s čas 00:02 a proto by v tabulce výsledků obsahovat pouze tento jeden řádek.

![Strukturované streamování režim připojení](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

Při použití režimu připojení, dotazu by být použití projekce (Výběr sloupce, které ho záleží), filtrování (výběr pouze řádky, které odpovídají určité podmínky) nebo připojení (rozšířit dat s daty ze statické vyhledávací tabulky). Připojení režimu díky usnadňují push pouze relevantní nová data poukazuje na externí úložiště.

### <a name="complete-mode"></a>Režim dokončení

Představte si situaci, stejné, tentokrát pomocí dokončení režimu. V dokončení režimu celý výstupní tabulka aktualizována na všechny aktivační události, v tabulce zahrnuje data z všechny spustí, ale ne jenom z nejnovější aktivační událost spouštět. Režim dokončení můžete použít ke zkopírování dat v nezměněném stavu ze vstupní tabulky do tabulky výsledky. Při každé spuštění spouštěná nové řádky výsledků se zobrazí spolu s všechny předchozí řádky. Výstupní tabulky výsledky dojdete k ukládání všech dat shromažďovaných od začátku poslední dotaz a nakonec by mít nedostatek paměti. Dokončení režimu je určena pro použití s agregační dotazy, které shrnují příchozích dat nějakým způsobem a tak dále se aktualizuje všechny aktivační události v tabulce výsledků s nový souhrn. 

Předpokládejme, pokud existují za pět sekund již zpracování dat, a je čas ke zpracování dat pro šesté druhý. Vstupní tabulka má události pro čas 00:01 a čas 00:03. Cílem tohoto příklad dotazu je umožnit teplota zařízení každých pět sekund. Implementace tohoto dotazu se vztahuje agregaci, která přebírá všechny hodnoty, které spadají do každé okno 5 sekund, zobrazí průměr teplota a vytvoří řádek pro průměrnou teplotu během tohoto intervalu. Na konci první okno 5 sekund, existují dvě řazené kolekce členů: (00:01, 1, 95) a (00:03, 1, 98). Ano pro okno 00:00 – 00:05 agregace vytváří řazené kolekce členů s průměrnou teplotu 96.5 stupňů. V dalším intervalu 5 sekund je pouze jeden datový bod na čas 00:06, tak, aby výsledné teplota 98 stupňů. Na dobu 00:10, pomocí dokončení režimu, výsledky tabulka obsahuje řádky pro obě windows 00:00 – 00:05 a 00:05 00:10 vzhledem k tomu, že dotaz vrací všechny agregované řádky, ne jen nové. Proto se v tabulce výsledků stále rostoucí při přidání nového systému windows.    

![Strukturovaná streamování dokončení režimu](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Ne všechny dotazy pomocí dokončení režimu způsobí, že v tabulce růst bez hranice.  Vezměte v úvahu v předchozím příkladu, nikoli průměrování teploty podle časový interval, průměr místo podle ID zařízení. Tabulka výsledek obsahuje pevný počet řádků (jeden na každého zařízení) s průměrnou teplotu zařízení napříč všechny datové body přijaté z těchto zařízení. Jako nový teploty přijme, v tabulce výsledků je aktualizována tak, aby byly vždy aktuální průměry v tabulce. 

## <a name="components-of-a-spark-structured-streaming-application"></a>Součásti aplikace Spark strukturovaných streamování

Jednoduchý příklad dotazu lze shrnout odečty teploty hour-long Windows. V takovém případě jsou data uložena v souborech JSON v Azure Storage (připojený jako výchozí úložiště pro HDInsight cluster):

    {"time":1469501107,"temp":"95"}
    {"time":1469501147,"temp":"95"}
    {"time":1469501202,"temp":"95"}
    {"time":1469501219,"temp":"95"}
    {"time":1469501225,"temp":"95"}

Tyto soubory JSON jsou uloženy v `temps` podsložky pod kontejneru clusteru HDInsight. 

### <a name="define-the-input-source"></a>Definování vstupního zdroje

Nejprve nakonfigurujte DataFrame, který popisuje zdroji dat, a všechna nastavení, které vyžadují tento zdroj. Tento příklad nevykresluje z soubory JSON ve službě Azure Storage a schéma, použije u nich během čtení.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    //Cluster-local path to the folder containing the JSON files
    val inputPath = "/temps/" 

    //Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
    val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

    //Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
    val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath) 

#### <a name="apply-the-query"></a>Použije dotaz

V dalším kroku použít dotaz, který obsahuje požadované operace u DataFrame streamování. V takovém případě agregace seskupí všechny řádky do windows 1 hodinu a pak vypočítá minimální, průměrný a maximální teplotu v okně 1 hodinu.

    val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))

### <a name="define-the-output-sink"></a>Definujte výstupní jímku

V dalším kroku definujte cíl pro řádky, které jsou přidány do tabulky výsledky v každém intervalu aktivační události. Tento příklad právě vypíše všechny řádky na tabulku v paměti `temps` , můžete zadat později dotaz s SparkSQL. Režim dokončení výstupu zajistí všechny řádky, pro všechny systémy windows výstup pokaždé, když.

    val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete") 

### <a name="start-the-query"></a>Spuštění dotazu

Streamování query a spustit, dokud neobdrží signál k ukončení. 

    val query = streamingOutDF.start()  

### <a name="view-the-results"></a>Zobrazení výsledků

Když dotaz běží ve stejné SparkSession SparkSQL dotaz můžete spustit `temps` tabulky, kde jsou uložené výsledky dotazu. 

    select * from temps

Tento dotaz dává výsledky podobné následujícím:


| Okno |  min(temp) | avg(temp) | Max(Temp) |
| --- | --- | --- | --- |
|{u'start': u'2016-07-26T02:00:00.000Z', u'end'... |    95 |    95.231579 | 99 |
|{u'start': u'2016-07-26T03:00:00.000Z', u'end'...  |95 |   96.023048 | 99 |
|{u'start': u'2016-07-26T04:00:00.000Z', u'end'...  |95 |   96.797133 | 99 |
|{u'start': u'2016-07-26T05:00:00.000Z', u'end'...  |95 |   96.984639 | 99 |
|{u'start': u'2016-07-26T06:00:00.000Z', u'end'...  |95 |   97.014749 | 99 |
|{u'start': u'2016-07-26T07:00:00.000Z', u'end'...  |95 |   96.980971 | 99 |
|{u'start': u'2016-07-26T08:00:00.000Z', u'end'...  |95 |   96.965997 | 99 |  

Podrobnosti o rozhraní API strukturovaných datového proudu Spark, společně s vstupních dat zdrojů, operace a výstup jímky ji podporuje, najdete v části [Spark strukturovaných streamování Průvodce programováním](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html).

## <a name="checkpointing-and-write-ahead-logs"></a>Vytváření kontrolních bodů a předběžné protokoly

Zajistit odolnost proti chybám a odolnost proti chybám strukturovaných streamování spoléhá na *vytváření kontrolních bodů* zajistit, že datový proud zpracování mohlo bez přerušení pokračovat, i když selhání uzlu. Spark v HDInsight, vytvoří kontrolní body do trvalého úložiště Azure Storage nebo Data Lake Store. Tyto kontrolní body ukládat informace o průběhu o streamování dotazu. Kromě toho používá strukturovaných streamování *předběžné protokolování* (WAL). WAL zaznamená ingestovaný data, která byla přijata, ale ještě nebyla zpracována dotazu. Pokud dojde k selhání a zpracování je restartovat z WAL, nejsou ke ztrátě všech událostí přijatých ze zdroje.

## <a name="deploying-spark-streaming-applications"></a>Nasazení aplikací Spark streamování

Obvykle sestavit aplikaci vysílání datového proudu Spark místně na soubor JAR a poté ji nasadit do Spark v HDInsight pomocí kopírování souborů JAR do výchozího úložiště připojené ke svému clusteru HDInsight. Aplikaci můžete spustit v rozhraní API REST LIVY dostupná z clusteru pomocí operaci POST. Text v příspěvku obsahuje dokument JSON, který poskytuje cestu k JAR, název třídy, jejichž hlavní metoda definuje a spouští aplikace streamování a volitelně požadavků na prostředky pro úlohy (například počet vykonavatelů, paměti a jader) , a vyžaduje nastavení konfigurace kódu aplikace.

![Nasazení aplikace datové proudy Spark](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

S požadavek GET na koncový bod LIVY můžete také zkontrolovat stav všech aplikací. Nakonec můžete ukončit spuštěné aplikace vydáním požadavek DELETE LIVY koncový bod. Informace o rozhraní API LIVY v [vzdálené úlohy se LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Další postup

* [Vytvořit cluster Apache Spark v HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Spark strukturovaná streamování Průvodce programováním](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Spuštění úlohy Spark vzdáleně pomocí LIVY](apache-spark-livy-rest-interface.md)