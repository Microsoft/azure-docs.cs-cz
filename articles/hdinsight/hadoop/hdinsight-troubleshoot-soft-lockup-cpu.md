---
title: Chyba měkkého uzamčení procesoru Watchdog BUG z clusteru Azure HDInsight
description: V syslogech jádra z clusteru Azure HDInsight se zobrazí soft lockup CPU watchdog bug
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 701e314ad2a3762b1e8ca022ce18d9435ce2db37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894108"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Scénář: "watchdog: BUG: soft lockup – CPU" chyba z clusteru Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Syslogy jádra obsahují chybovou zprávu: `watchdog: BUG: soft lockup - CPU`.

## <a name="cause"></a>Příčina

[Chyba](https://bugzilla.kernel.org/show_bug.cgi?id=199437) v linuxovém jádře způsobuje měkké uzamčení CPU.

## <a name="resolution"></a>Řešení

Aplikujte opravu jádra. Níže uvedený skript upgraduje linuxové jádro a restartuje počítače v různých časech během 24 hodin. Spusťte akci skriptu ve dvou dávkách. První dávka je na všech uzlech kromě hlavního uzlu. Druhá dávka je na hlavním uzlu. Nespouštějte na hlavní uzel a jiné uzly současně.

1. Přejděte do clusteru HDInsight z webu Azure Portal.

1. Přejděte na akce skriptu.

1. Vyberte **Odeslat nový** a zadejte vstup následujícím způsobem.

    | Vlastnost | Hodnota |
    | --- | --- |
    | Typ skriptu | -Vlastní |
    | Name (Název) |Oprava problému s měkkým zámkem jádra |
    | Bash skript URI |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Typ uzlu |Pracovník, Zookeeper |
    | Parametry |Není dostupné. |

    Vyberte **zachovat tuto akci skriptu...** pokud chcete spustit skript při přidání nových uzlů.

1. Vyberte **Vytvořit**.

1. Počkejte na spuštění úspěšné.

1. Spusťte akci skriptu na hlavním uzlu podle stejných kroků jako krok 3, ale tentokrát s typy uzlů: Hlava.

1. Počkejte na spuštění úspěšné.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
