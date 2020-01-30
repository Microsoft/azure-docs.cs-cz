---
title: Konflikt portů při spouštění služeb ve službě Azure HDInsight
description: Kroky řešení potíží a možná řešení pro problémy při komunikaci s clustery Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 4cb0d464a82d8da0a09f5391eb1d06dfacd84290
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776219"
---
# <a name="scenario-port-conflict-when-starting-services-in-azure-hdinsight"></a>Scénář: konflikt portů při spouštění služeb ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Spuštění služby se nezdařilo.

## <a name="cause"></a>Příčina

Existuje konflikt portů.

## <a name="resolution"></a>Rozlišení

### <a name="method-1"></a>Metoda 1

Pomocí níže uvedených příkazů můžete získat nebo ukončit všechny spuštěné procesy, které jsou ovlivněny problémem s portem.

```bash
netstat -lntp | grep <port>
ps -ef | grep <service>
kill -9 <service>
```

Pak spusťte službu.

### <a name="method-2"></a>Metoda 2

Restartujte uzel.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
