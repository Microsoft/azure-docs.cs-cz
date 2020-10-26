---
title: Chybová zpráva nezobrazená v zobrazení Apache Hive – Azure HDInsight
description: Dotaz se nezdařil v zobrazení Apache Hive bez jakýchkoli podrobností o clusteru Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 7945410c803964e664af20fb9bc91ab82e8ddc6e
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533032"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>Scénář: chybová zpráva dotazu není zobrazená v zobrazení Apache Hive ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání interaktivních komponent dotazů v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Chybová zpráva dotazu zobrazení Apache Hive bude vypadat podobně jako v tomto případě bez dalších informací:

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>Příčina

Někdy je chybová zpráva selhání dotazu pravděpodobně příliš velká a nelze ji zobrazit na hlavní stránce zobrazení podregistru.

## <a name="resolution"></a>Řešení

Podívejte se na kartu oznámení v pravém horním rohu Hive_view a zobrazte tak kompletní trasování zásobníku a chybovou zprávu.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).