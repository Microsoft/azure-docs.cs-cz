---
title: Úkoly správce skupiny Team Data Science Process
description: Postupujte podle tohoto podrobného návodu úkolů, které správce skupiny dokončí v projektu týmu datové vědy.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ec7f4242e5046e90fdf0eb8c6c0579f402e4f55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721349"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Úkoly správce skupiny Team Data Science Process

Tento článek popisuje úkoly, které *dokončí správce skupiny* pro organizaci datové vědy. Správce skupiny spravuje celou jednotku datové vědy v podniku. Jednotka datové vědy může mít několik týmů, z nichž každý pracuje na mnoha projektech datové vědy v různých obchodních vertikálách. Cílem manažera skupiny je vytvořit prostředí skupiny pro spolupráci, které standardizuje [proces vědecké vědy o týmových datech](overview.md) (TDSP). Přehled všech rolí personálu a přidružených úkolů zpracovávaných týmem pro datové vědy, který standardizuje standardizaci v oblasti TDSP, naleznete v [tématu Role a úkoly procesu vědeckého zpracování týmových dat](roles-tasks.md).

Následující diagram znázorňuje šest hlavních úloh nastavení správce skupiny. Správci skupin mohou delegovat své úkoly na náhradníky, ale úkoly spojené s rolí se nemění.

![Úkoly správce skupiny](./media/group-manager-tasks/tdsp-group-manager.png)

1. Nastavte organizaci **Azure DevOps** pro skupinu.
2. Vytvořte výchozí **projekt GroupCommon** v organizaci Azure DevOps.
3. Vytvořte úložiště **GroupProjectTemplate** v Azure Repos.
4. Vytvořte úložiště **GroupUtilities** v Azure Repos.
5. Importujte obsah repozitářů **ProjectTemplate** a **Utilities** týmu Microsoft TDSP do společných úložišť skupiny.
6. Nastavte **členství** a **oprávnění** pro členy týmu pro přístup ke skupině.

Následující kurz podrobně provede kroky. 

> [!NOTE] 
> Tento článek používá Azure DevOps k nastavení prostředí skupiny TDSP, protože to je, jak implementovat TDSP u Microsoftu. Pokud vaše skupina používá jiné platformy pro hostování kódu nebo vývoj, úkoly správce skupiny jsou stejné, ale způsob jejich dokončení se může lišit.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Vytvoření organizace a projektu v Azure DevOps

