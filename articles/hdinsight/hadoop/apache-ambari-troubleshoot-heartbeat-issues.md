---
title: Problémy s prezenčním signálem Apache Ambari ve službě Azure HDInsight
description: Kontrola různých důvodů problémů se srdečním tepem Apache Ambari v Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: ab88f65d535be2aef5f0b26fa1171c03276466e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057069"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problémy s prezenčním signálem Apache Ambari ve službě Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="scenario-high-cpu-utilization"></a>Scénář: Vysoké využití procesoru

### <a name="issue"></a>Problém

Agent Ambari má vysoké využití procesoru, což má za následek výstrahy z ui Ambari, že pro některé uzly agent Ambari prezenční signál je ztracen. Upozornění na ztrátu srdečního tepu je obvykle přechodné.

### <a name="cause"></a>Příčina

Vzhledem k různým chybám ambari-agent, ve vzácných případech, váš ambari-agent může mít vysoké (téměř 100) procento využití procesoru.

### <a name="resolution"></a>Řešení

1. Identifikujte ID procesu (pid) ambari-agenta:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Pak spuštěním následujícího příkazu zobrazte využití procesoru:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Restartujte ambari-agent, abyste zmírnili problém:

    ```bash
    service ambari-agent restart
    ```

1. Pokud restartování nefunguje, zabít ambari-agent proces a pak jej spustit:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Scénář: Agent Ambari nebyl spuštěn.

### <a name="issue"></a>Problém

Agent Ambari nebyl spuštěn, což má za následek výstrahy z ui Ambari, že pro některé uzly agent Ambari prezenční signál je ztracen.

### <a name="cause"></a>Příčina

Výstrahy jsou způsobeny agenta Ambari není spuštěn.

### <a name="resolution"></a>Řešení

1. Potvrdit stav ambari-agenta:

    ```bash
    service ambari-agent status
    ```

1. Zkontrolujte, zda jsou spuštěny služby řadiče s podporou převzetí služeb při selhání:

    ```bash
    ps -ef | grep failover
    ```

    Pokud služby řadiče s podporou převzetí služeb při selhání nejsou spuštěny, je to pravděpodobně z důvodu problému zabránit hdinsight agent spuštění řadiče převzetí služeb při selhání. Zkontrolujte protokol hdinsight-agent ze `/var/log/hdinsight-agent/hdinsight-agent.out` souboru.

## <a name="scenario-heartbeat-lost-for-ambari"></a>Scénář: Prezenční signál ztracený pro Ambari

### <a name="issue"></a>Problém

Ambarisrdeční agent byl ztracen.

### <a name="cause"></a>Příčina

Protokoly OMS způsobují vysoké využití procesoru.

### <a name="resolution"></a>Řešení

* Zakažte protokolování Azure Monitor pomocí rutiny [Disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) PowerShell.
* Odstranění `mdsd.warn` souboru protokolu

---

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
