---
title: Úkoly pro jednotlivé přispěvatele v rámci vědeckého zpracování týmových dat
description: Podrobný návod pro úlohy jednotlivých přispěvatelů v týmovém projektu pro datové vědy.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ecb5fef9c9b14bde72de29a45e29d7e16131bd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96000993"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Úkoly pro jednotlivé přispěvatele v rámci vědeckého zpracování týmových dat

Toto téma popisuje úkoly, které *jednotlivé přispěvatelé* dokončí k nastavení projektu v rámci [vědeckého zpracování týmových dat](overview.md) (TDSP). Cílem je pracovat ve společném týmovém prostředí, které se bude standardizovat na TDSP. TDSP je navržený tak, aby pomohla zdokonalit spolupráci a týmovou výuku. Přehled rolí personálu a jejich přidružených úloh, které jsou zpracovávány týmem pro datové vědy, se standardizací na TDSP, najdete v tématu [role procesů a úlohy vědeckého zpracování dat týmu](roles-tasks.md).

Následující diagram znázorňuje úkoly, které Project jednotliví přispěvatelé (odborníci přes data) dokončí k nastavení svého týmového prostředí. Pokyny, jak spustit projekt datové vědy v rámci TDSP, najdete v tématu [provádění projektů pro datové vědy](./agile-development.md). 

![Jednotlivé úlohy přispěvatele](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository** je úložiště, které váš projektový tým udržuje pro sdílení šablon projektů a prostředků.
- **TeamUtilities** je úložiště nástrojů, které váš tým udržuje speciálně pro váš tým. 
- **GroupUtilities** je úložiště, které vaše skupina udržuje pro sdílení užitečných nástrojů napříč celou skupinou. 

> [!NOTE] 
> Tento článek používá Azure Repos a Data Science Virtual Machine (DSVM) k nastavení prostředí TDSP, protože to znamená, jak implementovat TDSP v Microsoftu. Pokud váš tým používá jiné hostování kódu nebo vývojové platformy, jednotlivé úkoly přispěvatele jsou stejné, ale způsob jejich dokončení může být jiný.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že jste [správcem skupiny](group-manager-tasks.md), vedoucím [týmu](team-lead-tasks.md)a [vedoucím projektu](project-lead-tasks.md)nastavili následující prostředky a oprávnění:

- **Organizace** Azure DevOps pro vaši datovou věda jednotku
- **Úložiště projektu** nastavené vedoucím projektu pro sdílení šablon projektů a prostředků
- **GroupUtilities** a **TeamUtilities** úložiště nastavená správcem skupiny a vedoucím týmu, pokud je k dispozici
- Služba Azure **File Storage** nastavená pro sdílené prostředky pro váš tým nebo projekt (Pokud je k dispozici)
- **Oprávnění** , abyste mohli klonovat a vracet se zpátky do úložiště projektu 

Pokud chcete klonovat úložiště a upravovat obsah na vašem místním počítači nebo DSVM nebo k DSVM připojit službu Azure File Storage, je potřeba vzít v úvahu tento kontrolní seznam:

- Předplatné Azure.
- V počítači je nainstalovaný Git. Pokud používáte DSVM, Git se předem nainstaluje. V opačném případě si přečtěte článek věnované [platformám a nástrojům](platforms-and-tools.md#appendix).
- Pokud chcete použít DSVM, Windows nebo Linux DSVM vytvořeného a nakonfigurovaného v Azure. Další informace a pokyny najdete v dokumentaci k [Data Science Virtual Machine](../data-science-virtual-machine/index.yml).
- Pro Windows DSVM se na vašem počítači nainstaluje [Správce přihlašovacích údajů Git (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) . V souboru *Readme.MD* se posuňte dolů k části **Stažení a instalace** a vyberte **nejnovější instalační program**. Stáhněte instalační program *. exe* z instalační stránky a spusťte ho. 
- Pro Linux DSVM se v DSVM nastavil veřejný klíč SSH a přidal se do Azure DevOps. Další informace a pokyny najdete v části **vytvoření veřejného klíče SSH** v [příloze platformy a nástroje](platforms-and-tools.md#appendix). 
- Informace o službě Azure File Storage pro všechny služby Azure File Storage, které potřebujete připojit k vašemu DSVM. 

## <a name="clone-repositories"></a>Klonovat úložiště

Chcete-li pracovat s místními úložišti a nasdílet změny až do sdíleného týmu a úložišť projektů, nejprve zkopírujte nebo *naklonujte* úložiště do místního počítače. 

1. V Azure DevOps na stránce se souhrnem projektu svého týmu na adrese *https: \/ / \<server name> / \<organization name> / \<team name>* například **https: \/ /dev.Azure.com/DataScienceUnit/myTeam**.
   
1. V levém navigačním panelu vyberte úložiště **a v horní** části stránky vyberte úložiště, které chcete klonovat.
   
1. Na stránce úložiště vyberte **klonovat** v pravém horním rohu.
   
1. V dialogu **úložiště klonování** vyberte **https** pro připojení HTTP nebo **SSH** pro připojení SSH a zkopírujte adresu URL klonování do **příkazového řádku** do schránky.
   
   ![Klonovat úložiště](./media/project-ic-tasks/clone.png)
   
1. V místním počítači nebo DSVM vytvořte následující adresáře:
   
   - Pro Windows: **C:\GitRepos**
   - Pro Linux: **$Home/gitrepos**
   
1. Přejděte do adresáře, který jste vytvořili.
   
1. V Gitu bash spusťte příkaz `git clone <clone URL>` pro každé úložiště, které chcete klonovat. 
   
   Například následující příkaz naklonuje úložiště **TeamUtilities** do adresáře *myTeam* na místním počítači. 
   
   **Připojení HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **Připojení SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. Potvrďte, že se zobrazují složky pro klonované úložiště v adresáři místního projektu.
   
   ![Tři místní složky úložiště](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>Připojení služby Azure File Storage k vašemu DSVM

Pokud má váš tým nebo projekt sdílené prostředky ve službě Azure File Storage, připojte úložiště souborů k místnímu počítači nebo DSVM. Postupujte podle pokynů v tématu [připojení úložiště souborů Azure na místním počítači nebo DSVM](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm).

## <a name="next-steps"></a>Další kroky

Zde jsou odkazy na podrobné popisy dalších rolí a úloh, které jsou definovány v rámci vědeckého zpracování týmových dat:

- [Úlohy správce skupin pro tým pro datové vědy](group-manager-tasks.md)
- [Úkoly vedoucí týmu pro tým pro datové vědy](team-lead-tasks.md)
- [Úkoly vedoucího projektu pro tým pro datové vědy](project-lead-tasks.md)