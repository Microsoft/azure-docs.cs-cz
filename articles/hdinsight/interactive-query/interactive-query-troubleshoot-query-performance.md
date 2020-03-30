---
title: Nízký výkon v dotazech Apache Hive LLAP v Azure HDInsight
description: Dotazy v Apache Hive LLAP se spouštějí pomaleji, než se očekávalo v Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8bd20849b15f8c8d5a14653f702f78c6404d82e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895125"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Scénář: Nízký výkon v dotazech Apache Hive LLAP v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při použití komponent interaktivního dotazu v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Výchozí konfigurace clusteru nejsou dostatečně vyladěné pro vaše úlohy. Dotazy v Hive LLAP jsou prováděny pomaleji, než bylo očekáváno.

## <a name="cause"></a>Příčina

K tomu může dojít z různých důvodů.

## <a name="resolution"></a>Řešení

LLAP je optimalizován pro dotazy, které zahrnují spojení a agregace. Dotazy, jako je následující, nefungují v clusteru Interaktivní hive dobře:

```
select * from table where column = "columnvalue"
```

Chcete-li zlepšit výkon bodového dotazu v hive LLAP, nastavte následující konfigurace:

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

Můžete také zvýšit využití mezipaměti LLAP ke zlepšení výkonu s následující změnou konfigurace:

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
