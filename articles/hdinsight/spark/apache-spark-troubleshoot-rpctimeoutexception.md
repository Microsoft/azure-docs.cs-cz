---
title: RpcTimeoutException pro Apache Spark Thrift Server ve službě Azure HDInsight
description: Při zpracování velkých datových sad pomocí serveru Apache Spark Thrift se zobrazí chyby 502
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 0d4f2f9c8392e5b81f5abb8c67548baefae611c9
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620828"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Scénář: RpcTimeoutException pro Apache Spark Thrift Server ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Aplikace Spark se nezdařila s `org.apache.spark.rpc.RpcTimeoutException` výjimkou a zprávou: `Futures timed out`, jako v následujícím příkladu:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError`a `overhead limit exceeded` chyby mohou být také zobrazeny `sparkthriftdriver.log` v následujícím příkladu:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Příčina

Tyto chyby způsobují nedostatek paměťových prostředků během zpracování dat. Pokud se spustí proces uvolňování paměti Java, může to vést k zavěšení aplikace Spark. Dotazy začnou vyprší a zastavují se zpracování. `Futures timed out` Chyba indikuje cluster s vážným napětím.

## <a name="resolution"></a>Řešení

Zvyšte velikost clusteru přidáním dalších pracovních uzlů nebo zvýšením kapacity paměti pro existující uzly clusteru. Můžete také upravit datový kanál a snížit tak množství dat zpracovávaných najednou.

`spark.network.timeout` Řídí časový limit pro všechna síťová připojení. Zvýšení časového limitu sítě může trvat delší dobu, než se některé kritické operace dokončí, ale tento problém se zcela nevyřeší.

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
