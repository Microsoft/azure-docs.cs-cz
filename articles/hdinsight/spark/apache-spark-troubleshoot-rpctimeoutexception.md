---
title: RpcTimeoutException pro Apache Spark Thrift – Azure HDInsight
description: Při zpracování velkých datových sad pomocí serveru Apache Spark Thrift se zobrazí chyby 502
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: c9e71c745d62432af3c0fe035d28009e3e5be761
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241031"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Scénář: RpcTimeoutException pro server pro Apache Spark Thrift ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Aplikace Spark se nezdařila s výjimkou `org.apache.spark.rpc.RpcTimeoutException` a zprávou: `Futures timed out`, jak je uvedeno v následujícím příkladu:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

v `sparkthriftdriver.log`, jako v následujícím příkladu, se mohou zobrazit také chyby `OutOfMemoryError` a `overhead limit exceeded`:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Příčina

Tyto chyby způsobují nedostatek paměťových prostředků během zpracování dat. Pokud se spustí proces uvolňování paměti Java, může to vést k zavěšení aplikace Spark. Dotazy začnou vyprší a zastavují se zpracování. `Futures timed out` Chyba indikuje cluster pod vážným napětím.

## <a name="resolution"></a>Rozlišení

Zvyšte velikost clusteru přidáním dalších pracovních uzlů nebo zvýšením kapacity paměti pro existující uzly clusteru. Můžete také upravit datový kanál a snížit tak množství dat zpracovávaných najednou.

`spark.network.timeout` řídí časový limit pro všechna síťová připojení. Zvýšení časového limitu sítě může trvat delší dobu, než se některé kritické operace dokončí, ale tento problém se zcela nevyřeší.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení prostředí pro zákazníky tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
