---
title: Uzel clusteru nemá volné místo na disku v Azure HDInsight.
description: Řešení potíží s Apache Hadoop problémy s místem na disku uzlu clusteru ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: fbfd82473b68f5032d19834ac809191d498a5a67
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894125"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Scénář: uzel clusteru má nedostatek místa na disku ve službě Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Úloha může selhat s chybovou zprávou podobnou: `/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Nebo se může zobrazit výstraha Apache Ambari podobná této: `local-dirs usable space is below configured utilization percentage`.

## <a name="cause"></a>Příčina

Mezipaměť aplikace Apache nitě mohla spotřebovat veškeré dostupné místo na disku. Aplikace Spark je nejspíš neefektivně spuštěna.

## <a name="resolution"></a>Rozlišení

1. Pomocí uživatelského rozhraní Ambari určete, který uzel má nedostatek místa na disku.

1. Určete, která složka v uzlu problematické přispívá k většině místa na disku. Nejprve na uzel SSH a potom spusťte `df` k vypsání využití disku pro všechna připojení. Obvykle je `/mnt` což je dočasný disk používaný službou OSS. Můžete zadat do složky a potom zadat `sudo du -hs` a zobrazit tak souhrnné velikosti souborů ve složce. Pokud se zobrazí složka podobná `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`, znamená to, že je aplikace stále spuštěná. To může být způsobeno RDD persistencí nebo mezilehlého náhodného souboru.

1. Chcete-li tento problém zmírnit, ukončete aplikaci, která bude vydávat místo na disku využité touto aplikací.

1. Chcete-li tento problém vyřešit, optimalizujte svou aplikaci.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení prostředí pro zákazníky tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
