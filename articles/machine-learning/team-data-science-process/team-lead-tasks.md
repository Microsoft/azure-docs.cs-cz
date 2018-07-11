---
title: Týmu Data Science procesu týmu způsobit úlohy – Azure | Dokumentace Microsoftu
description: Přehled úloh pro vedoucí týmu v týmovém projektu datové vědy.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 9d2043808cbd61d5e2a69cbe0f2a5a611e3afa31
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "34839754"
---
# <a name="team-lead-tasks"></a>Úkoly vedoucí týmu

K dokončení pro jejich data science tým byl očekáván tohoto tématu jsou podrobněji popsány dále úlohy, které je vedoucí týmu. Cílem je vytvořit prostředí pro spolupráci týmu, který standardizuje na [vědecké zpracování týmových dat](overview.md) (TDSP). TDSP je metodologie pro efektivně dodávat prediktivní analytická řešení a inteligentní aplikace agile, iterativní datové vědy. Je určená jí pomohou zlepšit spolupráci a učení týmu. Proces je destilace osvědčených postupů a struktur od společnosti Microsoft, a také v odvětví, potřebné pro úspěšnou implementaci iniciativ datových věd umožnit společnostem plně využít výhod jejich analýzy aplikací. Přehled role pracovníky a jejich přidružených úloh, které jsou zpracovávány týmem datové vědy standardizovat o tomto procesu najdete v části [vědecké zpracování týmových dat role a úlohy](roles-tasks.md).

A **vedoucí týmu** řídí tým v jednotce data science podniku. Tým se skládá z několika odborníci přes data. Pro datové vědy jednotky s pouze malý počet odborníci přes data **správce skupiny** a **vedoucí týmu** může být stejná osoba nebo jejich úkol, může delegovat náhradní. Avšak nezměníte samotné úkoly. Pracovní postup pro úlohy provést pomocí týmových vedoucích k nastavení tohoto prostředí jsou znázorněné na následujícím obrázku:

![1](./media/team-lead-tasks/team-leads-1-creating-teams.png)

>[AZURE.NOTE] Úkoly v blocích po 1 a 2 na obrázku jsou potřeba, pokud používáte Visual Studio Team Services (VSTS) jako kód hostování platformy a budete chtít mít samostatný týmový projekt pro váš tým. Po dokončení těchto úloh jsou všechna úložiště vašeho týmu mohou být vytvořeny v tomto týmovém projektu. 

Úkoly uvedené v následující části jsou splněny správcem skupiny splnit několik předpokladů, existují po pěti hlavní úkolů (některé volitelné), který je dokončen v tomto kurzu. Tyto úlohy odpovídají hlavní číslované částech tohoto tématu:

1. Vytvoření **týmový projekt** na serveru skupiny VSTS skupiny a dvě týmových úložišť v projektu:
    - **ProjectTemplate úložiště** 
    - **TeamUtilities úložiště**
2. Počáteční hodnoty tým **ProjectTemplate** úložiště ze **GroupProjectTemplate** úložiště, který je nastavený správcem skupiny. 
3. Vytvoření zdroje dat a analýz týmu:
    - Přidat tým konkrétní nástroje, které **TeamUtilities** úložiště. 
    - (Volitelné) Vytvoření **Azure file storage** se použije k uložení datových assetů, které může být užitečné pro celý tým. 
4. (Volitelné) Připojení Azure file storage pro **virtuální počítač pro datové vědy** (DSVM) týmu způsobit a přidejte datové assety v něm.
5. Nastavit **řízení zabezpečení** tím, že přidání členů týmu a konfigurovat jejich oprávnění.

>[AZURE.NOTE] Uvádíme kroky potřebné k nastavení prostředí TDSP týmu pomocí VSTS v následujících pokynech. Zadáme tom, jak provádět tyto úlohy s využitím VSTS, protože to je, jak můžeme implementovat TDSP v Microsoftu. Pokud jiný kód hostování platformy se používá pro vaši skupinu, úlohy, které musíte provést vedoucí týmu obecně se nezmění. Ale způsob k provedení těchto úloh se bude lišit.

## <a name="repositories-and-directories"></a>Úložiště a adresáře

Toto téma používá zkrácené názvy pro úložiště a adresáře. Tyto názvy usnadňují sledování operací mezi úložišť a adresáře. Tato notace (**R** pro úložiště Git a **D** pro místní adresáře na vaše DSVM) se používá v následujících částech:

