---
title: 'Architektura: Místní Apache Hadoop do Azure HDInsight'
description: Naučte se osvědčené postupy architektury pro migraci místních clusterů Hadoop do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 2d0d5bb871612bc5e16a26eb49808c39661ffb50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934694"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Migrace místních clusterů Apache Hadoop do Azure HDInsight – osvědčené postupy architektury

Tento článek obsahuje doporučení pro architekturu systémů Azure HDInsight. Je součástí řady, která poskytuje osvědčené postupy, které vám pomohou s migrací místních systémů Apache Hadoop do Azure HDInsight.

## <a name="use-multiple-workload-optimized-clusters"></a>Použití více clusterů optimalizovaných pro úlohy

Mnoho místních nasazení Apache Hadoop se skládá z jednoho velkého clusteru, který podporuje mnoho úloh. Tento jediný cluster může být složitý a může vyžadovat kompromisy pro jednotlivé služby, aby vše fungovalo společně. Migrace místních clusterů Hadoop do Azure HDInsight vyžaduje změnu přístupu.

Clustery Azure HDInsight jsou navržené pro konkrétní typ výpočetního využití. Vzhledem k tomu, že úložiště lze sdílet ve více clusterech, je možné vytvořit více výpočetních clusterů optimalizovaných pro úlohy, aby vyhovovaly potřebám různých úloh. Každý typ clusteru má optimální konfiguraci pro konkrétní úlohu. V následující tabulce jsou uvedeny podporované typy clusterů v HDInsight a odpovídající úlohy.

|Úloha|Typ clusteru HDInsight|
|---|---|
|Dávkové zpracování (ETL / ELT)|Hadoop, Jiskra|
|Datové sklady|Hadoop, Spark, interaktivní dotaz|
|IoT / Streamování|Kafka, Bouře, Jiskra|
|Transakční zpracování NoSQL|HBase|
|Interaktivní a rychlejší dotazy s ukládáním do mezipaměti v paměti|Interaktivní dotaz|
|Datová věda|ML Služby, Jiskra|

V následující tabulce jsou uvedeny různé metody, které lze použít k vytvoření clusteru HDInsight.

