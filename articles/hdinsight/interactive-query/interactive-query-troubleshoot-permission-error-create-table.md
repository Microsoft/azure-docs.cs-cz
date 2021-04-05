---
title: Chyba odepření oprávnění s Apache Hive tabulkou ve službě Azure HDInsight
description: Při pokusu o vytvoření tabulky Apache Hive ve službě Azure HDInsight došlo k chybě odepření oprávnění.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/09/2019
ms.openlocfilehash: 6d91ca747bb264b4c7262eec2bb0745b8a40b7a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930920"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>Scénář: chyba odepření oprávnění při pokusu o vytvoření tabulky Apache Hive ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání interaktivních komponent dotazů v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Při pokusu o vytvoření tabulky se zobrazí následující chyba:

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

Pokud spustíte následující příkaz HDFS Storage, zobrazí se podobná chybová zpráva:

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>Příčina

Možnost vytvořit tabulku v Apache Hive je určena oprávněním použitým pro účet úložiště clusteru. Pokud oprávnění účtu úložiště clusteru nejsou správná, nebudete moct vytvářet tabulky. To znamená, že můžete mít správné zásady Ranger pro vytvoření tabulky a pořád se zobrazí chyba "oprávnění odepřeno".

## <a name="resolution"></a>Řešení

Příčinou je nedostatek dostatečných oprávnění na používaném kontejneru úložiště. Uživatel vytvářející tabulku podregistru potřebuje oprávnění číst, zapisovat a spustit pro kontejner. Další informace najdete [v tématu osvědčené postupy pro autorizaci podregistru pomocí Apache Ranger v HDP 2,2](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]