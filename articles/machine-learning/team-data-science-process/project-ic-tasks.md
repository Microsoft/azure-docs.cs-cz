---
title: Úkoly pro jednotlivého přispěvatele v procesu vědecké vědy o týmových datech
description: Podrobný návod úkolů pro jednotlivého přispěvatele v projektu týmu datové vědy.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d9942c31b63de77196b8b51b88376cb8ef74b990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721247"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Úkoly pro jednotlivého přispěvatele v procesu vědecké vědy o týmových datech

Toto téma popisuje úkoly, které *jednotlivé přispěvatele* dokončí nastavit projekt v [procesu vědecké ho procesu týmových dat](overview.md) (TDSP). Cílem je pracovat v týmovém prostředí pro spolupráci, které standardizuje systém TDSP. TDSP je navržen tak, aby pomohl zlepšit spolupráci a týmové učení. Přehled rolí personálu a jejich přidružených úkolů, které jsou zpracovány týmem pro datové vědy standardizujícím na tdsp, naleznete v [tématu Role a úkoly procesu vědeckého zpracování týmových dat](roles-tasks.md).

Následující diagram znázorňuje úkoly, které projekt jednotlivých přispěvatelů (datových vědců) dokončit nastavit jejich týmové prostředí. Pokyny k provedení projektu datové vědy v rámci tdsp naleznete [v tématu Provádění projektů datové vědy](project-execution.md). 

![Jednotlivé úkoly přispěvatele](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository** je úložiště, které váš projektový tým udržuje pro sdílení šablon a prostředků projektu.
- **TeamUtilities** je úložiště nástrojů, které váš tým udržuje speciálně pro váš tým. 
- **GroupUtilities** je úložiště, které vaše skupina udržuje pro sdílení užitečných nástrojů v celé skupině. 

> [!NOTE] 
> Tento článek používá Azure Repos a virtuální počítač pro datové vědy (DSVM) k nastavení prostředí TDSP, protože to je, jak implementovat TDSP u společnosti Microsoft. Pokud váš tým používá jiné kód hosting nebo vývojové platformy, jednotlivé úlohy přispěvatele jsou stejné, ale způsob jejich dokončení se může lišit.

## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že [vedoucí skupiny](group-manager-tasks.md), [vedoucí týmu](team-lead-tasks.md)a [vedoucí projektu](project-lead-tasks.md)nastavili následující zdroje a oprávnění :

- **Organizace** Azure DevOps pro vaši jednotku datové vědy
- **Úložiště projektu** nastavené vedoucím projektu pro sdílení šablon a datových zdrojů projektu
- **Registry GroupUtilities** a **TeamUtilities,** které vytvořil manažer skupiny a vedoucí týmu, pokud je to možné
- **Úložiště souborů** Azure nastavené pro sdílené datové zdroje pro váš tým nebo projekt, pokud je to možné
- **Oprávnění** k klonování a zasunutí zpět do úložiště projektu 

Chcete-li klonovat úložiště a upravovat obsah v místním počítači nebo DSVM nebo připojit úložiště souborů Azure do vašeho DSVM, je třeba zvážit tento kontrolní seznam:

- Předplatné Azure.
- Git nainstalovaný na vašem počítači. Pokud používáte DSVM, Git je předinstalovaný. V opačném případě se podívejte do [dodatku k platformám a nástrojům](platforms-and-tools.md#appendix).
- Pokud chcete použít DSVM, Windows nebo Linux DSVM vytvořené a nakonfigurované v Azure. Další informace a pokyny naleznete v [dokumentaci k virtuálním strojům pro datové vědy](/azure/machine-learning/data-science-virtual-machine/).
- Pro systém Windows DSVM je v počítači nainstalován [Správce přihlašovacích údajů (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) gitu. V *souboru README.md* přejděte dolů do části **Stáhnout a nainstalovat** a vyberte nejnovější instalační **program**. Stáhněte instalační *program .exe* ze stránky instalačního programu a spusťte jej. 
- Pro Linux DSVM, SSH veřejný klíč nastavit na vašem DSVM a přidány do Azure DevOps. Další informace a pokyny naleznete v části **Vytvoření veřejného klíče SSH** v [dodatku platformy a nástroje](platforms-and-tools.md#appendix). 
- Informace o úložišti souborů Azure pro jakékoli úložiště souborů Azure, které potřebujete k připojení k vašemu DSVM. 

## <a name="clone-repositories"></a>Klonovat repozitáře

Chcete-li pracovat s úložišti místně a posunout změny do sdílených týmových a projektových úložišť, nejprve zkopírujte nebo *naklonujte* úložiště do místního počítače. 

1. V Azure DevOps přejděte na stránku Souhrn projektu vašeho týmu na *adrese https:\//\<název serveru>/\<název organizace>/\<název týmu>*, například **https:\//dev.azure.com/DataScienceUnit/MyTeam**.
   
1. V levém navigačním panelu vyberte **Úložiště** a v horní části stránky vyberte úložiště, které chcete klonovat.
   
1. Na stránce repo vyberte **Klonování** vpravo nahoře.
   
1. V **dialogovém** okně Úložiště klonování vyberte pro připojení HTTP **protokol HTTPS** nebo **SSH** pro připojení SSH a zkopírujte adresu URL klonu pod **příkazovým řádkem** do schránky.
   
   ![Klonovat repo](./media/project-ic-tasks/clone.png)
   
1. V místním počítači nebo dsvm vytvořte následující adresáře:
   
   - Pro Windows: **C:\GitRepos**
   - Pro Linux: **$home/GitRepos**
   
1. Změňte adresář, který jste vytvořili.
   
1. V Git Bash spusťte příkaz `git clone <clone URL>` pro každé úložiště, které chcete klonovat. 
   
   Například následující příkaz klonuje úložiště **TeamUtilities** do adresáře *MyTeam* v místním počítači. 
   
   **Připojení HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **Připojení SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. Potvrďte, že můžete vidět složky pro klonované repozitáře v adresáři místního projektu.
   
   ![Tři složky místního úložiště](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>Připojení úložiště souborů Azure do vašeho DSVM

Pokud váš tým nebo projekt sdílí prostředky v úložišti souborů Azure, připojte úložiště souborů do místního počítače nebo DSVM. Postupujte podle pokynů na [mount Azure úložiště souborů na místním počítači nebo DSVM](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm).

## <a name="next-steps"></a>Další kroky

Zde jsou odkazy na podrobný popis dalších rolí a úkolů definovaných procesem vědecké vědy o týmových datech:

- [Úlohy správce skupiny pro tým datových věd](group-manager-tasks.md)
- [Úkoly vedoucího týmu pro tým datové vědy](team-lead-tasks.md)
- [Úkoly vedoucího projektu pro tým datové vědy](project-lead-tasks.md)

