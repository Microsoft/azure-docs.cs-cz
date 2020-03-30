---
title: Chyba interpreta Apache Hive Zeppelin – Azure HDInsight
description: Překladač JDBC Apache Zeppelin Hive ukazuje na nesprávnou adresu URL v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 20309babb9ece0ae20e7442543b0d378f9a51060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895061"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Scénář: Apache Hive Zeppelin Interpret udá zookeeper chybu v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při použití komponent interaktivního dotazu v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

V clusteru Apache Hive LLAP interpret Zeppelin zobrazí při pokusu o spuštění dotazu následující chybovou zprávu:

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Příčina

Zeppelin Hive JDBC Interpret ukazuje na nesprávnou adresu URL.

## <a name="resolution"></a>Řešení

1. Přejděte do souhrnu komponenty Hive a zkopírujte adresu URL Hive JDBC do schránky.

1. Přejít na`https://clustername.azurehdinsight.net/zeppelin/#/interpreter`

1. Úprava nastavení JDBC: aktualizace hodnoty hive.url na adresu URL Hive JDBC zkopírovanou v kroku 1

1. Uložit a potom opakovat dotaz

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
