---
title: Úvodní úlohy pro projekt vědeckého zpracování týmových dat
description: Přehled úloh, vedoucí projektu by měl dokončit v týmovém projektu datové vědy.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 39e8c5b8dab33ba95b34c4edb9d0a994bc8ec6dc
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135160"
---
# <a name="tasks-for-the-project-lead-in-the-team-data-science-process"></a>Úvodní úlohy pro projekt vědeckého zpracování týmových dat

Tento kurz ukazuje úlohy, které je vedoucí projektu očekává pro jeho tým projektu. Cílem je vytvořit prostředí pro spolupráci týmu, který standardizuje na [vědecké zpracování týmových dat](overview.md) (TDSP). TDSP je architektura vyvinutý microsoftem, která poskytuje strukturovaných posloupnost aktivit k úspěšnému provedení cloudové prediktivní analytická řešení. Přehled role pracovníky a jejich přidružených úloh, které jsou zpracovávány týmem datové vědy standardizovat o tomto procesu najdete v části [vědecké zpracování týmových dat role a úlohy](roles-tasks.md).

A **vedoucí projektu** spravuje každodenní činnosti jednotlivé datové vědce v projektu konkrétní datové vědy. Pracovní postup pro úkoly, které mají být dokončit zájemci o projekt k nastavení tohoto prostředí jsou znázorněné na následujícím obrázku:

