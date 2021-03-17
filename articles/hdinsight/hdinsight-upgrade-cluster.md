---
title: Migrovat cluster na novější verzi
titleSuffix: Azure HDInsight
description: Přečtěte si pokyny pro migraci clusteru Azure HDInsight na novější verzi.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: 04da5d668515fe96d50d4e6a7d0f5ff1c4c48c27
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931383"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>Migrace clusteru HDInsight na novější verzi

Pro využití nejnovějších funkcí HDInsight doporučujeme, aby clustery HDInsight byly pravidelně migrovány na nejnovější verzi. HDInsight nepodporuje místní upgrady, když je existující cluster upgradovaný na novější verzi součásti. Musíte vytvořit nový cluster s požadovanou verzí součásti a platformy a potom migrovat aplikace tak, aby používaly nový cluster. Pokud chcete migrovat verze clusteru HDInsight, postupujte podle pokynů níže.

> [!NOTE]  
> Informace o podporovaných verzích HDInsight najdete v tématu [verze komponent HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="migration-tasks"></a>Úlohy migrace

Postup upgradu clusteru HDInsight je následující.
![Diagram pracovního postupu upgradu HDInsight](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Přečtěte si jednotlivé části tohoto dokumentu, abyste pochopili změny, které mohou být požadovány při upgradu clusteru HDInsight.
2. Vytvořte cluster jako prostředí pro testování/zajištění kvality. Další informace o vytvoření clusteru najdete v tématu [informace o vytváření clusterů HDInsight se systémem Linux](hdinsight-hadoop-provision-linux-clusters.md) .
3. Zkopírujte existující úlohy, zdroje dat a jímky do nového prostředí.
4. Proveďte ověřovací testování a ujistěte se, že vaše úlohy fungují podle očekávání na novém clusteru.

Jakmile ověříte, že vše funguje podle očekávání, naplánujte prostoje při migraci. Během tohoto výpadku proveďte následující akce:

1. Zálohujte všechna přechodná data uložená místně na uzlech clusteru. Například pokud máte data uložená přímo na hlavní uzel.
1. [Odstraňte existující cluster](./hdinsight-delete-cluster.md).
1. Vytvořte cluster ve stejné podsíti virtuální sítě s nejnovější (nebo podporovanou) verzí HDI pomocí stejného výchozího úložiště dat, které použil předchozí cluster. Tím umožníte, aby nový cluster pokračoval v práci s vašimi stávajícími provozními daty.
1. Importujte všechna přechodná data, která jste zálohovali.
1. Spustí úlohy/pokračovat ve zpracování pomocí nového clusteru.

## <a name="workload-specific-guidance"></a>Pokyny pro konkrétní úlohy

Následující dokumenty obsahují pokyny k migraci konkrétních úloh:

* [Migrace adaptérů HBA](./hbase/apache-hbase-migrate-new-version.md)
* [Migrace Kafka](./kafka/migrate-versions.md)
* [Migrace podregistru/interaktivního dotazu](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>Zálohování a obnovení

Další informace o zálohování a obnovení databáze najdete v tématu [obnovení databáze v Azure SQL Database pomocí automatických záloh databáze](../azure-sql/database/recovery-using-backups.md).

## <a name="next-steps"></a>Další kroky

* [Naučte se vytvářet clustery HDInsight se systémem Linux.](hdinsight-hadoop-provision-linux-clusters.md)
* [Připojení k HDInsightu pomocí protokolu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Správa clusteru se systémem Linux pomocí Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Poznámky k verzi HDInsight](./hdinsight-version-release.md)
