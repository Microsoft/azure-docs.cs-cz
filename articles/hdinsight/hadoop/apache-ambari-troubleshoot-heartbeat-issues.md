---
title: Problémy s prezenčním signálem Apache Ambari ve službě Azure HDInsight
description: Přezkoumání různých důvodů problémů s prezenčním signálem Apache Ambari v Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 8d23b89ab155c47e09f82d22c065db47ab9ac73d
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540784"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problémy s prezenčním signálem Apache Ambari ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="scenario-high-cpu-utilization"></a>Scénář: vysoké využití procesoru

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

Agent Ambari se nespustil, což má za následek výstrahy z uživatelského rozhraní Ambari, které u některých uzlů ztratily prezenční signály agenta Ambari.

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

    Pokud služby řadiče pro převzetí služeb při selhání neběží, pravděpodobně z důvodu problému brání agentovi HDInsight v spuštění kontroleru převzetí služeb při selhání. Zkontroluje protokol HDInsight-Agent ze `/var/log/hdinsight-agent/hdinsight-agent.out` souboru.

## <a name="scenario-heartbeat-lost-for-ambari"></a>Scénář: prezenční signál se ztratil pro Ambari.

### <a name="issue"></a>Problém

Agent prezenčního signálu Ambari byl ztracen.

### <a name="cause"></a>Příčina

Protokoly OMS způsobují vysoké využití procesoru.

### <a name="resolution"></a>Řešení

* Zakažte protokolování Azure Monitor pomocí rutiny PowerShellu [Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) .
* Odstranění `mdsd.warn` souboru protokolu

---

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).