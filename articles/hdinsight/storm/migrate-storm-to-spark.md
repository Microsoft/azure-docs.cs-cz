---
title: Migrace Azure HDInsight 3.6 Apache Storm do HDInsight 4.0 Apache Spark
description: Rozdíly a tok migrace pro migraci úloh Apache Storm do streamování Spark nebo Strukturované streamování Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/16/2019
ms.openlocfilehash: 916c54c3739d1164e4e9c1db67aa1f4e0dbd0c6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157789"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>Migrace Azure HDInsight 3.6 Apache Storm do HDInsight 4.0 Apache Spark

Tento dokument popisuje, jak migrovat úlohy Apache Storm na HDInsight 3.6 na HDInsight 4.0. HDInsight 4.0 nepodporuje typ clusteru Apache Storm a budete muset migrovat na jinou streamovací datovou platformu. Dvě vhodné možnosti jsou Apache Spark Streaming a Spark Structured Streaming. Tento dokument popisuje rozdíly mezi těmito platformami a také doporučuje pracovní postup pro migraci úloh Apache Storm.

## <a name="storm-migration-paths-in-hdinsight"></a>Cesty migrace bouře v HDInsightu

Pokud chcete migrovat z Apache Storm na HDInsight 3.6 máte několik možností:

* Streamování spark na HDInsight 4.0
* Strukturované streamování spark na hdinsightu 4.0
* Azure Stream Analytics

Tento dokument poskytuje průvodce pro migraci z Apache Storm na Spark Streaming a Spark Structured Streaming.

> [!div class="mx-imgBorder"]
> ![Cesta migrace bouře HDInsight](./media/migrate-storm-to-spark/storm-migration-path.png)

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Porovnání mezi Apache Storm a Spark Streaming, Spark Structured Streaming

Apache Storm můžete poskytovat různé úrovně zaručeného zpracování zprávy. Například základní aplikace Storm může zaručit alespoň jednou zpracování a [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) může zaručit přesně jednou zpracování. Spark Streaming a Spark Structured Streaming zaručují, že všechny vstupní události jsou zpracovány přesně jednou, i když dojde k selhání uzlu. Storm má model, který zpracovává každou jednotlivou událost a můžete také použít model Micro Batch s Trident. Spark Streaming a Spark Structured Streaming poskytují model zpracování Micro-Batch.

