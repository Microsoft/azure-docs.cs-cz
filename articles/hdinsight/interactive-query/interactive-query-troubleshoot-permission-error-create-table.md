---
title: Chyba odepření oprávnění s Apache Hive tabulkou ve službě Azure HDInsight
description: Při pokusu o vytvoření tabulky Apache Hive ve službě Azure HDInsight došlo k chybě odepření oprávnění.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8ebad9300c126193ddb96d5f07057b9a825dbfcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75895146"
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

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
