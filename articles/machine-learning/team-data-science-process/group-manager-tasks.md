---
title: Úlohy správce skupin procesů týmu pro data vědeckého zpracování
description: Postupujte podle podrobného návodu k úlohám, které správce skupiny dokončí v týmovém projektu pro datové vědy.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7ab6d6511d1e2cec82b321003c9d663249ddcf49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "94740222"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Úlohy správce skupin procesů týmu pro data vědeckého zpracování

Tento článek popisuje úlohy, které *správce skupiny* dokončí pro organizaci pro datové vědy. Správce skupin spravuje celou jednotku datových věd v podniku. Jednotka pro datové vědy může mít několik týmů, z nichž každý pracuje na mnoha projektech pro datové vědy v různých obchodních svislých. Cílem správce skupin je vytvořit skupinové prostředí pro spolupráci, které se bude standardizovat v rámci [vědeckého zpracování týmových dat](overview.md) (TDSP). Přehled všech rolí personálu a přidružených úloh, které jsou zpracovávány týmem pro datové vědy na TDSP, najdete v tématu [role procesů a úlohy vědeckého zpracování dat týmu](roles-tasks.md).

Následující diagram znázorňuje šest hlavních úloh instalace správce skupin. Správci skupin mohou delegovat své úkoly na náhrady, ale úkoly přidružené k této roli se nezmění.

![Úlohy správce skupin](./media/group-manager-tasks/tdsp-group-manager.png)

1. Pro skupinu nastavte **organizaci Azure DevOps** .
2. Vytvořte výchozí **projekt GroupCommon** v organizaci Azure DevOps.
3. Vytvořte úložiště **GroupProjectTemplate** v Azure Repos.
4. Vytvořte úložiště **GroupUtilities** v Azure Repos.
5. Importujte obsah úložišť **ProjectTemplate** a **nástrojů** týmu Microsoft TDSP do společného úložiště skupin.
6. Nastavte **členství** a **oprávnění** pro členy týmu pro přístup ke skupině.

Následující kurz vás provede podrobnými kroky. 

> [!NOTE] 
> Tento článek používá Azure DevOps k nastavení prostředí skupiny TDSP, protože to implementuje TDSP v Microsoftu. Pokud vaše skupina používá jiné hostování kódu nebo vývojové platformy, jsou úkoly správce skupin stejné, ale způsob jejich dokončení může být jiný.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Vytvoření organizace a projektu v Azure DevOps

