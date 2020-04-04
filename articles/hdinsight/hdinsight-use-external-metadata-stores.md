---
title: Použití externích úložišť metadat – Azure HDInsight
description: Používejte externí úložiště metadat s clustery Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/03/2020
ms.openlocfilehash: 0cadf3930008868fe223e6e1024a2d14d17d8131
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657119"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Použití externích úložišť metadat v Azure HDInsightu

HDInsight umožňuje převzít kontrolu nad daty a metadaty pomocí externích úložišť dat. Tato funkce je k dispozici pro [Apache Hive metastore](#custom-metastore), [Apache Oozie metastore](#apache-oozie-metastore)a [Apache Ambari databáze](#custom-ambari-db).

Metastore Apache Hive v HDInsight je nezbytnou součástí architektury Apache Hadoop. Metastore je úložiště centrálního schématu. Metastore používá jiné nástroje pro přístup k velkým objemům dat, jako je Apache Spark, Interactive Query (LLAP), Presto nebo Apache Pig. HDInsight používá Azure SQL Database jako metastore Hive.

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
Toto výchozí metastore se obvykle používá pro relativně jednoduché úlohy. Úlohy, které nevyžadují více clusterů a nepotřebují metadata zachována mimo životní cyklus clusteru.

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

Před nastavením vlastního metaúložiště Hive pro cluster HDInsight vytvořte nebo máte existující Azure SQL Database.  Další informace najdete [v tématu Úvodní příručka: Vytvoření jedné databáze v Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal).

Nakonfigurujte pravidla brány firewall Azure SQL Database, abyste umožnili službám a prostředkům Azure přístup k serveru. Tuto možnost povolte na webu Azure Portal výběrem **možnosti Nastavit serverovou bránu firewall**. Pak vyberte **ZAPNUTO** pod **Povolit služby Azure a prostředky pro přístup k tomuto serveru** pro server Azure SQL Database nebo databáze. Další informace naleznete v [tématu Vytvoření a správa pravidel brány firewall IP](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

![tlačítko nastavit serverovou bránu firewall](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![povolit přístup ke službám Azure](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Výběr vlastního metaúložiště během vytváření clusteru

Cluster můžete kdykoli nasměrovat na dříve vytvořenou databázi Azure SQL. Pro vytváření clusteru prostřednictvím portálu je tato možnost zadána v **nastavení > úložiště**.

![Portál Azure pro úložiště metadat služby HDInsight Hive](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-guidelines"></a>Pokyny pro metastore hive

* Použijte vlastní metastore, kdykoli je to možné, abyste pomohli oddělit výpočetní prostředky (spuštěný cluster) a metadata (uložená v metastore).

* Začněte s úrovní S2, která poskytuje 50 DTU a 250 GB úložiště. Pokud se zobrazí kritické místo, můžete databázi škálovat.

* Pokud máte v úmyslu více clusterů HDInsight pro přístup k samostatným datům, použijte samostatnou databázi pro metastore v každém clusteru. Pokud sdílíte metastore ve více clusterech HDInsight, znamená to, že clustery používají stejná metadata a základní soubory uživatelských dat.

* Pravidelně zálohujte vlastní metastore. Azure SQL Database generuje zálohy automaticky, ale časový rámec uchovávání záloh se liší. Další informace naleznete v tématu [Další informace o automatickém zálohování databáze SQL](../sql-database/sql-database-automated-backups.md).

* Vyhledejte cluster metastore a HDInsight ve stejné oblasti. Tato konfigurace bude poskytovat nejvyšší výkon a nejnižší poplatky za odchozí síť.

* Sledujte svůj metastore pro výkon a dostupnost pomocí nástrojů Azure SQL Database Monitoring nebo protokoly Azure Monitor.

* Když se vytvoří nová vyšší verze Azure HDInsight u existující databáze vlastního metaúložiště, systém upgraduje schéma metastore. Upgrade je nevratný bez obnovení databáze ze zálohy.

* Pokud sdílíte metastore ve více clusterech, ujistěte se, že všechny clustery jsou stejné verze HDInsight. Různé verze Hive používají různá schémata databáze metastore. Metastore například nelze sdílet mezi clustery s verzí Hive 2.1 a Hive 3.1.

* V HDInsight 4.0 spark a hive používají nezávislé katalogy pro přístup ke stolům SparkSQL nebo Hive. Tabulka vytvořená Sparkem žije v katalogu Spark. Tabulka vytvořená Hive žije v katalogu Hive. Toto chování se liší od HDInsight 3.6, kde Hive a Spark sdílené společného katalogu. Integrace Hive a Spark v HDInsight 4.0 závisí na konektoru Hive Warehouse Connector (HWC). HWC funguje jako most mezi Spark a Hive. [Další informace o konektoru skladu Hive](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Apache Oozie metastore

Apache Oozie je systém koordinace pracovních postupů, který spravuje úlohy systému Hadoop. Oozie podporuje úlohy Hadoop pro Apache MapReduce, Pig, Hive a další.  Oozie používá metastore k ukládání podrobností o pracovních postupech. Chcete-li zvýšit výkon při použití Oozie, můžete použít Azure SQL Database jako vlastní metastore. Metastore poskytuje přístup k datům úlohOozie po odstranění clusteru.

Pokyny k vytvoření metaúložiště Oozie pomocí Azure SQL Database najdete v článku [Použití Apache Oozie pro pracovní postupy](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Vlastní Ambari DB

Informace o použití vlastní externí databáze s Apache Ambari na HDInsight, viz [Vlastní Apache Ambari databáze](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Další kroky

* [Nastavení clusterů ve službě HDInsight se softwarem Apache Hadoop, Apache Spark, Apache Kafka a dalšími](./hdinsight-hadoop-provision-linux-clusters.md)
