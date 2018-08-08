---
title: Strukturované streamování v Azure HDInsight Spark
description: Jak používat strukturované streamování Sparku aplikací v clusterech HDInsight Spark.
services: hdinsight
author: maxluk
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: maxluk
ms.openlocfilehash: 79e170349165dac286eda46ac1d4ff6945e71e8f
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621595"
---
# <a name="overview-of-spark-structured-streaming"></a>Přehled strukturovaného streamování Sparku

Strukturované streamování Sparku umožňuje implementovat škálovatelné, vysoce propustné odolné aplikace pro zpracování datových proudů. Strukturované streamování je založena na stroji Spark SQL a dále to vylepšuje konstrukce ze snímků dat Spark SQL a datové sady, takže můžete psát streamování dotazy stejným způsobem, který můžete by psát dotazy služby batch.  

Strukturované streamování aplikace poběží na clusterech HDInsight Spark a připojte se k streamování dat z Kafka, TCP soketu (pro účely ladění), Azure Storage nebo Azure Data Lake Store. Druhé dvě možnosti, které jsou závislé na externí úložiště, umožňují sledovat soubory přidané do úložiště a zpracování jejich obsah, jako kdyby byly zpracovány pomocí proudu. 

Strukturované streamování vytvoří dlouho běžící dotaz, během které použijete pro vstupní data, jako je například výběr, projekce, agregace, oken a propojení datových proudů datový rámec s odkazem na datových rámců operace. V dalším kroku vypíše výsledky do služby file storage (objekty BLOB Azure Storage nebo Data Lake Store) nebo na jakékoli úložiště dat pomocí vlastního kódu (jako je SQL Database nebo Power BI). Strukturované streamování také poskytuje výstup do konzoly pro ladění místně a do tabulky v paměti, abyste si mohli zobrazit data generovaná pro ladění v HDInsight. 

![Stream zpracování ve službě HDInsight a strukturovaného streamování Sparku ](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]
> Strukturované streamování Sparku nahrazuje Spark Streaming (diskretizovanými streamy). Od této chvíle, strukturované streamování obdrží vylepšení a údržbu, zatímco diskretizovanými streamy bude v pouze v režimu údržby. Strukturované streamování není aktuálně jako plně funkční jako diskretizovanými streamy pro zdroje a jímky, podporuje úprav, takže vyhodnotit požadavky na odpovídající Spark zvolit možnost zpracování datového proudu. 

## <a name="streams-as-tables"></a>Datové proudy jako tabulky

Strukturované streamování Sparku představuje datový proud dat jako tabulku, která je bez vazby do hloubky, to znamená, že se v tabulce stále rozrůstá příchodu nových dat. To *vstupní tabulky* průběžně zpracovává dlouhého dotazu a výsledky odesílat *výstupní tabulky*:

