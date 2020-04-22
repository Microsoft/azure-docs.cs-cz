---
title: Co je Apache HBase v Azure HDInsight?
description: Úvod do Apache HBase v HDInsight, databáze typu NoSQL postavené na Hadoop. Další informace o případech použití a porovnání HBase s dalšími clustery Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/20/2020
ms.openlocfilehash: e977d4d68a330f57c4b53da4270e4515d4e69ee0
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729813"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Co je Apache HBase v Azure HDInsight

[Apache HBase](https://hbase.apache.org/) je open-source, NoSQL databáze, která je postavena na Apache Hadoop a modelována podle [Google BigTable](https://cloud.google.com/bigtable/). HBase poskytuje náhodný přístup a silnou konzistenci pro velké množství dat v databázi bez schématu. Databáze je uspořádána podle rodin sloupců.

Z pohledu uživatele hbase je podobný databázi. Data jsou uložena v řádcích a sloupcích tabulky a data v rámci řádku jsou seskupena podle rodiny sloupců. HBase je databáze bez schématu. Sloupce a datové typy mohou být před použitím nedefinované. Kód open-source se škáluje lineárně pro manipulaci s petabajty dat na tisících uzlech. Může se spolehnout na redundanci dat, dávkové zpracování a další funkce, které poskytují distribuované aplikace v prostředí Hadoop.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Jak se Apache HBase implementuje v Azure HDInsight?

HDInsight HBase je nabízena jako spravovaný cluster, který je integrován do prostředí Azure. Clustery jsou nakonfigurované pro ukládání dat přímo ve [službě Azure Storage](./../hdinsight-hadoop-use-blob-storage.md), která poskytuje nízkou latenci a zvýšenou elasticitu ve výběru výkonu a nákladů. Tato vlastnost umožňuje zákazníkům vytvářet interaktivní weby, které pracují s velkými datovými sadami. Chcete-li vytvořit služby, které ukládají data senzorů a telemetrií z milionů koncových bodů. A analyzovat tato data pomocí pracovních míst hadoopu. HBase a Hadoop jsou dobrým výchozím bodem pro projekt velkých objemů dat v Azure. Služby mohou umožnit aplikacím v reálném čase pracovat s velkými datovými sadami.

Implementace HDInsight používá architekturu horizontálního navýšení kapacity HBase k zajištění automatického horizontálního horizontálového rozložení tabulek. A silná konzistence pro čtení a zápisy a automatické převzetí služeb při selhání. Výkon je zvýšen ukládáním do mezipaměti pro čtení a vysokou propustností datových proudů pro zápis. Cluster HBase můžete vytvořit uvnitř virtuální sítě. Podrobnosti najdete v tématu [Vytváření clusterů HDInsight v síti Azure Virtual Network](./apache-hbase-provision-vnet.md).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Jakým způsobem jsou data spravována v HDInsight HBase?

Data mohou být spravována v HBase pomocí příkazů `create`, `get`, `put`, a `scan` z prostředí HBase. Data se zapisují do databáze pomocí `put` a čtou se pomocí `get`. Příkaz `scan` se používá k načítání dat z více řádků v tabulce. Data lze také spravovat pomocí rozhraní API HBase C#, které poskytuje knihovna klienta nad HBase REST API. Databáze HBase může být také dotazovánpomocí [Apache Hive](https://hive.apache.org/). Úvod k těmto programovacím modelům najdete [v tématu Začínáme používat Apache HBase s Apache Hadoop v HDInsight](./apache-hbase-tutorial-get-started-linux.md). Koprocesory jsou také k dispozici, které umožňují zpracování dat v uzlech, které jsou hostitelem databáze.

> [!NOTE]  
> Thrift není podporovaný HBase v HDInsight.

## <a name="use-cases-for-apache-hbase"></a>Případy použití pro Apache HBase

Kanonický případ použití, pro který BigTable (a rozšíření HBase) byl vytvořen z vyhledávání na webu. Vyhledávací stroje sestavují indexy, které mapují termíny na webové stránky, které je obsahují. Ale existuje mnoho dalších případů použití, pro které je HBase vhodné – několik z nich je uvedeno v této části.

|Scénář |Popis |
|---|---|
|Ukládání hodnot klíče|HBase lze použít jako úložiště klíč-hodnota a je vhodný pro správu systémů zpráv. Facebook používá HBase pro svůj systém zasílání zpráv a je ideální pro ukládání a správu internetové komunikace. WebTable využívá HBase k hledání a správě tabulek, které jsou extrahovány z webových stránek.|
|Data snímače|HBase je užitečné pro zaznamenání dat shromážděných přírůstkově z různých zdrojů. Tato data zahrnují sociální analýzu a časové řady. A udržování interaktivních řídicích panelů v aktuálním stavu s trendy a čítači a správa systémů protokolů auditu. Mezi příklady patří bloombergský obchodní terminál a databáze Open Time Series (OpenTSDB). OpenTSDB ukládá a poskytuje přístup k metrikám shromážděným o stavu serverových systémů.|
|Dotaz v reálném čase|[Apache Phoenix](https://phoenix.apache.org/) je SQL dotazovací engine pro Apache HBase. Je přístupný jako ovladač JDBC a umožňuje dotazování a správu tabulek HBase pomocí SQL.|
|HBase jako platforma|Aplikace lze nad HBase spouštět v případě použití jako datového úložiště. Příklady zahrnují Phoenix, OpenTSDB `Kiji`a Titan. Aplikace lze také integrovat s HBase. Příklady: [Apache Hive](https://hive.apache.org/), Apache Pig, [Solr](https://lucene.apache.org/solr/), Apache Storm, Apache Flume, [Apache Impala](https://impala.apache.org/), Apache Spark `Ganglia`a Apache Drill.|

## <a name="next-steps"></a>Další kroky

* [Začínáme používat Apache HBase s Apache Hadoop v HDInsightu](./apache-hbase-tutorial-get-started-linux.md)
* [Vytváření clusterů HDInsight v síti Azure Virtual Network](./apache-hbase-provision-vnet.md)
* [Konfigurace replikace Apache HBase v HDInsightu](apache-hbase-replication.md)
