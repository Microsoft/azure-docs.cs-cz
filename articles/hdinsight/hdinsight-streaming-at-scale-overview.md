---
title: Škálovatelné streamování ve službě Azure HDInsight
description: Jak používat streamování dat s škálovatelnými clustery Apache ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 3bf036ff413f720f54deef1a62b502668d1d8ac6
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871992"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Streamování ve velkém měřítku ve službě HDInsight

Řešení pro velké objemy dat v reálném čase působí na data, která se pohybují. Tato data jsou obvykle nejužitečnější v době doručení. Pokud je příchozí datový Stream větší, než se dá v tomto okamžiku zpracovat, budete možná muset omezit prostředky. Případně můžete cluster HDInsight škálovat tak, aby splňoval vaše řešení streamování, a to přidáním uzlů na vyžádání.

V aplikaci pro streamování jeden nebo víc zdrojů dat generuje události (někdy v milionech za sekundu), které je potřeba rychle přijmout, aniž by se musely vyhodit žádné užitečné informace. Příchozí události jsou zpracovávány pomocí *ukládání do vyrovnávací paměti služby Stream*, označovaného také jako služba *Event queuing*, například [Apache Kafka](kafka/apache-kafka-introduction.md) nebo [Event Hubs](https://azure.microsoft.com/services/event-hubs/). Po shromáždění událostí můžete data analyzovat pomocí analytického systému v reálném čase v rámci vrstvy *zpracování streamu* , jako je například [Apache Storm](storm/apache-storm-overview.md) nebo [streamování Apache Spark](spark/apache-spark-streaming-overview.md). Zpracovaná data mohou být uložena v dlouhodobém systému úložiště, například [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)a zobrazená v reálném čase na řídicím panelu Business Intelligence, jako je například [Power BI](https://powerbi.microsoft.com), Tableau nebo vlastní webová stránka.

:::image type="content" source="./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png" alt-text="Vzory streamování Azure HDInsight":::

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka poskytuje službu fronty zpráv s vysokou propustností a nízkou latencí a teď je součástí sady Apache Suite open source software (OSS). Kafka používá model zasílání zpráv pro publikování a odběr a ukládá proudy rozdělených dat do distribuovaného replikovaného clusteru. Kafka se škáluje lineárně, protože se zvyšuje propustnost.

Další informace najdete v tématu [představujeme Apache Kafka v HDInsight](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

Apache Storm je distribuovaný a open source výpočetní systém odolný proti chybám, který je optimalizovaný pro zpracování datových proudů v reálném čase pomocí Hadoop. Základní jednotkou dat pro příchozí událost je řazená kolekce členů, což je neproměnlivá sada párů klíč/hodnota. Nevázaná sekvence těchto řazených kolekcí členů tvoří datový proud, který pochází z Spout. Spout zabalí zdroj dat streamování (například Kafka) a vygeneruje řazené kolekce členů. Topologie přeplavování je posloupnost transformací na těchto streamech.

Další informace najdete v tématu [co je Apache Storm ve službě Azure HDInsight?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Spark Streaming

Streamování Sparku je rozšíření pro Spark, které umožňuje znovu použít stejný kód, který používáte pro dávkové zpracování. V rámci jedné aplikace můžete kombinovat dávkové i interaktivní dotazy. Na rozdíl od neúspěchu poskytuje streamování Spark stav přesně po sémantikě zpracování. Pokud se používá v kombinaci s [rozhraním API Kafka Direct](https://spark.apache.org/docs/latest/streaming-kafka-integration.html), které zajišťuje, aby všechna Kafka data přijímala pouze jednou za vás, je možné, že se přesně zajistěte. Jedna z možností streamování Sparku je odolná proti chybám, která umožňuje rychle obnovit chybové uzly, když se v clusteru používá víc uzlů.

Další informace najdete v tématu [co je Apache Spark streaming?](./spark/apache-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Škálování clusteru

I když během vytváření můžete určit počet uzlů v clusteru, možná budete chtít zvětšit nebo zmenšit cluster tak, aby odpovídal zatížení. Všechny clustery HDInsight umožňují [změnit počet uzlů v clusteru](hdinsight-administer-use-portal-linux.md#scale-clusters). Clustery Spark můžete vyřadit bez ztráty dat, protože všechna data jsou uložená v Azure Storage nebo Data Lake Storage.

Existují výhody pro oddělení technologií. Kafka je například technologie pro ukládání událostí do vyrovnávací paměti, takže je velmi náročné na vstupně-výstupní operace a nevyžaduje mnohem výpočetní výkon. V porovnání jsou datové procesory, jako je Spark streamování, náročné na výpočetní výkon, což vyžaduje výkonnější virtuální počítače. Díky tomu, že tyto technologie jsou oddělené v různých clusterech, je můžete škálovat nezávisle a přitom nejlépe využívat virtuální počítače.

### <a name="scale-the-stream-buffering-layer"></a>Škálování vrstvy vyrovnávací paměti streamu

Technologie ukládání do vyrovnávací paměti datového proudu Event Hubs a Kafka používají oddíly a spotřebitelé si z těchto oddílů čtou. Škálování vstupní propustnosti vyžaduje škálování počtu oddílů a přidání oddílů poskytuje rostoucí paralelismus. V Event Hubs se po nasazení nedá změnit počet oddílů, takže je důležité začít s cílovým škálováním. Pomocí Kafka je možné [Přidat oddíly](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion)i v případě, že Kafka zpracovává data. Kafka poskytuje nástroj k opětovnému přiřazení oddílů  `kafka-reassign-partitions.sh` . HDInsight poskytuje [Nástroj pro vyrovnávání repliky oddílů](https://github.com/hdinsight/hdinsight-kafka-tools)  `rebalance_rackaware.py` . Tento nástroj pro opětovné vyrovnávání zatížení zavolá `kafka-reassign-partitions.sh` Nástroj takovým způsobem, že každá replika je v samostatné doméně selhání a aktualizační doméně, což Kafka zablokuje a zvyšuje odolnost proti chybám.

### <a name="scale-the-stream-processing-layer"></a>Škálování vrstvy zpracování datového proudu

Datové proudy Apache Storm i Spark podporují přidávání pracovních uzlů do jejich clusterů, i když jsou data zpracovávána.

Chcete-li využít výhody nových uzlů přidaných pomocí škálování na více verzí, je nutné znovu vyvážit všechny topologie zatížení spuštěné před zvýšením velikosti clusteru. Toto nové vyrovnávání se dá udělat pomocí webového uživatelského rozhraní systému, nebo jeho CLI. Další informace najdete v dokumentaci k [Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Apache Spark používá tři klíčové parametry pro konfiguraci svého prostředí v závislosti na požadavcích aplikace: `spark.executor.instances` , `spark.executor.cores` a `spark.executor.memory` . *Vykonavatel* je proces, který se spustí pro aplikaci Spark. Prováděcí modul běží na pracovním uzlu a zodpovídá za provádění úkolů aplikace. Výchozí počet prováděcích modulů a velikost prováděcího modulu pro každý cluster se vypočítávají na základě počtu pracovních uzlů a velikosti pracovního uzlu. Tato čísla se ukládají do `spark-defaults.conf` souboru na každém hlavním uzlu clusteru.

Tyto tři parametry lze nakonfigurovat na úrovni clusteru, pro všechny aplikace, které jsou spuštěny v clusteru, a lze je také zadat pro každou jednotlivou aplikaci. Další informace najdete v tématu [Správa prostředků pro Apache Spark clustery](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Další kroky

* [Vytvoření a monitorování topologie Apache Storm ve službě Azure HDInsight](storm/apache-storm-quickstart.md)
* [Příklad topologií pro Apache Storm v HDInsight](storm/apache-storm-example-topology.md)
* [Úvod do Apache Spark ve službě HDInsight](spark/apache-spark-overview.md)
* [Začínáme s Apache Kafka ve službě HDInsight](kafka/apache-kafka-get-started.md)