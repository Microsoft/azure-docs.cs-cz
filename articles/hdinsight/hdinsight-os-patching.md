---
title: Konfigurace plánu pro clustery HDInsight se systémem Linux - Azure opravy operačního systému | Microsoft Docs
description: Zjistěte, jak nakonfigurovat plán pro clustery HDInsight se systémem Linux opravy operačního systému.
services: hdinsight
documentationcenter: ''
author: bprakash
manager: asadk
editor: bprakash
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: bhanupr
ms.openlocfilehash: 42771b9ff0f177b6b31f626d1dd2d07046a53965
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31409751"
---
# <a name="os-patching-for-hdinsight"></a>Opravy chyb pro HDInsight operačního systému 
Služba se spravuje Hadoop HDInsight má na starosti opravy operačního systému základní virtuální počítače, které jsou používané clustery HDInsight. Od 1. srpna 2016 jsme změnili oprav zásady hostovaného operačního systému pro clustery HDInsight se systémem Linux (verze 3.4 nebo novější). Cílem nové zásady je výrazně snížit počet restartování z důvodu opravy. Nové zásady budou nadále opravy virtuálních počítačů (VM) v clusterech Linux každé pondělí a čtvrtek začínající na 12: 00 UTC postupný způsobem mezi uzly v jakémkoliv daného clusteru. Všechny daného virtuálního počítače se však pouze restartuje maximálně jednou za 30 dní z důvodu opravy operačního systému hosta. Kromě toho první restartování pro nově vytvořený cluster neprovede dřív než 30 dní od data vytvoření clusteru. Opravy bude platit, jakmile jsou virtuální počítače restartovat.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Postup konfigurace operačního systému opravy plán pro clustery HDInsight se systémem Linux
Virtuální počítače v clusteru služby HDInsight muset občas restartovat, aby se opravy důležité zabezpečení můžete nainstalovat. Od 1. srpna 2016 se restartují nových clusterů HDInsight se systémem Linux (verze 3.4 nebo novější,), pomocí následujícího plánu:

1. Virtuální počítač v clusteru můžete pouze po restartování počítače opravy maximálně jednou v období 30 dnů.
2. Počínaje 12: 00 UTC dojde k restartování.
3. Proces restartování rozložena mezi virtuální počítače v clusteru, takže clusteru je stále k dispozici během procesu restartování.
4. První restartování pro nově vytvořený cluster neprovede dřív než 30 dní od data vytvoření clusteru.

Pomocí akce skriptu popsaného v tomto článku, můžete upravit OS opravy plán následujícím způsobem:
1. Povolit nebo zakázat automatické restartování počítače
2. Sada četnost restartuje (ve dnech mezi jednotlivými restartováními)
3. Nastavit den v týdnu, kdy dojde k restartu

> [!NOTE]
> Tato akce skriptu budou fungovat jenom s clustery HDInsight se systémem Linux, které jsou vytvořené po 1. srpna 2016. Opravy bude platit pouze v případě, že jsou virtuální počítače restartovat. 
>

## <a name="how-to-use-the-script"></a>Jak používat skript 

Při použití tohoto skriptu vyžaduje následující informace:
1. Umístění skriptu: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv01/os-patching-reboot-config.sh.  HDInsight používá tento identifikátor URI k vyhledání a spuštění skriptu na všechny virtuální počítače v clusteru.
  
2. Typy uzlů clusteru, které skript se použije pro: headnode, workernode, zookeeper. Tento skript je nutné použít na všechny typy uzlů v clusteru. Pokud není použita pro typ uzlu, virtuálních počítačů pro daný typ uzlu bude nadále používat předchozí oprav plán.


3.  Parametr: Tento skript přijímá tři číselné parametry:

    | Parametr | Definice |
    | --- | --- |
    | Povolit nebo zakázat automatické restartování počítače |0 nebo 1. Hodnota 0 zakáže automatické restartování počítače během 1 povolí automatické restartování počítače. |
    | Frekvence |7 až 90 (včetně). Počet dní, které se má čekat před restartováním virtuálních počítačů pro opravy, které vyžadují restart počítače. |
    | Den v týdnu |1 až 7 (včetně). Hodnota 1 znamená restartování provedeno v pondělí a 7 označuje Sunday.For příklad, pomocí parametrů 1 60 2 výsledky v automaticky restartuje každých 60 dnů (maximálně) úterý. |
    | Trvalost |Při použití akce skriptu do existujícího clusteru, můžete skript označit jako trvalý. Trvalý skripty se použijí při přidání nového workernodes ke clusteru prostřednictvím operace škálování. |

> [!NOTE]
> Tento skript je třeba označit jako trvalý při použití existujícího clusteru. Všechny nové uzly, které jsou vytvořené pomocí operace škálování, jinak hodnota použije výchozí opravy plán.
Pokud použijete skript jako součást procesu vytváření clusteru, je automaticky trvalá.
>

## <a name="next-steps"></a>Další postup

Konkrétní kroky pomocí akce skriptu, naleznete v následujících částech [HDInsight se systémem Linuz přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md):

* [Použití akce skriptu při vytváření clusteru](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Použít akci skriptu do clusteru s podporou spuštěná](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
