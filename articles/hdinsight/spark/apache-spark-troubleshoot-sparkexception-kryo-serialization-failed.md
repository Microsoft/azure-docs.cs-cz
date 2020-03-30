---
title: Problémy s JDBC/ODBC & rámec Apache Thrift – Azure HDInsight
description: Nelze stahovat velké datové sady pomocí softwarového frameworku JDBC/ODBC a Apache Thrift v Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 23693dcae2f361b88440ec88ca39fd8ed229d85a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894263"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>Nelze stáhnout velké datové sady pomocí softwarového frameworku JDBC/ODBC a Apache Thrift v HDInsightu.

Tento článek popisuje kroky řešení potíží a možná řešení problémů při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Při pokusu o stažení velkých datových sad pomocí JDBC/ODBC a softwarového frameworku Apache Thrift v Azure HDInsight se zobrazí chybová zpráva podobná následujícím způsobem:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Příčina

Tato výjimka je způsobena procesem serializace, který se pokouší použít více místa ve vyrovnávací paměti, než je povoleno. V Spark 2.0.0, `org.apache.spark.serializer.KryoSerializer` třída se používá pro serializaci objektů při přístupu k datům prostřednictvím softwarového frameworku Apache Thrift. Pro data, která budou odeslána po síti nebo uložena do mezipaměti v serializované podobě, se používá jiná třída.

## <a name="resolution"></a>Řešení

Zvyšte `Kryoserializer` hodnotu vyrovnávací paměti. Přidejte klíč `spark.kryoserializer.buffer.max` s názvem `2048` a nastavte jej `Custom spark2-thrift-sparkconf`v spark2 config pod .

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
