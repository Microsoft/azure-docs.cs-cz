---
title: Uzlu clusteru dochází místo na disku v Azure HDInsight
description: Řešení problémů s problémy s uzlem uzlu clusteru Apache Hadoop na disku v Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: fbfd82473b68f5032d19834ac809191d498a5a67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894125"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Scénář: Uzlu clusteru dochází místo na disku v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Úloha může selhat s chybovou zprávou podobnou:`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Nebo můžete obdržet Apache Ambari `local-dirs usable space is below configured utilization percentage`upozornění podobné: .

## <a name="cause"></a>Příčina

Mezipaměť aplikací Apache Yarn mohla spotřebovat veškeré dostupné místo na disku. Vaše aplikace Spark pravděpodobně běží neefektivně.

## <a name="resolution"></a>Řešení

1. Pomocí uzlového řízení Ambari určete, kterému uzlu dochází místo na disku.

1. Určete, která složka v obtěžující uzel přispívá k většině místa na disku. SSH do uzlu první, `df` pak spustit seznam využití disku pro všechny připojení. Obvykle je `/mnt` to, což je dočasný disk používaný OSS. Můžete zadat do složky `sudo du -hs` a potom zadat a zobrazit souhrnné velikosti souborů pod složkou. Pokud se zobrazí složka `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`podobná aplikaci , znamená to, že aplikace je stále spuštěna. To může být způsobeno trvalost RDD nebo zprostředkující shuffle soubory.

1. Chcete-li tento problém zmírnit, usmrknete aplikaci, která uvolní místo na disku, které tato aplikace využívá.

1. Chcete-li nakonec problém vyřešit, optimalizujte aplikaci.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
