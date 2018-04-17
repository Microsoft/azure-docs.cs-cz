---
title: Streamování škálované v Azure HDInsight | Microsoft Docs
description: Jak používat data streamování s škálovatelné clusterů HDInsight.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: raghavmohan
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: ramoha
ms.openlocfilehash: 4f1a0873ccdffde7e3567d7e3c50336b20749116
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="streaming-at-scale-in-hdinsight"></a>Streamování ve velkém měřítku ve službě HDInsight

Řešení pro velká data v reálném čase fungují na data, která je v provozu. Tato data je obvykle nejcennější na jeho čas doručení. Příchozí datový proud přestane být delší, než se v daném okamžiku může být zpracována, musíte k omezení ukončí prostředky. Alternativně clusteru HDInsight můžete postupně škálovat podle řešení streamování tak, že přidáte uzly na vyžádání.

V aplikaci streamování jsou jeden nebo více zdrojů dat generování události (někdy v miliony za sekundu), které potřebují rychle konzumaci bez vyřadit všechny užitečné informace. Příchozí události jsou zpracovány *ukládání proudu do vyrovnávací paměti*, označovaný také *událostí služby Řízení front*, službu, jako třeba [Kafka](kafka/apache-kafka-introduction.md) nebo [Event Hubs](https://azure.microsoft.com/services/event-hubs/). Po můžete shromažďovat události, pak můžete analyzovat data s využitím systému analýzu v reálném čase v rámci *zpracování datového proudu* vrstvy, jako například [Storm](storm/apache-storm-overview.md) nebo [vysílání datového proudu Spark](spark/apache-spark-streaming-overview.md). Zpracovaná data mohou být uloženy v systémech dlouhodobé úložiště, jako je třeba [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)a zobrazí v reálném čase na řídicím panelu business intelligence, jako například [Power BI](https://powerbi.microsoft.com), Tableau, nebo vlastní webové stránky .

![Vzory streamování HDInsight](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka poskytuje vysokou propustností, služba služby Řízení front zpráv s nízkou latencí a je nyní součástí sady Apache softwaru otevřený zdroj (OSS). Kafka používá publikování a odběru zpráv modelu a úložišť, bezpečně v clusteru s podporou distribuované, replikované oddílů datových proudů. Kafka se škáluje lineárně jako zvyšuje propustnost.

Další informace najdete v tématu [představení Apache Kafka v HDInsight](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

Apache Storm je distribuované, odolný proti chybám, open source výpočetní systém, která je optimalizovaná pro zpracování datových proudů dat v reálném čase pomocí Hadoop. Základní jednotka dat pro příchozí události je řazené kolekce členů, které je neměnné sada párů klíč/hodnota. Bez vazby posloupností tyto formuláře řazených kolekcí členů datový proud, který je pochází z funkcí Spout. Spout zabalí streamování (například Kafka) zdroj dat a vysílá řazené kolekce členů. Topologie storm je posloupnost transformace na těchto datových proudů.

Další informace najdete v tématu [co je Apache Storm v Azure HDInsight?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Vysílání datového proudu Spark

Vysílání datového proudu Spark je rozšíření Spark, která umožňuje znovu použít stejný kód, který používáte pro dávkové zpracování. Můžete kombinovat batch a interaktivních dotazů ve stejné aplikaci. Na rozdíl od Storm, Spark streamování poskytuje stateful přesně-po zpracování sémantiku. Když se používá v kombinaci s [přímé rozhraní API Kafka](http://spark.apache.org/docs/latest/streaming-kafka-integration.html), který zajišťuje, že všechny Kafka přijatá data pomocí vysílání datového proudu Spark právě jednou, je možné dosáhnout začátku do konce přesně-jednou zaručuje. Jeden z Spark streamování síly je odolný proti chybám možnosti obnovení došlo k chybě uzly rychle při více uzlů se používají v rámci clusteru.

Další informace najdete v tématu [novinky vysílání datového proudu Spark?](hdinsight-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Škálování clusteru

Ačkoli můžete určit počet uzlů v clusteru během vytváření, můžete chtít zvětšit nebo zmenšit cluster tak, aby odpovídaly zatížení. Všechny clustery HDInsight umožňují [změnit počet uzlů v clusteru](hdinsight-administer-use-management-portal.md#scale-clusters). Clustery Spark můžete vyřadit bez ztráty dat, jako je všechna data uložená v Azure Storage nebo Data Lake Store.

Existují výhody oddělovací technologie. Například Kafka je událost ukládání do vyrovnávací paměti technologie, tak je velmi vstupně-výstupní operace náročné a nemusí mnohem výpočetní výkon. Porovnání jsou procesory datového proudu, jako je například vysílání datového proudu Spark náročné, vyžadování výkonnější virtuálních počítačů. Tak, že tyto technologie odpojené do různých clusterech, je možné škálovat je nezávisle při nejlepší využití virtuálních počítačů.

### <a name="scale-the-stream-buffering-layer"></a>Škálování datového proudu do vyrovnávací paměti vrstvy

Datového proudu do vyrovnávací paměti technologie Event Hubs a Kafka, jak použít oddíly a spotřebitelé číst z těchto oddílů. Škálování vstupu propustnost vyžaduje vertikálním navýšení kapacity počet oddílů a přidání oddílů poskytuje roste stupně paralelního zpracování. Proto je důležité začít s měřítkem cíl na paměti, nelze v Event Hubs, změnit počet oddílů po nasazení. S Kafka, je možné [přidat oddíly](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion), i když Kafka zpracovává data. Kafka poskytuje nástroj pro změnu přiřazení oddíly, `kafka-reassign-partitions.sh`. HDInsight nabízí [repliky oddílu vyrovnává nástroj](https://github.com/hdinsight/hdinsight-kafka-tools), `rebalance_rackaware.py`. Tento nástroj rebalancing volá `kafka-reassign-partitions.sh` nástroj tak, že každou repliku je v doméně samostatné selhání a aktualizace domény, provedení Kafka rack clustery a roste odolnost proti chybám.

### <a name="scale-the-stream-processing-layer"></a>Škálovat vrstvu zpracování datového proudu

Apache Storm a Spark streamování podporují přidání uzlů pracovního procesu pro své clustery i dat je zpracovávána.

Abyste mohli využívat nové uzly, které se přidávají prostřednictvím škálování Storm, budete muset vyrovnat žádné Storm topologie spuštěné před zvýšením velikosti clusteru. Toto nové vyrovnání lze provést pomocí Storm webového uživatelského rozhraní nebo její rozhraní příkazového řádku. Další informace najdete v tématu [dokumentaci Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Apache Spark pro konfiguraci jeho prostředí, v závislosti na požadavcích aplikace používá tři klíčové parametry: `spark.executor.instances`, `spark.executor.cores`, a `spark.executor.memory`. *Vykonavatele* je proces, který se spustí aplikace Spark. Vykonavatele spustí v pracovním uzlu a je odpovědná za plnění úkolů aplikace. Výchozí počet velikost vykonavatele pro každý cluster a vykonavatelů jsou vypočítáváno na počet uzlů pracovního procesu a velikost uzlu pracovníka. Tato čísla jsou uložené v `spark-defaults.conf`souboru každý hlavního uzlu clusteru.

Tyto tři parametry lze konfigurovat na úrovni clusteru, pro všechny aplikace, které běží na clusteru a dá se zadat taky pro všechny aplikace. Další informace najdete v tématu [správu prostředků pro clustery Apache Spark](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Další postup

* [Začínáme s Apache Storm v HDInsight](storm/apache-storm-tutorial-get-started-linux.md)
* [Příklad topologií pro Apache Storm v HDInsight](storm/apache-storm-example-topology.md)
* [Úvod do Spark v HDInsight](spark/apache-spark-overview.md)
* [Začněte s Apache Kafka v HDInsight](kafka/apache-kafka-get-started.md)
