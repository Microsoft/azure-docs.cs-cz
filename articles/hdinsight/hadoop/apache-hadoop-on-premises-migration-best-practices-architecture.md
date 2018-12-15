---
title: Migrace místních Apache Hadoop clusterů Azure HDInsight – osvědčené postupy architektury
description: Přečtěte si osvědčené postupy architektury pro migrace místních Hadoop clusterů pro Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 8295c149d513f89318aa63ddd7f4236013923203
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53433998"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Migrace místních Apache Hadoop clusterů Azure HDInsight – osvědčené postupy architektury

Tento článek obsahuje doporučení pro architekturu systému Azure HDInsight. To je součástí série, která poskytuje osvědčené postupy pro pomoc s migrací místních systémů Apache Hadoop do Azure HDInsight.

## <a name="use-multiple-workload-optimized-clusters"></a>Použití více clusterů optimalizovaných pro úlohu

V mnoha nasazeních systému Apache Hadoop v místním se skládají z jednoho velkého clusteru, který podporuje mnoho úloh. Tento jeden cluster může být složité a mohou vyžadovat ohrožení pro jednotlivé služby pro zajištění spolupráce všech. Migrace místních Hadoop clusterů Azure HDInsight vyžaduje změny v přístupu.

Clustery Azure HDInsight jsou určeny pro konkrétní typ využití služby compute. Protože úložiště mohou být sdíleny napříč více clusterů, je možné vytvořit více úloh paměťově optimalizované výpočetní clustery a vyhovět potřebám různých úloh. Každý typ clusteru má optimální konfiguraci pro tuto konkrétní úlohu. Následující tabulka uvádí typy podporovaných clusterů v HDInsight a odpovídající úlohy.

|**Úloha**|**Typ clusteru HDInsight**|
|---|---|
|Dávkové zpracování (ETL / ELT)|Hadoop, Spark|
|Datové sklady|Hadoop, Spark, Interactive Query|
|IoT / streamování|Kafka, Storm, Spark|
|Zpracování transakcí NoSQL|HBase|
|Interaktivní a rychlejší dotazy s ukládáním do mezipaměti v paměti|Interaktivní dotaz|
|Pro datové vědy|Služby ML, Spark|

V následující tabulce jsou uvedeny různé metody, které lze použít k vytvoření clusteru HDInsight.

