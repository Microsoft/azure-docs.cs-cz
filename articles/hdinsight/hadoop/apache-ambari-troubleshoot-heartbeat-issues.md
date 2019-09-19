---
title: Problémy s prezenčním signálem Apache Ambari v Azure HDInsight
description: Přezkoumání různých důvodů problémů s prezenčním signálem Apache Ambari v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 09/11/2019
ms.openlocfilehash: 9f7d3fb5363a5eb2d79287bc9a9398bfdcbc4aec
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087829"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problémy s prezenčním signálem Apache Ambari v Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="scenario-high-cpu-utilization"></a>Scénář: Vysoké využití procesoru

### <a name="issue"></a>Problém

Ambari agent má vysoké využití procesoru, což má za následek výstrahy z uživatelského rozhraní Ambari, které u některých uzlů dojde ke ztrátě prezenčního signálu agenta Ambari. Výstraha ztráty prezenčního signálu je obvykle přechodný. 

### <a name="cause"></a>Příčina

V důsledku různých chyb Ambari-agentů může být ve výjimečných případech vysoká úroveň využití procesoru Ambari agentem (téměř až 100).

### <a name="resolution"></a>Řešení

1. Identifikujte ID procesu (PID) Ambari-agent:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Pak spuštěním následujícího příkazu zobrazte využití procesoru:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Restartujte Ambari – Agent pro zmírnění potíží:

    ```bash
    service ambari-agent restart
    ```

1. Pokud restartování nefunguje, ukončete proces Ambari a potom ho spusťte:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Scénář: Agent Ambari se nespustil.

### <a name="issue"></a>Problém

Agent Ambari se nespustil, což vede k tomu, že se výstrahy z uživatelského rozhraní Ambari, které u některých uzlů ztratily prezenční signály agenta Ambari.

### <a name="cause"></a>Příčina

Výstrahy jsou způsobeny tím, že agent Ambari neběží.

### <a name="resolution"></a>Řešení

1. Potvrďte stav Ambari-agent:

    ```bash
    service ambari-agent status
    ```

1. Potvrďte, jestli jsou spuštěné služby řadiče pro převzetí služeb při selhání:

    ```bash
    ps -ef | grep failover
    ```

    Pokud služby řadiče pro převzetí služeb při selhání neběží, pravděpodobně z důvodu problému brání agentovi HDInsight v spuštění řadiče pro převzetí služeb při selhání. Zkontroluje protokol HDInsight-Agent ze `/var/log/hdinsight-agent/hdinsight-agent.out` souboru.

---

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
