---
title: UI Apache Ambari zobrazuje hostitele a služby v Azure HDInsight
description: Řešení potíží s problémem s uzpovamem Apache Ambari, když se zobrazuje dole hostitelé a služby v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895642"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Scénář: Rozhraní Apache Ambari zobrazuje hostitele a služby v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Apache Ambari UI je přístupný, ale uI ukazuje, že téměř všechny služby jsou dole, všichni hostitelé vykazují ztracený srdeční tep.

## <a name="cause"></a>Příčina

Ve většině případů se jedná o problém se serverem Ambari, který není spuštěn na aktivním headnode. Zkontrolujte, který headnode je aktivní headnode a ujistěte se, že váš ambari-server běží na ten správný. Nespouštějte ručně ambari-server, nechte službu řadiče převzetí služeb převzetí služeb převzetí služeb při selhání zodpovídat za spuštění ambari-serveru na pravém headnode. Restartujte aktivní headnode vynutit převzetí služeb při selhání.

Tento problém může způsobit také problém se sítí. Z každého uzlu clusteru zjistěte, zda můžete ping `headnodehost`. Existuje vzácná situace, kdy se žádný `headnodehost`uzel clusteru může připojit k :

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Řešení

Obvykle restartování aktivní houmnodu tento problém zmírní. Pokud ne, obraťte se na tým podpory HDInsight.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