![Strukturované streamování koncept](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

Ve strukturované streamování data dorazí na systém a ingestuje se hned do vstupní tabulku. Můžete psát dotazy (pomocí rozhraní API datové sady a datového rámce), které provedení operací s této vstupní tabulky. Výstup dotazu vrátí jinou tabulku, *tabulka výsledků*. Tabulka výsledků obsahuje výsledky dotazu, ze kterého můžete získávat data pro externí úložiště, relační databáze. Řídí načasování při zpracování dat ze vstupní tabulky *aktivační událost interval*. Ve výchozím intervalu aktivační události je nula, tak strukturované streamování pokusí zpracovat data hned po doručení. V praxi to znamená, že jako strukturované streamování se provádí zpracování spuštění předchozího dotazu, spustí běh pro libovolná data nově přijatých další zpracování. Aktivační událost, aby se spouštěla v intervalu, můžete nakonfigurovat tak, aby zpracování streamovaných dat v dávkách podle času. 

Zpracování dat ve výsledcích tabulek můžou obsahovat jenom data, která je nový od posledního času dotaz (*režimu připojení*), nebo v tabulce může být zcela aktualizují pokaždé, když se nová data tak, že v tabulce jsou zahrnuty všechny výstupní data od začátku datového proudu dotazu (*úplný režim*).

### <a name="append-mode"></a>Režimu připojení

V režimu připojení, pouze řádky přidány do tabulky výsledků od posledního spuštění dotazu jsou k dispozici v tabulce výsledků a zapisovat do externího úložiště. Například nejjednodušší dotazu právě kopíruje veškerá data ze vstupní tabulky do tabulky výsledků v nezměněném stavu. Pokaždé, když trigger interval uplyne, se zpracují nová data a řádky, které představují tato nová data se zobrazí v tabulce výsledků. 

Představte si třeba situaci, ve kterém jsou zpracování telemetrických dat ze senzorů teploty, jako je například termostat. Předpokládejme, že první aktivační událost zpracovat jednu událost času 00:01 pro zařízení 1 s teploty čtení 95 stupňů. V první aktivační události dotazu se zobrazí pouze řádek s času 00:01 v tabulce výsledků. Času 00:02 při přijetí další události pouze nový řádek je řádek s času 00:02 a proto tabulka výsledků by obsahovat pouze jednoho řádku.

![Strukturované streamování režimu připojení](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

Při použití režimu připojení, dotaz by být použití projekce (vyberte sloupce, které ho záleží) filtrování (výběr pouze řádky, které odpovídají určité podmínky) nebo připojení (rozšiřování data s daty z statické vyhledávací tabulky). Připojení režimu vám snadno vložit pouze relevantní nová data poukazuje na k externímu úložišti.

### <a name="complete-mode"></a>Úplný režim

Vezměte v úvahu stejný scénář, tentokrát pomocí úplný režim. V úplný režim celé výstupní tabulce aktualizována při každé aktivaci tak tabulka obsahuje data pouhým nejnovější spuštění aktivační události, ale z všechna spuštění. Úplný režim můžete použít ke zkopírování dat beze změny ze vstupní tabulky do tabulky výsledků. Při každém aktivovaných spuštění nových řádků výsledek zobrazí spolu s předchozím řádků. Tabulka výsledků výstupu skončí ukládání všechna data, shromáždit, protože dotaz začala a bude nakonec mít nedostatek paměti. Úplný režim je určena pro použití s agregačních dotazů, které shrnují příchozí data nějakým způsobem, a tak dále všechny aktivační události v tabulce výsledků se aktualizuje nový souhrn. 

Předpokládejme zatím existují data již zpracováno za pět sekund, a je čas ke zpracování dat pro šestého sekundu. Vstupní tabulka obsahuje události pro času 00:01 a času 00:03. Cílem tohoto příkladu dotazu je poskytnout průměrná teplota zařízení každých pět sekund. Provedení tohoto dotazu použije agregaci, která přebírá všechny hodnoty, které spadají do každé okno 5 sekund, předběhli aktuální fázi teplota a vytváří řádek pro průměrná teplota v tomto intervalu. Na konci první okno 5 sekund, existují dvě řazené kolekce členů: (00:01, 1, 95) a (00:03, 1, 98). Ano pro okno 00:00-00:05 agregaci vytvoří řazenou kolekci členů s průměrnou teplotou 96.5 stupňů. V dalším okně 5 sekund je pouze jeden datový bod v čase 00:06, takže výsledný průměrná teplota je 98 stupňů. V času 00:10, pomocí úplný režim výsledky tabulka obsahuje řádky pro obě windows 00:00-00:05 a 00-00:05:10 vzhledem k tomu, že dotaz vypíše všechny agregované řádky, ne jenom nové značky. Proto tabulka výsledků se stále rozrůstá při přidání nových oknech.    

![Strukturované streamování úplný režim](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

Ne všechny dotazy v režimu úplné způsobí, že tabulka, která má růst bez omezení.  Vezměte v úvahu v předchozím příkladu se místo odstávkou teploty od časový interval, byla místo toho podle ID zařízení. Tabulky výsledků obsahuje pevný počet řádků (jeden do každého zařízení) s průměrnou teplotou zařízení přes všechny body data přijatá z těchto zařízení. Se přijetí nové teploty, je tabulka výsledků aktualizovány tak, aby průměry v tabulce jsou vždy aktuální. 

## <a name="components-of-a-spark-structured-streaming-application"></a>Komponenty aplikace strukturovaného streamování Sparku

Jednoduchý příklad dotazu můžete vytvořit souhrn měření teploty hour-long systém Windows. V tomto případě jsou data uložená v souborech JSON ve službě Azure Storage (připojené jako výchozí úložiště pro HDInsight cluster):

    {"time":1469501107,"temp":"95"}
    {"time":1469501147,"temp":"95"}
    {"time":1469501202,"temp":"95"}
    {"time":1469501219,"temp":"95"}
    {"time":1469501225,"temp":"95"}

Tyto soubory JSON se ukládají v `temps` podsložky pod kontejneru v clusteru HDInsight. 

### <a name="define-the-input-source"></a>Definování vstupního zdroje

Nejprve nakonfigurujte datový rámec, který popisuje zdroje dat a všechna nastavení, které vyžadují tento zdroj. V tomto příkladu nakreslí ze souborů JSON ve službě Azure Storage a schéma se na ně vztahují během čtení.

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    //Cluster-local path to the folder containing the JSON files
    val inputPath = "/temps/" 

    //Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
    val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

    //Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
    val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath) 

#### <a name="apply-the-query"></a>Použije dotaz

V dalším kroku použije dotaz, který obsahuje požadované operace proti streamování datového rámce. V tomto případě agregaci všechny řádky seskupí do systému windows za 1 hodinu a pak vypočítá maximální, průměrné a minimální teploty v tomto okně 1 hodinu.

    val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))

