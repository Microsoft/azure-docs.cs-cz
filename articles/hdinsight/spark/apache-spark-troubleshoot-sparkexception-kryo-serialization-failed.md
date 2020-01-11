---
title: Problémy s JDBC/& ODBC – rozhraní Apache Thrift Framework – Azure HDInsight
description: Nepovedlo se stáhnout velké datové sady pomocí rozhraní JDBC/ODBC a Apache Thrift software Framework ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 23693dcae2f361b88440ec88ca39fd8ed229d85a
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894263"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>Nepovedlo se stáhnout velké datové sady pomocí rozhraní JDBC/ODBC a Apache Thrift software Framework v HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Při pokusu o stažení velkých datových sad pomocí JDBC/ODBC a rozhraní Apache Thrift software v Azure HDInsight se zobrazí chybová zpráva podobná následující:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Příčina

Tato výjimka je způsobená procesem serializace, který se pokouší použít více prostoru vyrovnávací paměti, než je povoleno. V Spark 2.0.0 třída `org.apache.spark.serializer.KryoSerializer` slouží k serializaci objektů, pokud jsou k datům přistupovaly prostřednictvím softwarového rozhraní Apache Thrift. Pro data, která budou odesílána přes síť nebo ukládána do mezipaměti v serializovaném formátu, se používá jiná třída.

## <a name="resolution"></a>Rozlišení

Zvyšte hodnotu `Kryoserializer` vyrovnávací paměti. Přidejte klíč s názvem `spark.kryoserializer.buffer.max` a nastavte ho tak, aby `2048` v spark2 config pod `Custom spark2-thrift-sparkconf`.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení prostředí pro zákazníky tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
