---
title: Úlohy vedoucího projektu v rámci vědeckého zpracování týmových dat
description: Podrobný návod pro úlohy vedoucí k projektu týmu vědeckého zpracování týmových dat
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9d9b2546553e03d1555cf4c587d699d9a4ea7e51
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321930"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>Úkoly vedoucího projektu v rámci vědeckého zpracování týmových dat

Tento článek popisuje úlohy, které *vedoucí projektu* dokončí, aby nastavil úložiště pro svůj projektový tým v rámci [vědeckého zpracování týmových dat](overview.md) (TDSP). TDSP je architektura vyvinutá Microsoftem, která poskytuje strukturovanou sekvenci aktivit pro efektivní spouštění cloudových řešení prediktivní analýzy. TDSP je navržený tak, aby pomohla zdokonalit spolupráci a týmovou výuku. Přehled rolí personálu a přidružených úloh pro tým pro datové vědy, který se na TDSPe standardizace, najdete v tématu [role procesů a úlohy vědeckého zpracování dat týmu](roles-tasks.md).

Vedoucí projektu spravuje každodenní činnosti jednotlivých vědeckých pracovníků dat v rámci konkrétního projektu pro datové vědy v TDSP. Následující diagram znázorňuje pracovní postup pro úkoly vedoucího projektu:

![Pracovní postup úkolu vedoucího projektu](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Tento kurz popisuje krok 1: vytvoření úložiště projektu a krok 2: počáteční úložiště projektu z vašeho týmového úložiště ProjectTemplate. 

V kroku 3: vytvoření pracovní položky funkce pro projekt a krok 4: Přidání scénářů pro fáze projektu, viz [agilní vývoj projektů pro datové vědy](agile-development.md).

Krok 5: vytvoření a přizpůsobení prostředků úložiště/analýzy a jejich sdílení v případě potřeby najdete v tématu [Vytvoření týmových dat a analytických prostředků](team-lead-tasks.md#create-team-data-and-analytics-resources).

Pro krok 6: nastavení řízení zabezpečení pro úložiště projektu, viz [Přidání členů týmu a konfigurace oprávnění](team-lead-tasks.md#add-team-members-and-configure-permissions).

> [!NOTE] 
> Tento článek používá Azure Repos k nastavení projektu TDSP, protože to znamená, jak implementovat TDSP v Microsoftu. Pokud váš tým používá jinou platformu hostování kódu, úkoly vedoucího projektu jsou stejné, ale způsob jejich dokončení může být jiný.

## <a name="prerequisites"></a>Předpoklady

V tomto kurzu se předpokládá, že váš [správce skupin](group-manager-tasks.md) a [vedoucí týmu](team-lead-tasks.md) si nastavili následující prostředky a oprávnění:

- **Organizace** Azure DevOps pro vaši datovou jednotku
- Týmový **projekt** pro tým pro datové vědy
- Šablony a **úložiště** nástrojů týmu
- **Oprávnění** pro váš účet organizace k vytváření a úpravám úložišť pro váš projekt

Pokud chcete klonovat úložiště a upravovat obsah na vašem místním počítači nebo Data Science Virtual Machine (DSVM) nebo nastavit službu Azure File Storage a připojit ji k DSVM, musíte zvážit tento kontrolní seznam:

- Předplatné Azure.
- V počítači je nainstalovaný Git. Pokud používáte DSVM, Git se předem nainstaluje. V opačném případě si přečtěte článek věnované [platformám a nástrojům](platforms-and-tools.md#appendix).
- Pokud chcete použít DSVM, Windows nebo Linux DSVM vytvořeného a nakonfigurovaného v Azure. Další informace a pokyny najdete v dokumentaci k [Data Science Virtual Machine](../data-science-virtual-machine/index.yml).
- Pro Windows DSVM se na vašem počítači nainstaluje [Správce přihlašovacích údajů Git (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) . V souboru *Readme.MD* se posuňte dolů k části **Stažení a instalace** a vyberte **nejnovější instalační program**. Stáhněte instalační program *. exe* z instalační stránky a spusťte ho. 
- Pro Linux DSVM se v DSVM nastavil veřejný klíč SSH a přidal se do Azure DevOps. Další informace a pokyny najdete v části **vytvoření veřejného klíče SSH** v [příloze platformy a nástroje](platforms-and-tools.md#appendix). 

## <a name="create-a-project-repository-in-your-team-project"></a>Vytvoření úložiště projektu v týmovém projektu

Vytvoření úložiště projektu v projektu **myTeam** vašeho týmu:

1. Přejít na stránku **souhrnu** projektu vašeho týmu na adrese *https \/ / \<server name> / \<organization name> / \<team name> :* například **https: \/ /dev.Azure.com/DataScienceUnit/myTeam** **a v levém navigačním panelu vyberte úložiště** . 
   
1. V horní části stránky vyberte název úložiště a v rozevíracím seznamu vyberte **nové úložiště** .
   
   ![Vybrat nové úložiště](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. V dialogovém okně **vytvořit nové úložiště** se ujistěte, že je v části **typ** zaškrtnuté políčko **Git** . Do **pole název úložiště** zadejte *DSProject1* a pak vyberte **vytvořit**.
   
   ![Vytvořit úložiště](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. Potvrďte, že na stránce nastavení projektu vidíte nové úložiště **DSProject1** . 
   
   ![Úložiště projektu v nastavení projektu](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>Import šablony týmu do úložiště projektu

Chcete-li naplnit úložiště projektu obsahem vašeho úložiště šablony týmu:

1. Na stránce **Souhrn** projektu vašeho **týmu vyberte úložiště** v levém navigačním panelu. 
   
1. V horní části stránky vyberte název úložiště a v rozevíracím seznamu vyberte **DSProject1** .
   
1. Na stránce **DSProject1 je prázdná** vyberte **importovat**. 
   
   ![Vybrat Import](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. V dialogovém okně **importovat úložiště Git** jako **typ zdroje** vyberte **Git** a do pole **Adresa URL klonu** zadejte adresu URL úložiště **TeamTemplate** . Adresa URL je *https: \/ / \<server name> / \<organization name> / \<team name> /_git/ \<team template repository name>*. Příklad: **https: \/ /dev.Azure.com/DataScienceUnit/myTeam/_git/TeamTemplate**. 
   
1. Vyberte **Importovat**. Obsah vašeho úložiště šablony týmu se importuje do vašeho úložiště projektu. 
   
   ![Importovat úložiště šablon týmu](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

Pokud potřebujete přizpůsobit obsah vašeho úložiště projektu tak, aby splňoval konkrétní potřeby vašeho projektu, můžete přidat, odstranit nebo upravit soubory a složky úložiště. Můžete pracovat přímo v Azure Repos nebo klonovat úložiště do místního počítače nebo DSVM, provést změny a zapsat a nasdílet své aktualizace do sdíleného úložiště projektu. Postupujte podle pokynů v tématu [přizpůsobení obsahu úložišť týmu](team-lead-tasks.md#customize-the-contents-of-the-team-repositories).

## <a name="next-steps"></a>Další kroky

Zde jsou odkazy na podrobné popisy dalších rolí a úloh, které jsou definovány v rámci vědeckého zpracování týmových dat:

- [Úlohy správce skupin pro tým pro datové vědy](group-manager-tasks.md)
- [Úkoly vedoucí týmu pro tým pro datové vědy](team-lead-tasks.md)
- [Jednotlivé úlohy přispěvatele pro tým pro datové vědy](project-ic-tasks.md)