1. Přejít na [VisualStudio.Microsoft.com](https://visualstudio.microsoft.com), vyberte možnost **Přihlásit** se v pravém horním rohu a přihlaste se k účet Microsoft. 
   
   ![Přihlaste se ke svému účet Microsoft](./media/group-manager-tasks/signinvs.png)
   
   Pokud nemáte účet Microsoft, vyberte **zaregistrovat** se a vytvořte účet Microsoft a přihlaste se pomocí tohoto účtu. Pokud má vaše organizace předplatné sady Visual Studio, přihlaste se pomocí přihlašovacích údajů k tomuto předplatnému.
   
1. Po přihlášení klikněte v pravém horním rohu na stránce Azure DevOps vyberte **vytvořit novou organizaci**.
   
   ![Vytvořit novou organizaci](./media/group-manager-tasks/create-organization.png)
   
1. Pokud se zobrazí výzva k vyjádření souhlasu s podmínkami služby, prohlášením o zásadách ochrany osobních údajů a pravidly chování, vyberte **pokračovat**.
   
1. V dialogovém okně přihlášení pojmenujte organizaci Azure DevOps a přijměte přiřazení oblasti hostitele, nebo rozevírací seznam vyberte jinou oblast. Potom vyberte **Pokračovat**. 

1. V části **vytvořit projekt pro** začátek zadejte *GroupCommon* a pak vyberte **vytvořit projekt**. 
   
   ![Vytvoření projektu](./media/group-manager-tasks/create-project.png)

Otevře se stránka **Souhrn** projektu **GroupCommon** . Adresa URL stránky je *https: \/ / \<servername> / \<organization-name> /GroupCommon*.

![Stránka souhrnu projektu](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>Nastavení společného úložiště skupin

Azure Repos hostuje následující typy úložišť pro skupinu:

- **Seskupit společná úložiště**: úložiště pro obecné účely, které může pro mnoho projektů pro datové vědy přijmout více týmů v rámci datové vědy. 
- **Týmová úložiště**: úložiště pro konkrétní týmy v rámci datové vědy jednotky. Tato úložiště jsou specifická pro potřeby týmu a můžou se používat pro více projektů v rámci tohoto týmu, ale nejsou dostatečně obecné, aby je bylo možné používat v rámci více týmů v rámci datové vědy jednotky.
- Úložiště **projektů**: úložiště pro konkrétní projekty. Taková úložiště nemusí být dostatečně všeobecně pro více projektů v rámci týmu nebo pro jiné týmy v datové vědy jednotce.

Chcete-li nastavit společné úložiště skupin ve vašem projektu, postupujte takto: 
- Přejmenujte výchozí úložiště **GroupCommon** na **GroupProjectTemplate** .
- Vytvořit nové úložiště **GroupUtilities**

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>Přejmenujte výchozí úložiště projektu na GroupProjectTemplate.

Chcete-li přejmenovat výchozí úložiště projektu **GroupCommon** na **GroupProjectTemplate**:

1. Na stránce **Souhrn** projektu **GroupCommon** **Vyberte úložiště**. Tato akce přejde do výchozího úložiště **GroupCommon** projektu GroupCommon, který je aktuálně prázdný.
   
1. V horní části stránky rozbalte šipku vedle **GroupCommon** a vyberte **Spravovat úložiště**.
   
   ![Spravovat úložiště](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. Na stránce **nastavení projektu** vyberte **...** vedle **GroupCommon** a pak vyberte **Přejmenovat úložiště**. 
   
   ![Vybrat... a pak vyberte Přejmenovat úložiště](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. V místní nabídce **přejmenování úložiště GroupCommon** zadejte *GroupProjectTemplate* a pak vyberte **Přejmenovat**. 
   
   ![Přejmenovat úložiště](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>Vytvoření úložiště GroupUtilities

Vytvoření úložiště **GroupUtilities** :

1. Na stránce **Souhrn** projektu **GroupCommon** **Vyberte úložiště**. 
   
1. V horní části stránky rozbalte šipku vedle **GroupProjectTemplate** a vyberte **nové úložiště**.
   
   ![Vybrat nové úložiště](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. V dialogovém okně **vytvořit nové úložiště** jako **typ** vyberte **Git** , jako **název úložiště** zadejte *GroupUtilities* a pak vyberte **vytvořit**.
   
   ![Vytvořit úložiště GroupUtilities](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. Na stránce **nastavení projektu** vyberte **úložiště** v části úložiště v **levém** navigačním panelu, abyste viděli dvě úložiště skupin: **GroupProjectTemplate** a **GroupUtilities**.
   
   ![Dvě úložiště skupin](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>Import úložišť týmu Microsoft TDSP

V této části kurzu naimportujete obsah úložišť **ProjectTemplate** a **nástrojů** , která spravuje tým Microsoft TDSP, do úložišť **GroupProjectTemplate** a **GroupUtilities** . 

Import úložišť týmu TDSP:

1. Na domovské stránce projektu **GroupCommon** v levém navigačním **panelu vyberte úložiště** . Otevře se výchozí úložiště **GroupProjectTemplate** . 
   
1. Na stránce **GroupProjectTemplate je prázdná** vyberte **importovat**. 
   
   ![Vybrat Import](./media/group-manager-tasks/import-repo.png)
   
1. V dialogovém okně **importovat úložiště Git** jako **typ zdroje** vyberte **Git** a jako **adresu URL klonu** zadejte *https: \/ /GitHub.com/Azure/Azure-TDSP-ProjectTemplate.Git* . Pak vyberte **importovat**. Obsah úložiště Microsoft TDSP Team ProjectTemplate se importuje do úložiště GroupProjectTemplate. 
   
   ![Importovat týmový úložiště Microsoft TDSP](./media/group-manager-tasks/import-repo-2.png)
   
1. V horní části stránky **úložišť** rozbalte rozevírací seznam a vyberte úložiště **GroupUtilities** .
   
Každé ze dvou úložišť skupin teď obsahuje všechny soubory kromě těch, které jsou v adresáři *. Git* , z odpovídajícího úložiště týmu Microsoft TDSP. 

## <a name="customize-the-contents-of-the-group-repositories"></a>Přizpůsobení obsahu úložišť skupin

Pokud chcete přizpůsobit obsah úložišť skupin tak, aby splňovala konkrétní potřeby vaší skupiny, můžete to udělat hned teď. Můžete upravovat soubory, měnit adresářovou strukturu nebo přidávat soubory, které vytvořila vaše skupina, nebo které jsou užitečné pro vaši skupinu.

### <a name="make-changes-in-azure-repos"></a>Provést změny v Azure Repos

Postup přizpůsobení obsahu úložiště:

1. Na stránce **Souhrn** projektu **GroupCommon** **Vyberte úložiště**. 
   
1. V horní části stránky vyberte úložiště, které chcete upravit.

1. V adresářové struktuře úložiště přejděte do složky nebo souboru, který chcete změnit. 
   
   - Chcete-li vytvořit nové složky nebo soubory, vyberte šipku vedle tlačítka **Nový**. 
     
     ![Vytvořit nový soubor](./media/group-manager-tasks/new-file.png)
     
   - Chcete-li odeslat soubory, vyberte možnost **nahrát** soubory. 
     
     ![Nahrání souborů](./media/group-manager-tasks/upload-files.png)
     
   - Pokud chcete upravit existující soubory, přejděte k souboru a pak vyberte **Upravit**. 
     
     ![Upravit soubor](./media/group-manager-tasks/edit-file.png)
     
1. Po přidání nebo úpravě souborů vyberte **Potvrdit**.
   
   ![Potvrdit změny](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>Provádění změn pomocí místního počítače nebo DSVM

Pokud chcete provádět změny pomocí místního počítače nebo DSVM a vložit změny do úložišť skupiny, ujistěte se, že máte předpoklady pro práci s Git a DSVMs:

- Předplatné Azure, pokud chcete vytvořit DSVM.
- V počítači je nainstalovaný Git. Pokud používáte DSVM, Git se předem nainstaluje. V opačném případě si přečtěte článek věnované [platformám a nástrojům](platforms-and-tools.md#appendix).
- Pokud chcete použít DSVM, Windows nebo Linux DSVM vytvořeného a nakonfigurovaného v Azure. Další informace a pokyny najdete v dokumentaci k [Data Science Virtual Machine](../data-science-virtual-machine/index.yml).
- Pro Windows DSVM se na vašem počítači nainstaluje [Správce přihlašovacích údajů Git (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) . V souboru *Readme.MD* se posuňte dolů k části **Stažení a instalace** a vyberte **nejnovější instalační program**. Stáhněte instalační program *. exe* z instalační stránky a spusťte ho. 
- Pro Linux DSVM se v DSVM nastavil veřejný klíč SSH a přidal se do Azure DevOps. Další informace a pokyny najdete v části **vytvoření veřejného klíče SSH** v [příloze platformy a nástroje](platforms-and-tools.md#appendix). 

Nejdřív zkopírujte nebo *naklonujte* úložiště do místního počítače. 
   
1. Na stránce **Souhrn** projektu **GroupCommon** **Vyberte úložiště a v** horní části stránky vyberte úložiště, které chcete klonovat.
   
1. Na stránce úložiště vyberte **klonovat** v pravém horním rohu.
   
1. V dialogu **úložiště klonování** vyberte **https** pro připojení HTTP nebo **SSH** pro připojení SSH a zkopírujte adresu URL klonování do **příkazového řádku** do schránky.
   
   ![Klonovat úložiště](./media/group-manager-tasks/clone.png)
   
1. Na místním počítači vytvořte následující adresáře:
   
   - Pro Windows: **C:\GitRepos\GroupCommon**
   - Pro Linux, **$/GitRepos/GroupCommon** v domovském adresáři 
   
1. Přejděte do adresáře, který jste vytvořili.
   
1. V Gitu bash spusťte příkaz. `git clone <clone URL>.`
   
   Například některý z následujících příkazů naklonuje úložiště **GroupUtilities** do adresáře *GroupCommon* na místním počítači. 
   
   **Připojení HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **Připojení SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

Až provedete libovolné změny v místním klonu úložiště, můžete tyto změny vložit do společného úložiště sdílené skupiny. 

Spusťte následující příkazy Git bash z místního adresáře **GroupProjectTemplate** nebo **GroupUtilities** .

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Pokud se jedná o první potvrzení do úložiště Git, možná budete muset před spuštěním příkazu nakonfigurovat globální parametry *User.Name* a *User.email* `git commit` . Spusťte následující dva příkazy:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Pokud potvrdíte několik úložišť Git, použijte stejný název a e-mailovou adresu pro všechny z nich. Použití stejného jména a e-mailové adresy je vhodné při vytváření Power BI řídicích panelů ke sledování aktivit Git ve více úložištích.

## <a name="add-group-members-and-configure-permissions"></a>Přidat členy skupiny a nakonfigurovat oprávnění

Přidání členů do skupiny:

1. V Azure DevOps na domovské stránce projektu **GroupCommon** v levém navigačním panelu vyberte **nastavení projektu** . 
   
1. V **nastavení projektu** vlevo navigace vyberte **týmy** a pak na stránce **týmy** vyberte **tým GroupCommon**. 
   
   ![Konfigurace týmů](./media/group-manager-tasks/teams.png)
   
1. Na stránce **týmový profil** vyberte **Přidat**.
   
   ![Přidat do GroupCommon týmu](./media/group-manager-tasks/add-to-team.png)
   
1. V dialogovém okně **Přidat uživatele a skupiny** vyhledejte a vyberte členy, které chcete přidat do skupiny, a pak vyberte **Uložit změny**. 
   
   ![Přidat uživatele a skupiny](./media/group-manager-tasks/add-users.png)
   

Konfigurace oprávnění pro členy:

1. V **nastavení projektu** vlevo navigace vyberte **oprávnění**. 
   
1. Na stránce **oprávnění** vyberte skupinu, do které chcete přidat členy. 
   
1. Na stránce pro tuto skupinu vyberte **členy** a pak vyberte **Přidat**. 
   
1. V místní nabídce **pozvat členy** vyhledejte a vyberte členy, které chcete přidat do skupiny, a pak vyberte **Uložit**. 
   
   ![Udělení oprávnění členům](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>Další kroky

Tady jsou odkazy na podrobné popisy dalších rolí a úloh v rámci vědeckého zpracování týmových dat:

- [Úkoly vedoucí týmu pro tým pro datové vědy](team-lead-tasks.md)
- [Úkoly vedoucího projektu pro tým pro datové vědy](project-lead-tasks.md)
- [Jednotlivé úkoly přispěvatele projektu pro tým pro datové vědy](project-ic-tasks.md)
