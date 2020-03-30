---
title: Co je Apache HBase v Azure HDInsight?
description: Úvod do Apache HBase v HDInsight, databáze typu NoSQL postavené na Hadoop. Další informace o případech použití a porovnání HBase s dalšími clustery Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/03/2020
ms.openlocfilehash: 97814f4d22629fd74f395887a7361a3aabe55012
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78271840"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Co je Apache HBase v Azure HDInsight

[Apache HBase](https://hbase.apache.org/) je open-source, NoSQL databáze, která je postavena na [Apache Hadoop](https://hadoop.apache.org/) a modelována podle [Google BigTable](https://cloud.google.com/bigtable/). HBase poskytuje náhodný přístup a silnou konzistenci pro velké objemy nestrukturovaných a částečně strukturovaných dat v databázi schemaless uspořádané podle rodin sloupců.

Z pohledu uživatele hbase je podobný databázi. Data jsou uložena v řádcích a sloupcích tabulky a data v rámci řádku jsou seskupena podle rodiny sloupců. HBase je schemaless databáze ve smyslu, že před jejich použitím není třeba definovat sloupce ani v nich uložený typ dat. Kód open-source se škáluje lineárně pro manipulaci s petabajty dat na tisících uzlech. Může se spoléhat na redundanci dat, zpracování dávkou a další funkce, které jsou poskytovány pomocí distribuovaných aplikací v ekosystému Hadoop.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Jak se Apache HBase implementuje v Azure HDInsight?

HDInsight HBase je nabízena jako spravovaný cluster, který je integrován do prostředí Azure. Clustery jsou nakonfigurované pro ukládání dat přímo ve [službě Azure Storage](./../hdinsight-hadoop-use-blob-storage.md), která poskytuje nízkou latenci a zvýšenou elasticitu ve výběru výkonu a nákladů. To umožňuje zákazníkům vytvářet interaktivní weby, které pracují s rozsáhlými datovými sadami a vytvářet služby, které ukládají údaje ze snímačů a telemetrie z milionů koncových bodů a analyzovat tato data pomocí úloh Hadoop. HBase a Hadoop jsou dobré počáteční body pro projekt velkých objemů dat v Azure; zejména umožňují spolupráci aplikací v reálném čase s rozsáhlými datovými sadami.

Implementace HDInsight využívá architekturu škálování HBase k zajištění automatického dělení tabulek, silnou konzistenci pro čtení a zápis a automatické převzetí služeb při selhání. Výkon je zvýšen ukládáním do mezipaměti pro čtení a vysokou propustností datových proudů pro zápis. Cluster HBase můžete vytvořit uvnitř virtuální sítě. Podrobnosti najdete v tématu [Vytváření clusterů HDInsight v síti Azure Virtual Network](./apache-hbase-provision-vnet.md).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Jakým způsobem jsou data spravována v HDInsight HBase?

Data mohou být spravována v HBase pomocí příkazů `create`, `get`, `put`, a `scan` z prostředí HBase. Data se zapisují do databáze pomocí `put` a čtou se pomocí `get`. Příkaz `scan` se používá k načítání dat z více řádků v tabulce. Data lze také spravovat pomocí rozhraní API HBase C#, které poskytuje knihovna klienta nad HBase REST API. Databáze HBase může být také dotazovánpomocí [Apache Hive](https://hive.apache.org/). Úvod k těmto programovacím modelům najdete [v tématu Začínáme používat Apache HBase s Apache Hadoop v HDInsight](./apache-hbase-tutorial-get-started-linux.md). Koprocesory jsou také k dispozici, které umožňují zpracování dat v uzlech, které jsou hostitelem databáze.

> [!NOTE]  
> Thrift není podporovaný HBase v HDInsight.

## <a name="use-cases-for-apache-hbase"></a>Případy použití pro Apache HBase

Kanonický případ použití, pro který BigTable (a rozšíření HBase) byl vytvořen z vyhledávání na webu. Vyhledávací stroje sestavují indexy, které mapují termíny na webové stránky, které je obsahují. Ale existuje mnoho dalších případů použití, pro které je HBase vhodné – několik z nich je uvedeno v této části.

|Scénář |Popis |
|---|---|
|Ukládání hodnot klíče|HBase lze použít jako úložiště klíč-hodnota a je vhodný pro správu systémů zpráv. Facebook používá HBase pro svůj systém zasílání zpráv a je ideální pro ukládání a správu internetové komunikace. WebTable využívá HBase k hledání a správě tabulek, které jsou extrahovány z webových stránek.|
|Data snímače|HBase je užitečné pro zaznamenání dat shromážděných přírůstkově z různých zdrojů. To zahrnuje sociální analýzy, časové řady, udržování interaktivních řídicích panelů v aktuálním stavu s trendy a čítači a správa systémů protokolů auditu. Mezi příklady patří obchodní terminál Bloomberg a otevřené časové řady databáze (OpenTSDB), které ukládají a poskytují přístup k metrikám získaným o stavu serverových systémů.|
|Dotaz v reálném čase|[Apache Phoenix](https://phoenix.apache.org/) je SQL dotazovací engine pro Apache HBase. Je přístupný jako ovladač JDBC a umožňuje dotazování a správu tabulek HBase pomocí SQL.|
|HBase jako platforma|Aplikace lze nad HBase spouštět v případě použití jako datového úložiště. Příklady zahrnují Phoenix, [OpenTSDB](http://opentsdb.net/), Kiji a Titan. Aplikace lze také integrovat s HBase. Příklady zahrnují [Apache Hive](https://hive.apache.org/), [Apache Pig](https://pig.apache.org/), [Solr](https://lucene.apache.org/solr/), [Apache Storm](https://storm.apache.org/), Apache [Flume](https://flume.apache.org/), [Apache Impala](https://impala.apache.org/), [Apache Spark](https://spark.apache.org/) , [Ganglia](http://ganglia.info/)a [Apache Drill](https://drill.apache.org/).|

## <a name="next-steps"></a>Další kroky

* [Začínáme používat Apache HBase s Apache Hadoop v HDInsightu](./apache-hbase-tutorial-get-started-linux.md)
* [Vytváření clusterů HDInsight v síti Azure Virtual Network](./apache-hbase-provision-vnet.md)
* [Konfigurace replikace Apache HBase v HDInsightu](apache-hbase-replication.md)
