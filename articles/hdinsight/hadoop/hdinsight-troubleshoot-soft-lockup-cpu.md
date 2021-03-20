---
title: Chyba sledovacího procesoru s provizorním zamrznutím chyby z clusteru Azure HDInsight
description: V protokolu syslogs z clusteru Azure HDInsight se objevuje procesor s provizorním zamrznutím chyb v jádrech.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/05/2019
ms.openlocfilehash: 5d9d7b0fc21660dd22ff92bbe2de38c759c440ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944347"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Scénář: "sledovací: Chyba: měkké uzamčení-CPU" z clusteru Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Syslogy jádra obsahují chybovou zprávu: `watchdog: BUG: soft lockup - CPU` .

## <a name="cause"></a>Příčina

[Chyba](https://bugzilla.kernel.org/show_bug.cgi?id=199437) v jádru systému Linux způsobuje vynucené přemrznutí procesoru.

## <a name="resolution"></a>Řešení

Použijte opravu jádra. Skript níže upgraduje jádro Linux a restartuje počítače v různých časech za 24 hodin. Spusťte akci skriptu ve dvou dávkách. První dávka je na všech uzlech kromě hlavního uzlu. Druhá dávka je na hlavním uzlu. Nespouštějte u hlavního uzlu a dalších uzlů současně.

1. Přejděte do clusteru HDInsight z Azure Portal.

1. Přejít na akce skriptu.

1. Vyberte **Odeslat novou** a zadejte vstup následujícím způsobem.

    | Vlastnost | Hodnota |
    | --- | --- |
    | Typ skriptu | – Vlastní |
    | Name |Oprava potíží s provizorním zámkem jádra |
    | Identifikátor URI skriptu bash |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Typ (typy) uzlů |Pracovní proces, Zookeeper |
    | Parametry |– |

    Vyberte možnost **zachovat tuto akci se skriptem...** , pokud chcete, aby skript po přidání nových uzlů spustil skript.

1. Vyberte **Vytvořit**.

1. Počkejte, než bude spuštění úspěšné.

1. Spusťte akci skriptu u hlavního uzlu podle stejných kroků jako v kroku 3, ale tentokrát s typy uzlů: Head.

1. Počkejte, než bude spuštění úspěšné.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]