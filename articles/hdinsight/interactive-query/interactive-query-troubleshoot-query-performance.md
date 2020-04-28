---
title: Nízký výkon Apache Hive dotazy LLAP ve službě Azure HDInsight
description: Dotazy v Apache Hive LLAP ve službě Azure HDInsight běží pomaleji, než se očekává.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8bd20849b15f8c8d5a14653f702f78c6404d82e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895125"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Scénář: nízký výkon Apache Hive dotazy LLAP ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání interaktivních komponent dotazů v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Výchozí konfigurace clusteru nejsou dostatečně vyladěny pro vaše zatížení. Dotazy v LLAP podregistru jsou spouštěny pomaleji, než se čekalo.

## <a name="cause"></a>Příčina

K tomu může dojít z nejrůznějších důvodů.

## <a name="resolution"></a>Řešení

LLAP je optimalizovaná pro dotazy, které zahrnují spojení a agregace. Dotazy, jako jsou následující, se v interaktivním clusteru podregistru neprovádějí správně:

```
select * from table where column = "columnvalue"
```

Chcete-li zlepšit výkon dotazů na body v LLAPu podregistru, nastavte následující konfigurace:

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

Můžete také zvýšit využití mezipaměti LLAP, aby se zvýšil výkon s následující změnou konfigurace:

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
