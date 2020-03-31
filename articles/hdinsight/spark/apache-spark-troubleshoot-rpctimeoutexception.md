---
title: RpcTimeoutException pro šetrnost Apache Spark - Azure HDInsight
description: Při zpracování velkých datových sad pomocí sešitového serveru Apache Spark se zobrazí 502 chyb
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: b15ac80295a0113eb0c384e1cc3185f3304c39c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894286"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Scénář: RpcTimeoutException pro šetrnost serveru Apache Spark v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Aplikace Spark se `org.apache.spark.rpc.RpcTimeoutException` nezdaří s `Futures timed out`výjimkou a zprávou: , jako v následujícím příkladu:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError`a `overhead limit exceeded` chyby se `sparkthriftdriver.log` mohou objevit také v následujícím příkladu:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Příčina

Tyto chyby jsou způsobeny nedostatkem paměťových prostředků během zpracování dat. Pokud se spustí proces uvolňování paměti java, může to vést k předsazení aplikace Spark. Dotazy začnou časový modus out a zastavit zpracování. Chyba `Futures timed out` označuje cluster u silného stresu.

## <a name="resolution"></a>Řešení

Zvětšete velikost clusteru přidáním dalších pracovních uzlů nebo zvýšením kapacity paměti existujících uzlů clusteru. Můžete také upravit datový kanál, abyste snížili množství zpracovávaných dat najednou.

Určuje `spark.network.timeout` časový rozsah pro všechna síťová připojení. Zvýšení časového času sítě může umožnit více času pro některé kritické operace dokončit, ale to nebude problém zcela vyřešit.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
