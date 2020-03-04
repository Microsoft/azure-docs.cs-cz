---
title: Použití externích úložišť metadat – Azure HDInsight
description: Používejte externí úložiště metadat s clustery Azure HDInsight a osvědčenými postupy.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: edb2d256d3e5d98c52dbdff1162e0e030ebe2be3
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251056"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Použití externích úložišť metadat ve službě Azure HDInsight

HDInsight umožňuje převzít kontrolu nad daty a metadaty nasazením klíčových řešení metadat a databází pro správu do externích úložišť dat. Tato funkce je aktuálně dostupná pro [Apache Hive metastore](#custom-metastore), [Apache Oozie metastore](#apache-oozie-metastore) a [Apache Ambari Database](#custom-ambari-db).

Apache Hive metastore v HDInsight je důležitou součástí architektury Apache Hadoop. Metastore je centrální úložiště schémat, které můžou používat jiné nástroje pro přístup k velkým datům, jako jsou Apache Spark, interaktivní dotazy (LLAP), presto nebo Apache prasete. HDInsight používá jako metastore Hive Azure SQL Database.

![Architektura úložiště metadat podregistru HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Existují dva způsoby, jak můžete nastavit metastore pro clustery HDInsight:

* [Výchozí metastore](#default-metastore)
* [Vlastní metastore](#custom-metastore)

## <a name="default-metastore"></a>Výchozí metastore

Ve výchozím nastavení HDInsight vytvoří metastore s každým typem clusteru. Místo toho můžete zadat vlastní metastore. Výchozí metastore zahrnuje následující požadavky:

* Žádné další náklady. HDInsight vytvoří metastore s každým typem clusteru bez dalších nákladů.

* Každé výchozí metastore je součástí životního cyklu clusteru. Při odstranění clusteru se odstraní také odpovídající metastore a metadata.

* Výchozí metastore nejde sdílet s ostatními clustery.

* Výchozí metastore používá základní Azure SQL DB, který má pět jednotek DTU (databázová jednotka).
Tento výchozí metastore se obvykle používá pro poměrně jednoduché úlohy, které nevyžadují více clusterů, a nepotřebují uchovávat metadata po dobu životního cyklu clusteru.

## <a name="custom-metastore"></a>Vlastní metastore

HDInsight podporuje také vlastní metaúložiště, které se doporučují pro produkční clustery:

* Jako metastore zadáte vlastní Azure SQL Database.

* Životní cyklus metastore není svázán s životním cyklem clusterů, takže můžete vytvářet a odstraňovat clustery bez ztráty metadat. Metadata, jako jsou schémata podregistru, zůstanou zachována i po odstranění a opětovném vytvoření clusteru HDInsight.

* Vlastní metastore umožňuje připojit k danému metastore několik clusterů a typů clusterů. Například jeden metastore může být sdílen napříč interaktivními clustery, podregistru a Sparky v HDInsight.

* Platíte za náklady na metastore (Azure SQL DB) podle úrovně výkonu, kterou si zvolíte.

* Metastore můžete škálovat podle potřeby.

* Cluster a externí metastore musí být hostované ve stejné oblasti.

![Případ použití úložiště metadat podregistru HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Vytvoření a konfigurace Azure SQL Database pro vlastní metastore

Než nastavíte vlastní metastore Hive pro cluster HDInsight, musíte vytvořit nebo mít existující Azure SQL Database.  Další informace najdete v tématu [rychlý Start: vytvoření izolované databáze ve službě Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal).

Aby se zajistilo, že cluster HDInsight bude mít přístup k připojeným Azure SQL Database, nakonfigurujte Azure SQL Database pravidla brány firewall tak, aby umožňovala službám a prostředkům Azure přístup k serveru.

Tuto možnost můžete povolit v Azure Portal kliknutím na **nastavit bránu firewall serveru**a kliknutím **na** pod položkou **Povolit službám a prostředkům Azure přístup k tomuto serveru** pro Azure SQL Database Server nebo databázi. Další informace najdete v tématu [Vytvoření a Správa pravidel brány firewall protokolu IP](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) .

![tlačítko nastavit bránu firewall serveru](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![povolení přístupu ke službám Azure](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Vybrat vlastní metastore při vytváření clusteru

Cluster můžete nasměrovat na dříve vytvořený Azure SQL Database během vytváření clusteru, nebo můžete nakonfigurovat SQL Database po vytvoření clusteru. Tato možnost se při vytváření nového clusteru Hadoop, Sparku nebo interaktivního podregistru v Azure Portal používá v **Nastavení úložiště > metastore** .

![Azure Portal úložiště metadat podregistru HDInsight](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-best-practices"></a>metastore Hive osvědčené postupy

Tady jsou některé obecné metastore Hive osvědčené postupy pro HDInsight:

* Pokud je to možné, využijte vlastní metastore, abyste mohli oddělit výpočetní prostředky (váš běžící cluster) a metadata (uložené v metastore).

* Začněte s vrstvou S2, která poskytuje 50 DTU a 250 GB úložiště. Pokud se zobrazí kritický bod, můžete databázi škálovat nahoru.

* Pokud máte v úmyslu více clusterů HDInsight pro přístup k samostatným datům, použijte pro metastore na jednotlivých clusterech samostatnou databázi. Pokud sdílíte metastore napříč několika clustery HDInsight, znamená to, že clustery používají stejná metadata a základní soubory uživatelských dat.

* Pravidelně zálohujte vlastní metastore. Azure SQL Database automaticky generuje zálohy, ale časový interval pro uchovávání záloh se liší. Další informace najdete v tématu [informace o automatickém zálohování SQL Database](../sql-database/sql-database-automated-backups.md).

* Najděte cluster metastore a HDInsight ve stejné oblasti pro nejvyšší výkon a nejnižší poplatky za odchozí přenos v síti.

* Monitorujte metastore o výkonu a dostupnosti pomocí nástrojů pro monitorování Azure SQL Database, jako jsou protokoly Azure Portal nebo Azure Monitor.

* Když je v existující vlastní databázi metastore vytvořená nová, vyšší verze Azure HDInsight, systém upgraduje schéma metastore, což je nevratné bez obnovení databáze ze zálohy.

* Pokud sdílíte metastore napříč několika clustery, ujistěte se, že všechny clustery mají stejnou verzi HDInsight. Různé verze podregistru používají různá schémata metastore Database. Nemůžete například sdílet metastore mezi podregistrem 2,1 a clustery s verzemi v registru 3,1.

* Spark a podregistr v HDInsight 4,0 používají nezávislé katalogy pro přístup k SparkSQL nebo tabulkám podregistru. Tabulka vytvořená nástrojem Spark se nachází v katalogu Spark. Tabulka vytvořená podregistrem se nachází v katalogu podregistru. To se liší od HDInsight 3,6, ve kterém se společný katalog pro podregistr a Spark sdílí. Integrace podregistru a Sparku v HDInsight 4,0 spoléhá na umožní (podregistr Warehouse Connector). UMOŽNÍ funguje jako most mezi Sparkem a podregistrem. [Seznamte se s konektorem skladiště pro podregistr](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Apache Oozie metastore

Apache Oozie je systém koordinace pracovních postupů, který spravuje úlohy Hadoop.  Oozie podporuje úlohy Hadoop pro Apache MapReduce, prase, podregistr a další.  Oozie používá metastore k ukládání podrobností o aktuálních a dokončených pracovních postupech. Pokud chcete zvýšit výkon při použití Oozie, můžete použít Azure SQL Database jako vlastní metastore. Metastore může také po odstranění clusteru poskytnout přístup k datům úlohy Oozie.

Pokyny k vytvoření Oozie metastore pomocí Azure SQL Database najdete v tématu [použití Apache Oozie pro pracovní postupy](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Vlastní Ambari DB

Pokud chcete používat svou vlastní externí databázi s Apache Ambari ve službě HDInsight, podívejte se na [vlastní databázi Apache Ambari](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Další kroky

* [Nastavení clusterů ve službě HDInsight se softwarem Apache Hadoop, Apache Spark, Apache Kafka a dalšími](./hdinsight-hadoop-provision-linux-clusters.md)
