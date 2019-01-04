---
title: Streamování ve velkém měřítku v Azure HDInsight
description: Jak používat data streamování s škálovatelné clustery HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 01db1de5c6b533c346ce35c8474d996213873d10
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "54002191"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Streamování ve velkém měřítku ve službě HDInsight

Řešení pro velké objemy dat v reálném čase reagovat na data, která jsou v pohybu. Tato data jsou obvykle nejcennější v době jeho přijetí. Příchozí datový proud bude větší, než může být zpracována v daném okamžiku, budete muset omezení ukončí prostředky. Alternativně clusteru služby HDInsight můžete vertikálně navýšit kapacitu pro splnění vašich řešení streamování přidáním uzlů na vyžádání.


V případě streamování aplikace jsou jeden nebo více zdrojů dat generování události (někdy v milionech za sekundu), které je potřeba ingestovat rychle bez jejich odstranění jakékoli užitečné informace. Příchozí události jsou zpracovány *ukládání do vyrovnávací paměti datového proudu*, označované také jako *řazení událostí do front*, službou, jako [Apache Kafka](kafka/apache-kafka-introduction.md) nebo [Event Hubs](https://azure.microsoft.com/services/event-hubs/). Jakmile shromáždíte události, pak můžete analyzovat data s využitím systému analýzy v reálném čase v rámci *zpracování datových proudů* vrstvy, jako například [Apache Storm](storm/apache-storm-overview.md) nebo [Apache Spark Streaming](spark/apache-spark-streaming-overview.md). Zpracovaná data mohou být uloženy v systémech dlouhodobého úložiště, jako je třeba [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)a zobrazí v reálném čase na řídicím panelu business intelligence, jako například [Power BI](https://powerbi.microsoft.com), Tableau nebo vlastní web stránka.


![Vzory streamování HDInsight](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka poskytuje vysokou propustnost, služba služby Řízení front zpráv s nízkou latencí a je nyní součástí sady Apache Software Open Source (OSS). Kafka používá publikování a přihlášení k odběru zasílání zpráv model a úložiště proudů dělená data bezpečně v distribuované, replikované clusteru. Kafka se škáluje lineárně, jak se zvyšuje propustnost.

Další informace najdete v tématu [představení Apache Kafka v HDInsight](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

Apache Storm je distribuovaný, odolné proti chybám, open source výpočetní systém, který je optimalizovaný pro zpracování streamů dat v reálném čase pomocí Hadoop. Základní jednotka dat pro příchozí události je řazená kolekce členů, které je neměnný sada párů klíč/hodnota. Bez vazby posloupnost tyto formy řazených kolekcí členů Stream, který je pochází z Spout. Spout zabalí streamování zdroje dat (například Kafka) a vysílá řazené kolekce členů. Topologie storm je posloupnost transformace na těchto datových proudů.

Další informace najdete v tématu [co je Apache Storm v Azure HDInsight?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Streamování Sparku

Streamování Sparku je rozšířením pro Spark, který vám umožní znovu použít stejný kód, který používáte pro dávkové zpracování. Můžete kombinovat dávkové a interaktivní dotazy ve stejné aplikaci. Na rozdíl od Storm, Spark Streaming poskytuje stavová přesně – jedno zpracování sémantiku. Když použijete v kombinaci s [Kafka API s přímým přístupem](https://spark.apache.org/docs/latest/streaming-kafka-integration.html), který zajišťuje, že všechny dat Kafka se službou Spark Streaming obdrženy pouze jednou, je možné dosáhnout začátku do konce přesně-jednou záruky. Jednou z Spark Streaming předností je odolné proti chybám možnosti obnovení došlo k chybě uzly rychle při více uzlů se používají v rámci clusteru.

Další informace najdete v tématu [co je Apache Spark Streaming?](hdinsight-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Škálování clusteru

I když můžete určit počet uzlů v clusteru během vytváření, může chtít zvětšit nebo zmenšit cluster tak, aby odpovídaly zatížení. Všechny clustery HDInsight umožňují [změnit počet uzlů v clusteru](hdinsight-administer-use-management-portal.md#scale-clusters). Clustery Spark můžete vyřadit bez ztráty dat, jako ukládána všechna data v Azure Storage nebo Azure Data Lake Storage.

Existují výhody oddělovací technologie. Například Kafka je událost ukládání do vyrovnávací paměti technologie, takže je velmi vstupně-výstupní operace náročné na a nepotřebuje většinu výpočetní výkon. Procesorů streamu, jako je Spark Streaming v porovnání, jsou náročné na výpočetní, které vyžadují výkonnější virtuální počítače. Tím, že tyto technologie odděleném do různých clusterech, je možné škálovat je nezávisle na sobě při nejlíp využít virtuálních počítačů.

### <a name="scale-the-stream-buffering-layer"></a>Škálování datového proudu do vyrovnávací paměti vrstvy

Datového proudu do vyrovnávací paměti technologie služby Event Hubs a Kafka používají oddíly a spotřebitelé číst z těchto oddílů. Škálování propustnost vstupu, vyžaduje škálování počtu oddílů a přidání oddílů poskytuje rostoucí paralelismu. Proto je důležité začít v měřítku cíl na paměti, není ve službě Event Hubs změnit počet oddílů po nasazení. S využitím Kafka, je možné [přidat oddíly](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion), dokonce i za běhu Kafka zpracovává data. Kafka poskytuje nástroj pro změnu přiřazení oddíly, `kafka-reassign-partitions.sh`. HDInsight poskytuje [repliky oddílu opětovné vyvážení nástroj](https://github.com/hdinsight/hdinsight-kafka-tools), `rebalance_rackaware.py`. Tento nástroj rebalancing volá `kafka-reassign-partitions.sh` nástroj takovým způsobem, že každá replika je v doméně samostatné selhání a aktualizačních domén provádění Kafka vědět a rostoucí odolnost stojanu proti chybám.

### <a name="scale-the-stream-processing-layer"></a>Škálování zpracování vrstvě stream

Apache Storm a Spark Streaming podporovat přidávání pracovních uzlů do svých clusterů, dokonce i za běhu data zpracovávají.

Abyste mohli využívat nové uzly přidané prostřednictvím škálování Storm, budete muset vyrovnat jakékoli topologie Storm spuštěné před zvýšením velikosti clusteru. Toto nové vyvážení lze provést pomocí Storm webového uživatelského rozhraní nebo její rozhraní příkazového řádku. Další informace najdete v tématu [dokumentaci Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Apache Spark pro konfiguraci prostředí, v závislosti na požadavcích aplikace používá tři klíčové parametry: `spark.executor.instances`, `spark.executor.cores`, a `spark.executor.memory`. *Prováděcí modul* je proces, který se spustí aplikace Spark. Vykonavatele běží na pracovní uzel a zodpovídá za plnění úkolů vaší aplikace. Výchozí číslo prováděcí moduly a velikostí prováděcí modul pro každý cluster se počítají na základě počtu pracovních uzlů a velikost uzlu pracovního procesu. Tato čísla ukládají do `spark-defaults.conf`soubor na každém hlavního uzlu clusteru.

Tyto tři parametry lze nastavit na úrovni clusteru pro všechny aplikace, které běží na clusteru a je taky možné specifikovat pro každou jednotlivou aplikaci. Další informace najdete v tématu [správu prostředků pro clustery Apache Spark](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Další postup

* [Začínáme s Apache Storm v HDInsight](storm/apache-storm-tutorial-get-started-linux.md)
* [Příklad topologií pro Apache Storm v HDInsight](storm/apache-storm-example-topology.md)
* [Úvod do Apache Spark v HDInsight](spark/apache-spark-overview.md)
* [Začínáme s Apache Kafka v HDInsight](kafka/apache-kafka-get-started.md)
