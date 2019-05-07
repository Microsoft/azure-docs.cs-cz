---
title: Konfigurovat plán pro clustery založené na Linuxu HDInsight – Azure oprav operačního systému
description: Zjistěte, jak nakonfigurovat plán pro clustery HDInsight založené na Linuxu oprav operačního systému.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: 5b8ed75863087e077d483c792ac4134a0c3e1eb0
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65203640"
---
# <a name="os-patching-for-hdinsight"></a>Opravy operačního systému pro HDInsight 

> [!IMPORTANT]
> K dispozici pro vytvoření nového clusteru HDInsight 3 měsících publikování Image Ubuntu. Od ledna 2019 spuštěné clustery jsou **není** automaticky opravit. Zákazníkům musíte použít akce skriptů nebo jiných mechanismů o opravu spuštěný cluster. Nově vytvořený clustery bude mít vždy nejnovější dostupné aktualizace, včetně nejnovější opravy zabezpečení.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Jak nakonfigurovat plán pro clustery HDInsight založené na Linuxu oprav operačního systému
Virtuální počítače v clusteru služby HDInsight je potřeba restartovat příležitostně tak, že je možné nainstalovat důležité opravy. 

Pomocí skriptových akcí popsaných v tomto článku, můžete upravit následujícím způsobem plán oprav operačního systému:
1. Úplná aktualizace operačního systému nebo jenom aktualizace zabezpečení
2. Restartujte virtuální počítač

> [!NOTE]  
> Tuto akci se skripty budou fungovat jenom s clustery HDInsight založené na Linuxu vytvořená po 1. srpna 2016. Opravy bude platit pouze v případě, že virtuální počítače se restartují. Tento skript nebude automaticky použít aktualizace pro všechny aktualizace budoucí cykly. Spusťte skript, který každý čas nové aktualizace je potřeba použít k instalaci aktualizací a restartování virtuálního počítače.

## <a name="how-to-use-the-script"></a>Jak používat skript 

Při použití tento skript vyžaduje následující informace:
1. Umístění skriptu: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/os-patching-reboot-config.sh.  HDInsight používá tento identifikátor URI k vyhledání a spuštění skriptu na všech virtuálních počítačů v clusteru.
  
2. Typy uzlů clusteru, které skript platí pro: hlavního uzlu, workernode, zookeeper. Tento skript se musí použít na všechny typy uzlů v clusteru. Pokud není použité u typu uzlu, nebudou aktualizovány virtuálních počítačů pro daný typ uzlu.


3.  Parametr: Tento skript přijímá jeden číselný parametr:

    | Parametr | Definice |
    | --- | --- |
    | Aktualizace/nainstalovat úplný operační systém instalovat pouze aktualizace zabezpečení |0 nebo 1. Hodnota 0 se nainstaluje aktualizace zabezpečení jenom v průběhu 1 nainstaluje úplná aktualizace operačního systému. Pokud je k dispozici žádný parametr, že výchozí hodnota je 0. |

> [!NOTE]  
> Tento skript je třeba označit jako trvalý, při použití do existujícího clusteru. V opačném případě žádné nové uzly, které jsou vytvořené prostřednictvím operací škálování bude používat výchozí plán oprav.  Pokud použijete skript jako součást procesu vytváření clusteru, se ukládají automaticky.

## <a name="next-steps"></a>Další postup

Konkrétní kroky pomocí akce skriptu, najdete v následujících částech [HDInsight založených na Linuxu přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md):

* [Použití akce skriptu při vytváření clusteru](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Použít akci skriptu spuštěného clusteru](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
