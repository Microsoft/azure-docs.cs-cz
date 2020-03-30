---
title: Konfigurace plánu oprav operačního systému pro clustery Azure HDInsight
description: Přečtěte si, jak nakonfigurovat plán oprav operačního systému pro clustery HDInsight založené na Linuxu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: f8e694f658d6e9de04c92001214ecd5c32ff7753
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206856"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Konfigurace plánu oprav operačního systému pro clustery HDInsight založený na Linuxu

> [!IMPORTANT]
> Obrázky Ubuntu budou dostupné pro vytvoření nového clusteru Azure HDInsight do tří měsíců od publikování. Od ledna 2019 nejsou spuštěné clustery automaticky opraveny. Zákazníci musí k opravě spuštěného clusteru použít akce skriptu nebo jiné mechanismy. Nově vytvořené clustery budou mít vždy nejnovější dostupné aktualizace, včetně nejnovějších oprav zabezpečení.

HDInsight poskytuje podporu k provádění běžných úloh v clusteru, jako je instalace oprav operačního systému, aktualizace zabezpečení a restartování uzlů. Tyto úlohy jsou provedeny pomocí následujících dvou skriptů, které lze spustit jako [akce skriptu](hdinsight-hadoop-customize-cluster-linux.md)a nakonfigurovat s parametry:

- `schedule-reboots.sh`- Proveďte okamžité restartování nebo naplánujte restartování v uzlech clusteru.
- `install-updates-schedule-reboots.sh`- Nainstalujte všechny aktualizace, pouze jádro + aktualizace zabezpečení, nebo pouze aktualizace jádra.

> [!NOTE]  
> Akce skriptu nebudou automaticky používat aktualizace pro všechny budoucí cykly aktualizací. Spusťte skripty pokaždé, když je nutné použít nové aktualizace k instalaci aktualizací a restartujte virtuální počítač.

## <a name="preparation"></a>Příprava

Oprava na reprezentativní morálce před nasazením do produkčního prostředí. Vytvořte plán, který adekvátně otestuje váš systém před vlastním opravováním.

Čas od času se může zobrazit zpráva, že je k dispozici upgrade, z relace ssh s clusterem. Zpráva může vypadat podobně:

```
New release '18.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade it
```

Oprava je volitelná a podle vašeho uvážení.

## <a name="restart-nodes"></a>Restartování uzlů
  
Plán [skriptu restartuje](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh), nastaví typ restartování, které bude provedeno na počítačích v clusteru. Při odesílání akce skriptu nastavte ji tak, aby se vztahovala na všechny tři typy uzlů: hlavní uzel, uzel pracovníka a zookeeper. Pokud skript není použit pro typ uzlu, virtuální chody pro tento typ uzlu nebudou aktualizovány ani restartovány.

Přijímá `schedule-reboots script` jeden číselný parametr:

| Parametr | Přípustné hodnoty | Definice |
| --- | --- | --- |
| Typ restartování, který má být | 1 nebo 2 | Hodnota 1 umožňuje restartování plánu (naplánováno na 12-24 hodin). Hodnota 2 umožňuje okamžité restartování (za 5 minut). Pokud není uveden žádný parametr, výchozí hodnota je 1. |  

## <a name="install-updates-and-restart-nodes"></a>Instalace aktualizací a restartování uzlů

Skript [install-updates-schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) poskytuje možnosti instalace různých typů aktualizací a restartování virtuálního počítače.

Skript `install-updates-schedule-reboots` přijímá dva číselné parametry, jak je popsáno v následující tabulce:

| Parametr | Přípustné hodnoty | Definice |
| --- | --- | --- |
| Typ aktualizací k instalaci | 0, 1 nebo 2 | Hodnota 0 nainstaluje pouze aktualizace jádra. Hodnota 1 nainstaluje všechny aktualizace a 2 nainstaluje pouze jádro + aktualizace zabezpečení. Pokud není k dispozici žádný parametr, výchozí hodnota je 0. |
| Typ restartování, který má být | 0, 1 nebo 2 | Hodnota 0 zakáže restartování. Hodnota 1 umožňuje restartování plánu a 2 umožňuje okamžité restartování. Pokud není k dispozici žádný parametr, výchozí hodnota je 0. Uživatel musí změnit vstupní parametr 1 na vstupní parametr 2. |

> [!NOTE]
> Po použití existujícího clusteru je nutné označit skript jako trvalý. V opačném případě budou všechny nové uzly vytvořené operacemi škálování používat výchozí plán oprav. Pokud použijete skript jako součást procesu vytváření clusteru, bude automaticky zachován.

## <a name="next-steps"></a>Další kroky

Konkrétní kroky při používání akcí skriptu najdete v následujících částech v [tématu Přizpůsobení clusterů HDInsight založených na Linuxu pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md):

- [Použití akce skriptu při vytváření clusteru](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [Použití akce skriptu na spuštěný cluster](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
