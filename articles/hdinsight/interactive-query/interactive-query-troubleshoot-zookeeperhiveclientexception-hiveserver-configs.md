---
title: Chyba interpretace Apache Hive Zeppelin – Azure HDInsight
description: Překladač JDBC pro podregistr Apache Zeppelin odkazuje na nesprávnou adresu URL ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: a5dd7d9f292036af00140d89347678e5e548b8d9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534630"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Scénář: Apache Hive překladač Zeppelin poskytuje chybu Zookeeper ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání interaktivních komponent dotazů v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

V clusteru Apache Hive LLAP vyvolá překladač Zeppelin při pokusu o spuštění dotazu následující chybovou zprávu:

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Příčina

Překladač JDBC podregistru Zeppelin odkazuje na nesprávnou adresu URL.

## <a name="resolution"></a>Řešení

1. Přejděte do části Souhrn komponenty podregistr a zkopírujte do schránky "JDBC URL registru".

1. Přejděte na adresu `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`.

1. Upravte nastavení JDBC: Aktualizujte hodnotu podregistru. URL na JDBC adresu URL registru zkopírovanou v kroku 1.

1. Uložte a pak dotaz opakujte.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).