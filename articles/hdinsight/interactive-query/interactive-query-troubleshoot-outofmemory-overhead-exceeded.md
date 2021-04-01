---
title: Spojení v Apache Hive vede k OutOfMemory chybě – Azure HDInsight
description: Při obchodování s chybami OutOfMemory "překročení limitu režie na uvolňování paměti"
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 0c396cde38d8cba8e1f3eaf8527429647868a0c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98935949"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Scénář: spojení v Apache Hive vede k chybě OutOfMemory ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání interaktivních komponent dotazů v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Výchozím chováním pro Apache Hive JOIN je načtení celého obsahu tabulky do paměti, aby bylo možné provést spojení, aniž by bylo nutné provést mapový krok. Pokud je tabulka podregistru příliš velká, aby se vešla do paměti, dotaz může selhat.

## <a name="cause"></a>Příčina

Při spuštění spojení v podregistru dostatečné velikosti dojde k následující chybě:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Řešení

Zabránit v načítání tabulek do paměti ve spojeních (místo toho, aby se prováděla mapa/snížení kroku) nastavením následující hodnoty konfigurace podregistru:

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>Další kroky

Pokud nastavení této hodnoty nevyřešilo váš problém, navštivte jednu z následujících možností...

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).