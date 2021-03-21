---
title: 'Architektura: místní Apache Hadoop do Azure HDInsight'
description: Naučte se osvědčené postupy pro migraci místních clusterů Hadoop do Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 519dc53f6373ae1a9c8853d3fa90d137e9fa934b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435405"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Migrace místních Apache Hadoop clusterů do Azure HDInsight – osvědčené postupy pro architekturu

Tento článek obsahuje doporučení pro architekturu systémů Azure HDInsight. Je součástí série, která poskytuje osvědčené postupy, které vám pomůžou s migrací místních Apache Hadoop systémů do Azure HDInsight.

## <a name="use-multiple-workload-optimized-clusters"></a>Použití více clusterů optimalizovaných pro úlohy

Mnoho místních Apache Hadoop nasazení se skládá z jednoho velkého clusteru, který podporuje mnoho úloh. Tento samostatný cluster může být složitý a může vyžadovat ohrožení jednotlivých služeb, aby všechno spolupracovalo. Migrace místních clusterů Hadoop do Azure HDInsight vyžaduje změnu v přístupu.

Clustery Azure HDInsight jsou navržené pro konkrétní typ výpočetního využití. Vzhledem k tomu, že úložiště je možné sdílet mezi několika clustery, je možné vytvořit několik výpočetních clusterů optimalizovaných pro úlohy, které budou vyhovovat potřebám různých úloh. Každý typ clusteru má optimální konfiguraci pro konkrétní úlohu. Následující tabulka uvádí podporované typy clusterů ve službě HDInsight a příslušné úlohy.

|Úloha|Typ clusteru HDInsight|
|---|---|
|Dávkové zpracování (ETL/ELT)|Hadoop, Spark|
|Datové sklady|Hadoop, Spark, interaktivní dotaz|
|IoT/streamování|Kafka, vyplavení, Spark|
|Zpracování transakcí NoSQL|HBase|
|Interaktivní a rychlejší dotazy s ukládáním do mezipaměti v paměti|Interaktivní dotaz|
|Datové vědy|Služby ML, Spark|

V následující tabulce jsou uvedeny různé metody, které lze použít k vytvoření clusteru HDInsight.

