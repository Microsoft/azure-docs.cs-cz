---
title: Strukturované streamování Sparku ve službě Azure HDInsight
description: Jak používat aplikace strukturovaného streamování Sparku v clusterech HDInsight Spark.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: fd65177fb6202b0396545043c2e63a87c7f01bbb
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864597"
---
# <a name="overview-of-apache-spark-structured-streaming"></a>Přehled strukturovaného streamování Apache Spark

[Apache Spark](https://spark.apache.org/) Strukturované streamování umožňuje implementovat škálovatelné a vysoce odolné aplikace odolné proti chybám pro zpracování datových proudů. Strukturované streamování je postavené na stroji Spark SQL a vylepšuje konstrukce z datových rámců Spark SQL a datových sad, takže můžete zapisovat dotazy streamování stejným způsobem, jako byste psali dávkové dotazy.  

Strukturované aplikace streamování běží na clusterech HDInsight Spark a připojují se k datovým proudům z [Apache Kafka](https://kafka.apache.org/), soketu TCP (pro účely ladění), Azure Storage nebo Azure Data Lake Storage. Tyto dvě možnosti, které spoléhají na externí služby úložiště, vám umožní sledovat nové soubory přidané do úložiště a zpracovávat jejich obsah, jako kdyby byly streamované.

Strukturované streamování vytvoří dlouhotrvající dotaz, během kterého se operace aplikují na vstupní data, jako je výběr, projekce, agregace, okna a připojení datového rámce streamování s referenčními datovými snímky. Dál vypíšete výsledky do úložiště souborů (Azure Storage objekty blob nebo Data Lake Storage) nebo do libovolného úložiště dat pomocí vlastního kódu (například SQL Database nebo Power BI). Strukturované streamování také poskytuje výstup do konzoly pro místní ladění a do tabulky v paměti, abyste viděli data generovaná pro ladění v HDInsight.

:::image type="content" source="./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png" alt-text="Zpracování datových proudů pomocí strukturovaného streamování HDInsight a Sparku" border="false":::

> [!NOTE]  
> Strukturované streamování Sparku nahrazuje Spark streamování (DStreams). Po přeposlání bude strukturované streamování získávat vylepšení a údržbu, zatímco DStreams bude pouze v režimu údržby. Strukturované streamování se v současnosti nepoužívá jako DStreams pro zdroje a jímky, které podporuje mimo pole. proto vyhodnoťte vaše požadavky a vyberte příslušnou možnost zpracování datového proudu Spark.

## <a name="streams-as-tables"></a>Streamování jako tabulky

Strukturované streamování Spark představuje datový proud dat jako tabulky, která je neohraničená hloubkou. to znamená, že tabulka se dále zvětšuje, protože dorazí na nová data. Tato *vstupní tabulka* se nepřetržitě zpracovává dlouhodobě běžícím dotazem a výsledky se odešlou do *výstupní tabulky*:

:::image type="content" source="./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png" alt-text="Koncept strukturovaného streamování" border="false":::

Ve strukturovaném streamování dorazí data do systému a okamžitě se ingestuje do vstupní tabulky. Zapisujete dotazy (pomocí rozhraní API dataframe a DataSet), které provádějí operace proti této vstupní tabulce. Výstup dotazu vydává jinou tabulku a *tabulku výsledků*. Tabulka výsledků obsahuje výsledky dotazu, ze kterého vykreslíte data pro externí úložiště dat, jako je relační databáze. Časování, kdy se data zpracovávají ze vstupní tabulky, řídí *interval triggeru*. Ve výchozím nastavení je interval triggeru nula, takže strukturované streamování se pokusí data zpracovat ihned po doručení. V praxi to znamená, že jakmile strukturované streamování dokončí zpracování předchozího dotazu, spustí se jiné zpracování na základě nově přijímaných dat. Aktivační událost se dá nakonfigurovat tak, aby se spouštěla v intervalu, takže streamovaná data se zpracují v dávkách založených na čase.

Data v tabulkách výsledků mohou obsahovat pouze data, která jsou od posledního zpracování dotazu (*režim připojení*) nová, nebo tabulka může být obnovena pokaždé, když jsou k dispozici nová data, takže tabulka bude obsahovat všechna výstupní data od začátku dotazu streamování (*režim úplné*).

### <a name="append-mode"></a>Režim připojení

V režimu připojení jsou v tabulce výsledků k dispozici pouze řádky přidané do tabulky výsledků od posledního spuštění dotazu, které jsou zapsány do externího úložiště. Například nejjednodušší dotaz pouze kopíruje všechna data ze vstupní tabulky do tabulky Results bez změny. Pokaždé, když interval triggeru uplyne, zpracují se nová data a řádky, které představují tato nová data, se zobrazí v tabulce výsledků.

Vezměte v úvahu scénář, ve kterém zpracováváte telemetrii ze senzorů teploty, jako je například termostat. Předpokládejte, že první aktivační událost zpracovala jednu událost v čase 00:01 pro zařízení 1 s rychlostí čtení 95 stupňů. V první aktivační události dotazu se v tabulce výsledků zobrazí pouze řádek s časem 00:01. V čase 00:02 když dorazí jiná událost, jediným novým řádkem je řádek s časem 00:02, takže tabulka výsledků by obsahovala pouze jeden řádek.

:::image type="content" source="./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png" alt-text="Režim připojení strukturovaného streamování" border="false":::

Při použití režimu Append by dotaz použil projekce (výběr sloupců, o kterých se stojí), vyfiltroval (vybírají pouze řádky, které odpovídají určitým podmínkám), nebo se připojovat (rozšiřuje data s daty z tabulky statického vyhledávání). Režim připojení usnadňuje odesílání pouze relevantních nových datových bodů do externího úložiště.

### <a name="complete-mode"></a>Režim dokončení

Vezměte v úvahu stejný scénář, tentokrát pomocí režimu úplného použití. V úplném režimu se při každé aktivační události aktualizuje celá výstupní tabulka, takže tabulka obsahuje data nejenom z posledního spuštění triggeru, ale ze všech běhů. K kopírování dat ze vstupní tabulky do tabulky výsledků můžete použít příkaz dokončit režim. Při každém aktivovaném spuštění se zobrazí nové řádky výsledků společně se všemi předchozími řádky. Tabulka výstupních výsledků bude mít za následek uložení všech shromažďovaných dat od začátku dotazu a nakonec by nedostatek paměti. Režim Complete je určený pro použití s agregačními dotazy, které nějakým způsobem sumarizují příchozí data, takže při každé aktivační události se tabulka výsledků aktualizuje pomocí nového souhrnu.

Předpokládat, že data už jsou zpracovaná na pět sekund a je čas na zpracování dat za šest sekund. Vstupní tabulka obsahuje události pro čas 00:01 a čas 00:03. Cílem tohoto ukázkového dotazu je poskytnout průměrnou teplotu zařízení každých pět sekund. Implementace tohoto dotazu použije agregaci, která převezme všechny hodnoty, které spadají do každého 5 sekundového okna, vypočítá průměrnou teplotu a vytvoří řádek pro průměrnou teplotu v tomto intervalu. Na konci prvního 5-druhého okna jsou dvě řazené kolekce členů: (00:01, 1, 95) a (00:03, 1, 98). Proto pro okno 00:00-00:05 agregace vytvoří řazenou kolekci členů s průměrnou teplotou 96,5 stupňů. V dalších 5 sekundových oknech je k dispozici pouze jeden datový bod v čase 00:06, takže výsledná Průměrná teplota je 98 stupňů. V čase 00:10 používá tabulka výsledků v režimu úplného zobrazení řádky pro Windows 00:00-00:05 a 00:05-00:10, protože dotaz vypisuje všechny agregované řádky, nikoli pouze nové. Tabulka výsledků proto nadále roste při přidání nových oken.

:::image type="content" source="./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png" alt-text="Úplný režim strukturovaného streamování" border="false":::

Ne všechny dotazy používající režim úplného režimu způsobí, že se tabulka rozrůstá bez hranic.  V předchozím příkladu zvažte, že místo průměrného počtu teplot podle časového intervalu se vybralo průměrně podle ID zařízení. Tabulka výsledků obsahuje pevný počet řádků (jeden na zařízení) s průměrnou teplotou zařízení ve všech datových bodech přijatých z tohoto zařízení. Při přijetí nových teplot se tabulka výsledků aktualizuje tak, aby byly vždy aktuální průměry v tabulce.

## <a name="components-of-a-spark-structured-streaming-application"></a>Komponenty aplikace strukturovaného streamování Sparku

Jednoduchý vzorový dotaz může sumarizovat čtení z teploty po hodinách dlouhých oknech. V tomto případě se data ukládají do souborů JSON v Azure Storage (připojí se jako výchozí úložiště pro cluster HDInsight):

```json
{"time":1469501107,"temp":"95"}
{"time":1469501147,"temp":"95"}
{"time":1469501202,"temp":"95"}
{"time":1469501219,"temp":"95"}
{"time":1469501225,"temp":"95"}
```

Tyto soubory JSON jsou uložené v `temps` podsložce pod kontejnerem clusteru HDInsight.

### <a name="define-the-input-source"></a>Definování vstupního zdroje

Nejprve nakonfigurujte datový rámec, který popisuje zdroj dat a všechna nastavení požadovaná tímto zdrojem. Tento příklad kreslí ze souborů JSON v Azure Storage a v době čtení aplikuje schéma na tyto soubory.

```sql
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

//Cluster-local path to the folder containing the JSON files
val inputPath = "/temps/" 

//Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

//Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath)
``` 

#### <a name="apply-the-query"></a>Použít dotaz

Dále použijte dotaz, který obsahuje požadované operace proti datovému snímku streamování. V tomto případě agregace seskupí všechny řádky do oken o velikosti 1 hodiny a pak vypočítá minimální, průměrnou a maximální teplotu v tomto okně.

```sql
val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))
```

### <a name="define-the-output-sink"></a>Definování výstupní jímky

Dále definujte cíl pro řádky, které jsou přidány do tabulky výsledků v rámci každého intervalu triggeru. V tomto příkladu se jenom vytvoří výstup všech řádků do tabulky v paměti `temps` , kde se později můžete dotazovat na SparkSQL. Úplný režim výstupu zajistí, že všechny řádky pro všechna okna budou ve výstupu pokaždé.

```sql
val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete")
``` 

### <a name="start-the-query"></a>Spustit dotaz

Spusťte dotaz streamování a spusťte příkaz, dokud se nepřijme signál ukončení.

```sql
val query = streamingOutDF.start() 
``` 

### <a name="view-the-results"></a>Zobrazení výsledků

Když je dotaz spuštěný, můžete ve stejném SparkSession spustit dotaz SparkSQL na `temps` tabulku, ve které jsou uložené výsledky dotazu.

```sql
select * from temps
```

Tento dotaz vrací výsledky podobné následujícímu:

| okno |  minimum (Temp) | střední (Temp) | Max (Temp) |
| --- | --- | --- | --- |
|{u'start ': u ' 2016-07-26T02:00:00.000 Z ', u'end '... |    95 |    95,231579 | 99 |
|{u'start ': u ' 2016-07-26T03:00:00.000 Z ', u'end '...  |95 |   96,023048 | 99 |
|{u'start ': u ' 2016-07-26T04:00:00.000 Z ', u'end '...  |95 |   96,797133 | 99 |
|{u'start ': u ' 2016-07-26T05:00:00.000 Z ', u'end '...  |95 |   96,984639 | 99 |
|{u'start ': u ' 2016-07-26T06:00:00.000 Z ', u'end '...  |95 |   97,014749 | 99 |
|{u'start ': u ' 2016-07-26T07:00:00.000 Z ', u'end '...  |95 |   96,980971 | 99 |
|{u'start ': u ' 2016-07-26T08:00:00.000 Z ', u'end '...  |95 |   96,965997 | 99 |  

Podrobnosti o rozhraní API strukturovaného streamu Spark spolu se vstupními zdroji dat, operacemi a výstupními jímkami, které podporuje, najdete Apache Spark v tématu [Průvodce programováním strukturovaného streamování](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html).

## <a name="checkpointing-and-write-ahead-logs"></a>Protokoly pro kontrolní body a zápisy s předstihem

Pro zajištění odolnosti proti chybám se strukturované streamování spoléhá na *kontrolní body* , aby bylo zajištěno, že zpracování streamování může pokračovat bez přerušení, i když selže selhání uzlu. Ve službě HDInsight vytvoří Spark kontrolní body pro trvalé úložiště, a to buď Azure Storage, nebo Data Lake Storage. Tyto kontrolní body ukládají informace o průběhu dotazu streamování. Strukturované streamování navíc používá *protokol zápisu* předem (WAL). WAL zachycuje ingestovaná data přijatá, ale ještě nebyla zpracována dotazem. Pokud dojde k selhání a probíhá restart z WAL, všechny události obdržené ze zdroje se neztratí.

## <a name="deploying-spark-streaming-applications"></a>Nasazení aplikací pro streamování Sparku

Aplikaci pro streamování Sparku obvykle vytváříte místně do souboru JAR a potom ji nasadíte do Sparku ve službě HDInsight zkopírováním souboru JAR do výchozího úložiště připojeného ke clusteru HDInsight. Aplikaci můžete spustit s rozhraními REST API [Apache Livy](https://livy.incubator.apache.org/) dostupnými z clusteru pomocí operace post. Tělo příspěvku obsahuje dokument JSON, který poskytuje cestu k JAR, název třídy, jejíž hlavní Metoda definuje a spouští aplikaci pro streamování, a volitelně požadavky na prostředky úlohy (například počet prováděcích modulů, paměti a jader) a všechna nastavení konfigurace, která váš kód aplikace vyžaduje.

:::image type="content" source="./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png" alt-text="Nasazení aplikace pro streamování Sparku" border="false":::

Stav všech aplikací lze také zkontrolovat pomocí požadavku GET na LIVY koncový bod. Nakonec můžete ukončit běžící aplikaci vyvoláním žádosti o odstranění na koncový bod LIVY. Podrobnosti o rozhraní LIVY API najdete v tématu [vzdálené úlohy s Apache LIVY](apache-spark-livy-rest-interface.md) .

## <a name="next-steps"></a>Další kroky

* [Vytvoření clusteru Apache Spark v HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Průvodce programováním strukturovaného streamování Apache Spark](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Vzdálené spouštění úloh Apache Spark pomocí Apache LIVY](apache-spark-livy-rest-interface.md)