|Nástroj|Na základě prohlížeče|Příkazový řádek|REST API|Sada SDK|
|---|---|---|---|---|
|[Portál Azure](../hdinsight-hadoop-create-linux-clusters-portal.md)|×||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|×|×|×|×|
|[Příkaz cli Azure (ver.1.0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||×|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||×|||
|[Curl](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||×|×||
|[Sada SDK rozhraní .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)||||×|
|[Python SDK](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)||||×|
|[Java SDK](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)||||×|
|[Šablony Azure Resource Manageru](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||×|||

Další informace naleznete v článku [Typy clusterů v HDInsight](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Použití přechodných clusterů na vyžádání

Clustery HDInsight mohou být delší dobu nepoužívané. Aby se ušetřily náklady na prostředky, podporuje HDInsight přechodné clustery na vyžádání, které lze odstranit po úspěšném dokončení úlohy.

Když odstraníte cluster, přidružený účet úložiště a externí metadata se neodeberou. Cluster lze později znovu vytvořit pomocí stejných účtů úložiště a meta-úložišť.

Azure Data Factory se dá naplánovat vytváření clusterů HDInsight na vyžádání. Další informace najdete v článku [Vytvoření clusterů Apache Hadoop na vyžádání v HDInsight pomocí Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md).

## <a name="decouple-storage-from-compute"></a>Oddělení úložiště od výpočetních prostředků

Typická místní nasazení Hadoopu používají stejnou sadu počítačů pro ukládání a zpracování dat. Vzhledem k tomu, že jsou colocated, výpočetní prostředky a úložiště musí být škálovány společně.

V clusterech HDInsight nemusí být úložiště společné a může být buď v úložišti Azure, Azure Data Lake Storage nebo obojím. Oddělení úložiště od výpočetních prostředků má následující výhody:

- Sdílení dat mezi clustery.
- Použití přechodných clusterů, protože data nejsou závislá na clusteru.
- Nižší náklady na úložiště.
- Škálování úložiště a výpočetní zvlášť.
- Replikace dat napříč oblastmi.

Výpočetní clustery se vytvářejí v blízkosti prostředků účtu úložiště v oblasti Azure, aby se zmírnily náklady na výkon oddělení výpočetních prostředků a úložiště. Vysokorychlostní sítě efektivní pro výpočetní uzly pro přístup k datům v úložišti Azure.

## <a name="use-external-metadata-stores"></a>Použití externích úložišť metadat

Existují dva hlavní metastores, které pracují s HDInsight clustery: [Apache Hive](https://hive.apache.org/) a [Apache Oozie](https://oozie.apache.org/). Metastore Hive je centrální úložiště schématu, které lze použít v motorech pro zpracování dat včetně Hadoop, Spark, LLAP, Presto a Apache Pig. Metastore Oozie ukládá podrobnosti o plánování a stavu probíhajících a dokončených úloh Hadoop.

HDInsight používá Azure SQL Database pro metaobchody Hive a Oozie. Metastore lze v clusterech HDInsight nastavit dvěma způsoby:

1. Výchozí metastore

    - Žádné další náklady.
    - Metastore je odstraněn při odstranění clusteru.
    - Metastore nelze sdílet mezi různými clustery.
    - Používá základní Azure SQL DB, který má pět limit DTU.

1. Vlastní externí metastore

    - Zadejte externí Azure SQL Database jako metastore.
    - Clustery lze vytvořit a odstranit bez ztráty metadat, včetně hive schema Oozie podrobnosti o úloze.
    - Jeden metastore db lze sdílet s různými typy clusterů.
    - Metastore může být zmenšen nahoru podle potřeby.
    - Další informace najdete [v tématu Použití externích úložišť metadat v Azure HDInsight](../hdinsight-use-external-metadata-stores.md).

## <a name="best-practices-for-hive-metastore"></a>Doporučené postupy pro Hive Metastore

Některé osvědčené postupy metastore HDInsight Hive jsou následující:

- Použijte vlastní externí metastore k oddělení výpočetních prostředků a metadat.
- Začněte s instancí Azure SQL úrovně S2, která poskytuje 50 DTU a 250 GB úložiště. Pokud se zobrazí kritické místo, můžete databázi škálovat.
- Nesdílejte metastore vytvořený pro jednu verzi clusteru HDInsight s clustery jiné verze. Různé verze Hive používají různá schémata. Metastore nelze například sdílet s clustery Hive 1.2 a Hive 2.1.
- Pravidelně zálohujte vlastní metastore.
- Udržujte cluster metastore a HDInsight ve stejné oblasti.
- Sledujte metastore pro výkon a dostupnost pomocí nástrojů Azure SQL Database Monitoring, jako je portál Azure nebo protokoly Azure Monitor.
- Proveďte `ANALYZE TABLE` příkaz podle potřeby pro generování statistik pro tabulky a sloupce. Například, `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Doporučené postupy pro různé úlohy

- Zvažte použití clusteru LLAP pro interaktivní dotazy Hive s vylepšenou dobou odezvy [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) je nová funkce v Hive 2.0, která umožňuje ukládání dotazů do mezipaměti v paměti. LLAP umožňuje hive dotazy mnohem rychleji, až [26x rychlejší než Hive 1.x v některých případech](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).
- Zvažte použití úloh Spark místo úloh Hive.
- Zvažte nahrazení dotazů založených na impala dotazy LLAP.
- Zvažte nahrazení úloh MapReduce úlohami Spark.
- Zvažte nahrazení dávkových úloh Spark s nízkou latencí pomocí úloh strukturovaného streamování Spark.
- Zvažte použití Azure Data Factory (ADF) 2.0 pro orchestraci dat.
- Zvažte Ambari pro správu clusteru.
- Změňte úložiště dat z místního HDFS na WASB nebo ADLS nebo ADFS pro zpracování skriptů.
- Zvažte použití Ranger RBAC na hive tabulky a auditování.
- Zvažte použití CosmosDB místo MongoDB nebo Cassandra.

## <a name="next-steps"></a>Další kroky

Přečtěte si další článek v této sérii:

- [Osvědčené postupy pro infrastrukturu pro místní migraci Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)