|Nástroj|Založené na prohlížeči|Příkazový řádek|REST API|Sada SDK|
|---|---|---|---|---|
|[Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md)|×||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|×|×|×|×|
|[Rozhraní příkazového řádku Azure (ver 1,0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||×|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||×|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||×|×||
|[.NET SDK](/dotnet/api/overview/azure/hdinsight)||||×|
|[Python SDK](/python/api/overview/azure/hdinsight)||||×|
|[Java SDK](/java/api/overview/azure/hdinsight)||||×|
|[Šablony Azure Resource Manageru](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||×|||

Další informace najdete v článku [typy clusterů ve službě HDInsight](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Použití přechodných clusterů na vyžádání

Clustery HDInsight se po dlouhou dobu nepoužívá. V zájmu úspory nákladů na prostředky podporuje služba HDInsight přechodný clustery na vyžádání, které je možné po úspěšném dokončení úlohy odstranit.

Když odstraníte cluster, přidružený účet úložiště a externí metadata se neodeberou. Cluster se dá později znovu vytvořit pomocí stejných účtů úložiště a meta úložišť.

Azure Data Factory můžete použít k plánování vytváření clusterů HDInsight na vyžádání. Další informace najdete v článku [Vytvoření clusterů Apache Hadoop na vyžádání v HDInsight pomocí Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md).

## <a name="decouple-storage-from-compute"></a>Oddělit úložiště od výpočetní služby

Typická místní nasazení Hadoop používají stejnou sadu počítačů pro ukládání a zpracování dat. Vzhledem k tomu, že se společně nacházejí, výpočetní prostředky a úložiště musí být škálované dohromady.

V clusterech HDInsight nemusí být úložiště společně umístěno s výpočetními prostředky a může být buď v Azure Storage, Azure Data Lake Storage nebo obojí. Odpojujení úložiště od výpočetní služby má následující výhody:

- Sdílení dat napříč clustery.
- Použití přechodných clusterů, protože data nejsou závislá na clusteru.
- Snížení nákladů na úložiště.
- Škálování úložiště a výpočetních prostředků odděleně.
- Replikace dat mezi oblastmi.

Výpočetní clustery jsou vytvořeny blízko prostředků účtu úložiště v oblasti Azure, čímž se sníží náklady na výkon oddělení výpočetní kapacity a úložiště. Vysokorychlostní sítě umožňují výpočetním uzlům efektivní přístup k datům ve službě Azure Storage.

## <a name="use-external-metadata-stores"></a>Použití externích úložišť metadat

Existují dva hlavní metaúložiště, které pracují s clustery HDInsight: [Apache Hive](https://hive.apache.org/) a [Apache Oozie](https://oozie.apache.org/). Metastore Hive je centrální úložiště schémat, které mohou používat moduly pro zpracování dat, včetně Hadoop, Spark, LLAP, presto a Apache prasete. Oozie metastore ukládá údaje o plánování a stavu probíhajících a dokončených úloh Hadoop.

HDInsight používá Azure SQL Database pro podregistr a Oozie metaúložiště. Existují dva způsoby, jak nastavit metastore v clusterech HDInsight:

1. Výchozí metastore

    - Žádné další náklady.
    - Metastore se odstraní, když se cluster odstraní.
    - Metastore nejde sdílet mezi různými clustery.
    - Používá základní službu Azure SQL DB, která má pět omezení DTU.

1. Vlastní externí metastore

    - Zadejte externí Azure SQL Database jako metastore.
    - Clustery je možné vytvořit a odstranit bez ztráty metadat včetně podrobností o úloze Oozie schématu podregistru.
    - Jedna metastore DB se dá sdílet s různými typy clusterů.
    - Metastore se dá škálovat podle potřeby.
    - Další informace najdete v tématu [použití externích úložišť metadat ve službě Azure HDInsight](../hdinsight-use-external-metadata-stores.md).

## <a name="best-practices-for-hive-metastore"></a>Osvědčené postupy pro metastore podregistru

Mezi osvědčené postupy metastore Hive HDInsight patří následující:

- Použijte vlastní externí metastore k oddělení výpočetních prostředků a metadat.
- Začněte s instancí Azure SQL úrovně S2, která poskytuje 50 jednotek DTU a 250 GB úložiště. Pokud se zobrazí kritický bod, můžete databázi škálovat nahoru.
- Nesdílejte metastore vytvořenou pro jednu verzi clusteru HDInsight s clustery s jinou verzí. Různé verze podregistru používají různá schémata. Například metastore nelze sdílet s clustery registru 1,2 a podregistr 2,1.
- Pravidelně zálohujte vlastní metastore.
- Udržujte cluster metastore a HDInsight ve stejné oblasti.
- Monitorujte metastore o výkonu a dostupnosti pomocí nástrojů pro monitorování Azure SQL Database, jako jsou protokoly Azure Portal nebo Azure Monitor.
- Spusťte `ANALYZE TABLE` příkaz podle potřeby a vygenerujte statistiku pro tabulky a sloupce. Například, `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Osvědčené postupy pro různé úlohy

- Zvažte použití clusteru LLAP pro interaktivní dotazy podregistru s vyšší dobou odezvy [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) je nová funkce v podregistru 2,0, která umožňuje ukládání dotazů do mezipaměti v paměti. LLAP vydává dotazy na podregistr mnohem rychleji, až do [26x rychleji než v registru 1. x v některých případech](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).
- Zvažte použití úloh Spark místo úloh podregistru.
- Zvažte nahrazení dotazů založených na Impala pomocí dotazů LLAP.
- Zvažte nahrazení úloh MapReduce pomocí úloh Spark.
- Zvažte použití úloh strukturovaného streamování Spark s nízkou latencí v dávkovém zpracování.
- Zvažte použití Azure Data Factory (ADF) 2,0 pro orchestraci dat.
- Zvažte Ambari pro správu clusteru.
- Změňte úložiště dat z místního HDFS na WASB nebo ADLS nebo ADFS pro zpracování skriptů.
- Zvažte použití Ranger RBAC na tabulkách a auditování tabulek podregistru.
- Zvažte použití CosmosDB místo MongoDB nebo Cassandra.

## <a name="next-steps"></a>Další kroky

Přečtěte si další článek v této sérii:

- [Osvědčené postupy infrastruktury pro migraci z místního prostředí do Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)