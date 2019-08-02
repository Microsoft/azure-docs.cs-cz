---
title: Chyba prostoru v haldě Java při pokusu o otevření serveru historie Apache Spark ve službě Azure HDInsight
description: Server Apache Livy se nepodařilo spustit s jazykem Java. lang. OutOfMemoryError ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 5c2ae90bdca8512802c845a5ac58c3c4aeedd5b7
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667805"
---
# <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server-in-azure-hdinsight"></a>Scénář: Chyba prostoru v haldě Java při pokusu o otevření serveru historie Apache Spark ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Při otevírání událostí na serveru historie Sparku se zobrazí následující chyba:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

## <a name="cause"></a>Příčina

Tato chyba je často způsobena nedostatkem prostředků při otevírání rozsáhlých souborů Spark-Event. Velikost haldy Sparku je ve výchozím nastavení nastavená na 1 GB, ale velké soubory událostí Spark můžou vyžadovat víc než tento postup.

Pokud chcete ověřit velikost souborů, které se pokoušíte načíst, můžete provést následující příkazy:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

## <a name="resolution"></a>Řešení

Velikost paměti serveru Spark můžete zvýšit úpravou `SPARK_DAEMON_MEMORY` vlastnosti v konfiguraci Sparku a restartováním všech služeb.

To můžete provést v uživatelském rozhraní prohlížeče Ambari, a to tak, že vyberete oddíl Spark2/config/Advanced Spark2-env.

![Oddíl Advanced spark2-ENV](./media/apache-spark-ts-outofmemory-heap-space/image01.png)

Přidejte následující vlastnost pro změnu paměti serveru Spark historie z 1G na 4G: `SPARK_DAEMON_MEMORY=4g`.

![Spark – vlastnost](./media/apache-spark-ts-outofmemory-heap-space/image02.png)

Ujistěte se, že jste všechny zasažené služby restartovali z Ambari.

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
