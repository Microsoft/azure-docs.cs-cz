---
title: Použití externích úložišť metadat – Azure HDInsight
description: Použití externích úložišť metadat s clustery HDInsight.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.author: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: fb1401578237a92a6f164ec98e8dbcdbbb88be38
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39595394"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Použití externích úložišť metadat v Azure HDInsight

Metastore Hive v HDInsight je zásadní součástí systému Hadoop architektury. Metastoru je centrální schéma úložiště, které lze použít v jiných nástrojích přístup velké objemy dat, jako je Spark, interaktivní dotazy (LLAP), Presto nebo Pig. HDInsight používá Azure SQL Database jako úložiště metadat Hive.

![Architektura HDInsight Hive metadat Store](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Existují dva způsoby, které můžete nastavit metastoru clusterů HDInsight:

* [Výchozí metastore](#default-metastore)
* [Vlastní úložiště metadat](#custom-metastore)

## <a name="default-metastore"></a>Výchozí metastore

Ve výchozím nastavení zřídí HDInsight metastore s každý typ clusteru. Místo toho můžete určit vlastní úložiště metadat. Výchozí metastore zahrnuje následující aspekty:
- Bez dalších poplatků. HDInsight zřídí metastore s každý typ clusteru bez žádné další poplatky.
- Každý výchozí metastore je součástí životního cyklu clusteru. Při odstranění clusteru také odstranit metastore a metadata.
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

<!-- Image – Typical shared custom Metastore scenario in HDInsight (?) -->



### <a name="select-a-custom-metastore-during-cluster-creation"></a>Vyberte vlastní úložiště metadat během vytváření clusteru

Cluster může odkazovat na dříve vytvořenou databázi SQL Azure při vytváření clusteru, nebo SQL Database můžete nakonfigurovat po vytvoření clusteru. Tato možnost je zadána s úložištěm > Nastavení Metastoru při vytváření nového systému Hadoop, Spark nebo interaktivní Hive clusteru z webu Azure portal.

![Portál Azure Store HDInsight Hive metadat](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

Můžete také přidat další clustery na vlastní úložiště metadat z webu Azure portal nebo z Ambari konfigurace (Hive > Upřesnit)

![Ambari Store metadat Hive HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Osvědčené postupy metastoru Hive

Tady jsou některé obecné HDInsight Hive metastore osvědčených postupů:

- Použijte vlastní úložiště metadat, kdykoli je to možné, jak to vám pomůže samostatné výpočetní prostředky (spuštěného clusteru) a metadata (uložená v metastore).
- Začněte s vrstvou S2, která poskytuje 50 DTU a 250 GB úložiště. Pokud se kritický bod, databázi můžete škálovat.
- Ujistěte se, že metastore vytvořit pro jednu verzi clusteru HDInsight se sdílet mezi různými verzemi clusteru HDInsight. Různé verze Hive pomocí různých schémat. Například nemůžete sdílet metastoru Hive 1.2 a Hive 2.1 clustery.
- Pravidelně zálohujte vlastní úložiště metadat.
- Udržujte metastore a HDInsight cluster ve stejné oblasti.
- Monitorování vaší metastore výkonu a dostupnosti s využitím Azure SQL Database monitorování nástroje, jako je Azure portal nebo Azure Log Analytics.

## <a name="oozie-metastore"></a>Úložiště metadat Oozie

Apache Oozie je systém koordinace pracovních postupů, které spravuje úlohy platformy Hadoop.  Oozie podporuje úlohy systému Hadoop pro Apache MapReduce, Pig, Hive a další.  Oozie metastoru používá k ukládání podrobnosti o aktuálních a dokončených pracovních postupů. Pokud chcete zvýšit výkon při použití Oozie, můžete použít Azure SQL Database jako vlastní úložiště metadat. Metastore také poskytuje přístup k datům úlohy Oozie po odstranění clusteru.

Pokyny týkající se vytvoření úložiště metadat Oozie s Azure SQL Database najdete v tématu [použití Oozie pro pracovní postupy](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>Další postup

- [Nastavení clusterů v HDInsight pomocí Hadoop, Spark, Kafka a další](./hdinsight-hadoop-provision-linux-clusters.md)
