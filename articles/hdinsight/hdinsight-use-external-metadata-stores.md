---
title: Použití externích úložišť metadat – Azure HDInsight
description: Používejte externí úložiště metadat s clustery Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 08/06/2020
ms.openlocfilehash: 1c02f9de5b41d58e40001ba103191f3ef015f5c5
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534902"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Použití externích úložišť metadat v Azure HDInsightu

HDInsight umožňuje převzít kontrolu nad daty a metadaty pomocí externích úložišť dat. Tato funkce je k dispozici pro [Apache Hive metastore](#custom-metastore), [Apache Oozie metastore](#apache-oozie-metastore)a [Apache Ambari Database](#custom-ambari-db).

Apache Hive metastore v HDInsight je důležitou součástí architektury Apache Hadoop. Metastore je centrální úložiště schémat. Metastore používají jiné nástroje pro přístup k velkým datům, jako jsou Apache Spark, interaktivní dotazy (LLAP), presto nebo Apache prasete. HDInsight používá jako metastore Hive Azure SQL Database.

![Architektura úložiště metadat podregistru HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Existují dva způsoby, jak můžete nastavit metastore pro clustery HDInsight:

* [Výchozí metastore](#default-metastore)
* [Vlastní metastore](#custom-metastore)

## <a name="default-metastore"></a>Výchozí metastore

Ve výchozím nastavení HDInsight vytvoří metastore s každým typem clusteru. Místo toho můžete zadat vlastní metastore. Výchozí metastore zahrnuje následující požadavky:

* Žádné další náklady. HDInsight vytvoří metastore s každým typem clusteru bez dalších nákladů.

* Každé výchozí metastore je součástí životního cyklu clusteru. Při odstranění clusteru se odstraní také odpovídající metastore a metadata.

* Výchozí metastore nejde sdílet s ostatními clustery.

* Výchozí metastore se doporučuje jenom pro jednoduché úlohy. Úlohy, které nevyžadují více clusterů a nepotřebují metadata zachovaná nad rámec životního cyklu clusteru.

> [!IMPORTANT]
> Výchozí metastore poskytuje Azure SQL Database s **limitem DTU úrovně Basic 5 (nelze aktualizovat)** ! Vhodné pro účely základního testování. U rozsáhlých nebo produkčních úloh doporučujeme migrovat na externí metastore.

## <a name="custom-metastore"></a>Vlastní metastore

HDInsight podporuje také vlastní metaúložiště, které se doporučují pro produkční clustery:

* Jako metastore zadáte vlastní Azure SQL Database.

* Životní cyklus metastore není svázán s životním cyklem clusterů, takže můžete vytvářet a odstraňovat clustery bez ztráty metadat. Metadata, jako jsou schémata podregistru, zůstanou zachována i po odstranění a opětovném vytvoření clusteru HDInsight.

* Vlastní metastore umožňuje připojit k danému metastore několik clusterů a typů clusterů. Například jeden metastore může být sdílen napříč interaktivními clustery, podregistru a Sparky v HDInsight.

* Za náklady na metastore (Azure SQL Database) platíte podle zvolené úrovně výkonu.

* Metastore můžete škálovat podle potřeby.

* Cluster a externí metastore musí být hostované ve stejné oblasti.

![Případ použití úložiště metadat podregistru HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Vytvoření a konfigurace Azure SQL Database pro vlastní metastore

Vytvořte nebo existující Azure SQL Database před nastavením vlastního metastore Hive pro cluster HDInsight.  Další informace najdete v tématu [rychlý Start: vytvoření izolované databáze v Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal).

Při vytváření clusteru se musí služba HDInsight připojit k externímu metastore a ověřit vaše přihlašovací údaje. Nakonfigurujte Azure SQL Database pravidla brány firewall tak, aby umožňovala službám a prostředkům Azure přístup k serveru. Tuto možnost povolte v Azure Portal výběrem možnosti **nastavit bránu firewall serveru** . Pak vyberte **žádné** **pod položkou** **Odepřít přístup k veřejné síti** a v části **Povolit přístup k tomuto serveru pro Azure SQL Database pomocí služeb a prostředků Azure** . Další informace najdete v tématu [Vytvoření a Správa pravidel brány firewall protokolu IP](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) .

Privátní koncové body pro úložiště SQL nejsou podporovány.

![tlačítko nastavit bránu firewall serveru](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![povolení přístupu ke službám Azure](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Vybrat vlastní metastore při vytváření clusteru

Cluster můžete kdykoli nasměrovat na dříve vytvořenou Azure SQL Database. Při vytváření clusteru prostřednictvím portálu je tato možnost zadaná v **Nastavení úložiště > metastore** .

![Azure Portal úložiště metadat podregistru HDInsight](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-guidelines"></a>Pokyny pro metastore Hive

> [!NOTE]
> Pokud je to možné, využijte vlastní metastore, abyste mohli oddělit výpočetní prostředky (váš běžící cluster) a metadata (uložené v metastore). Začněte s vrstvou S2, která poskytuje 50 DTU a 250 GB úložiště. Pokud se zobrazí kritický bod, můžete databázi škálovat nahoru.

* Pokud máte v úmyslu více clusterů HDInsight pro přístup k samostatným datům, použijte pro metastore na jednotlivých clusterech samostatnou databázi. Pokud sdílíte metastore napříč několika clustery HDInsight, znamená to, že clustery používají stejná metadata a základní soubory uživatelských dat.

* Pravidelně zálohujte vlastní metastore. Azure SQL Database automaticky generuje zálohy, ale časový interval pro uchovávání záloh se liší. Další informace najdete v tématu [informace o automatickém zálohování SQL Database](../azure-sql/database/automated-backups-overview.md).

* Najděte cluster metastore a HDInsight ve stejné oblasti. Tato konfigurace bude poskytovat nejvyšší výkon a nejnižší poplatky za síťové přenosy.

* Monitorujte metastore o výkonu a dostupnosti pomocí nástrojů pro monitorování Azure SQL Database nebo protokoly Azure Monitor.

* Když je v existující vlastní databázi metastore vytvořená nová, vyšší verze Azure HDInsight, systém upgraduje schéma metastore. Upgrade je nevratný bez obnovení databáze ze zálohy.

* Pokud sdílíte metastore napříč několika clustery, ujistěte se, že všechny clustery mají stejnou verzi HDInsight. Různé verze podregistru používají různá schémata metastore Database. Nemůžete například sdílet metastore mezi podregistrem 2,1 a clustery s verzemi v registru 3,1.

* Spark a podregistr v HDInsight 4,0 používají nezávislé katalogy pro přístup k SparkSQL nebo tabulkám podregistru. Tabulka vytvořená Sparkem v katalogu Spark. Tabulka vytvořená podregistrem v katalogu podregistru Toto chování se liší od HDInsight 3,6, ve kterém se společný katalog pro podregistr a Spark sdílí. Integrace podregistru a Sparku v HDInsight 4,0 spoléhá na umožní (podregistr Warehouse Connector). UMOŽNÍ funguje jako most mezi Sparkem a podregistrem. [Seznamte se s konektorem skladiště pro podregistr](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

* Pokud chcete metastore sdílet mezi podregistrem a Sparkem, můžete v HDInsight 4,0 změnit vlastnost metastore. Catalog. default na podregistr v clusteru Spark. Tuto vlastnost najdete v Ambari Advanced spark2-podregistr-site-override. Je důležité si uvědomit, že sdílení metastore funguje jenom pro externí tabulky podregistru, takže pokud máte interní/spravované tabulky podregistru nebo tabulky s kyselým obsahem, nebude to fungovat.  

## <a name="apache-oozie-metastore"></a>Apache Oozie metastore

Apache Oozie je systém koordinace pracovních postupů, který spravuje úlohy systému Hadoop. Oozie podporuje úlohy Hadoop pro Apache MapReduce, prase, podregistr a další.  Oozie používá metastore k ukládání podrobností o pracovních postupech. Pokud chcete zvýšit výkon při použití Oozie, můžete použít Azure SQL Database jako vlastní metastore. Metastore poskytuje přístup k datům úlohy Oozie po odstranění clusteru.

Pokyny k vytvoření Oozie metastore pomocí Azure SQL Database najdete v tématu [použití Apache Oozie pro pracovní postupy](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Vlastní Ambari DB

Pokud chcete používat svou vlastní externí databázi s Apache Ambari ve službě HDInsight, podívejte se na [vlastní databázi Apache Ambari](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Další kroky

* [Nastavení clusterů ve službě HDInsight se softwarem Apache Hadoop, Apache Spark, Apache Kafka a dalšími](./hdinsight-hadoop-provision-linux-clusters.md)