---
title: Použití externích úložišť metadat – Azure HDInsight
description: Použití externích úložišť metadat s clustery HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 9c35a4a811925abaf8dcb64d3e7060bbb1f91cce
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408319"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Použití externích úložišť metadat v Azure HDInsight

Metastore Apache Hive v HDInsight je zásadní součástí architektury Apache Hadoop. Metastoru je centrální schéma úložiště, které lze použít v jiných nástrojích přístup velké objemy dat, jako je Apache Spark, interaktivní dotazy (LLAP), Presto nebo Apache Pig. HDInsight používá Azure SQL Database jako úložiště metadat Hive.

![Architektura HDInsight Hive metadat Store](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Existují dva způsoby, které můžete nastavit metastoru clusterů HDInsight:

* [Výchozí metastore](#default-metastore)
* [Vlastní úložiště metadat](#custom-metastore)

## <a name="default-metastore"></a>Výchozí metastore

Ve výchozím nastavení vytvoří HDInsight metastoru s každý typ clusteru. Místo toho můžete určit vlastní úložiště metadat. Výchozí metastore zahrnuje následující aspekty:
- Bez dalších poplatků. HDInsight vytváří metastoru s každý typ clusteru bez žádné další poplatky.
- Každý výchozí metastore je součástí životního cyklu clusteru. Při odstranění clusteru se odstraní také odpovídající metastore a metadata.
- Výchozí metastore nelze sdílet s další clustery.
- Výchozí metastore používá základní databáze SQL Azure, který má limit 5 DTU (jednotek transakce databáze).
Tento výchozí metastore se obvykle používá pro relativně jednoduché úlohy, které nevyžadují více clusterů které nevyžadují metadata zachovány nad rámec životního cyklu clusteru.


## <a name="custom-metastore"></a>Vlastní úložiště metadat

HDInsight také podporuje vlastní metaúložiště, které se doporučují pro produkční clustery:
- Zadejte vlastní databázi SQL Azure jako metastore.
- Životní cyklus metastore objektu se neváže na životní cyklus clustery, tak můžete vytvářet a odstraňovat clustery bez ztráty metadat. Metadata, jako jsou schémata vaše Hive se zachová, i když odstraníte a znovu vytvoříte HDInsight cluster.
- Vlastní úložiště metadat umožňuje připojit více clusterů a typy clusterů na tento metaúložiště. Například jeden metastore je sdílet napříč clustery Spark, Interactive Query a Hive v HDInsight.
- Platíte za cenu podle úrovně výkonu, které zvolíte metastoru (databáze SQL Azure).
- Můžete vertikálně navýšit kapacitu metastore podle potřeby.

![Případ použití Store metadat Hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)


### <a name="select-a-custom-metastore-during-cluster-creation"></a>Vyberte vlastní úložiště metadat během vytváření clusteru

Cluster může odkazovat na dříve vytvořenou databázi SQL Azure při vytváření clusteru, nebo SQL Database můžete nakonfigurovat po vytvoření clusteru. Tato možnost je zadána s úložištěm > Nastavení Metastoru při vytváření nového systému Hadoop, Spark nebo interaktivní Hive clusteru z webu Azure portal.

![Portál Azure Store HDInsight Hive metadat](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

Můžete také přidat další clustery na vlastní úložiště metadat z webu Azure portal nebo z Ambari konfigurace (Hive > Upřesnit)

![Ambari Store metadat Hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Osvědčené postupy metastoru Hive

Tady jsou některé obecné HDInsight Hive metastore osvědčených postupů:

- Použijte vlastní úložiště metadat, kdykoli je to možné, aby samostatné výpočetní prostředky (spuštěného clusteru) a metadata (uložená v metastore).
- Začněte s vrstvou S2, která poskytuje 50 DTU a 250 GB úložiště. Pokud se kritický bod, databázi můžete škálovat.
- Pokud máte v úmyslu více clusterů HDInsight pro přístup k datům samostatný, použijte samostatné databáze pro metastore v každém clusteru. Pokud metastoru sdílíte mezi více clusterů HDInsight, znamená to, že clustery používají stejný metadat a základní uživatelských dat souborů.
- Pravidelně zálohujte vlastní úložiště metadat. Azure SQL Database automaticky generuje zálohování, ale období uchování zálohy se liší. Další informace najdete v tématu [přečtěte si víc o automatické zálohování SQL Database](../sql-database/sql-database-automated-backups.md).
- Vyhledejte metastore a HDInsight cluster ve stejné oblasti, pro nejvyšší výkon a nejnižší poplatky za výchozí přenos dat sítě.
- Monitorování vaší metastore výkonu a dostupnosti s využitím Azure SQL Database monitorování nástroje, jako je Azure portal nebo Azure Log Analytics.
- Po vytvoření nové, vyšší verzi Azure HDInsight na stávající databázi vlastní úložiště metadat systému upgraduje schématu metaúložiště, které je nevratná operace bez obnovení ze zálohy databáze.
- Pokud metastoru sdílíte mezi více clusterů, ujistěte se, že všechny clustery jsou na stejné verzi HDInsight. Různé verze Hive pomocí různých metastore databázových schématech. Například nemůžete sdílet metastoru napříč clustery Hive 1.2 a Hive 2.1 systémovou správou verzí. 

##  <a name="apache-oozie-metastore"></a>Apache Oozie Metastore

Apache Oozie je systém koordinace pracovních postupů, které spravuje úlohy platformy Hadoop.  Oozie podporuje úlohy systému Hadoop pro Apache MapReduce, Pig, Hive a další.  Oozie metastoru používá k ukládání podrobnosti o aktuálních a dokončených pracovních postupů. Pokud chcete zvýšit výkon při použití Oozie, můžete použít Azure SQL Database jako vlastní úložiště metadat. Metastore také poskytuje přístup k datům úlohy Oozie po odstranění clusteru.

Pokyny týkající se vytvoření úložiště metadat Oozie s Azure SQL Database najdete v tématu [použití Apache Oozie pro pracovní postupy](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>Další postup

- [Nastavení clusterů v HDInsight se Apache Hadoop, Apache Spark, Apache Kafka a další](./hdinsight-hadoop-provision-linux-clusters.md)
