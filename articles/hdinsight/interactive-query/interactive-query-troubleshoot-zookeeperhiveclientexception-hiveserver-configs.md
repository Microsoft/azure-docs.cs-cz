---
title: Chyba interpretace Apache Hive Zeppelin – Azure HDInsight
description: Překladač JDBC pro podregistr Apache Zeppelin odkazuje na nesprávnou adresu URL ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 2d6e660459d9f350cc5c63fb8d312bcbcb300a64
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930751"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]