![1](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Toto téma popisuje aktuálně úlohy 1,2 a 6 tento pracovní postup pro zájemci o projekt.

> [!NOTE]
> Uvádíme kroky potřebné k nastavení prostředí TDSP týmu projektu pomocí Azure DevOps v následujících pokynech. Můžeme určit způsob k provedení těchto úloh s Azure DevOps, protože to je, jak můžeme implementovat TDSP v Microsoftu. Pokud je pro vaši skupinu pro jinou platformu pro hosting kódu, úlohy, které musíte provést vedoucí týmu obecně se nezmění. Ale způsob k provedení těchto úloh se bude lišit.


## <a name="repositories-and-directories"></a>Úložiště a adresáře

Tento kurz používá zkrácené názvy pro úložiště a adresáře. Tyto názvy usnadňují sledování operací mezi úložišť a adresáře. Tato notace (R pro úložiště Git) a D pro místní adresáře na vaše DSVM se používá v následujících částech:

- **R3**: tým **ProjectTemplate** úložiště v Gitu, které nastavil váš vedoucí týmu.
- **R5**: V úložišti projektů na Git můžete nastavit pro váš projekt.
- **D3**: místní adresář naklonovali z R3.
- **D5**: místní adresář naklonovali z R5.


## <a name="0-prerequisites"></a>0. Požadavky

Požadavky splněny dokončení úkolů přiřazena vedoucímu skupiny uvedených v [úkoly správce skupiny pro datové vědy tým](group-manager-tasks.md) a k seskupení zájemce uvedených v [úkoly vedoucí týmu pro datové vědy tým](team-lead-tasks.md). 

Slouží ke shrnutí tady, třeba tyto požadavky splnit, předtím, než začnete s úkoly vedoucí týmu: 

- Vaše **skupině služby Azure DevOps** (nebo skupinový účet na některé jiné platformě hosting kódu) je nastavený správcem skupiny.
- Vaše **TeamProjectTemplate úložiště** (R3) je nastavený v rámci vašeho účtu skupiny ve vaší vedoucí týmu na hosting kódu platformy, plánujete použít.
- Byli jste **oprávnění** podle váš vedoucí týmu k vytvoření úložiště ve vašem účtu skupiny pro váš tým.
- Git musí být nainstalován na počítači. Pokud používáte Data virtuálního počítače VĚDY, Git je předem nainstalovaný a jste připravení. V opačném případě najdete v článku [platformami a nástroji příloha](platforms-and-tools.md#appendix).  
- Pokud používáte **Windows DSVM**, musíte mít [Git Credential Manageru (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) na vašem počítači nainstalovaný. V souboru README.md přejděte dolů k položce **stáhněte a nainstalujte** a klikněte *nejnovější instalační program*. Tím přejdete na stránku pro nejnovější instalační program. Stažení instalačního programu .exe odsud a spustíme ji. 
- Pokud používáte **Linux DSVM**vytvořte veřejný klíč SSH na vaše DSVM a přidejte k vašim službám Azure DevOps skupiny. Další informace o SSH najdete v tématu **vytvořit veřejný klíč SSH** tématu [platformami a nástroji příloha](platforms-and-tools.md#appendix). 


## <a name="1-create-a-project-repository-r5"></a>1. Vytvořit projekt úložiště (R5)

- Přihlaste se k vašim službám skupinu Azure DevOps v *https://\<název služby Azure DevOps\>. visualstudio.com*. 
- V části **poslední projekty a týmy**, klikněte na tlačítko **Procházet**. Okna, která se zobrazí seznam všech projektů ve službě Azure DevOps. 

    ![2](./media/project-lead-tasks/project-leads-2-create-project-repo.png)

- Klikněte na název projektu, ve které se chystáte vytvořit úložiště vašeho projektu. V tomto příkladu klikněte na tlačítko **MyTeam**. 
- Potom klikněte na **Navigovat** k přesměrováni na domovskou stránku projektu **MyTeam**:

    ![3](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)

- Klikněte na tlačítko **spolupráce na kódu** k přesměrováni na domovskou stránku projektu git.  

    ![4](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

- Klikněte na šipku dolů v levém horním rohu a vyberte **+ nové úložiště**. 
    
    ![5](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)

- V **vytvořit nové úložiště** okno, zadejte název pro úložiště git vašeho projektu. Ujistěte se, že jste vybrali **Git** jako typ úložiště. V tomto příkladu používáme název *DSProject1*. 

    ![6](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

- Chcete-li vytvořit váš ***DSProject1*** projektu úložiště git, klikněte na tlačítko **vytvořit**.


## <a name="2-seed-the-dsproject1-project-repository"></a>2. Počáteční hodnoty v úložišti projektů DSProject1

Je zde úkol na počáteční hodnotu **DSProject1** úložiště projektu (R5) z úložiště šablony projektu (R3). Osazení postup používá adresáře D3 a D5 na vaše místní DSVM jako zprostředkující pracovní lokality. Stručně řečeno, osazení cestu je: D3-R3 > -> D5 -> R5.

Pokud je potřeba upravit vaše **DSProject1** úložiště projektu pro splnění některé specifické potřeby projektu, uděláte v předposlední kroku tohoto postupu. Zde je uveden seznam kroky používají naplnit obsah **DSProject1** úložiště projektu. Témata v postupu osazení odpovídají jednotlivé kroky:

- Klonování úložiště šablon projektu do místního adresáře: týmu R3 - do -> místní D3 klonovat.
- Klonování úložiště DSProject1 do místního adresáře: týmu R5 - klonovat do místního D5 ->.
- Zkopírujte obsah šablony naklonované projektu do místní klon úložiště DSProject1: D3 - obsah bude zkopírován do -> D5.
- (Volitelné) Místní D5 přizpůsobení.
- Předávaný obsah místní DSProject1 do týmových úložišť: D5 – obsah, přidejte do týmu R5 ->.


### <a name="clone-your-project-template-repository-r3-to-a-directory-d3-on-your-local-machine"></a>Klonování úložiště šablony projektu (R3) do adresáře (D3) na místním počítači.

Na místním počítači vytvořte adresář:

- *C:\GitRepos\MyTeamCommon* pro Windows 
- *$home/GitRepos/MyTeamCommon* pro Linux

Přechod do této složky. Spusťte následující příkaz a naklonujte úložiště šablony projektu do místního počítače. 

**Windows**
            
    git clone <the HTTPS URL of the TeamProjectTemplate repository>
    
Pokud používáte Azure DevOps jako platformu pro hosting kódu, obvykle *adresy URL HTTPS úložiště šablony projektu* je:

 ***https://\<název služby Azure DevOps\>.visualstudio.com/\<název vašeho projektu\>/_git/\<úložiště název šablony projektu\>***. 

V tomto příkladu jsme provedli následující:

***https://mysamplegroup.visualstudio.com/MyTeam/_git/MyTeamProjectTemplate***. 

![7](./media/project-lead-tasks/project-leads-7-clone-team-project-template.png)
            
**Linux**

    git clone <the SSH URL of the TeamProjectTemplate repository>
        
![8](./media/project-lead-tasks/project-leads-8-clone-team-project-template-linux.png)

Pokud používáte Azure DevOps jako platformu pro hosting kódu, obvykle *SSH adresu URL úložiště šablon projektu* je:

***SSH: / /\<název služby Azure DevOps\>@\<název služby Azure DevOps\>.visualstudio.com:22/\<svůj název projektu > /_git/\<šablony projektu Název úložiště\>.*** 

V tomto příkladu jsme provedli následující:

***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/MyTeamProjectTemplate***. 

### <a name="clone-dsproject1-repository-r5-to-a-directory-d5-on-your-local-machine"></a>Naklonujte úložiště DSProject1 (R5) do adresáře (D5) na místním počítači

Změňte adresář na **GitRepos**, a spusťte následující příkaz a naklonujte úložiště projektu do místního počítače. 

**Windows**
            
    git clone <the HTTPS URL of the Project repository>

![9](./media/project-lead-tasks/project-leads-9-clone-project-repository.png)

Pokud používáte Azure DevOps jako platformu pro hosting kódu, obvykle _adresu URL HTTPS v úložišti projektů_ je ***https://\<název služby Azure DevOps\>.visualstudio.com/\<Svůj název projektu > /_git/ < název vašeho projektu úložiště\>***. V tomto příkladu máme ***https://mysamplegroup.visualstudio.com/MyTeam/_git/DSProject1***.

**Linux**

    git clone <the SSH URL of the Project repository>

![10](./media/project-lead-tasks/project-leads-10-clone-project-repository-linux.png)

Pokud používáte Azure DevOps jako platformu pro hosting kódu, obvykle _adresu URL SSH úložiště projektu_ je _ssh: / / < název služby Azure DevOps\>@< název služby Azure DevOps\>. VisualStudio.com:22/<Your Project Name>/\_git / < název vašeho projektu úložiště\>. V tomto příkladu máme ***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/DSProject1***.

### <a name="copy-contents-of-d3-to-d5"></a>Zkopírujte obsah D3 D5 

Teď v místním počítači, je nutné zkopírovat obsah _D3_ k _D5_, s výjimkou metadat gitu adresář .Git, na který. Následující skripty provede úlohu. Nezapomeňte zadat správné a úplné cesty k adresářům. Zdrojová složka je pro váš tým (_D3_); cílová složka je pro váš projekt (_D5_).    

**Windows**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 -role 3
    
![11](./media/project-lead-tasks/project-leads-11-local-copy-project-lead-new.png)

Teď vidíte v _DSProject1_ složky, všechny soubory (s výjimkou .git) jsou zkopírovány z _MyTeamProjectTemplate_.

![12](./media/project-lead-tasks/project-leads-12-teamprojectTemplate_copied_to_local.png)

**Linux**
            
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 3
        
![13](./media/project-lead-tasks/project-leads-13-local_copy_project_lead_linux_new.png)

Teď vidíte v _DSProject1_ složky, všechny soubory (s výjimkou metadata v .git) jsou zkopírovány z _MyTeamProjectTemplate_.

![14](./media/project-lead-tasks/project-leads-14-teamprojectTemplate_copied_to_local_linux_new.png)


### <a name="customize-d5-if-you-need-to-optional"></a>Přizpůsobení D5, pokud je potřeba (volitelné)

Pokud váš projekt potřebuje některé konkrétní adresáře nebo dokumenty, než ty, které získáte ze šablony projektu (zkopírován do adresáře D5 v předchozím kroku), můžete přizpůsobit obsah D5 nyní. 

### <a name="add-contents-of-dsproject1-in-d5-to-r5-on-your-group-azure-devops-services"></a>Přidat obsah DSProject1 v D5 R5 ve vašich službách Azure DevOps skupiny

Teď musíte vložit obsah **_DSProject1_** k _R5_ úložiště v projektu na vaši skupinu Azure DevOps služby. 


- Přejděte do adresáře **D5**. 
- Pomocí následujících příkazů git přidat obsah **D5** k **R5**. Příkazy jsou stejné pro systémy Windows a Linux. 
    
    Git status git přidat.
    Git commit -m "push z win DSVM" git push
    
- Potvrzení změn a nabízených oznámení. 

> [!NOTE]
> Pokud to je poprvé, kdy jste se zavázali k úložišti Git, musíte nakonfigurovat globální parametry *user.name* a *user.email* předtím, než spustíte `git commit` příkazu. Spusťte následující dva příkazy:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Pokud jsou přiřazeni k více úložišť Git, použijte stejný název a e-mailovou adresu ve všech z nich. Pomocí stejného jména a e-mailovou adresu prokáže pohodlný později při vytváření řídicích panelů Power BI ke sledování aktivit Git na více úložišť.

![15](./media/project-lead-tasks/project-leads-15-git-config-name.png)


## <a name="6-create-and-mount-azure-file-storage-as-project-resources-optional"></a>6. Vytvoření a připojení Azure file storage jako projekt prostředků (volitelné)

Pokud chcete vytvořit Azure file storage pro sdílení dat, jako je například projekt nezpracovaná data nebo funkce vygenerovaný pro váš projekt tak, aby všichni členové projektu mají přístup do stejné datové sady z více datové postupujte podle pokynů v části 3 a 4 [ Týmu úkoly potenciálních zákazníků pro datové vědy tým](team-lead-tasks.md). 


## <a name="next-steps"></a>Další postup

Tady jsou odkazy na podrobnější popis role a úlohy určené vědecké zpracování týmových dat:

- [Úlohy správce skupiny pro datové vědy tým](group-manager-tasks.md)
- [Úkoly vedoucí týmu pro datové vědy tým](team-lead-tasks.md)
- [Vedoucí úkoly pro datové vědy tým projektu](project-lead-tasks.md)
- [Jednotlivé přispěvatelé projektu týmu datové vědy](project-ic-tasks.md)