---
title: Použití externích úložišť metadat – Azure HDInsight
description: Používejte externí úložiště metadat s clustery Azure HDInsight a doporučené postupy.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: edb2d256d3e5d98c52dbdff1162e0e030ebe2be3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272159"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Použití externích úložišť metadat v Azure HDInsightu

HDInsight umožňuje převzít kontrolu nad daty a metadaty nasazením klíčových řešení metadat a databází pro správu do externích úložišť dat. Tato funkce je v současné době k dispozici pro [Apache Hive metastore](#custom-metastore), [Apache Oozie metastore](#apache-oozie-metastore) a [Apache Ambari databáze](#custom-ambari-db).

Metastore Apache Hive v HDInsight je nezbytnou součástí architektury Apache Hadoop. Metastore je centrální úložiště schématu, které lze použít v jiných nástrojích pro přístup k velkým objemům dat, jako je Apache Spark, Interaktivní dotaz (LLAP), Presto nebo Apache Pig. HDInsight používá Azure SQL Database jako metastore Hive.

![Architektura úložiště metadat hive hive](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Metastore pro clustery HDInsight můžete nastavit dvěma způsoby:

* [Výchozí metastore](#default-metastore)
* [Vlastní metastore](#custom-metastore)

## <a name="default-metastore"></a>Výchozí metastore

Ve výchozím nastavení hdinsight vytvoří metastore s každým typem clusteru. Místo toho můžete zadat vlastní metastore. Výchozí metastore obsahuje následující aspekty:

* Žádné další náklady. HDInsight vytvoří metastore s každým typem clusteru bez dalších nákladů.

* Každé výchozí metastore je součástí životního cyklu clusteru. Když odstraníte cluster, odstraní se také odpovídající metastore a metadata.

* Výchozí metastore nelze sdílet s jinými clustery.

* Výchozí metastore používá základní Azure SQL DB, který má limit pěti DTU (jednotka databázové transakce).
Toto výchozí metastore se obvykle používá pro relativně jednoduché úlohy, které nevyžadují více clusterů a nepotřebují metadata zachována mimo životní cyklus clusteru.

## <a name="custom-metastore"></a>Vlastní metastore

HDInsight také podporuje vlastní metastores, které jsou doporučeny pro produkční clustery:

* Jako metastore zadáte vlastní Azure SQL Database.

* Životní cyklus metastore není vázán na životní cyklus clusterů, takže můžete vytvářet a odstraňovat clustery bez ztráty metadat. Metadata, jako jsou schémata Hive, budou přetrvávat i po odstranění a opětovném vytvoření clusteru HDInsight.

* Vlastní metastore umožňuje připojit více clusterů a typů clusterů k tomuto metastore. Například jedno metastore lze sdílet mezi interaktivní dotaz, Hive a Spark clustery v HDInsight.

* Platíte za náklady metastore (Azure SQL DB) podle úrovně výkonu, kterou zvolíte.

* Můžete vertikálně navýšit kapacitu metastore podle potřeby.

* Cluster a externí metastore musí být hostovány ve stejné oblasti.

![Případ použití úložiště metadat služby HDInsight Hive](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Vytvoření a konfigurace Azure SQL Database pro vlastní metastore

Před nastavením vlastního metaúložiště Hive pro cluster HDInsight je potřeba vytvořit nebo mít existující Azure SQL Database.  Další informace najdete [v tématu Úvodní příručka: Vytvoření jedné databáze v Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal).

Chcete-li se ujistit, že váš cluster HDInsight má přístup k připojené azure sql database, nakonfigurujte pravidla brány firewall Azure SQL Database, abyste umožnili službám a prostředkům Azure přístup k serveru.

Tuto možnost můžete povolit na webu Azure Portal tak, že kliknete na **Nastavit server ovou bránu firewall**a kliknete na **zapnuto** pod **povolit služby a prostředky Azure pro přístup k tomuto serveru** pro server nebo databázi Azure SQL Database. Další informace naleznete v [tématu Vytvoření a správa pravidel brány firewall IP](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

![tlačítko nastavit serverovou bránu firewall](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![povolit přístup ke službám Azure](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Výběr vlastního metaúložiště během vytváření clusteru

Během vytváření clusteru můžete nasměrovat cluster na dříve vytvořenou databázi Azure SQL, nebo můžete databázi SQL nakonfigurovat po vytvoření clusteru. Tato možnost je určena **nastavením > úložiště metastore** při vytváření nového clusteru Hadoop, Spark nebo interaktivního Hive z portálu Azure.

![Portál Azure pro úložiště metadat služby HDInsight Hive](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-best-practices"></a>Osvědčené postupy metastore hive

Zde jsou některé obecné HDInsight Hive metastore osvědčené postupy:

* Použijte vlastní metastore, kdykoli je to možné, abyste pomohli oddělit výpočetní prostředky (spuštěný cluster) a metadata (uložená v metastore).

* Začněte s úrovní S2, která poskytuje 50 DTU a 250 GB úložiště. Pokud se zobrazí kritické místo, můžete databázi škálovat.

* Pokud máte v úmyslu více clusterů HDInsight pro přístup k samostatným datům, použijte samostatnou databázi pro metastore v každém clusteru. Pokud sdílíte metastore ve více clusterech HDInsight, znamená to, že clustery používají stejná metadata a základní soubory uživatelských dat.

* Pravidelně zálohujte vlastní metastore. Azure SQL Database generuje zálohy automaticky, ale časový rámec uchovávání záloh se liší. Další informace naleznete v tématu [Další informace o automatickém zálohování databáze SQL](../sql-database/sql-database-automated-backups.md).

* Vyhledejte cluster metastore a HDInsight ve stejné oblasti, kde najdete nejvyšší výkon a nejnižší poplatky za odchozí přenos dat v síti.

* Sledujte svůj metastore pro výkon a dostupnost pomocí nástrojů Azure SQL Database Monitoring, jako je portál Azure nebo protokoly Azure Monitor.

* Když je vytvořena nová vyšší verze Azure HDInsight proti existující vlastní databázi metastore, systém upgraduje schéma metastore, což je nevratné bez obnovení databáze ze zálohy.

* Pokud sdílíte metastore ve více clusterech, ujistěte se, že všechny clustery jsou stejné verze HDInsight. Různé verze Hive používají různá schémata databáze metastore. Metastore například nelze sdílet mezi clustery s verzí Hive 2.1 a Hive 3.1.

* V HDInsight 4.0 spark a hive používají nezávislé katalogy pro přístup ke stolům SparkSQL nebo Hive. Tabulka vytvořená Sparkem je umístěna v katalogu Spark. Tabulka vytvořená Hive se nachází v katalogu Hive. To je jiné než HDInsight 3.6, kde Hive a Spark sdílené společného katalogu. Integrace Hive a Spark v HDInsight 4.0 závisí na konektoru Hive Warehouse Connector (HWC). HWC funguje jako most mezi Spark a Hive. [Další informace o konektoru skladu Hive](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Apache Oozie metastore

Apache Oozie je systém koordinace pracovních postupů, který spravuje úlohy systému Hadoop.  Oozie podporuje úlohy Hadoop pro Apache MapReduce, Pig, Hive a další.  Oozie používá metastore k ukládání podrobností o aktuálních a dokončených pracovních postupech. Chcete-li zvýšit výkon při použití Oozie, můžete použít Azure SQL Database jako vlastní metastore. Metastore může také poskytnout přístup k datům úlohovzie Oozie po odstranění clusteru.

Pokyny k vytvoření metaúložiště Oozie pomocí Azure SQL Database najdete v článku [Použití Apache Oozie pro pracovní postupy](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Vlastní Ambari DB

Informace o použití vlastní externí databáze s Apache Ambari na HDInsight, viz [Vlastní Apache Ambari databáze](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Další kroky

* [Nastavení clusterů ve službě HDInsight se softwarem Apache Hadoop, Apache Spark, Apache Kafka a dalšími](./hdinsight-hadoop-provision-linux-clusters.md)
