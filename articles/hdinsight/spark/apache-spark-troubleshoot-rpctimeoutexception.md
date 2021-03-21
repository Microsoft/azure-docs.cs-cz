---
title: RpcTimeoutException pro Apache Spark Thrift – Azure HDInsight
description: Při zpracování velkých datových sad pomocí serveru Apache Spark Thrift se zobrazí chyby 502
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: c2975599272474fed9d61702fc1dbceb946c1190
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932705"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Scénář: RpcTimeoutException pro server pro Apache Spark Thrift ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Aplikace Spark se nezdařila s `org.apache.spark.rpc.RpcTimeoutException` výjimkou a zprávou: `Futures timed out` , jako v následujícím příkladu:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError` a `overhead limit exceeded` chyby mohou být také zobrazeny v `sparkthriftdriver.log` následujícím příkladu:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Příčina

Tyto chyby způsobují nedostatek paměťových prostředků během zpracování dat. Pokud se spustí proces uvolňování paměti Java, může to vést k tomu, že aplikace Spark přestane reagovat. Dotazy začnou vyprší a zastavují se zpracování. `Futures timed out`Chyba indikuje cluster s vážným napětím.

## <a name="resolution"></a>Řešení

Zvyšte velikost clusteru přidáním dalších pracovních uzlů nebo zvýšením kapacity paměti pro existující uzly clusteru. Můžete také upravit datový kanál a snížit tak množství dat zpracovávaných najednou.

`spark.network.timeout`Řídí časový limit pro všechna síťová připojení. Zvýšení časového limitu sítě může trvat delší dobu, než se některé kritické operace dokončí, ale tento problém se zcela nevyřeší.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]