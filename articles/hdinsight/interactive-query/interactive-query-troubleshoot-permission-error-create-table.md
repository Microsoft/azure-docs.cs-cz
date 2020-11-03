---
title: Chyba odepření oprávnění s Apache Hive tabulkou ve službě Azure HDInsight
description: Při pokusu o vytvoření tabulky Apache Hive ve službě Azure HDInsight došlo k chybě odepření oprávnění.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: b6e71f5f2c389926a2e6267fadeedebdce1c51d9
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288900"
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