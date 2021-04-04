---
title: 'Řešení potíží: protokoly Apache Hive zaplní místo na disku – Azure HDInsight'
description: Tento článek popisuje postup řešení potíží, když Apache Hive protokoly doplňují místo na disku v hlavních uzlech ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.date: 10/05/2020
ms.openlocfilehash: cd7e6a7f13f6cccb5be5d23d69c2a44fc655cf55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98930955"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Scénář: protokoly Apache Hive zaplňují místo na disku v hlavních uzlech ve službě Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení pro problémy související s nedostatkem místa na disku v hlavních uzlech v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

V clusteru Apache Hive/LLAP zabírají nechtěné protokoly v hlavních uzlech celé místo na disku. Tento stav může způsobit následující problémy:

- Přístup SSH se nezdařil, protože na hlavním uzlu není žádné místo.
- Ambari vyvolá *chybu protokolu http: služba 503 není k dispozici*.
- HiveServer2 Interactive se nerestartuje.

`ambari-agent`Protokoly budou zahrnovat následující položky, když dojde k problému:
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Příčina

V části Pokročilá konfigurace log4j podregistru je aktuálním výchozím plánem odstranění odstranění souborů, které jsou starší než 30 dní, na základě data poslední úpravy.

## <a name="resolution"></a>Řešení

1. Na portálu Ambari přejít na souhrn komponenty podregistr a vyberte kartu **Konfigurace** .

2. Přejít na `Advanced hive-log4j` oddíl v části **Upřesnit nastavení**.

3. Nastavte `appender.RFA.strategy.action.condition.age` parametr na stáří podle vašeho výběru. V tomto příkladu se nastaví stáří na 14 dnů: `appender.RFA.strategy.action.condition.age = 14D`

4. Pokud nevidíte žádná související nastavení, přidejte tato nastavení:
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. Nastavte `hive.root.logger` na `INFO,RFA` , jak je znázorněno v následujícím příkladu. Výchozí nastavení je `DEBUG` , což znamená, že protokoly budou velké.

    ```
    # Define some default values that can be overridden by system properties
    hive.log.threshold=ALL
    hive.root.logger=INFO,RFA
    hive.log.dir=${java.io.tmpdir}/${user.name}
    hive.log.file=hive.log
    ```

6. Uložte konfigurace a restartujte požadované součásti.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
