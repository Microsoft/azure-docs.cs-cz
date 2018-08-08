---
title: Co je to HBase v Azure HDInsight?
description: Úvod do Apache HBase v HDInsight, databáze typu NoSQL postavené na Hadoop. Další informace o případech použití a porovnání HBase s dalšími clustery Hadoop.
keywords: bigtable, nosql, what is hbase, apache hbase, hbase, habase overview
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: jasonh
ms.openlocfilehash: efbb591c92f40f1c620a6dda2cb6f19232bb0acc
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597537"
---
# <a name="what-is-hbase-in-hdinsight-a-nosql-database-that-provides-bigtable-like-capabilities-for-hadoop"></a>Co je HBase v HDInsight: Databáze NoSQL, která poskytuje pro Hadoop funkce podobné BigTable
Apache HBase je NoSQL databáze typu open source, která je založena na Hadoop a modelována podle Google BigTable. HBase poskytuje náhodný přístup a silnou konzistenci pro velké objemy nestrukturovaných a částečně strukturovaných dat v databázi schemaless uspořádané podle rodin sloupců.

Data se ukládají na řádky tabulky a data v řádku jsou seskupena podle rodin sloupců. HBase je schemaless databáze ve smyslu, že před jejich použitím není třeba definovat sloupce ani v nich uložený typ dat. Kód open-source se škáluje lineárně pro manipulaci s petabajty dat na tisících uzlech. Může se spoléhat na redundanci dat, zpracování dávkou a další funkce, které jsou poskytovány pomocí distribuovaných aplikací v ekosystému Hadoop.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

## <a name="how-is-hbase-implemented-in-azure-hdinsight"></a>Jak je implementována HBase v Azure HDInsight?
HDInsight HBase je nabízena jako spravovaný cluster, který je integrován do prostředí Azure. Clustery jsou nakonfigurovány k přímému ukládání dat do služby [Azure Storage](./../hdinsight-hadoop-use-blob-storage.md) nebo [Azure Data Lake Store](./../hdinsight-hadoop-use-data-lake-store.md), což zajišťuje nízkou latenci a zvýšení pružnosti ve volbách výkonu a nákladů. To umožňuje zákazníkům vytvářet interaktivní weby, které pracují s rozsáhlými datovými sadami a vytvářet služby, které ukládají údaje ze snímačů a telemetrie z milionů koncových bodů a analyzovat tato data pomocí úloh Hadoop. HBase a Hadoop jsou dobré počáteční body pro projekt velkých objemů dat v Azure; zejména umožňují spolupráci aplikací v reálném čase s rozsáhlými datovými sadami.

Implementace HDInsight využívá architekturu škálování HBase k zajištění automatického dělení tabulek, silnou konzistenci pro čtení a zápis a automatické převzetí služeb při selhání. Výkon je zvýšen ukládáním do mezipaměti pro čtení a vysokou propustností datových proudů pro zápis. Cluster HBase můžete vytvořit uvnitř virtuální sítě. Podrobnosti najdete v tématu [Vytváření clusterů HDInsight v síti Azure Virtual Network](./apache-hbase-provision-vnet.md).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Jakým způsobem jsou data spravována v HDInsight HBase?
Data mohou být spravována v HBase pomocí příkazů `create`, `get`, `put`, a `scan` z prostředí HBase. Data se zapisují do databáze pomocí `put` a čtou se pomocí `get`. Příkaz `scan` se používá k načítání dat z více řádků v tabulce. Data lze také spravovat pomocí rozhraní API HBase C#, které poskytuje knihovna klienta nad HBase REST API. Databáze aplikace HBase může být dotazována také pomocí Hive. Úvod do těchto programovacích modelů najdete v tématu [Začínáme používat HBase s Hadoopem ve službě HDInsight](./apache-hbase-tutorial-get-started-linux.md). K dispozici jsou také koprocesory, které umožňují zpracování dat v uzlech, které hostují databáze.

> [!NOTE]
> Thrift není podporovaný HBase v HDInsight.
>

## <a name="scenarios-use-cases-for-hbase"></a>Scénáře: Případy využití HBase
Byl vytvořen případ použití Canonical, pro které je vytvořené BigTable (a pomocí rozšíření také HBase) vyhledávání na webu. Vyhledávací stroje sestavují indexy, které mapují termíny na webové stránky, které je obsahují. Ale existuje mnoho dalších případů použití, pro které je HBase vhodné – několik z nich je uvedeno v této části.

* Ukládání hodnot klíče
  
    HBase lze použít jako úložiště hodnota klíčů a je vhodný pro správu systémů zasílání zpráv. Facebook používá HBase pro svůj systém zasílání zpráv a je ideální pro ukládání a správu internetové komunikace. WebTable využívá HBase k hledání a správě tabulek, které jsou extrahovány z webových stránek.
* Data snímače
  
    HBase je užitečné pro zaznamenání dat shromážděných přírůstkově z různých zdrojů. To zahrnuje sociální analýzy, časové řady, aktualizaci interaktivních řídicích panelů s trendy a čítači a řízení systémů protokolu auditu. Mezi příklady patří obchodní terminál Bloomberg a otevřené časové řady databáze (OpenTSDB), které ukládají a poskytují přístup k metrikám získaným o stavu serverových systémů.
* Dotaz v reálném čase
  
    [Phoenix](http://phoenix.apache.org/) je modul dotazů SQL pro Apache HBase. Je přístupný jako ovladač JDBC a umožňuje dotazování a správu tabulek HBase pomocí SQL.
* HBase jako platforma
  
    Aplikace lze nad HBase spouštět v případě použití jako datového úložiště. Příklady zahrnují Phoenix, OpenTSDB, Kiji a Titan. Aplikace lze také integrovat s HBase. Příklady zahrnují Hive, Pig, Solr, Storm, Flume, Impala, Spark, Ganglia a Drill.

## <a name="next-steps"></a>Další kroky
* [Začínáme používat HBase s Hadoopem ve službě HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Vytváření clusterů HDInsight v síti Azure Virtual Network](./apache-hbase-provision-vnet.md)
* [Konfigurace replikace HBase v HDInsight](apache-hbase-replication.md)
* [Použití Mavenu k sestavování aplikací v Javě, které používají HBase s HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md)

## <a name="see-also"></a>Viz také
* [Apache HBase](https://hbase.apache.org/)
* [Bigtable: Systém distribuovaného úložiště pro strukturovaná data](http://research.google.com/archive/bigtable.html)




