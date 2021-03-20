---
title: Migrace Azure HDInsight 3,6 Apache Storm do HDInsight 4,0 Apache Spark
description: Rozdíly a tok migrace pro migraci Apache Storm úloh do streamování Sparku nebo strukturovaného streamování Sparku.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/16/2019
ms.openlocfilehash: aa57c01558cfdcf069b17fad9e86f7640553dcfd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944781"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>Migrace Azure HDInsight 3,6 Apache Storm do HDInsight 4,0 Apache Spark

Tento dokument popisuje, jak migrovat Apache Storm úlohy ve službě HDInsight 3,6 do HDInsight 4,0. HDInsight 4,0 nepodporuje typ clusteru Apache Storm a budete muset migrovat na jinou platformu streamování dat. Apache Spark streamování a strukturované streamování Sparku jsou dvě vhodné možnosti. Tento dokument popisuje rozdíly mezi těmito platformami a také doporučuje pracovní postup pro migraci Apache Stormch úloh.

## <a name="storm-migration-paths-in-hdinsight"></a>Cesty migrace pro vyplavení v HDInsight

Pokud chcete migrovat z Apache Storm ve službě HDInsight 3,6 máte několik možností:

* Streamování Sparku ve službě HDInsight 4,0
* Strukturované streamování Sparku ve službě HDInsight 4,0
* Azure Stream Analytics

Tento dokument poskytuje návod pro migraci z Apache Storm na streamování Spark a strukturované streamování Sparku.

> [!div class="mx-imgBorder"]
> ![Cesta pro migraci ve službě HDInsight](./media/migrate-storm-to-spark/storm-migration-path.png)

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Porovnání mezi Apache Storm a datovým proudem Spark, strukturované streamování Sparku

Apache Storm můžete poskytovat různé úrovně zaručeného zpracování zprávy. Například základní aplikace pro zaplavení může zaručit alespoň jedno zpracování a [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) může zaručit přesně jedno zpracování. Datové proudy Spark a strukturované streamování Sparku zaručují, že jakákoli vstupní událost je zpracována přesně jednou, i když dojde k selhání uzlu. Operace displave má model, který zpracovává každou jednotlivou událost, a můžete také použít model mikrodávkování pomocí Trident. Streamování Sparku a strukturované streamování Spark poskytují model Micro-Batchho zpracování.