- **R1**: **GroupProjectTemplate** úložiště v Gitu, která správce skupiny nastavit na vašem serveru skupiny VSTS.
- **R3**: tým **ProjectTemplate** úložiště v Gitu můžete nastavit.
- **R4**: **TeamUtilities** úložiště v Gitu můžete nastavit.
- **D1**: místní adresář naklonovali z R1 a zkopírován do D3.
- **D3**: místní adresář naklonovali z R3, přizpůsobení a zkopírována zpět k R3.
- **D4**: místní adresář naklonovali z R4, přizpůsobení a zkopírována zpět k R4.

Názvy stanovené pro úložiště a adresářů v tomto kurzu byly zadány na za předpokladu, že je váš cíl vytvořit samostatný týmový projekt pro váš tým v rámci větší datové vědy skupiny. Ale existují další možnosti Otevřít jako vedoucí týmu:

- Celé skupině můžete zvolit vytvoření jednoho týmového projektu. Všechny projekty ze všech datové vědy týmy pak bude v rámci této jeden týmový projekt. Za tím účelem můžete určit git správce postupovat podle těchto pokynů a vytvořte jeden týmový projekt. Tento scénář může být platná, třeba:
    -  Malé datové vědy skupiny, který nemá více týmů datové vědy 
    -  větší data science skupinu s více týmy vědy data, která však chce, aby k optimalizaci mezi Týmová spolupráce aktivity, jako je plánování sprintu úrovni skupiny. 
- Týmy mohou zvolit, aby šablon specifické pro týmový projekt nebo nástroje specifické pro tým v rámci jednoho týmového projektu pro celou skupinu. V takovém případě vedoucí týmu měli vytvořit úložiště šablony týmového projektu nebo týmu nástroje úložišť v rámci stejného projektu týmu. Pojmenujte tato úložiště *< TeamName\>ProjectTemplate* a *< TeamName\>nástroje*, například *TeamJohnProjectTemplate*a *TeamJohnUtilities*. 

V každém případě týmů třeba chcete, aby členové týmu vědět, jaké šablony a nástroje úložiště přijmout při jejich vytvoření a klonování úložišť projektů a nástrojů. Postupujte podle zájemci o projekt [vedoucí projektu úlohy pro datové vědy tým](project-lead-tasks.md) k vytvoření projektu úložišť, ať už vyplývající z samostatné týmové projekty nebo v rámci jednoho týmového projektu. 


## <a name="0-prerequisites"></a>0. Požadavky

Požadavky splněny dokončení úkolů přiřazena vedoucímu skupiny uvedených v [úkoly správce skupiny pro datové vědy tým](group-manager-tasks.md). Slouží ke shrnutí tady, třeba tyto požadavky splnit, předtím, než začnete s úkoly vedoucí týmu: 

