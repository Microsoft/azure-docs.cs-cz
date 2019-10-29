---
title: Konfigurace plánu oprav operačního systému pro clustery Azure HDInsight
description: Naučte se konfigurovat plán oprav operačního systému pro clustery HDInsight se systémem Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: d0a490fd3b23c96923af10db3c1f9ee9ea0dfad5
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044884"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Konfigurace plánu oprav operačního systému pro clustery HDInsight se systémem Linux 

> [!IMPORTANT]
> Image Ubuntu budou k dispozici pro nové vytvoření clusteru Azure HDInsight do tří měsíců od jejich publikování. Od ledna 2019 nejsou spuštěné clustery automaticky opraveny. Pokud chtějí zákazníci opravit běžící cluster, musí použít akce skriptu nebo jiné mechanismy. Nově vytvořené clustery budou mít vždycky nejnovější dostupné aktualizace, včetně nejnovějších oprav zabezpečení.

V některých případech je nutné restartovat virtuální počítače v clusteru HDInsight, aby se nainstalovaly důležité opravy zabezpečení.

Pomocí akcí skriptu popsaných v tomto článku můžete upravit plán oprav operačního systému následujícím způsobem:

1. Nainstalujte všechny aktualizace nebo nainstalujte jenom aktualizace jádra a zabezpečení nebo aktualizace jádra.
2. Proveďte okamžité restartování nebo naplánujte restartování virtuálního počítače.

> [!NOTE]  
> Akce skriptu popsané v tomto článku budou fungovat jenom s clustery HDInsight se systémem Linux vytvořenými od 1. srpna 2016. Opravy jsou platné až po restartování virtuálních počítačů.
> Akce skriptu nebudou automaticky používat aktualizace pro všechny budoucí aktualizační cykly. Spusťte skripty pokaždé, když se musí použít nové aktualizace, aby se nainstalovaly aktualizace, a pak se virtuální počítač restartuje.

## <a name="add-information-to-the-script"></a>Přidání informací do skriptu

Použití skriptu vyžaduje následující informace:

- Umístění skriptu install-Updates-Schedule-restarts: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   HDInsight používá tento identifikátor URI k vyhledání a spuštění skriptu na všech virtuálních počítačích v clusteru. Tento skript poskytuje možnosti pro instalaci aktualizací a restartování virtuálního počítače.
  
- Umístění skriptu pro restartování nástroje Schedule: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   HDInsight používá tento identifikátor URI k vyhledání a spuštění skriptu na všech virtuálních počítačích v clusteru. Tento skript restartuje virtuální počítač.
  
- Typ uzlu clusteru, pro který je skript použit, je hlavnímu uzlu, workernode a Zookeeper. Použije skript pro všechny typy uzlů v clusteru. Pokud se skript nepoužije na typ uzlu, virtuální počítače pro tento typ uzlu se neaktualizují ani nerestartují.

- Skript Install-Updates-Schedule-restarts akceptuje dva číselné parametry:

    | Parametr | Definice |
    | --- | --- |
    | Nainstalovat pouze aktualizace jádra/nainstalovat všechny aktualizace/instalovat pouze aktualizace jádra a zabezpečení|0, 1 nebo 2. Hodnota 0 nainstaluje pouze aktualizace jádra. Hodnota 1 nainstaluje všechny aktualizace a 2 nainstaluje pouze aktualizace jádra a zabezpečení. Pokud není zadán žádný parametr, výchozí hodnota je 0. |
    | Bez restartování/povolení restartování plánu/povolení okamžitého restartování |0, 1 nebo 2. Hodnota 0 zakáže restart. Hodnota 1 umožňuje naplánovat restart a 2 umožňuje okamžité restartování. Pokud není zadán žádný parametr, výchozí hodnota je 0. Uživatel musí změnit vstupní parametr 1 na vstupní parametr 2. |
   
 - Skript pro restartování nástroje Schedule-restarts akceptuje jeden číselný parametr:

    | Parametr | Definice |
    | --- | --- |
    | Povolit restart plánu/povolit okamžité restartování |1 nebo 2. Hodnota 1 povolí restart plánu (naplánováno během 12-24 hodin). Hodnota 2 umožňuje okamžité restartování (5 minut). Pokud není zadán žádný parametr, výchozí hodnota je 1. |  

> [!NOTE]
> Po použití v existujícím clusteru musíte označit skript jako trvalý. Jinak budou všechny nové uzly vytvořené prostřednictvím operací škálování používat výchozí plán oprav. Použijete-li skript jako součást procesu vytváření clusteru, bude automaticky trvalý.


## <a name="next-steps"></a>Další kroky

Konkrétní kroky týkající se použití akcí skriptů najdete v následujících částech v tématu [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md):

* [Použití akce skriptu během vytváření clusteru](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Použití akce skriptu u běžícího clusteru](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