### <a name="define-the-output-sink"></a>Definování výstupní jímky

Dále definujte cíl pro řádky, které se přidají do tabulky výsledků v rámci v každém intervalu aktivační události. V tomto příkladu pouze vypíše všechny řádky do tabulky v paměti `temps` , můžete zadat později dotaz s SparkSQL. Úplný výstup režim zajišťuje, že všechny řádky pro všechna okna výstup pokaždé, když.

    val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete") 

### <a name="start-the-query"></a>Spustit dotaz

Spusťte streamování dotazu a až do ukončení signál. 

    val query = streamingOutDF.start()  

### <a name="view-the-results"></a>Zobrazit výsledky

Když dotaz běží ve stejném SparkSession spustíte SparkSQL dotazovat `temps` tabulky, kde jsou uloženy výsledky dotazu. 

    select * from temps

Tento dotaz vrací výsledky podobné následujícím:


| Okno |  min(Temp) | AVG(Temp) | Max(Temp) |
| --- | --- | --- | --- |
|{u'start': u "2016-07-26T02:00:00.000Z", u'end'... |    95 |    95.231579 | 99 |
|{u'start': u "2016-07-26T03:00:00.000Z", u'end'...  |95 |   96.023048 | 99 |
|{u'start': u "2016-07-26T04:00:00.000Z", u'end'...  |95 |   96.797133 | 99 |
|{u'start': u "2016-07-26T05:00:00.000Z", u'end'...  |95 |   96.984639 | 99 |
|{u'start': u "2016-07-26T06:00:00.000Z", u'end'...  |95 |   97.014749 | 99 |
|{u'start': u "2016-07-26T07:00:00.000Z", u'end'...  |95 |   96.980971 | 99 |
|{u'start': u "2016-07-26T08:00:00.000Z", u'end'...  |95 |   96.965997 | 99 |  

Podrobnosti o rozhraní API Spark strukturovaný Stream, spolu s daty o vstupní zdroje, operace a výstupní jímky ji podporuje, najdete v článku [Spark strukturované streamování Průvodce programováním pro službu](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html).

## <a name="checkpointing-and-write-ahead-logs"></a>Vytváření kontrolních bodů a zápis dávky protokolů

K zajištění odolnosti proti chybám a odolnost proti chybám, strukturované streamování spoléhá na *vytváření kontrolních bodů* zajistit tohoto datového proudu zpracování mohlo bez přerušení pokračovat, i při selhání uzlů. Spark v HDInsight, vytvoří kontrolní body do trvalého úložiště Azure Storage nebo Data Lake Store. Tyto kontrolní body ukládat informace o průběhu o streamování dotazu. Kromě toho používá strukturované streamování *zápisu dopředné protokolování* (WAL). WAL zaznamená přijatých dat, která byla přijata, ale dosud nejsou zpracovány v dotazu. Pokud dojde k chybě a zpracování je restartováno ze WAL, nejsou žádné události přijata ze zdroje ztraceny.

## <a name="deploying-spark-streaming-applications"></a>Nasazení aplikací Spark Streaming

Obvykle sestavit aplikace Spark Streaming místně do souboru JAR a nasadit ho na Spark v HDInsight zkopírováním souboru JAR do výchozího úložiště připojené ke clusteru HDInsight. Vaše aplikace může začínat LIVY REST API, který je k dispozici z clusteru pomocí operace POST. Text příspěvku obsahuje dokument JSON, který obsahuje cestu k JAR, název třídy, jejichž hlavní metoda definuje a spouští aplikace streaming a volitelně požadavky na prostředky úlohy (například počet moduly provádění, paměti a jader) , a všechna nastavení konfigurace kódu aplikace vyžaduje.

![Nasazení aplikace Spark Streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Stav všech aplikací můžete také zkontrolovat požadavek GET na koncový bod LIVY. Nakonec můžete ukončit běžící aplikaci vydáním odstranit požadavek na koncový bod LIVY. Podrobnosti o rozhraní API LIVY najdete v tématu [vzdálené úlohy s LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Další postup

* [Vytvoření clusteru Apache Spark v HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Spark strukturované streamování Průvodce programováním](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Spuštění úlohy Spark vzdáleně pomocí LIVY](apache-spark-livy-rest-interface.md)