- Vaše **serveru skupiny VSTS** (nebo skupinový účet na některé další kód hostování platformu) je nastavený správcem skupiny.
- Vaše **GroupProjectTemplate úložiště** (R1) je nastavený na vašem účtu skupiny správcem skupiny kódu hostování platformy plánujete použít.
- Byli jste **oprávnění** na vašem účtu skupiny k vytvoření úložiště pro váš tým.
- Git musí být nainstalován na počítači. Pokud používáte Data virtuálního počítače VĚDY, Git je předem nainstalovaný a jste připravení. V opačném případě najdete v článku [platformami a nástroji příloha](platforms-and-tools.md#appendix).  
- Pokud používáte **Windows DSVM**, musíte mít [Git Credential Manageru (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) na vašem počítači nainstalovaný. V souboru README.md přejděte dolů k položce **stáhněte a nainstalujte** a klikněte *nejnovější instalační program*. Tím přejdete na stránku pro nejnovější instalační program. Stažení instalačního programu .exe odsud a spustíme ji. 
- Pokud používáte **Linux DSVM**vytvořte veřejný klíč SSH na vaše DSVM a přidejte ke svému serveru skupiny VSTS. Další informace o SSH najdete v tématu **vytvořit veřejný klíč SSH** tématu [platformami a nástroji příloha](platforms-and-tools.md#appendix). 
    
## <a name="1-create-a-team-project-and-repositories"></a>1. Vytvoření týmového projektu a úložiště

Tento krok proveďte, pokud používáte VSTS jako svůj kód hostování platforem pro správu verzí a spolupráci. Tato část obsahuje, můžete vytvořit tři artefaktů ve VSTS serveru skupiny:

- **MyTeam** projektu ve VSTS
- **MyProjectTemplate** úložiště (**R3**) na Git
- **MyTeamUtilities** úložiště (**R4**) na Git

### <a name="create-the-myteam-project"></a>Vytvoření projektu MyTeam

- Přejděte na domovskou stránku serveru VSTS vaší skupiny na adrese URL `https://<VSTS Server Name\>.visualstudio.com`. 
- Klikněte na tlačítko **nový** k vytvoření týmového projektu. 

    ![2](./media/team-lead-tasks/team-leads-2-create-new-team.png)

- Okno Vytvoření týmu projektu vás vyzve k zadání názvu projektu (**MyTeam** v tomto příkladu). Ujistěte se, že jste vybrali **Agile** jako **šablony procesu** a **Git** jako **verzí**. 

    ![3](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)

- Klikněte na tlačítko **vytvořit projekt**. Váš týmový projekt **MyTeam** se vytvoří v méně než 1 minuta. 

- Po týmový projekt **MyTeam** je vytvořený, klikněte na tlačítko **Navigovat do projektu** tlačítko, přejdete na domovské stránce týmového projektu. 

    ![4](./media/team-lead-tasks/team-leads-4-create-new-team-3.png)

- Pokud se zobrazí **Blahopřejeme!** automaticky otevíraném okně, klikněte na tlačítko **přidejte kód** (tlačítko v červeným rámečkem). V opačném případě klikněte na tlačítko **kód** (v žlutým rámečkem). To vás přesměruje na stránku úložiště Git vašeho týmového projektu. 

    ![5](./media/team-lead-tasks/team-leads-5-team-project-home.png)

### <a name="create-the-myprojecttemplate-repository-r3-on-git"></a>Vytvořit úložiště MyProjectTemplate (R3) na Git

- Na stránce úložiště Git vašeho týmového projektu, klikněte na šipku dolů vedle názvu úložiště **MyTeam**a vyberte **spravovat úložiště...** .

    ![6](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)

- Na **verzí** kartu ovládacího panelu vašeho týmového projektu, klikněte na tlačítko **MyTeam**a pak vyberte **přejmenovat úložiště...** . 

    ![7](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)

- Zadejte nový název do úložiště v **přejmenovat úložiště MyTeam** okna. V tomto příkladu *MyTeamProjectTemplate*. Můžete použít něco jako *< název vašeho týmu\>ProjectTemplate*. Klikněte na tlačítko **přejmenovat** pokračujte.

    ![8](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)

### <a name="create-the-myteamutilities-repository-r4-on-git"></a>Vytvořit úložiště MyTeamUtilities (R4) na Git

- Chcete-li vytvořit nové úložiště *< název vašeho týmu\>nástroje* ve vašem týmovém projektu, klikněte na tlačítko **nové úložiště...**  na **verzí** kartu ovládacího panelu týmového projektu.  

    ![9](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)

- V **vytvořit nové úložiště** okna, která se otevře, zadejte název pro toto úložiště. V tomto příkladu používáme název jako *MyTeamUtilities*, což je **R4** naše notaci. Zvolte něco jako *< název vašeho týmu\>nástroje*. Ujistěte se, že jste vybrali **Git** pro **typ**. Potom klikněte na **vytvořit** pokračujte.

    ![10](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)

- Ověřte, že se dvě nové úložiště Git vytvoří ve vašem týmovém projektu **MyTeam**. V tomto příkladu: 

- **MyTeamProjectTemplate** (R3) 
- **MyTeamUtilities** (R4).

    ![11](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)


## <a name="2-seed-your-team-projecttemplate-and-teamutilities-repositories"></a>2. Počáteční hodnoty týmových ProjectTemplate a TeamUtilities úložišť

Osazení postup používá adresáře na místním DSVM jako zprostředkující pracovní lokality. Pokud je potřeba upravit vaše **ProjectTemplate** a **TeamUtilities** úložiště pro splnění některé konkrétní potřebám týmu, můžete udělat v předposlední kroku tohoto postupu. Zde je uveden seznam kroky používají naplnit obsah **MyTeamProjectTemplate** a **MyTeamUtilities** úložiště pro datové vědy tým. Témata v postupu osazení odpovídají jednotlivé kroky:

- Klonování úložiště skupiny do místního adresáře: týmu R1 - klonovat do místního D1 ->
- Klonujte svá úložiště týmu do místních adresářů: týmu R3 & R4 - klonovat na -> místní D3 & D4
- Zkopírujte obsah šablony skupiny projektu do složky místní týmu: D1 - obsah bude zkopírován do D3 ->
- (Volitelné) přizpůsobení místní D3 & D4
- Vložit obsah místní adresáře do týmových úložišť: D3 & D4 - do -> přidat obsah týmu R3 & R4


### <a name="initialize-the-team-repositories"></a>Inicializovat týmových úložišť

V tomto kroku inicializovat své úložiště týmových projektů šablony z úložiště šablon projektu skupiny:

- **MyTeamProjectTemplate** úložiště (**R3**) z vaší **GroupProjectTemplate** (**R1**) úložiště


### <a name="clone-group-repositories-into-local-directories"></a>Naklonujte úložiště skupiny do místního adresáře

Chcete-li začít tento postup:

- Vytvoření adresáře na místním počítači:
    - Pro **Windows**: **C:\GitRepos\GroupCommon** a **C:\GitRepos\MyTeam**
    - Pro **Linux**: **GitRepos\GroupCommon** a **GitRepos\MyTeam** na domovský adresář 
- Přejděte do adresáře **GitRepos\GroupCommon**.
- Spusťte následující příkaz, v závislosti na operačním systému místního počítače.

**Windows**

    git clone https://<Your VSTS Server name>.visualstudio.com/GroupCommon/_git/GroupProjectTemplate
    

![12](./media/team-lead-tasks/team-leads-12-create-two-group-repos.png)

**Linux**
    
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/GroupCommon/_git/GroupProjectTemplate
    
    
![13](./media/team-lead-tasks/team-leads-13-clone_two_group_repos_linux.png)

Tyto příkazy klonování vaší **GroupProjectTemplate** úložiště (R1) na serveru skupiny VSTS do místního adresáře v **GitRepos\GroupCommon** na místním počítači. Po klonování, adresář **GroupProjectTemplate** (D1) se vytvoří v adresáři **GitRepos\GroupCommon**. Tady předpokládáme, že váš správce skupiny vytvoření týmového projektu **GroupCommon**a **GroupProjectTemplate** úložiště je v tomto týmovém projektu. 


### <a name="clone-your-team-repositories-into-local-directories"></a>Klonujte svá úložiště týmu do místních adresářů

Tyto příkazy klonování vaší **MyTeamProjectTemplate** (R3) a **MyTeamUtilities** úložišť (R4) ve vašem týmovém projektu **MyTeam** na vašem serveru skupiny VSTS a  **MyTeamProjectTemplate** (D3) a **MyTeamUtilities** adresářů (D4) v **GitRepos\MyTeam** na místním počítači. 

- Přejděte do adresáře **GitRepos\MyTeam**
- Spusťte následující příkazy, v závislosti na operačním systému místního počítače. 

**Windows**

    git clone https://<Your VSTS Server name>.visualstudio.com/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone https://<Your VSTS Server name>.visualstudio.com/<Your Team Name>/_git/MyTeamUtilities

![14](./media/team-lead-tasks/team-leads-14-clone_two_empty_team_repos.png)
        
**Linux**
    
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamUtilities
    
![15](./media/team-lead-tasks/team-leads-15-clone_two_empty_team_repos_linux.png)

Po klonování, dva adresáře **MyTeamProjectTemplate** (D3) a **MyTeamUtilities** (D4) se vytvoří v adresáři **GitRepos\MyTeam**. Budeme mít předpokládá, že tady název týmového projektu šablony a nástroje úložiště **MyTeamProjectTemplate** a **MyTeamUtilities**. 

### <a name="copy-the-group-project-template-content-to-the-local-team-project-template-directory"></a>Zkopírujte obsah šablony projektu skupiny do šablony adresář místního týmového projektu

Kopírování obsahu místní **GroupProjectTemplate** (D1) složku do místní **MyTeamProjectTemplate** (D3), spusťte jeden z následujících skriptů prostředí: 

#### <a name="from-the-powershell-command-line-for-windows"></a>V příkazovém řádku Windows Powershellu       

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 2

    
![16](./media/team-lead-tasks/team-leads-16-local_copy_team_lead_new.png)

#### <a name="from-the-linux-shell-for-the-linux-dsvm"></a>V prostředí Linuxu pro **Linux DSVM**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 2
    
![17](./media/team-lead-tasks/team-leads-17-local-copy-team-lead-linux-new.png)

Skripty vyloučit obsah adresář .Git, na který. Skript vyzve k zadání **dokončení cesty** zdrojový adresář D1 a cílový adresář D3.
        

### <a name="customize-your-team-project-template-or-team-utilities-optional"></a>Přizpůsobení šablony týmového projektu nebo týmu nástroje (volitelné)

Přizpůsobení vašich **MyTeamProjectTemplate** (D3) a **MyTeamUtilities** (D4), v případě potřeby v této fázi procesu instalace. 

- Pokud chcete upravit obsah D3 pro konkrétní potřeby vašeho týmu, můžete upravit šablony dokumentů nebo změnit strukturu adresářů.

- Pokud váš tým vyvíjí některé nástroje, které chcete sdílet se svým týmem celý, zkopírujte a vložte do adresáře D4 těchto nástrojů. 


### <a name="push-local-directory-content-to-team-repositories"></a>Vložit obsah místní adresáře do týmových úložišť

Přidat obsah k úložištím týmu R3 a R4 (volitelně přizpůsobené) místní adresáře D3 a D4, spusťte následující git bash příkazy z konzoly Windows Powershellu nebo z prostředí Linuxu. Spusťte příkazy z **GitRepos\MyTeam\MyTeamProjectTemplate** adresáře.

    git status
    git add .
    git commit -m"push from DSVM"
    git push
    
![18](./media/team-lead-tasks/team-leads-18-push-to-group-server-2.png)

Soubory v úložišti MyTeamProjectTemplate serveru pro vaši skupinu VSTS se synchronizují skoro okamžitě při spuštění tohoto skriptu.

![19](./media/team-lead-tasks/team-leads-19-push-to-group-server-showed-up.png)

Nyní spustit stejnou sadu ze čtyř příkazů gitu **GitRepos\MyTeam\MyTeamUtilities** adresáře. 

> [AZURE.NOTE]Pokud to je poprvé, kdy jste se zavázali k úložišti Git, musíte nakonfigurovat globální parametry *user.name* a *user.email* předtím, než spustíte `git commit` příkazu. Spusťte následující dva příkazy:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Pokud jsou přiřazeni k více úložišť Git, použijte stejný název a e-mailovou adresu když jste se zavázali k každý z nich. Pomocí stejného jména a e-mailovou adresu prokáže pohodlný později při vytváření řídicích panelů Power BI ke sledování aktivit Git na více úložišť.

![20](./media/team-lead-tasks/team-leads-20-git-config-name.png)


## <a name="3-create-team-data-and-analytics-resources-optional"></a>3. Vytvoření týmových dat a analýz prostředků (volitelné)

Sdílení dat a analýz prostředků se celý tým má výhody výkonu a nákladů: členové týmu mohou spustit svých projektů na sdílené prostředky, uložte na rozpočty a efektivněji spolupracovat. V této části poskytujeme pokyny o tom, jak vytvořit Azure file storage. V další části poskytujeme pokyny o tom, jak připojení Azure file storage do místního počítače. Další informace o sdílení další prostředky, jako je Azure virtuální počítače pro datové vědy, clusterů Spark Azure HDInsight, naleznete v tématu [platformami a nástroji](platforms-and-tools.md). Toto téma poskytuje pokyny z hlediska datové vědy o výběru prostředky, které jsou vhodné pro vaše potřeby a odkazy na stránky produktu a další důležité a užitečné kurzy, které jsme publikovali.

>[AZURE.NOTE] Aby se zabránilo přenosu dat mezi datových center, které může být pomalé a nákladná, ujistěte se, že skupina prostředků, účet úložiště a virtuálních počítačů Azure (například DSVM) jsou ve stejném datovém centru Azure. 

Spuštěním následujících skriptů k vytvoření Azure file storage pro váš tým. Azure file storage pro váš tým může použít pro ukládání datových assetů, které jsou užitečné pro celý tým. Skripty výzvu vaše informace o Azure účet a předplatné, proto jste připraveni zadejte tyto přihlašovací údaje. 

### <a name="create-azure-file-storage-with-powershell-from-windows"></a>Vytvoření Azure file storage pomocí Powershellu z Windows

Spusťte tento skript z prostředí PowerShell příkazového řádku:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
    .\CreateFileShare.ps1

![21](./media/team-lead-tasks/team-leads-21-create-fileshare-win.png)   

Přihlaste se ke svému účtu Microsoft Azure po zobrazení výzvy:

![22](./media/team-lead-tasks/team-leads-22-file-create-s1.png)

Vyberte předplatné Azure, které chcete použít:

![23](./media/team-lead-tasks/team-leads-23-file-create-s2.png)

Který účet úložiště nebo vytvořte novou ve vybraném předplatném vyberte:

![24](./media/team-lead-tasks/team-leads-24-file-create-s3.png)

Zadejte název služby Azure file storage k vytvoření. Pouze dolní malá a velká písmena a čísla a - jsou přijímány:

![25](./media/team-lead-tasks/team-leads-25-file-create-s4.png)

K usnadnění připojení a sdílení tohoto úložiště po jeho vytvoření, uložit informace o Azure file storage do textového souboru a poznamenejte si cestu k jeho umístění. Zejména je nutné tento soubor připojte vaše Azure file storage pro virtuální počítače Azure v další části. 

Je vhodné zkontrolovat v tomto souboru do úložiště ProjectTemplate vašeho týmu. Doporučujeme umístit do adresáře **Docs\DataDictionaries**. Proto tento datový asset přístupný všech projektů ve vašem týmu. 

![26](./media/team-lead-tasks/team-leads-26-file-create-s5.png)


### <a name="create-azure-file-storage-with-a-linux-script"></a>Vytvoření Azure file storage s Linuxem skriptu

Spusťte tento skript v prostředí Linux:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
    bash CreateFileShare.sh

Přihlaste se ke svému účtu Microsoft Azure, postupujte podle pokynů na této obrazovce:

![27](./media/team-lead-tasks/team-leads-27-file-create-linux-s1.png)

Vyberte předplatné Azure, kterou chcete použít:

![28](./media/team-lead-tasks/team-leads-28-file-create-linux-s2.png)

Který účet úložiště nebo vytvořte novou ve vybraném předplatném vyberte:

![29](./media/team-lead-tasks/team-leads-29-file-create-linux-s3.png)

Zadejte název Azure file storage pro vytvoření, jenom malá písmena, čísla a - jsou přijímány:

![30](./media/team-lead-tasks/team-leads-30-file-create-linux-s4.png)

Pro usnadnění přístupu k toto úložiště po jeho vytvoření, uložit informace o Azure file storage do textového souboru a poznamenejte si cestu k jeho umístění. Zejména je nutné tento soubor připojte vaše Azure file storage pro virtuální počítače Azure v další části.

Je vhodné zkontrolovat v tomto souboru do úložiště ProjectTemplate vašeho týmu. Doporučujeme umístit do adresáře **Docs\DataDictionaries**. Proto tento datový asset přístupný všech projektů ve vašem týmu. 

![31](./media/team-lead-tasks/team-leads-31-file-create-linux-s5.png)


## <a name="4-mount-azure-file-storage-optional"></a>4. Připojení Azure file storage (volitelné)

Po úspěšném vytvoření Azure file storage je možné připojit do místního počítače pomocí jedné z následujících skriptů prostředí PowerShell nebo Linux.

### <a name="mount-azure-file-storage-with-powershell-from-windows"></a>Připojení Azure file storage s využitím Powershellu z Windows

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
    .\AttachFileShare.ps1
    
Zobrazí se výzva k přihlášení první, pokud jste ještě přihlášeni. 

Klikněte na tlačítko **Enter** nebo **y** k pokračování, zobrazí se výzva, pokud máte Azure storage informace o souboru souboru a pak zadejte ***dokončete cestu a název** vytvoříte v souboru předchozí krok. Informace pro připojení služby Azure file storage je pro čtení přímo z, souboru a jste připraveni přejít k dalšímu kroku.

![32](./media/team-lead-tasks/team-leads-32-attach-s1.png)

> [AZURE.NOTE] Pokud nemáte soubor obsahující informace o Azure file storage, kroky pro vstup z klávesnice informace jsou k dispozici na konci této části.

Pak budete vyzváni k zadání názvu disk přidat do virtuálního počítače. Vytiskne seznam existujících názvů jednotky se na obrazovce. Název jednotky, který již neexistuje v seznamu by měla poskytnout.

![33](./media/team-lead-tasks/team-leads-33-attach-s2.png)

Potvrďte, že nové jednotky F úspěšně připojil k vašemu počítači.

![34](./media/team-lead-tasks/team-leads-34-attach-s3.png)

**Jak ručně zadat informace o Azure file storage:** Pokud nemáte údaje o Azure file storage s textovým souborem, může podle pokynů na následující obrazovce zadejte požadované předplatné, účet úložiště a Azure informace o souboru úložiště:

![35](./media/team-lead-tasks/team-leads-35-attach-s4.png)

Zadejte vaše předplatné Azure název, vyberte účet úložiště, kde se vytvoří Azure file storage, a zadejte název Azure file storage:

![36](./media/team-lead-tasks/team-leads-36-attach-s5.png)

### <a name="mount-azure-file-storage-with-a-linux-script"></a>Připojení Azure file storage s Linuxem skriptu

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
    bash AttachFileShare.sh

![37](./media/team-lead-tasks/team-leads-37-attach-s1-linux.png)

Zobrazí se výzva k přihlášení první, pokud jste ještě přihlášeni. 

Klikněte na tlačítko **Enter** nebo **y** k pokračování, zobrazí se výzva, pokud máte Azure storage informace o souboru souboru a pak zadejte ***dokončete cestu a název** vytvoříte v souboru předchozí krok. Informace pro připojení služby Azure file storage je pro čtení přímo z, souboru a jste připraveni přejít k dalšímu kroku.

![38](./media/team-lead-tasks/team-leads-38-attach-s2-linux.png)

Pak budete vyzváni k zadání názvu disk přidat do virtuálního počítače. Vytiskne seznam existujících názvů jednotky se na obrazovce. Název jednotky, který již neexistuje v seznamu by měla poskytnout.

![39](./media/team-lead-tasks/team-leads-39-attach-s3-linux.png)

Potvrďte, že nové jednotky F úspěšně připojil k vašemu počítači.

![40](./media/team-lead-tasks/team-leads-40-attach-s4-linux.png)

**Jak ručně zadat informace o Azure file storage:** Pokud nemáte údaje o Azure file storage s textovým souborem, může podle pokynů na následující obrazovce zadejte požadované předplatné, účet úložiště a Azure informace o souboru úložiště:

- Vstup **n**.
- Vyberte index název předplatného, ve kterém byla vytvořena Azure file storage v předchozím kroku:

    ![41](./media/team-lead-tasks/team-leads-41-attach-s5-linux.png)

- Vyberte účet úložiště v rámci vašeho předplatného a typ v názvu Azure file storage:

    ![42](./media/team-lead-tasks/team-leads-42-attach-s6-linux.png)

- Zadejte název disku, aby se přidají do počítače, které by se měly lišit od všechny existující aplikace:

    ![43](./media/team-lead-tasks/team-leads-43-attach-s7-linux.png)


## <a name="5-set-up-security-control-policy"></a>5. Nastavení zásad zabezpečení ovládacího prvku 

Z domovské stránky serveru skupiny VSTS, klikněte na tlačítko **ikonu ozubeného kola** vedle vaše uživatelské jméno v pravém horním rohu vyberte **zabezpečení** kartu. Přidat členy do týmu tady s různými oprávněními.

![44](./media/team-lead-tasks/team-leads-44-add-team-members.png)

## <a name="next-steps"></a>Další postup

Tady jsou odkazy na podrobnější popis role a úlohy určené vědecké zpracování týmových dat:

- [Úlohy správce skupiny pro datové vědy tým](group-manager-tasks.md)
- [Úkoly vedoucí týmu pro datové vědy tým](team-lead-tasks.md)
- [Vedoucí úkoly pro datové vědy tým projektu](project-lead-tasks.md)
- [Jednotlivé přispěvatelé projektu týmu datové vědy](project-ic-tasks.md)