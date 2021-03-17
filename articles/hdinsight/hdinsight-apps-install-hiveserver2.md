---
title: Škálování HiveServer2 ve službě Azure HDInsight
description: Horizontální škálování HiveServer2 v clusterech Azure HDInsight pomocí hraničních uzlů ke zvýšení odolnosti proti chybám a dostupnosti.
services: hdinsight
ms.service: hdinsight
ms.topic: conceptual
ms.reviewer: hrasheed-msft
ms.author: kecheung
author: kcheeeung
ms.date: 08/12/2020
ms.openlocfilehash: d0a0df4791492c1c9f0d600630d723024c46c1b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88227191"
---
# <a name="scale-hiveserver2-on-azure-hdinsight-clusters-for-high-availability"></a>Škálování HiveServer2 v clusterech Azure HDInsight pro zajištění vysoké dostupnosti

Naučte se, jak do svého clusteru nasadit další HiveServer2, aby se zvýšila dostupnost a distribuce zatížení. Když se zvětší velikost hlavnímu uzlu, můžete k nasazení HiveServer2 použít taky hraniční uzly. 

> [!NOTE]
> V závislosti na využití může zvýšení počtu HiveServer2 zvýšit počet připojení k metastore Hive. Také se ujistěte, že vaše databáze SQL Azure má správnou velikost.

## <a name="prerequisites"></a>Požadavky

Pro použití tohoto průvodce budete muset porozumět následujícímu článku:
- [Použití prázdných hraničních uzlů na Apache Hadoop clusterech v HDInsight](hdinsight-apps-use-edge-node.md)

## <a name="install-hiveserver2"></a>Nainstalovat HiveServer2

V této části nainstalujete další HiveServer2 do cílových hostitelů.

1. V prohlížeči otevřete Ambari a klikněte na cílového hostitele.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-a.png" alt-text="Nabídka hostitelů v Ambari.":::

2. Klikněte na tlačítko Přidat a potom klikněte na HiveServer2.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-b.png" alt-text="Nabídka hostitelů v Ambari.":::

3. Potvrďte a proces se spustí. Opakujte 1-3 pro všechny požadované hostitele.

4. Po dokončení instalace restartujte všechny služby se zastaralými konfiguracemi a spusťte HiveServer2.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-c.png" alt-text="Nabídka hostitelů v Ambari.":::

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili, jak nainstalovat HiveServer2 do clusteru. Další informace o hraničních uzlech a aplikacích najdete v následujících článcích:

* [Instalace hraničního uzlu](hdinsight-apps-use-edge-node.md): Naučte se instalovat hraniční uzel do clusteru HDInsight.
* [Instalace aplikací HDInsight](hdinsight-apps-install-applications.md): Naučte se instalovat aplikace HDInsight do svých clusterů.
* [Omezení připojení DTU Azure SQL](../azure-sql/database/resource-limits-dtu-single-databases.md): Přečtěte si o omezeních Azure SQL Database pomocí DTU.
* [Omezení připojení Azure SQL Vcore](../azure-sql/database/resource-limits-vcore-elastic-pools.md): Přečtěte si o omezeních Azure SQL Database pomocí virtuální jádra.
