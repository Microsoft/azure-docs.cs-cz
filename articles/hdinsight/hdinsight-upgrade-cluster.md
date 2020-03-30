---
title: Migrace clusteru do novější verze
titleSuffix: Azure HDInsight
description: Přečtěte si pokyny pro migraci clusteru Azure HDInsight na novější verzi.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: f7198aeff5e9ef6d37e29c2336dc38e4eec0dda1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023969"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>Migrace clusteru HDInsight na novější verzi

Chcete-li využívat nejnovější funkce HDInsight, doporučujeme, aby clustery HDInsight byly pravidelně migrovány na nejnovější verzi. HDInsight nepodporuje inadované inovace na místě, kde je existující cluster upgradován na novější verzi komponenty. Je nutné vytvořit nový cluster s požadovanou verzí komponenty a platformy a potom migrovat aplikace, aby bylo nutné použít nový cluster. Chcete-li migrovat verze clusteru HDInsight, postupujte podle následujících pokynů.

> [!NOTE]  
> Informace o podporovaných verzích hdinsightu najdete v [tématu verze komponent HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="migration-tasks"></a>Úkoly migrace

Pracovní postup pro upgrade clusteru HDInsight je následující.
![Diagram pracovního postupu upgradu HDInsight](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Přečtěte si jednotlivé části tohoto dokumentu, abyste pochopili změny, které mohou být vyžadovány při upgradu clusteru HDInsight.
2. Vytvořte cluster jako testovací prostředí nebo prostředí zabezpečování kvality. Další informace o vytvoření clusteru najdete v [tématu Informace o vytváření clusterů HDInsight založených na Linuxu](hdinsight-hadoop-provision-linux-clusters.md)
3. Zkopírujte existující úlohy, zdroje dat a propady do nového prostředí.
4. Proveďte ověřovací testování a ujistěte se, že vaše úlohy fungují podle očekávání v novém clusteru.

Jakmile ověříte, že vše funguje podle očekávání, naplánujte prostoje migrace. Během této prostoje proveďte následující akce:

1. Zálohujte všechna přechodná data uložená místně v uzlech clusteru. Například pokud máte data uložená přímo na hlavním uzlu.
1. [Odstraňte existující cluster](./hdinsight-delete-cluster.md).
1. Vytvořte cluster ve stejné podsíti virtuální sítě s nejnovější (nebo podporovanou) verzí HDI pomocí stejného výchozího úložiště dat, které používal předchozí cluster. To umožňuje novému clusteru pokračovat v práci s existujícími produkčními daty.
1. Importujte všechna přechodná data, která jste zálohovali.
1. Spusťte úlohy/pokračujte ve zpracování pomocí nového clusteru.

## <a name="workload-specific-guidance"></a>Pokyny specifické pro pracovní vytížení

Následující dokumenty poskytují pokyny k migraci konkrétních úloh:

* [Migrace HBase](./hbase/apache-hbase-migrate-new-version.md)
* [Migrace Kafky](./kafka/migrate-versions.md)
* [Migrace hive/interaktivního dotazu](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>Zálohování a obnovení

Další informace o zálohování a obnovení databáze najdete v [tématu Obnovení databáze Azure SQL pomocí automatického zálohování databáze](../sql-database/sql-database-recovery-using-backups.md).

## <a name="next-steps"></a>Další kroky

* [Naučte se vytvářet clustery HDInsight založené na Linuxu](hdinsight-hadoop-provision-linux-clusters.md)
* [Připojení k HDInsightu pomocí protokolu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Správa clusteru založeného na Linuxu pomocí Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Poznámky k verzi HDInsight](./hdinsight-version-release.md)