1. Přejděte na [visualstudio.microsoft.com](https://visualstudio.microsoft.com), **vyberte Přihlásit** se vpravo nahoře a přihlaste se ke svému účtu Microsoft. 
   
   ![Přihlášení k účtu Microsoft](./media/group-manager-tasks/signinvs.png)
   
   Pokud účet Microsoft nemáte, vyberte **Zaregistrovat se ,** vytvořte si účet Microsoft a přihlaste se pomocí tohoto účtu. Pokud má vaše organizace předplatné Sady Visual Studio, přihlaste se pomocí přihlašovacích údajů pro toto předplatné.
   
1. Po přihlášení v pravém horním bodě na stránce Azure DevOps vyberte **Vytvořit novou organizaci**.
   
   ![Vytvořit novou organizaci](./media/group-manager-tasks/create-organization.png)
   
1. Pokud budete vyzváni k souhlasu se smluvními podmínkami, prohlášením o ochraně osobních údajů a etickým kodexem, vyberte **pokračovat**.
   
1. V dialogovém okně registrace pojmenujte organizaci Azure DevOps a přijměte přiřazení oblasti hostitele nebo rozbalte a vyberte jinou oblast. Potom vyberte **Pokračovat**. 

1. V části **Vytvořit projekt, abyste mohli začít**, zadejte *GroupCommon*a vyberte **Vytvořit projekt**. 
   
   ![Vytvoření projektu](./media/group-manager-tasks/create-project.png)

Otevře se stránka **Souhrn** projektu **GroupCommon.** Adresa URL stránky je *\//\<https:\<název serveru>/ název organizace>/GroupCommon*.

![Stránka souhrnu projektu](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>Nastavení společných úložišť skupiny

Azure Repos hostuje následující typy úložišť pro vaši skupinu:

- **Seskupení společných úložišť**: Univerzální úložiště, která může přijmout více týmů v rámci jednotky datové vědy pro mnoho projektů datové vědy. 
- **Týmové repozitáře**: Repozitáře pro konkrétní týmy v rámci jednotky datové vědy. Tato úložiště jsou specifická pro potřeby týmu a mohou být použita pro více projektů v rámci tohoto týmu, ale nejsou dostatečně obecné pro použití ve více týmech v rámci jednotky datové vědy.
- **Repozitáře projektů**: Repozitáře pro konkrétní projekty. Taková úložiště nemusí být dostatečně obecná pro více projektů v rámci týmu nebo pro jiné týmy v jednotce datové vědy.

Chcete-li v projektu nastavit společné úložiště skupiny, můžete: 
- Přejmenování výchozího úložiště **GroupCommon** na **GroupProjectTemplate**
- Vytvoření nového úložiště **GroupUtilities**

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>Přejmenování výchozího úložiště projektu na GroupProjectTemplate

Přejmenování výchozího úložiště projektů **GroupCommon** na **GroupProjectTemplate**:

1. Na stránce **Souhrn** projektu **GroupCommon** vyberte **možnost Repos**. Tato akce vás přenese do výchozího úložiště **GroupCommon** projektu GroupCommon, které je aktuálně prázdné.
   
1. V horní části stránky rozbalte šipku vedle **položky GroupCommon** a vyberte **Spravovat úložiště**.
   
   ![Správa úložišť](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. Na stránce **Nastavení projektu** vyberte **...** vedle **Položky GroupCommon**a pak **vyberte Přejmenovat úložiště**. 
   
   ![Vyberte... a pak vyberte Přejmenovat úložiště](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. V místní části **Přejmenovat groupcommon repozitář** zadejte *GroupProjectTemplate*a pak vyberte **Přejmenovat**. 
   
   ![Přejmenovat úložiště](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>Vytvoření úložiště GroupUtilities

Vytvoření úložiště **GroupUtilities:**

1. Na stránce **Souhrn** projektu **GroupCommon** vyberte **možnost Repos**. 
   
1. V horní části stránky rozbalte šipku vedle **groupprojecttemplate** a vyberte **Nové úložiště**.
   
   ![Vybrat nové úložiště](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. V **dialogovém** okně Vytvořit nové úložiště vyberte Jako **typ** **Git** , zadejte *GroupUtilities* jako **název úložiště**a pak vyberte **Vytvořit**.
   
   ![Vytvořit úložiště GroupUtilities](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. Na stránce **Nastavení projektu** vyberte úložiště v části **Úložiště** v levém navigačním panelu, abyste viděli dvě úložiště skupin: **GroupProjectTemplate** a **GroupUtilities**. **Repos**
   
   ![Dvě skupinová úložiště](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>Import týmových úložišť Microsoft TDSP

V této části kurzu importujete obsah úložišť **ProjectTemplate** a **Utilities** spravovaných týmem Microsoft TDSP do úložišť **GroupProjectTemplate** a **GroupUtilities.** 

Import týmových úložišť TDSP:

1. Na domovské stránce projektu **GroupCommon** vyberte v levé navigaci **možnost Repos.** Otevře se výchozí repo **šablony GroupProjectTemplate.** 
   
1. Na stránce **GroupProjectTemplate je prázdná** stránka vyberte **Importovat**. 
   
   ![Vybrat import](./media/group-manager-tasks/import-repo.png)
   
1. V **dialogovém** okně Importovat úložiště Git vyberte **Git** jako **typ zdroje**a zadejte *https:\//github.com/Azure/Azure-TDSP-ProjectTemplate.git* pro **adresu URL klonování**. Pak vyberte **Importovat**. Obsah úložiště Microsoft TDSP týmu ProjectTemplate se importuje do úložiště GroupProjectTemplate. 
   
   ![Import týmového úložiště Microsoft TDSP](./media/group-manager-tasks/import-repo-2.png)
   
1. V horní části stránky **Úložiště** rozbalte a vyberte úložiště **GroupUtilities.**
   
1. Postupem importu opakujte a importujte obsah úložiště Microsoft TDSP team **Utilities,** *\/https: /github.com/Azure/Azure-TDSP-Utilities.git*, do úložiště **GroupUtilities.** 
   
Každé z vašich dvou úložišť skupin nyní obsahuje všechny soubory, s výjimkou souborů v adresáři *GIT,* z odpovídajícího úložiště týmu Microsoft TDSP. 

## <a name="customize-the-contents-of-the-group-repositories"></a>Přizpůsobení obsahu repozitářů skupiny

Pokud chcete přizpůsobit obsah repozitářů skupiny tak, aby vyhovoval specifickým potřebám vaší skupiny, můžete to udělat nyní. Můžete upravit soubory, změnit strukturu adresářů nebo přidat soubory, které vaše skupina vyvinula nebo které jsou pro vaši skupinu užitečné.

### <a name="make-changes-in-azure-repos"></a>Provádění změn v azure repos

Přizpůsobení obsahu úložiště:

1. Na stránce **Souhrn** projektu **GroupCommon** vyberte **možnost Repos**. 
   
1. V horní části stránky vyberte úložiště, které chcete přizpůsobit.

1. Ve struktuře adresářů úložiště přejděte do složky nebo souboru, který chcete změnit. 
   
   - Chcete-li vytvořit nové složky nebo soubory, vyberte šipku vedle položky **Nový**. 
     
     ![Vytvořit nový soubor](./media/group-manager-tasks/new-file.png)
     
   - Chcete-li nahrát soubory, vyberte **Nahrát soubory**. 
     
     ![Nahrání souborů](./media/group-manager-tasks/upload-files.png)
     
   - Chcete-li upravit existující soubory, přejděte do souboru a vyberte **upravit**. 
     
     ![Úprava souboru](./media/group-manager-tasks/edit-file.png)
     
1. Po přidání nebo úpravě souborů vyberte **Potvrdit**.
   
   ![Potvrzení změn](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>Provádění změn pomocí místního počítače nebo dsvm

Pokud chcete provádět změny pomocí místního počítače nebo DSVM a posunout změny do repozitářů skupin, ujistěte se, že máte předpoklady pro práci s Git a DSVM:

- Předplatné Azure, pokud chcete vytvořit DSVM.
- Git nainstalovaný na vašem počítači. Pokud používáte DSVM, Git je předinstalovaný. V opačném případě se podívejte do [dodatku k platformám a nástrojům](platforms-and-tools.md#appendix).
- Pokud chcete použít DSVM, Windows nebo Linux DSVM vytvořené a nakonfigurované v Azure. Další informace a pokyny naleznete v [dokumentaci k virtuálním strojům pro datové vědy](/azure/machine-learning/data-science-virtual-machine/).
- Pro systém Windows DSVM je v počítači nainstalován [Správce přihlašovacích údajů (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) gitu. V *souboru README.md* přejděte dolů do části **Stáhnout a nainstalovat** a vyberte nejnovější instalační **program**. Stáhněte instalační *program .exe* ze stránky instalačního programu a spusťte jej. 
- Pro Linux DSVM, SSH veřejný klíč nastavit na vašem DSVM a přidány do Azure DevOps. Další informace a pokyny naleznete v části **Vytvoření veřejného klíče SSH** v [dodatku platformy a nástroje](platforms-and-tools.md#appendix). 

Nejprve zkopírujte nebo *naklonujte* úložiště do místního počítače. 
   
1. Na stránce **Souhrn** projektu **GroupCommon** vyberte **Úložiště**a v horní části stránky vyberte úložiště, které chcete klonovat.
   
1. Na stránce repo vyberte **Klonování** vpravo nahoře.
   
1. V **dialogovém** okně Úložiště klonování vyberte pro připojení HTTP **protokol HTTPS** nebo **SSH** pro připojení SSH a zkopírujte adresu URL klonu pod **příkazovým řádkem** do schránky.
   
   ![Klonovat repo](./media/group-manager-tasks/clone.png)
   
1. V místním počítači vytvořte následující adresáře:
   
   - Pro Windows: **C:\GitRepos\GroupCommon**
   - Pro Linux, **$/GitRepos/GroupCommon** na vašem domovském adresáři 
   
1. Změňte adresář, který jste vytvořili.
   
1. V Git Bash spusťte příkaz`git clone <clone URL>.`
   
   Například některý z následujících příkazů klonuje úložiště **GroupUtilities** do adresáře *GroupCommon* v místním počítači. 
   
   **Připojení HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **Připojení SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

Po provedení jakékoli změny, které chcete v místním klonu úložiště, můžete tlačit změny do společných úložišť sdílené skupiny. 

Spusťte následující příkazy Git Bash z místního adresáře **GroupProjectTemplate** nebo **GroupUtilities.**

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Pokud je to poprvé, co se potvrdíte do úložiště Git, budete muset před `git commit` spuštěním příkazu nakonfigurovat globální parametry *user.name* a *user.email.* Spusťte následující dva příkazy:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Pokud se zavazujete k několika úložištím Git, použijte pro všechny stejné jméno a e-mailovou adresu. Použití stejného jména a e-mailové adresy je vhodné při vytváření řídicích panelů Power BI ke sledování aktivit Gitu ve více repozitářích.

## <a name="add-group-members-and-configure-permissions"></a>Přidání členů skupiny a konfigurace oprávnění

Přidání členů do skupiny:

1. V Azure DevOps, na domovské stránce projektu **GroupCommon,** vyberte **nastavení projektu** z levé navigace. 
   
1. V levém **navigačním panelu Nastavení projektu** vyberte **Týmy**a potom na stránce **Týmy** vyberte **GroupCommon Team**. 
   
   ![Konfigurace týmů](./media/group-manager-tasks/teams.png)
   
1. Na stránce **Profil týmu** vyberte **Přidat**.
   
   ![Přidat do společného týmu Skupiny](./media/group-manager-tasks/add-to-team.png)
   
1. V dialogovém okně **Přidat uživatele a skupiny** vyhledejte a vyberte členy, které chcete přidat do skupiny, a pak vyberte **Uložit změny**. 
   
   ![Přidání uživatelů a skupin](./media/group-manager-tasks/add-users.png)
   

Konfigurace oprávnění pro členy:

1. V levém navigačním **panelu Nastavení projektu** vyberte **oprávnění**. 
   
1. Na stránce **Oprávnění** vyberte skupinu, do které chcete členy přidat. 
   
1. Na stránce pro tuto skupinu vyberte **Členové**a pak vyberte **Přidat**. 
   
1. Ve vyskakovacím blokování **členů** vyhledání a výběru členů, které chcete přidat do skupiny, a pak vyberte **Uložit**. 
   
   ![Udělit oprávnění členům](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>Další kroky

Zde jsou odkazy na podrobný popis dalších rolí a úkolů v procesu vědecké vědy o týmových datech:

- [Úkoly vedoucího týmu pro tým datové vědy](team-lead-tasks.md)
- [Úkoly vedoucího projektu pro tým datové vědy](project-lead-tasks.md)
- [Projekt ové úkoly jednotlivých přispěvatelů pro tým pro datové vědy](project-ic-tasks.md)
