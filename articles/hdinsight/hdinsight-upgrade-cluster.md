---
title: Upgrade clusteru HDInsight na novější verzi – Azure
description: Přečtěte si pokyny, jak upgradovat cluster Azure HDInsight na novější verzi.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 808e592a01bca2066b7d3d37a5fdfa697d739d4b
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122451"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>Upgrade clusteru HDInsight na novější verzi

Pro využití nejnovějších funkcí HDInsight doporučujeme, aby clustery HDInsight byly upgradovány na nejnovější verzi. Pokud chcete upgradovat verze clusteru HDInsight, postupujte podle pokynů níže.

> [!NOTE]  
> Informace o podporovaných verzích HDInsight najdete v tématu [verze komponent HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="upgrade-tasks"></a>Úkoly upgradu

Postup upgradu clusteru HDInsight je následující.
![Diagram pracovního postupu upgradu HDInsight](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Přečtěte si jednotlivé části tohoto dokumentu, abyste pochopili změny, které mohou být požadovány při upgradu clusteru HDInsight.
2. Vytvořte cluster jako prostředí pro testování/zajištění kvality. Další informace o vytvoření clusteru najdete v tématu [informace o vytváření clusterů HDInsight se systémem Linux](hdinsight-hadoop-provision-linux-clusters.md) .
3. Zkopírujte existující úlohy, zdroje dat a jímky do nového prostředí.
4. Proveďte ověřovací testování a ujistěte se, že vaše úlohy fungují podle očekávání na novém clusteru.

Jakmile ověříte, že vše funguje podle očekávání, naplánujte prostoje při migraci. Během tohoto výpadku proveďte následující akce:

1.  Zálohujte všechna přechodná data uložená místně na uzlech clusteru. Například pokud máte data uložená přímo na hlavní uzel.
2.  Odstraňte existující cluster.
3.  Vytvořte cluster ve stejné podsíti virtuální sítě s nejnovější (nebo podporovanou) verzí HDI pomocí stejného výchozího úložiště dat, které použil předchozí cluster. Tím umožníte, aby nový cluster pokračoval v práci s vašimi stávajícími provozními daty.
4.  Importujte všechna přechodná data, která jste zálohovali.
5.  Spustí úlohy/pokračovat ve zpracování pomocí nového clusteru.

## <a name="next-steps"></a>Další kroky

* [Naučte se vytvářet clustery HDInsight se systémem Linux.](hdinsight-hadoop-provision-linux-clusters.md)
* [Připojení k HDInsightu pomocí protokolu SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Správa clusteru se systémem Linux pomocí Apache Ambari](hdinsight-hadoop-manage-ambari.md)
