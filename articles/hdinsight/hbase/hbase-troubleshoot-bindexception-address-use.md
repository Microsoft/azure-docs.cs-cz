---
title: BindException – adresa se už používá ve službě Azure HDInsight.
description: BindException – adresa se už používá ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/08/2019
ms.openlocfilehash: 8851a4dfb7deafab7ad77ef80619dd49ca46ed71
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947848"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Scénář: BindException – adresa se už používá ve službě Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Operaci restartování na serveru oblasti Apache Hbas se nepodařilo dokončit. V adresáři `/var/log/hbase` v pracovních uzlech, kde se nepovede Server oblasti Start, se může zobrazit chybová zpráva podobná následující: `region-server.log`

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Příčina

Restartování serverů oblastí HBA během náročné aktivity úlohy. Níže najdete informace o tom, co se stane na pozadí, když uživatel zahájí operaci restartování na adaptérech HBA serveru oblasti z uživatelského rozhraní Ambari:

1. Agent Ambari odešle požadavek na zastavení na server oblasti.

1. Agent Ambari počká, až 30 sekund, aby server oblasti mohl řádně vypnout.

1. Pokud se aplikace i nadále připojuje k serveru oblastí, nebude okamžitě ukončena a platnost časového limitu 30 sekund vyprší dřív.

1. Po vypršení platnosti 30 sekund agent Ambari odešle vynucené dezaktivační (kill-9) na server oblasti.

1. Z důvodu tohoto náhlého vypnutí se i když proces serveru oblasti ukončí, port přidružený k procesu se nemusí uvolnit, což nakonec vede k `AddressBindException`tomu.

## <a name="resolution"></a>Řešení

Před zahájením restartování snižte zatížení serverů oblastí HBA.

Případně můžete zkusit ručně restartovat servery oblastí na pracovních uzlech pomocí následujících příkazů:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
