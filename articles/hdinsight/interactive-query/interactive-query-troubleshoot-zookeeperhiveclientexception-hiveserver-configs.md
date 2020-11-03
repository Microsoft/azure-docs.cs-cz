---
title: Chyba interpretace Apache Hive Zeppelin – Azure HDInsight
description: Překladač JDBC pro podregistr Apache Zeppelin odkazuje na nesprávnou adresu URL ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8e66db96f895d0d29d6c5018478e98c1f39e62ad
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288708"
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