|**Nástroj**|**Prohlížeč**|**Příkazový řádek**|**REST API**|**Sada SDK**|
|---|---|---|---|---|
|[Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[Rozhraní příkazového řádku Azure (verze 1.0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[.NET SDK](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)||||X|
|[Python SDK](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)||||X|
|[Java SDK](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)||||X|
|[Šablony Azure Resource Manageru](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

Další informace najdete v článku [typy v HDInsight clusterů](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Použití přechodné clusterů na vyžádání

Clustery HDInsight můžou získat nevyužité dlouhou dobu. Pomáhá šetřit na nákladech na prostředky, HDInsight podporuje přechodné clustery na vyžádání, které můžete odstraňovat, jakmile úloha byla úspěšně dokončena.

Při odstranění clusteru se neodeberou přidružený účet úložiště a externí metadat. Clusteru lze později znovu vytvořit pomocí stejné účty úložiště a meta úložišť.

Azure Data Factory je možné naplánovat vytváření clusterů HDInsight na vyžádání. Další informace najdete v článku [vytváření clusterů na vyžádání Apache Hadoop v HDInsight pomocí Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md).

## <a name="decouple-storage-from-compute"></a>Oddělit úložiště od výpočetních prostředků

Typické místních Hadoop nasazení používá stejnou sadu počítačů pro ukládání a zpracování dat. Protože jsou společně umístěný, výpočetní výkon a úložiště musí škálovat společně.

Na clusterech HDInsight úložiště nemusí být umístěna společně s výpočetními prostředky a může být v Azure storage, Azure Data Lake Storage nebo obojí. Oddělení úložiště od výpočetních prostředků má následující výhody:

- Sdílení dat mezi clustery.
- Použití přechodné clustery, protože data nejsou závislé na clusteru.
- Snížit náklady na úložiště.
- Škálování úložiště a výpočetní samostatně.
- Replikace dat mezi oblastmi.

Výpočetní clustery jsou vytvořeny blízko prostředků účtu úložiště v oblasti Azure ke zmírnění snížení výkonu oddělení výpočetního výkonu a úložiště. Vysokorychlostní sítí usnadňují efektivní pro výpočetní uzly pro přístup k datům ve službě Azure storage.

## <a name="use-external-metadata-stores"></a>Použití externích úložišť metadat


Existují dva hlavní metaúložiště, které fungují s clustery HDInsight: [Apache Hive](https://hive.apache.org/) a [Apache Oozie](https://oozie.apache.org/). Hive metastore je centrální schéma úložiště, které můžete používat moduly pro zpracování dat včetně Hadoopu, Sparku, LLAP, Presto a Apache Pig. Úložiště metadat Oozie ukládá informace o plánování a stavu v průběhu a dokončení úlohy systému Hadoop.


HDInsight používá Azure SQL Database u metaúložiště Hive a Oozie. Existují dva způsoby, jak nastavit metastore v clusterech HDInsight:

1. Výchozí metastore

    - Bez dalších poplatků.
    - Metastore se odstraní při odstranění clusteru.
    - Úložiště metadat se nedají sdílet mezi různých clusterech.
    - Používá základní databáze Azure SQL, který má limit 5 DTU.

1. Vlastní externí metastore

    - Zadejte externí databázi SQL Azure jako metastore.
    - Clustery můžete vytvořit a odstranit bez ztráty metadata, včetně podrobností o úloze Hive schématu Oozie.
    - Jeden metastore db je sdílet s různými typy clusterů.
    - Metastore se dá škálovat podle potřeby.
    - Další informace najdete v tématu [použití externích úložišť metadat v Azure HDInsight](../hdinsight-use-external-metadata-stores.md).

## <a name="best-practices-for-hive-metastore"></a>Osvědčené postupy pro Hive Metastore

Některé osvědčené postupy metastore Hive v HDInsight jsou následující:

- Použijte vlastní externí metaúložiště do samostatné výpočetní prostředky a metadata.
- Začněte s instancí Azure SQL úrovně S2, která poskytuje 50 DTU a 250 GB úložiště. Pokud se kritický bod, databázi můžete škálovat.
- Nesdílejte metastore vytvořit pro jednu verzi clusteru HDInsight s clustery s jinou verzí. Různé verze Hive pomocí různých schémat. Například metastoru nelze sdílet s clustery Hive 1.2 a Hive 2.1.
- Pravidelně zálohujte vlastní úložiště metadat.
- Udržujte metastore a HDInsight cluster ve stejné oblasti.
- Monitorování metastore výkonu a dostupnosti s využitím Azure SQL Database monitorování nástrojů, jako je Azure portal nebo Azure Log Analytics.
- Spustit **analyzovat tabulky** příkaz jako vyžadovaných ke generování statistik pro tabulky a sloupce. Například, `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Osvědčené postupy pro různé úlohy

- Zvažte použití clusteru LLAP pro interaktivní dotazy Hive s lepší odezvu [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) je nová funkce ve verzi 2.0 Hive, umožňující ukládání v mezipaměti dotazů. LLAP díky dotazů Hive mnohem rychlejší, až [26 x rychlejší než Hive 1.x v některých případech](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).
- Zvažte použití Sparkových úloh místo úloh Hive.
- Zvažte nahrazení impala dotazy s funkcí LLAP dotazy.
- Zvažte nahrazení úlohy mapreduce je možné s Sparkových úloh.
- Zvažte nahrazení použití strukturovaného streamování Sparku úloh s nízkou latencí Spark dávkových úloh.
- Vezměte v úvahu pro Orchestrace dat pomocí Azure Data Factory (ADF) 2.0.
- Vezměte v úvahu Ambari za správu clusteru.
- Změna úložiště dat z místní HDFS WASB nebo ADLS nebo služby AD FS pro zpracování skripty.
- Zvažte použití Ranger RBAC u tabulek Hive a auditování.
- Zvažte použití služby cosmos DB místo MongoDB nebo Cassandra.

## <a name="next-steps"></a>Další postup

Přečtěte si další článek v této sérii:

- [Osvědčené postupy infrastruktury pro místní migrace Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)
