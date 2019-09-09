---
title: Chyba sledovacího zařízení s provizorním zamrznutím chyby z clusteru Azure HDInsight
description: V protokolu syslogs z clusteru Azure HDInsight se objevuje procesor s provizorním zamrznutím chyb v jádrech.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/05/2019
ms.openlocfilehash: 618a871bee762900d4c99700cde42d759506b4b9
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810583"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Scénář: sledovací zařízení: Chyba: měkké zamrznutí – chyba procesoru v clusteru Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Syslogy jádra obsahují chybovou zprávu: `watchdog: BUG: soft lockup - CPU`.

## <a name="cause"></a>Příčina

[Chyba](https://bugzilla.kernel.org/show_bug.cgi?id=199437) v jádru systému Linux způsobuje vynucené přemrznutí procesoru.

## <a name="resolution"></a>Řešení

Použijte opravu jádra. Skript níže upgraduje jádro Linux a restartuje počítače v různých časech za 24 hodin. Spusťte akci skriptu ve dvou dávkách. První dávka je na všech uzlech kromě hlavního uzlu. Druhá dávka je na hlavním uzlu. Nespouštějte u hlavního uzlu a dalších uzlů současně.

1. Přejděte do clusteru HDInsight z Azure Portal.

1. Přejít na akce skriptu.

1. Vyberte **Odeslat novou** a zadejte vstup následujícím způsobem.

    | Vlastnost | Value |
    | --- | --- |
    | Typ skriptu | – Vlastní |
    | Name |Oprava potíží s provizorním zámkem jádra |
    | URI skriptu Bash |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Typ (typy) uzlů |Pracovní proces, Zookeeper |
    | Parametry |Není k dispozici |

    Vyberte možnost **zachovat tuto akci se skriptem...** , pokud chcete, aby skript po přidání nových uzlů spustil skript.

1. Vyberte **Vytvořit**.

1. Počkejte, než bude spuštění úspěšné.

1. Spusťte akci skriptu u hlavního uzlu podle stejných kroků jako v kroku 3, ale tentokrát s typy uzlů: Záhlaví.

1. Počkejte, než bude spuštění úspěšné.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
