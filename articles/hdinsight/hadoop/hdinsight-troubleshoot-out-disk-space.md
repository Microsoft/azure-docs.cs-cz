---
title: Uzel clusteru nemá volné místo na disku v Azure HDInsight.
description: Řešení potíží s Apache Hadoop problémy s místem na disku uzlu clusteru ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/05/2019
ms.openlocfilehash: 139fb0e77f8f6960e7b13899f9586dd78bf46a92
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735851"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Scénář: Uzel clusteru nemá volné místo na disku v Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Úloha může selhat s chybovou zprávou podobnou následující:`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Nebo se může zobrazit výstraha Apache Ambari podobná `local-dirs usable space is below configured utilization percentage`této:.

## <a name="cause"></a>Příčina

Mezipaměť aplikace Apache nitě mohla spotřebovat veškeré dostupné místo na disku. Aplikace Spark je nejspíš neefektivně spuštěna.

## <a name="resolution"></a>Řešení

1. Pomocí uživatelského rozhraní Ambari určete, který uzel má nedostatek místa na disku.

1. Určete, která složka v uzlu problematické přispívá k většině místa na disku. Nejprve na uzel SSH a potom spusťte příkaz `df` pro vypsání využití disku pro všechna připojení. Obvykle se `/mnt` jedná o dočasný disk používaný službou OSS. Můžete zadat do složky a potom `sudo du -hs` do složky zadat zobrazení souhrnných velikostí souborů. Pokud se zobrazí složka podobná `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`, znamená to, že je aplikace stále spuštěná. To může být způsobeno RDD persistencí nebo mezilehlého náhodného souboru.

1. Chcete-li tento problém zmírnit, ukončete aplikaci, která bude vydávat místo na disku využité touto aplikací.

1. Chcete-li tento problém vyřešit, optimalizujte svou aplikaci.

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