|  |Storm |Streamování Sparku | Strukturované streamování Sparku|
|---|---|---|---|
|**Záruka zpracování událostí**|Aspoň jednou <br> Právě jednou (Trident) |[Právě jednou](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[Právě jednou](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Model zpracování**|Reálný čas <br> Micro Batch (Trident) |Mikrodávka |Mikrodávka |
|**Podpora času události**|[Ano](https://storm.apache.org/releases/2.0.0/Windowing.html)|No|[Ano](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Jazyky**|Java atd.|Scala, Java, Python|Python, R, Scala, Java, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Streamování Sparku vs strukturované streamování Sparku

Strukturované streamování Sparku nahrazuje Spark streamování (DStreams). Strukturované streamování bude nadále získávat vylepšení a údržbu, zatímco DStreams bude pouze v režimu údržby. **Poznámka: k zdůraznění tohoto bodu je potřeba mít odkazy**. Strukturované streamování nemá k dispozici tolik funkcí jako DStreams pro zdroje a jímky, které podporuje mimo pole. proto vyhodnoťte vaše požadavky a vyberte příslušnou možnost zpracování datového proudu Spark.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>Zpracování datových proudů (jedna událost) vs Micro-Batch zpracování

Operace vyplavování poskytuje model, který zpracovává každou jednotlivou událost. To znamená, že všechny příchozí záznamy budou zpracovány ihned po doručení. Před odesláním této dávky ke zpracování musí aplikace streamování Sparku počkat na zlomek sekund a shromáždit každou mikrodávku událostí. Naproti tomu aplikace řízená událostmi zpracovává každou událost okamžitě. Latence streamování Sparku obvykle trvá několik sekund. Výhody mikrodávkového přístupu jsou efektivnější zpracování dat a jednodušší agregační výpočty.

> [!div class="mx-imgBorder"]
> ![streamování a zpracování mikrodávkování](./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png)

## <a name="storm-architecture-and-components"></a>Architektura a součásti pro zaplavení

Topologie Storm se skládají z několika součástí, které jsou uspořádány do orientovaného acyklického grafu (DAG). Data proudí mezi komponentami v grafu. Každá komponenta spotřebovává jeden či více datových streamů a případně může i jeden či více streamů vysílat.

|Komponenta |Popis |
|---|---|
|Spout|Přinese data do topologie. Vysílají do topologie jeden nebo více datových proudů.|
|Bolt|Spotřebovává datové proudy emitované z spoutů nebo jiného šrouby. Bolty mohou volitelně vysílat do topologie datové streamy. Bolty také odpovídají za zápis dat do externích služeb nebo úložiště, například HDFS, Kafka nebo HBase.|

> [!div class="mx-imgBorder"]
> ![interakce součástí s více prvky](./media/migrate-storm-to-spark/apache-storm-components.png)

Přetvoření se skládá z následujících tří démonů, které udržují cluster pro zaplavení fungovat.

|Proces |Description |
|---|---|
|Nimbus|Podobně jako Hadoop JobTracker je zodpovědný za distribuci kódu v rámci clusteru a přiřazování úloh do počítačů a sledování selhání.|
|Zookeeper|Používá se pro koordinaci clusteru.|
|Supervisor|Čeká na práci přiřazenou k počítači a spustí a zastaví pracovní procesy na základě direktiv z Nimbus. Každý pracovní proces spustí podmnožinu topologie. Tady se spustí logika uživatele (Spoutů a šroub) uživatele.|

> [!div class="mx-imgBorder"]
> ![démoni Nimbus, Zookeeper a vedoucího procesu](./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png)

## <a name="spark-streaming-architecture-and-components"></a>Architektura a součásti streamování Sparku

Následující kroky shrnují, jak komponenty spolupracují v Spark streaming (DStreams) a strukturované streamování Spark:

* Když se spustí streamování Sparku, ovladač spustí úlohu v vykonavateli.
* Vykonavatel obdrží datový proud ze zdroje dat streamování.
* Když vykonavatel obdrží datové proudy, rozdělí datový proud na bloky a zachová je v paměti.
* Bloky dat jsou replikovány do jiných prováděcích modulů.
* Zpracovaná data se pak uloží do cílového úložiště dat.

> [!div class="mx-imgBorder"]
> ![cesta streamování Sparku na výstup](./media/migrate-storm-to-spark/spark-streaming-to-output.png)

## <a name="spark-streaming-dstream-workflow"></a>Pracovní postup Spark streaming (DStream)

Po uplynutí každého intervalu dávky bude vytvořen nový RDD, který obsahuje všechna data z tohoto intervalu. Souvislé sady RDD jsou shromažďovány do DStream. Pokud je například interval dávky jedna sekunda, DStream vygeneruje dávku každou sekundu obsahující jednu RDD, která obsahuje všechna data ingestovaná během této sekundy. Při zpracování DStream se událost teploty zobrazuje v jedné z těchto dávek. Aplikace pro streamování Spark zpracovává dávky, které obsahují události a nakonec fungují s daty uloženými v jednotlivých RDD.

> [!div class="mx-imgBorder"]
> ![dávkové zpracování Spark streamování](./media/migrate-storm-to-spark/spark-streaming-batches.png)

Podrobnosti o různých transformacích, které jsou k dispozici u Spark streamování, najdete v tématu [transformace v DStreams](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="spark-structured-streaming"></a>Strukturované streamování Sparku

Strukturované streamování Spark představuje datový proud dat jako tabulky, která je neohraničená hloubkou. Tabulka se dále zvětšuje, protože dorazí na nová data. Tato vstupní tabulka se průběžně zpracovává dlouhotrvajícím dotazem a výsledky se odesílají do výstupní tabulky.

Ve strukturovaném streamování dorazí data do systému a okamžitě se ingestuje do vstupní tabulky. Zapisujete dotazy (pomocí rozhraní API dataframe a DataSet), které provádějí operace proti této vstupní tabulce.

Výstup dotazu vrací *tabulku výsledků*, která obsahuje výsledky dotazu. Data můžete vykreslit z tabulky výsledků pro externí úložiště dat, jako je například relační databáze.

Časování, kdy se data zpracovávají ze vstupní tabulky, řídí interval triggeru. Ve výchozím nastavení je interval triggeru nula, takže strukturované streamování se pokusí data zpracovat ihned po doručení. V praxi to znamená, že jakmile strukturované streamování dokončí zpracování předchozího dotazu, spustí se jiné zpracování na základě nově přijímaných dat. Aktivační událost se dá nakonfigurovat tak, aby se spouštěla v intervalu, takže streamovaná data se zpracují v dávkách založených na čase.

> [!div class="mx-imgBorder"]
> ![zpracování dat ve strukturovaném streamování](./media/migrate-storm-to-spark/structured-streaming-data-processing.png)

> [!div class="mx-imgBorder"]
> ![programovací model pro strukturované streamování](./media/migrate-storm-to-spark/structured-streaming-model.png)

## <a name="general-migration-flow"></a>Obecný tok migrace

Doporučený postup migrace z procesu naplnění na Spark předpokládá následující počáteční architekturu:

* Kafka se používá jako zdroj dat streamování.
* Kafka a zaplavení se nasazují ve stejné virtuální síti.
* Data zpracovaná po zaplavení se zapisují do datové jímky, jako je například Azure Storage nebo Azure Data Lake Storage Gen2.

    > [!div class="mx-imgBorder"]
    > ![Diagram předpokládaného aktuálního prostředí](./media/migrate-storm-to-spark/presumed-current-environment.png)

Chcete-li migrovat aplikaci ze systému na jedno z rozhraní API pro streamování Spark, postupujte následovně:

1. **Nasaďte nový cluster.** Nasaďte nový cluster HDInsight 4,0 Spark ve stejné virtuální síti a nasaďte na něj aplikaci pro streamování Sparku nebo strukturované streamování Sparku a důkladně ho otestujte.

    > [!div class="mx-imgBorder"]
    > ![nové nasazení Sparku ve službě HDInsight](./media/migrate-storm-to-spark/new-spark-deployment.png)

1. **V původním clusteru s více vlákny zastavte nenáročné.** V existujícím vlákně zastavte zpracování dat ze zdroje dat streamování a počkejte na dokončení zápisu dat do cílové jímky.

    > [!div class="mx-imgBorder"]
    > ![zastavit zpracování v aktuálním clusteru](./media/migrate-storm-to-spark/stop-consuming-current-cluster.png)

1. **Začněte spotřebovávat na novém clusteru Spark.** Spustí streamovaná data z nově nasazeného clusteru HDInsight 4,0 Spark. V tuto chvíli je proces převzatý z nejnovějšího posunu Kafka.

    > [!div class="mx-imgBorder"]
    > ![začít spotřebovávat na novém clusteru](./media/migrate-storm-to-spark/start-consuming-new-cluster.png)

1. **Odeberte původní cluster podle potřeby.** Jakmile je přepínač dokončen a funguje správně, odeberte starý cluster HDInsight 3,6 s tím, jak je potřeba.

    > [!div class="mx-imgBorder"]
    > ![Odeberte staré clustery HDInsight podle potřeby.](./media/migrate-storm-to-spark/remove-old-clusters1.png)

## <a name="next-steps"></a>Další kroky

Další informace o zaplavení, streamování Sparku a strukturovaném streamování Sparku najdete v následujících dokumentech:

* [Přehled streamování Apache Spark](../spark/apache-spark-streaming-overview.md)
* [Přehled strukturovaného streamování Apache Spark](../spark/apache-spark-structured-streaming-overview.md)
