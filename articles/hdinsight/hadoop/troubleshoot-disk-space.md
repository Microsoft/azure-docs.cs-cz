---
title: Správa místa na disku v Azure HDInsight
description: Řešení potíží s kroky a možná řešení problémů při interakci s clustery Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 577bed7ce342be14a50077a3ffd841cd901b5b31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77473009"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Správa místa na disku v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="hive-log-configurations"></a>Konfigurace protokolu Hive

1. Z webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net`přejděte `CLUSTERNAME` na , kde je název clusteru.

1. Přejděte na **hive** > **configs** > **Advanced Advanced** > **hive-log4j**. Zkontrolujte následující nastavení:

    * `hive.root.logger=DEBUG,RFA`. Toto je výchozí hodnota, upravte [úroveň protokolu](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) tak, aby `INFO` tiskméně položek protokolů.

    * `log4jhive.log.maxfilesize=1024MB`. Toto je výchozí hodnota, upravte podle potřeby.

    * `log4jhive.log.maxbackupindex=10`. Toto je výchozí hodnota, upravte podle potřeby. Pokud byl parametr vynechán, generované soubory protokolu budou nekonečné.

## <a name="yarn-log-configurations"></a>Konfigurace protokolu příze

Projděte si následující konfigurace:

* Apache Ambari

    1. Z webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net`přejděte `CLUSTERNAME` na , kde je název clusteru.

    1. Přejděte na soubor **Hive** > **Configs** > **Advanced** > Resource**Manager**. Ujistěte se, že je zaškrtnuto **políčko Povolit agregaci protokolů.** Pokud je zakázáno, uzly názvů bude uchovávat protokoly místně a není jejich agregaci ve vzdáleném úložišti při dokončení nebo ukončení aplikace.

* Ujistěte se, že velikost clusteru odpovídá zatížení. Zatížení se pravděpodobně nedávno změnilo nebo byla změněna velikost clusteru. [Vertikálně navýšit](../hdinsight-scaling-best-practices.md) kapacitu clusteru tak, aby odpovídala vyšší zatížení.

* `/mnt/resource`může být vyplněno osamocenými soubory (jako v případě restartování správce prostředků). V případě potřeby `/mnt/resource/hadoop/yarn/log` ručně `/mnt/resource/hadoop/yarn/local`vyčistěte a .

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
