---
title: Konfigurovat plán pro clustery založené na Linuxu HDInsight – Azure oprav operačního systému
description: Zjistěte, jak nakonfigurovat plán pro clustery HDInsight založené na Linuxu oprav operačního systému.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: a73866a8898042b546fa47d9c3d14ab4e58e9a12
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503237"
---
# <a name="os-patching-for-hdinsight"></a>Opravy operačního systému pro HDInsight 

> [!IMPORTANT]
> K dispozici pro vytvoření nového clusteru HDInsight v rámci tří měsíců od publikování Image Ubuntu. Od ledna 2019 spuštěné clustery jsou **není** automaticky opravit. Zákazníkům musíte použít akce skriptů nebo jiných mechanismů o opravu spuštěný cluster. Nově vytvořený clustery bude mít vždy nejnovější dostupné aktualizace, včetně nejnovější opravy zabezpečení.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Jak nakonfigurovat plán pro clustery HDInsight založené na Linuxu oprav operačního systému
Virtuální počítače v clusteru služby HDInsight je potřeba restartovat příležitostně tak, že je možné nainstalovat důležité opravy. 

Pomocí akcí skriptů je popsáno v tomto článku, můžete upravit následujícím způsobem plán oprav operačního systému:
1. Instalace aktualizací nebo instalace jádra + pouze aktualizace zabezpečení nebo instalace jádra jenom aktualizace.
2. Okamžitý restart nebo plán restartování na virtuálním počítači.

> [!NOTE]  
> Tyto akce skriptu bude fungovat jenom s clustery HDInsight založené na Linuxu vytvořená po 1. srpna 2016. Opravy bude platit pouze v případě, že virtuální počítače se restartují. Tyto skripty nebude automaticky použít aktualizace pro všechny aktualizace budoucí cykly. Spusťte skripty, které každý čas nové aktualizace je potřeba použít k instalaci aktualizací a restartování virtuálního počítače.

## <a name="how-to-use-the-script"></a>Jak používat skript 

Pomocí tohoto skriptu vyžaduje následující informace:
1. Umístění skriptu install aktualizace plán – restartování: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   HDInsight používá tento identifikátor URI k vyhledání a spuštění skriptu na všech virtuálních počítačů v clusteru. Tento skript poskytuje možnosti pro instalaci aktualizací a restartování virtuálního počítače.
  
2. Plán – restartování umístění skriptu: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   HDInsight používá tento identifikátor URI k vyhledání a spuštění skriptu na všech virtuálních počítačů v clusteru. Tento skript restartuje virtuální počítač.
  
3. Typy uzlů clusteru, které skript platí pro: hlavního uzlu, workernode, zookeeper. Tento skript se musí použít na všechny typy uzlů v clusteru. Pokud není použité u typu uzlu, pak virtuální počítače pro tento typ uzlu nebude aktualizovat ani restartovat.

4. Parametr: Skript instalace aktualizace plánu restartování přijímá dva číselné parametry:

    | Parametr | Definice |
    | --- | --- |
    | Instalovat pouze aktualizace jádra / instalace všech aktualizací nebo instalace jádra + zabezpečení pouze aktualizace |0 nebo 1 nebo 2. Hodnota 0 nainstaluje aktualizace jádra pouze během 1 nainstaluje všechny aktualizace a nainstaluje 2 jádra + security pouze aktualizace. Pokud je k dispozici žádný parametr, výchozí hodnota je 0. |
    | Bez okamžité restartování restartování nebo povolit plán restartování nebo povolit |0 nebo 1 nebo 2. Hodnota 0 zakáže restartování, při restartování plán umožňuje 1 a 2 umožňuje okamžitý restart. Pokud je k dispozici žádný parametr, výchozí hodnota je 0. Uživatel musí vstupní parametr 1 vstupní parametr 2. |
   
 5. Parametr: Skript plánu restartování přijímá jeden číselný parametr:

    | Parametr | Definice |
    | --- | --- |
    | Povolit plán restartování nebo povolit okamžitý restart |1 nebo 2. Hodnota 1 povolí plán restartování (restartování je plánované v příštích 12 až 24 hodin). během restartování 2 umožňuje okamžité (během 5 minut). Pokud je k dispozici žádný parametr, výchozí hodnota je 1. |  

> [!NOTE] 
> Skript je třeba označit jako trvalý, při použití do existujícího clusteru. V opačném případě žádné nové uzly, které jsou vytvořené prostřednictvím operací škálování bude používat výchozí plán oprav.  Pokud použijete skript jako součást procesu vytváření clusteru, se ukládají automaticky.


## <a name="next-steps"></a>Další postup

Konkrétní kroky pomocí akce skriptu, najdete v následujících částech [HDInsight založených na Linuxu přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md):

* [Použití akce skriptu při vytváření clusteru](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Použít akci skriptu spuštěného clusteru](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
