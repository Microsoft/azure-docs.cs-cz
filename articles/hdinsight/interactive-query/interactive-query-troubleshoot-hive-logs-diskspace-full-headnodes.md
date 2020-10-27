---
title: Protokoly Apache Hive zaplňujících místo na disku – Azure HDInsight
description: Protokoly Apache Hive zaplňují místo na disku v hlavních uzlech ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 10/05/2020
ms.openlocfilehash: 5554a66927fc70f22ec552b938ae62038a04acb9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533015"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Scénář: protokoly Apache Hive zaplňují místo na disku v hlavních uzlech ve službě Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení pro problémy související s nedostatkem místa na disku v hlavních uzlech v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

V clusteru Apache Hive/LLAP zabírají nechtěné protokoly v hlavních uzlech celé místo na disku. V důsledku toho mohou být zjištěny následující problémy.

1. Přístup SSH se nezdařil z důvodu nedostatku místa na hlavním uzlu.
2. Ambari poskytuje *chybu protokolu http: služba 503 není k dispozici* .
3. HiveServer2 Interactive se nerestartuje.

V `ambari-agent` protokolech se při potížích zobrazí následující.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Příčina

V pokročilých konfiguracích log4j podregistru je aktuální výchozí plán odstranění nastavený na soubory starší než 30 dní na základě data poslední úpravy.

## <a name="resolution"></a>Řešení

1. Přejděte na souhrn komponenty podregistru na portálu Ambari a klikněte na `Configs` kartu.

2. Přejít na `Advanced hive-log4j` část v části Upřesnit nastavení.

3. Nastavte `appender.RFA.strategy.action.condition.age` parametr na stáří podle vašeho výběru. Příklad 14 dnů: `appender.RFA.strategy.action.condition.age = 14D`

4. Pokud nevidíte žádná související nastavení, přidejte prosím následující nastavení.
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. Nastavte `hive.root.logger` následujícím `INFO,RFA` způsobem. Výchozím nastavením je ladění, které způsobí, že se protokoly budou velmi velké.

    ```
    # Define some default values that can be overridden by system properties
    hive.log.threshold=ALL
    hive.root.logger=INFO,RFA
    hive.log.dir=${java.io.tmpdir}/${user.name}
    hive.log.file=hive.log
    ```

6. Uložte konfigurace a restartujte požadované součásti.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).