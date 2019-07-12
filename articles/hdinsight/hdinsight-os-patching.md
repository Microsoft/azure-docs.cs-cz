---
title: Konfigurovat plán pro clustery založené na Linuxu HDInsight – Azure oprav operačního systému
description: Zjistěte, jak nakonfigurovat plán pro clustery HDInsight založené na Linuxu oprav operačního systému.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: efe74618b269000749f7ba6c24d35903e540dcfb
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657060"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Konfigurovat plán pro clustery HDInsight založené na Linuxu oprav operačního systému 

> [!IMPORTANT]
> K dispozici pro vytvoření nového clusteru Azure HDInsight do tří měsíců od publikování Image Ubuntu. Od ledna 2019 spuštěné clustery nejsou automaticky opravit. Zákazníkům musíte použít akce skriptů nebo jiných mechanismů o opravu spuštěný cluster. Nově vytvořený clustery bude mít vždy nejnovější dostupné aktualizace, včetně nejnovější opravy zabezpečení.

V některých případech je nutné restartovat virtuální počítače (VM) v clusteru služby HDInsight nainstalovat důležité opravy.

Pomocí akcí skriptů je popsáno v tomto článku můžete upravit následujícím způsobem plán oprav operačního systému:

1. Nainstalovat všechny aktualizace, nebo nainstalovat pouze jádra + aktualizacemi zabezpečení nebo aktualizace jádra.
2. Provést okamžité restartování nebo schématu restartování ve virtuálním počítači.

> [!NOTE]  
> Akce skriptů je popsáno v tomto článku budou fungovat jenom s clustery HDInsight založené na Linuxu vytvořená po 1. srpna 2016. Opravy chyb jsou efektivní až po restartování virtuálních počítačů.
> Akce skriptů nebude automaticky použít aktualizace pro všechny aktualizace budoucí cykly. Spuštěním skriptů pokaždé, když nové aktualizace je nutné použít na nainstalovat aktualizace a potom restartujte virtuální počítač.

## <a name="add-information-to-the-script"></a>Přidejte informace o skriptu

Pomocí skriptu, který vyžaduje následující informace:

- Umístění skriptu install aktualizace plán – restartování: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   HDInsight používá tento identifikátor URI k vyhledání a spuštění skriptu na všech virtuálních počítačů v clusteru. Tento skript nabízí možnosti pro instalaci aktualizací a restartování virtuálního počítače.
  
- Plán – restartování umístění skriptu: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   HDInsight používá tento identifikátor URI k vyhledání a spuštění skriptu na všech virtuálních počítačů v clusteru. Tento skript restartuje virtuální počítač.
  
- Typy uzlů clusteru, které skript se použije na jsou hlavního uzlu, workernode a zookeeper. Skript platí pro všechny typy uzlů v clusteru. Pokud skript není použité u typu uzlu, nebude aktualizovat nebo restartování virtuálních počítačů pro daný typ uzlu.

- Skript instalace aktualizace plánu restartování přijímá dva číselné parametry:

    | Parametr | Definice |
    | --- | --- |
    | Instalovat pouze aktualizace jádra / instalace všech aktualizací nebo instalace jádra + zabezpečení pouze aktualizace|0, 1 nebo 2. Hodnota 0 nainstaluje jenom aktualizace jádra. Hodnota 1 nainstaluje všechny aktualizace a 2 nainstaluje pouze jádra a aktualizace zabezpečení. Pokud je k dispozici žádný parametr, výchozí hodnota je 0. |
    | Bez okamžité restartování restartování nebo povolit plán restartování nebo povolit |0, 1 nebo 2. Hodnota 0 zakáže restartování. Hodnota 1 povolí plán restartování a 2 umožňuje okamžitý restart. Pokud je k dispozici žádný parametr, výchozí hodnota je 0. Uživatel bude muset změnit vstupní parametr 1 vstupní parametr 2. |
   
 - Skript plánu restartování přijímá jeden číselný parametr:

    | Parametr | Definice |
    | --- | --- |
    | Povolit plán restartování nebo povolit okamžitý restart |1 nebo 2. Hodnota 1 povolí plán restart (naplánované v 12 až 24 hodin). Hodnota 2 umožňuje okamžitý restart (v 5 minut). Pokud není uveden žádný parametr, výchozí hodnota je 1. |  

> [!NOTE]
> Skript je třeba označit jako trvalý po použití do existujícího clusteru. V opačném případě žádné nové uzly, které jsou vytvořené prostřednictvím operací škálování bude používat výchozí plán oprav. Pokud použijete skript jako součást procesu vytváření clusteru, obsahuje trvalé automaticky.


## <a name="next-steps"></a>Další postup

Konkrétní kroky pomocí akcí skriptů najdete v následujících částech [HDInsight založených na Linuxu přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md):

* [Použití akce skriptu při vytváření clusteru](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Použít akci skriptu spuštěného clusteru](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
