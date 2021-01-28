---
title: Co jsou Apache HBA ve službě Azure HDInsight?
description: Úvod do Apache HBase v HDInsight, databáze typu NoSQL postavené na Hadoop. Další informace o případech použití a porovnání HBase s dalšími clustery Hadoop.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 98872cc1315d946c63825318d2b98460031e128a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942913"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Co je Apache HBA ve službě Azure HDInsight

[Apache HBA](https://hbase.apache.org/) je open source databáze NoSQL, která je postavená na Apache Hadoop a modelována po [Google BigTable](https://cloud.google.com/bigtable/). HBA poskytují náhodný přístup a silnou konzistenci pro velké objemy dat v databázi bez schématu. Databáze je uspořádána podle rodin sloupců.

V perspektivě uživatele je adaptér HBA podobný databázi. Data jsou uložená v řádcích a sloupcích tabulky a data v rámci řádku se seskupují podle řady sloupců. HBA je databáze bez schématu. Sloupce a datové typy lze před použitím nedefinovat. Kód open-source se škáluje lineárně pro manipulaci s petabajty dat na tisících uzlech. Může spoléhat na redundanci dat, dávkové zpracování a další funkce, které jsou poskytovány distribuovanými aplikacemi v prostředí Hadoop.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Jak se ve službě Azure HDInsight implementuje Apache HBA?

HDInsight HBase je nabízena jako spravovaný cluster, který je integrován do prostředí Azure. Clustery jsou nakonfigurovány k přímému ukládání dat v [Azure Storage](./../hdinsight-hadoop-use-blob-storage.md), což zajišťuje nízkou latenci a zvýšení pružnosti ve volbách výkonu a nákladů. Tato vlastnost umožňuje zákazníkům vytvářet interaktivní weby, které pracují s velkými datovými sadami. Pro vytváření služeb, které ukládají data ze senzorů a telemetrie z milionů koncových bodů. A k analýze těchto dat pomocí úloh Hadoop. HBA a Hadoop jsou dobrým počátečním bodem pro projekty s velkými objemy dat v Azure. Služby umožňují aplikacím v reálném čase pracovat s velkými datovými sadami.

Implementace HDInsight využívá architekturu pro škálování na více instancí HBA k poskytování automatických horizontálního dělení tabulek. A silná konzistence pro čtení a zápisy a automatické převzetí služeb při selhání. Výkon je zvýšen ukládáním do mezipaměti pro čtení a vysokou propustností datových proudů pro zápis. Cluster HBase můžete vytvořit uvnitř virtuální sítě. Podrobnosti najdete v tématu [Vytváření clusterů HDInsight v síti Azure Virtual Network](./apache-hbase-provision-vnet.md).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Jakým způsobem jsou data spravována v HDInsight HBase?

Data mohou být spravována v HBase pomocí příkazů `create`, `get`, `put`, a `scan` z prostředí HBase. Data se zapisují do databáze pomocí `put` a čtou se pomocí `get`. Příkaz `scan` se používá k načítání dat z více řádků v tabulce. Data lze také spravovat pomocí rozhraní API HBase C#, které poskytuje knihovna klienta nad HBase REST API. K databázi HBA je také možné zadat dotaz pomocí [Apache Hive](https://hive.apache.org/). Úvod do těchto programovacích modelů najdete v tématu [Začínáme používat Apache HBA s Apache Hadoop ve službě HDInsight](./apache-hbase-tutorial-get-started-linux.md). Jsou k dispozici i spoluprocesory, které umožňují zpracování dat v uzlech, které jsou hostiteli databáze.

> [!NOTE]  
> Thrift není podporovaný HBase v HDInsight.

## <a name="use-cases-for-apache-hbase"></a>Případy použití pro Apache HBA

Kanonický případ použití, pro který se vytvořilo BigTable (a podle rozšíření, adaptérů HBA) z hledání na webu. Vyhledávací stroje sestavují indexy, které mapují termíny na webové stránky, které je obsahují. Ale existuje mnoho dalších případů použití, pro které je HBase vhodné – několik z nich je uvedeno v této části.

|Scénář |Popis |
|---|---|
|Ukládání hodnot klíče|HBA lze použít jako úložiště hodnot klíčů a je vhodné pro správu systémů zpráv. Facebook používá pro svůj systém zasílání zpráv HBA a je ideální pro ukládání a správu internetové komunikace. WebTable využívá HBase k hledání a správě tabulek, které jsou extrahovány z webových stránek.|
|Data snímače|HBase je užitečné pro zaznamenání dat shromážděných přírůstkově z různých zdrojů. Tato data zahrnují sociální analýzy a časové řady. A udržování interaktivních řídicích panelů v aktuálním stavu díky trendům a čítačům a správě systémů protokolů auditu. Mezi příklady patří terminál Bloomberg účastník a databáze Open Time Series (OpenTSDB). OpenTSDB ukládá a poskytuje přístup ke metrikám shromážděným o stavu serverových systémů.|
|Dotaz v reálném čase|[Apache Phoenix](https://phoenix.apache.org/) je DOTAZOVACÍ modul SQL pro Apache HBA. Je k němu přistupované jako ovladač JDBC a umožňuje dotazování a správu v tabulkách HBA pomocí SQL.|
|HBase jako platforma|Aplikace lze nad HBase spouštět v případě použití jako datového úložiště. Mezi příklady patří Phoenix, OpenTSDB, `Kiji` a Titan. Aplikace lze také integrovat s HBase. Mezi příklady patří: [Apache Hive](https://hive.apache.org/), Apache prasete, [Solr](https://lucene.apache.org/solr/), Apache Storm, Apache Flume, [Apache Impala](https://impala.apache.org/), Apache Spark, `Ganglia` a Apache.|

## <a name="next-steps"></a>Další kroky

* [Začínáme používat Apache HBA s Apache Hadoop ve službě HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Vytváření clusterů HDInsight v síti Azure Virtual Network](./apache-hbase-provision-vnet.md)
* [Konfigurace replikace Apache HBA v HDInsight](apache-hbase-replication.md)
