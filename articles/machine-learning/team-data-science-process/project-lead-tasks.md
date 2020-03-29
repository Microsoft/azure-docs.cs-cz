---
title: Úkoly pro vedoucího projektu v procesu vědecké vědy o týmových datech
description: Podrobný návod úkolů pro vedoucího projektu v týmu Team Data Science Process
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4a4d3a1480a852218e698862a509c4af45e49eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76714413"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>Úkoly vedoucího projektu v procesu vědecké ho procesu týmových dat

Tento článek popisuje úkoly, které *vedoucí projektu* dokončí nastavit úložiště pro svůj projektový tým v [procesu vědecké ho procesu týmových dat](overview.md) (TDSP). TDSP je framework vyvinutý společností Microsoft, který poskytuje strukturovanou posloupnost aktivit pro efektivní provádění cloudových prediktivních analytických řešení. TDSP je navržen tak, aby pomohl zlepšit spolupráci a týmové učení. Přehled rolí personálu a přidružených úkolů pro tým pro datové vědy standardizující na tdsp, najdete v [tématu Role a úkoly procesu týmových dat](roles-tasks.md).

Vedoucí projektu řídí každodenní aktivity jednotlivých datových vědců na konkrétním projektu datové vědy v TDSP. Následující diagram znázorňuje pracovní postup pro úkoly zájemce o projekt:

![Pracovní postup úkolu zájemce o projekt](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Tento kurz se zabývá krokem 1: Vytvoření úložiště projektu a krokem 2: Úložiště projektu seed z vašeho týmového úložiště ProjectTemplate. 

Krok 3: Vytvoření pracovní položky funkce pro projekt a Krok 4: Přidat články pro fáze projektu naleznete [v tématu Agilní vývoj projektů datových věd](agile-development.md).

Krok 5: Vytvoření a přizpůsobení prostředků úložiště/analýzy a sdílení v případě potřeby najdete [v tématu Vytváření týmových dat a analytických prostředků](team-lead-tasks.md#create-team-data-and-analytics-resources).

Krok 6: Nastavení řízení zabezpečení úložiště projektů najdete v tématu [Přidání členů týmu a konfigurace oprávnění](team-lead-tasks.md#add-team-members-and-configure-permissions).

> [!NOTE] 
> Tento článek používá Azure Repos k nastavení projektu TDSP, protože to je, jak implementovat TDSP u Microsoftu. Pokud váš tým používá jinou platformu pro hostování kódu, úkoly zájemců projektu jsou stejné, ale způsob jejich dokončení se může lišit.

## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že [váš správce skupiny](group-manager-tasks.md) a [vedoucí týmu](team-lead-tasks.md) nastavili následující prostředky a oprávnění:

- **Organizace** Azure DevOps pro vaši datovou jednotku
- Týmový **projekt** pro váš tým pro datové vědy
- **Týmové** šablony a úložiště nástrojů
- **Oprávnění** k vytvoření a úpravám úložišť pro váš projekt.

Chcete-li klonovat úložiště a upravovat obsah v místním počítači nebo virtuálním počítači datové vědy (DSVM) nebo nastavit úložiště souborů Azure a připojit ho k vašemu DSVM, je také třeba zvážit tento kontrolní seznam:

- Předplatné Azure.
- Git nainstalovaný na vašem počítači. Pokud používáte DSVM, Git je předinstalovaný. V opačném případě se podívejte do [dodatku k platformám a nástrojům](platforms-and-tools.md#appendix).
- Pokud chcete použít DSVM, Windows nebo Linux DSVM vytvořené a nakonfigurované v Azure. Další informace a pokyny naleznete v [dokumentaci k virtuálním strojům pro datové vědy](/azure/machine-learning/data-science-virtual-machine/).
- Pro systém Windows DSVM je v počítači nainstalován [Správce přihlašovacích údajů (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) gitu. V *souboru README.md* přejděte dolů do části **Stáhnout a nainstalovat** a vyberte nejnovější instalační **program**. Stáhněte instalační *program .exe* ze stránky instalačního programu a spusťte jej. 
- Pro Linux DSVM, SSH veřejný klíč nastavit na vašem DSVM a přidány do Azure DevOps. Další informace a pokyny naleznete v části **Vytvoření veřejného klíče SSH** v [dodatku platformy a nástroje](platforms-and-tools.md#appendix). 

## <a name="create-a-project-repository-in-your-team-project"></a>Vytvoření úložiště projektu v týmovém projektu

Vytvoření úložiště projektu v projektu **MyTeam** vašeho týmu:

1. Přejděte na stránku **Souhrn** projektu vašeho týmu na *adrese https:\//\<název serveru>/\<název organizace>/\<název týmu>*, například **https:\//dev.azure.com/DataScienceUnit/MyTeam**a z levé navigace vyberte **Repos.** 
   
1. Vyberte název úložiště v horní části stránky a pak v rozevíracím seznamu vyberte **Nový repozitář.**
   
   ![Vybrat nové úložiště](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. V **dialogovém** okně Vytvořit nové úložiště zkontrolujte, jestli je vybraná volba **Git** v části **Typ**. Zadejte *DSProject1* pod **názvem úložiště**a pak vyberte **Vytvořit**.
   
   ![Vytvořit úložiště](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. Potvrďte, že se nové úložiště **DSProject1** zobrazí na stránce nastavení projektu. 
   
   ![Úložiště projektů v nastavení projektu](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>Import šablony týmu do úložiště projektů

Naplnění úložiště projektu obsahem úložiště šablon týmu:

1. Na stránce **Souhrn** projektu vašeho týmu vyberte v levé navigaci **možnost Repos.** 
   
1. Vyberte název úložiště v horní části stránky a v rozevíracím seznamu vyberte **DSProject1.**
   
1. Na **dsprojectu1 je prázdná** stránka, vyberte **Importovat**. 
   
   ![Vybrat import](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. V **dialogovém** okně Importovat úložiště Git vyberte **Git** jako **typ zdroje**a zadejte adresu URL úložiště **TeamTemplate** v části **Adresa URL klonování**. Adresa URL je *\//\<https:\<název serveru\<>/ název organizace\<>/ název týmu>/_git/team template repository>*. Například: **\/https: /dev.azure.com/DataScienceUnit/MyTeam/_git/TeamTemplate**. 
   
1. Vyberte **Importovat**. Obsah úložiště šablon týmu se importuje do úložiště projektu. 
   
   ![Importovat úložiště šablon týmu](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

Pokud potřebujete přizpůsobit obsah úložiště projektu tak, aby vyhovoval specifickým potřebám projektu, můžete přidávat, odstraňovat nebo upravovat soubory a složky úložiště. Můžete pracovat přímo v Azure Repos nebo klonovat úložiště do místního počítače nebo DSVM, provádět změny a potvrdit a push aktualizace do úložiště sdíleného projektu. Postupujte podle pokynů na [vlastní nastavení obsahu týmových úložišť](team-lead-tasks.md#customize-the-contents-of-the-team-repositories).

## <a name="next-steps"></a>Další kroky

Zde jsou odkazy na podrobný popis dalších rolí a úkolů definovaných procesem vědecké vědy o týmových datech:

- [Úlohy správce skupiny pro tým datových věd](group-manager-tasks.md)
- [Úkoly vedoucího týmu pro tým datové vědy](team-lead-tasks.md)
- [Úkoly jednotlivých přispěvatelů pro tým datové vědy](project-ic-tasks.md)