|  |Storm |Streamování Sparku | Strukturované streamování jiskry|
|---|---|---|---|
|**Záruka zpracování událostí**|Alespoň jednou <br> Přesně jednou (Trident) |[Přesně jednou](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[Přesně jednou](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Model zpracování**|Reálný čas <br> Mikro dávka (Trident) |Mikro dávka |Mikro dávka |
|**Podpora času na akci**|[Ano](https://storm.apache.org/releases/2.0.0/Windowing.html)|Ne|[Ano](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Jazyky**|Java, atd.|Scala, Java, Python|Python, R, Scala, Java, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Streamování spark vs Strukturované streamování Spark

Strukturované streamování Spark nahrazuje streamování Spark (DStreams). Strukturované streamování bude nadále přijímat vylepšení a údržbu, zatímco DStreams bude pouze v režimu údržby. **Poznámka: potřebujete odkazy pro zdůraznění tohoto bodu**. Strukturované streamování nemá tolik funkcí jako DStreams pro zdroje a propady, které podporuje po vybalení z krabice, takže vyhodnoťte své požadavky a zvolte příslušnou možnost zpracování datového proudu Spark.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>Zpracování streamování (jedna událost) vs Mikrodávkové zpracování

Storm poskytuje model, který zpracovává každou jednotlivou událost. To znamená, že všechny příchozí záznamy budou zpracovány ihned po jejich doručení. Aplikace streamování spark musí čekat zlomek sekundy shromažďovat každý mikro dávky událostí před odesláním této dávky na zpracování. Naproti tomu aplikace řízená událostmi zpracuje každou událost okamžitě. Latence streamování spark je obvykle pod několik sekund. Výhodami mikrodávkového přístupu jsou efektivnější zpracování dat a jednodušší souhrnné výpočty.

> [!div class="mx-imgBorder"]
> ![streamování a mikrodávkové zpracování](./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png)

## <a name="storm-architecture-and-components"></a>Architektura a komponenty bouře

Topologie Storm se skládají z několika součástí, které jsou uspořádány do orientovaného acyklického grafu (DAG). Data proudí mezi komponentami v grafu. Každá komponenta spotřebovává jeden či více datových streamů a případně může i jeden či více streamů vysílat.

|Komponenta |Popis |
|---|---|
|Hubička|Přináší data do topologie. Vysílají do topologie jeden nebo více datových proudů.|
|Šroub|Spotřebovává proudy vyzařované z hubic nebo jiných šroubů. Bolty mohou volitelně vysílat do topologie datové streamy. Bolty také odpovídají za zápis dat do externích služeb nebo úložiště, například HDFS, Kafka nebo HBase.|

> [!div class="mx-imgBorder"]
> ![interakce komponent bouře](./media/migrate-storm-to-spark/apache-storm-components.png)

Storm se skládá z následujících tří daemons, které udržují Storm clusteru funkční.

|Daemon |Popis |
|---|---|
|Nimbus|Podobně jako Hadoop JobTracker, je zodpovědný za distribuci kódu kolem clusteru a přiřazení úkolů počítačům a sledování poruch.|
|Zookeeper|Používá se pro koordinaci clusteru.|
|Supervisor|Naslouchá práci přiřazené svému počítači a spouští a zastavuje pracovní procesy na základě směrnic nimbusu. Každý pracovní proces provede podmnožinu topologie. Zde běží aplikační logika uživatele (Spouts a Bolt).|

> [!div class="mx-imgBorder"]
> ![nimbus, zookeeper a dozorce daemons](./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png)

## <a name="spark-streaming-architecture-and-components"></a>Architektura a komponenty streamování Spark

Následující kroky shrnují, jak komponenty spolupracují ve streamech Spark Streaming (DStreams) a Strukturované streamování Spark:

* Při spuštění streamování Spark ovladač spustí úlohu v prováděcím modulu.
* Vykonavatel přijme datový proud ze zdroje dat datového proudu.
* Když vykonavatel přijímá datové proudy, rozdělí datový proud na bloky a uchová je v paměti.
* Bloky dat jsou replikovány do jiných vykonavatelů.
* Zpracovaná data jsou pak uložena v cílovém úložišti dat.

> [!div class="mx-imgBorder"]
> ![spark streaming cesta k výstupu](./media/migrate-storm-to-spark/spark-streaming-to-output.png)

## <a name="spark-streaming-dstream-workflow"></a>Pracovní postup Streamování jiskry (DStream)

S ukončením každého intervalu dávky se vytváří nový RDD, který obsahuje všechna data z tohoto intervalu. Souvislé sady RDD jsou shromažďovány do DStream. Například pokud interval dávky je jedna sekunda dlouhá, DStream vyzařuje dávku každou sekundu obsahující jeden RDD, který obsahuje všechna data pohostinovaná během této sekundy. Při zpracování DStream, teplota událost se zobrazí v jedné z těchto dávek. Aplikace Spark Streaming zpracovává dávky, které obsahují události a nakonec funguje na data uložená v každém RDD.

> [!div class="mx-imgBorder"]
> ![dávky zpracování streamování](./media/migrate-storm-to-spark/spark-streaming-batches.png)

Podrobnosti o různých transformacích dostupných pomocí streamování Spark najdete [v tématu Transformace na DStreams](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="spark-structured-streaming"></a>Strukturované streamování jiskry

Strukturované streamování spark představuje datový proud jako tabulku, která je neohraničená do hloubky. Tabulka se s příchodem nových dat nadále rozrůstá. Tato vstupní tabulka je průběžně zpracovávána dlouhotrvajícím dotazem a výsledky jsou odesílány do výstupní tabulky.

Ve strukturovaném streamování data dorazí do systému a je okamžitě ingestována do vstupní tabulky. Můžete psát dotazy (pomocí DataFrame a Dataset API), které provádějí operace proti této vstupní tabulce.

Výstup dotazu poskytuje *tabulku výsledků*, která obsahuje výsledky dotazu. Můžete čerpat data z tabulky výsledků pro externí úložiště dat, jako je relační databáze.

Časování zpracování dat ze vstupní tabulky je řízeno intervalem aktivační události. Ve výchozím nastavení je interval aktivační události nulový, takže strukturované streamování se pokusí zpracovat data, jakmile dorazí. V praxi to znamená, že jakmile strukturované streamování je provedeno zpracování spuštění předchozího dotazu, spustí další zpracování spustit proti všechna nově přijatá data. Aktivační událost můžete nakonfigurovat tak, aby se spouštěla v intervalu, aby byla streamovaná data zpracována v dávkách založených na čase.

> [!div class="mx-imgBorder"]
> ![zpracování dat ve strukturovaném streamování](./media/migrate-storm-to-spark/structured-streaming-data-processing.png)

> [!div class="mx-imgBorder"]
> ![programovací model pro strukturované streamování](./media/migrate-storm-to-spark/structured-streaming-model.png)

## <a name="general-migration-flow"></a>Obecný migrační tok

Doporučený tok migrace z Storm u Spark předpokládá následující počáteční architekturu:

* Kafka se používá jako zdroj dat streamování
* Kafka a Storm jsou nasazeny ve stejné virtuální síti
* Data zpracovaná stormem se zapisují do jímky dat, jako je Azure Storage nebo Azure Data Lake Storage Gen2.

    > [!div class="mx-imgBorder"]
    > ![diagram předpokládaného současného prostředí](./media/migrate-storm-to-spark/presumed-current-environment.png)

Pokud chcete aplikaci migrovat z Stormu do jednoho z datových proudů Spark, postupujte takto:

1. **Nasaďte nový cluster.** Nasaďte nový cluster HDInsight 4.0 Spark ve stejné virtuální síti a nasaďte na něj aplikaci Spark Streaming nebo Spark Structured Streaming a důkladně ji otestujte.

    > [!div class="mx-imgBorder"]
    > ![nové nasazení jiskry v HDInsightu](./media/migrate-storm-to-spark/new-spark-deployment.png)

1. **Přestaňte spotřebovávat na staré Storm clusteru.** V existující storm, zastavit spotřebovávat data ze zdroje dat streamování a počkejte, až data dokončit zápis do cílovéjímu propadu.

    > [!div class="mx-imgBorder"]
    > ![zastavení spotřeby v aktuálním clusteru](./media/migrate-storm-to-spark/stop-consuming-current-cluster.png)

1. **Začněte spotřebovávat nový cluster Spark.** Spusťte streamování dat z nově nasazeného clusteru HDInsight 4.0 Spark. V tomto okamžiku je proces převzat spotřebovává z nejnovějšího posunu Kafka.

    > [!div class="mx-imgBorder"]
    > ![začít spotřebovávat v novém clusteru](./media/migrate-storm-to-spark/start-consuming-new-cluster.png)

1. **Podle potřeby odeberte starý cluster.** Po dokončení přepínače a správném fungování odeberte podle potřeby starý cluster HDInsight 3.6 Storm.

    > [!div class="mx-imgBorder"]
    > ![podle potřeby odeberte staré clustery HDInsight](./media/migrate-storm-to-spark/remove-old-clusters1.png)

## <a name="next-steps"></a>Další kroky

Další informace o stormu, streamování Spark a strukturovaném streamování Spark najdete v následujících dokumentech:

* [Přehled streamování Apache Spark](../spark/apache-spark-streaming-overview.md)
* [Přehled strukturovaného streamování Apache Spark](../spark/apache-spark-structured-streaming-overview.md)