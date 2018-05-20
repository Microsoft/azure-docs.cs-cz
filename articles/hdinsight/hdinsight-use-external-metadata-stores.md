---
title: Použít úložiště externí metadata - Azure HDInsight | Microsoft Docs
description: Použijte externí metadata úložiště s clustery HDInsight.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
tags: azure-portal
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 2eadee1a8695450e2219031ea1a65ee3624f26b5
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Použijte externí metadata úložiště v Azure HDInsight

Metaúložiště Hive v HDInsight, je zásadní součástí architektury Hadoop. Metaúložiště je úložiště centrální schéma, které lze použít v jiných nástrojích přístup velkých objemů dat, například Spark, interaktivní dotazu (LLAP), Presto nebo Pig. HDInsight používá Azure SQL Database jako metaúložiště Hive.

![Architektura úložiště HDInsight Hive metadat](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Existují dva způsoby, které můžete nastavit metaúložiště pro vaše clustery služby HDInsight:

* [Výchozí metaúložiště.](#default-metastore)
* [Vlastní metaúložiště.](#custom-metastore)

## <a name="default-metastore"></a>Výchozí metaúložiště.

Ve výchozím nastavení zřídí HDInsight metaúložiště s každý typ clusteru. Místo toho můžete zadat vlastní metaúložiště. Výchozí metaúložiště zahrnuje následující aspekty:
- Bez dalších nákladů. HDInsight zřídí metaúložiště s každý typ clusteru bez žádné další náklady.
- Každý výchozí metaúložiště je součástí clusteru životního cyklu. Při odstranění cluster taky odstranit metaúložiště a metadata.
- Výchozí metaúložiště nelze sdílet s další clustery.
- Výchozí metaúložiště používá základní databáze SQL Azure, což může mít 5 DTU (jednotky transakcí databáze).
Tento výchozí metaúložiště se obvykle používá pro relativně jednoduché úlohy, které nevyžadují víc clusterů a nepotřebujete metadata zachovaná překročení životního cyklu clusteru.


## <a name="custom-metastore"></a>Vlastní metaúložiště.

HDInsight podporuje také vlastní metaúložiště, které se doporučují pro produkčních clusterů:
- Vlastní databázi SQL Azure je zadat jako metaúložiště.
- Životní cyklus metaúložiště není vázaný životní cyklus clustery, takže můžete vytvářet a odstraňovat clustery bez ztráty metadata. Metadata, jako je vaše Hive schémata zachová i po odstranit a znovu vytvořit HDInsight cluster.
- Vlastní metaúložiště vám umožňuje připojení více clusterů a typy clusteru k této metaúložiště. Například jeden metaúložiště lze sdílet napříč clustery interaktivní dotaz Hive a Spark v HDInsight.
- Platíte nákladů metaúložiště (databázi SQL Azure) podle úrovně výkonu, které zvolíte.
- Metaúložiště můžete škálovat podle potřeby.


![Případ použití úložiště HDInsight Hive metadat](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

<!-- Image – Typical shared custom Metastore scenario in HDInsight (?) -->



### <a name="select-a-custom-metastore-during-cluster-creation"></a>Vyberte vlastní metaúložiště při vytváření clusteru

Cluster může ukazovat na dříve vytvořenou databázi SQL Azure při vytváření clusteru, nebo můžete nakonfigurovat databázi SQL, po vytvoření clusteru. Tato možnost je zadána s úložištěm > Metaúložiště nastavení při vytváření nové Hadoop, Spark nebo interaktivní Hive clusteru z portálu Azure.

![Portál Azure úložiště HDInsight Hive metadat](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

Můžete také přidat další clustery pro vlastní metaúložiště z portálu Azure nebo z konfigurace Ambari (Hive > Upřesnit)

![Ambari HDInsight Hive Metadata úložiště](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Osvědčené postupy metaúložiště Hive

Tady jsou některé obecné HDInsight Hive metaúložiště osvědčené postupy:

- Použijte vlastní metaúložiště, kdykoli je to možné, jak to pomůže samostatné výpočetní prostředky (spuštěného clusteru) a metadata (uložená v metaúložiště).
- Začněte S2 vrstvy, která poskytuje 50 DTU a 250 GB úložiště. Pokud se zobrazí problémové místo, můžete postupně škálovat databáze.
- Zajistěte, aby metaúložiště vytvořené pro jednu verze clusteru HDInsight není sdílená mezi různými verzemi clusteru HDInsight. Různé verze Hive pomocí různých schémat. Například nelze sdílet metaúložiště Hive 1.2 a Hive 2.1 clustery s.
- Vaše vlastní metaúložiště zálohujte pravidelně.
- Zachovat metaúložiště a HDInsight cluster ve stejné oblasti.
- Sledujte vaše metaúložiště pro výkon a dostupnost prostřednictvím nástrojů pro monitorování databáze SQL Azure, jako je například portál Azure nebo Azure Log Analytics.

## <a name="oozie-metastore"></a>Metaúložiště Oozie

Apache Oozie je systém koordinace pracovního postupu, který spravuje úloh Hadoop.  Oozie podporuje úlohy systému Hadoop pro Apache MapReduce, Pig, Hive a dalších.  Oozie používá k ukládání podrobnosti o aktuálních a dokončených pracovních postupů metaúložiště. Pokud chcete zvýšit výkon při použití Oozie, můžete použít Azure SQL Database jako vlastní metaúložiště. Metaúložiště můžete zadat taky přístup k datům úlohy Oozie po odstranění clusteru.

Pokyny pro vytvoření metaúložiště Oozie s Azure SQL Database, najdete v části [Oozie použijte pro pracovní postupy](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>Další postup

- [Nastavit clusterů v HDInsight Hadoop, Spark, Kafka a dalšími](./hdinsight-hadoop-provision-linux-clusters.md)
