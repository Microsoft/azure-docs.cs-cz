---
title: Streamování ve velkém měřítku v Azure HDInsight
description: Jak používat datové streamování s škálovatelnými clustery Apache v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 006310f1a0efa69881bbe6d6ea4403b9c50402e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435397"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Streamování ve velkém měřítku ve službě HDInsight

Řešení pro velké objemy dat v reálném čase působí na data, která jsou v pohybu. Obvykle jsou tato data nejcennější v době příjezdu. Pokud příchozí datový proud se stane větší, než lze zpracovat v tomto okamžiku, budete muset omezit prostředky. Případně může cluster HDInsight škálovat tak, aby vyhovoval vašemu řešení streamování přidáním uzlů na vyžádání.

V aplikaci streamování jeden nebo více zdrojů dat jsou generování událostí (někdy v milionech za sekundu), které je třeba ingestovat rychle bez ztráty jakékoli užitečné informace. Příchozí události jsou zpracovány s *ukládáním datového proudu do vyrovnávací paměti*, nazývané také fronty *událostí*, službou, jako je [Apache Kafka](kafka/apache-kafka-introduction.md) nebo [Event Hubs](https://azure.microsoft.com/services/event-hubs/). Po shromáždění událostí pak můžete analyzovat data pomocí analytického systému v reálném čase ve vrstvě *zpracování datového proudu,* jako je [Apache Storm](storm/apache-storm-overview.md) nebo Apache [Spark Streaming](spark/apache-spark-streaming-overview.md). Zpracovaná data se můžou ukládat v systémech dlouhodobého úložiště, jako je [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), a zobrazovat je v reálném čase na řídicím panelu business intelligence, jako je Power [BI](https://powerbi.microsoft.com), Tableau nebo vlastní webová stránka.

![Vzory streamování Azure HDInsight](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka poskytuje službu fronty zpráv s vysokou propustností a nízkou latencí a nyní je součástí sady Apache open source softwaru (OSS). Kafka používá model publikování a odběru zpráv a bezpečně ukládá datové proudy dělených dat v distribuovaném replikovaném clusteru. Kafka měří lineárně jako propustnost zvyšuje.

Další informace naleznete [v tématu Introducing Apache Kafka on HDInsight](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

Apache Storm je distribuovaný, bezchybný výpočetní systém s otevřeným zdrojovým kódem, který je optimalizován pro zpracování datových proudů dat v reálném čase s Hadoopem. Základní jednotkou dat pro příchozí událost je tuple, což je neměnná sada párů klíč/hodnota. Neohraničená sekvence těchto n-tic tvoří Stream, který pochází z výtoku. Výtok zabalí zdroj dat streamování (například Kafka) a vyzařuje n-tic. Bouřková topologie je posloupnost transformací na těchto proudech.

Další informace najdete v tématu [Co je Apache Storm na Azure HDInsight?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Streamování jiskry

Spark Streaming je rozšíření pro Spark, které umožňuje znovu použít stejný kód, který používáte pro dávkové zpracování. Dávkové i interaktivní dotazy můžete kombinovat ve stejné aplikaci. Na rozdíl od Storm, Spark Streaming poskytuje stavové přesně jednou zpracování sémantiky. Při použití v kombinaci s [rozhraním Kafka Direct API](https://spark.apache.org/docs/latest/streaming-kafka-integration.html), které zajišťuje, že všechna data Kafka jsou přijímána spark streamingem přesně jednou, je možné dosáhnout end-to-end přesně jednou záruky. Jednou ze silných stránek streamování Spark je možnosti odolné proti chybám, které rychle obnovují chybové uzly, když se v clusteru používá více uzlů.

Další informace najdete v tématu [Co je Apache Spark Streaming?](hdinsight-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Změna velikosti clusteru

I když můžete zadat počet uzlů v clusteru během vytváření, můžete chtít zvětšit nebo zmenšit clusteru tak, aby odpovídaly zatížení. Všechny clustery HDInsight umožňují [změnit počet uzlů v clusteru](hdinsight-administer-use-portal-linux.md#scale-clusters). Clustery Spark lze vynechat bez ztráty dat, protože všechna data se ukládají ve službě Azure Storage nebo Data Lake Storage.

Oddělení technologií má své výhody. Například Kafka je technologie ukládání do vyrovnávací paměti událostí, takže je velmi náročná na IO a nepotřebuje mnoho výpočetního výkonu. Ve srovnání s procesory datových proudů, jako je Spark Streaming jsou náročné na výpočetní výkon, které vyžadují výkonnější virtuální počítače. Tím, že tyto technologie oddělené do různých clusterů, můžete škálovat nezávisle a zároveň nejlépe využívat virtuálních můl.

### <a name="scale-the-stream-buffering-layer"></a>Změna velikosti vrstvy vyrovnávací paměti datového proudu

Technologie ukládání dat do vyrovnávací paměti technologie Event Hubs a Kafka použití oddílů a spotřebitelé číst z těchto oddílů. Škálování vstupní propustnost vyžaduje škálování do počtu oddílů a přidání oddílů poskytuje zvýšení paralelismu. V event hubech se počet oddílů po nasazení nezmění, takže je důležité začít s ohledem na cílové škálování. S Kafkou je možné [přidávat oddíly](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion), i když Kafka zpracovává data. Kafka poskytuje nástroj pro opětovné `kafka-reassign-partitions.sh`přiřazení oddílů. HDInsight poskytuje [nástroj pro vyvažování replik oddílů](https://github.com/hdinsight/hdinsight-kafka-tools), `rebalance_rackaware.py`. Tento nástroj pro vyvažování volá `kafka-reassign-partitions.sh` nástroj takovým způsobem, že každá replika je v samostatné doméně selhání a aktualizační doméně, takže kafka rack vědom a zvyšuje odolnost proti chybám.

### <a name="scale-the-stream-processing-layer"></a>Změna velikosti vrstvy zpracování datového proudu

Apache Storm i Spark Streaming podporují přidávání pracovních uzlů do svých clusterů, i když se data zpracovávají.

Chcete-li využít výhod nových uzlů přidaných pomocí škálování Storm, je třeba znovu vyvážit všechny topologií Storm spuštěna před zvětšením velikosti clusteru. Toto vyvážení lze provést pomocí webového uživatelského uživatelského uživatelského uživatelského uživatelského uživatelského pravidla Storm nebo jeho cli. Další informace naleznete v [dokumentaci apache storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Apache Spark používá tři klíčové parametry pro konfiguraci `spark.executor.instances`svého `spark.executor.cores`prostředí, v závislosti na požadavcích aplikace: , a `spark.executor.memory`. *Vykonavatel* je proces, který se spustí pro aplikaci Spark. Vykonavatel běží na uzlu pracovníka a je zodpovědný za provádění úkolů aplikace. Výchozí počet vykonavatelů a velikosti vykonavatelů pro každý cluster se počítají na základě počtu pracovních uzlů a velikosti pracovního uzlu. Tato čísla jsou `spark-defaults.conf`uložena v souboru v každém hlavním uzlu clusteru.

Tyto tři parametry lze nakonfigurovat na úrovni clusteru pro všechny aplikace spuštěné v clusteru a lze je také zadat pro každou jednotlivou aplikaci. Další informace naleznete v [tématu Správa prostředků pro clustery Apache Spark](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Další kroky

* [Vytvoření a monitorování topologie Apache Storm v Azure HDInsight](storm/apache-storm-quickstart.md)
* [Příklad topologií pro Apache Storm v HDInsight](storm/apache-storm-example-topology.md)
* [Úvod do Apache Spark na HDInsight](spark/apache-spark-overview.md)
* [Začínáme s Apache Kafka ve službě HDInsight](kafka/apache-kafka-get-started.md)
