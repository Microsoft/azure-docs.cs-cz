---
title: Tým datové vědy procesu Team vést úlohy – Azure | Microsoft Docs
description: Přehled úloh pro tým vést na datové vědě týmového projektu.
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev;
ms.openlocfilehash: 995ad557eb06e545b1813e1f4631e243a98830b3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="team-lead-tasks"></a>Tým realizace úlohy

Toto téma obsahuje přehled úlohy, které je vedoucí týmu očekává dokončení pro jejich tým vědecké účely data. Cílem je vytvoření prostředí pro spolupráci team, standardizující na [proces vědecké účely dat Team](overview.md) (TDSP). TDSP je metodika vědecké účely agilní, iterativní data k poskytování efektivní řešení prediktivní analýzy a inteligentní aplikací. Je určený k vylepšení spolupráce a team learning. Proces je destilace osvědčených postupů a struktury z obou Microsoft i v odvětví, potřebné pro úspěšné dokončení implementace iniciativy vědecké účely data ke společnosti plně pochopit výhody jejich analýzy programy. Přehled role pracovníky a jejich přidružených úloh, které zpracovávají datové vědy tým standardizace tohoto postupu najdete v tématu [proces vědecké účely dat Team rolí a úloh](roles-tasks.md).

A **vést Team** spravuje tým v jednotce vědecké účely dat organizace. Tým se skládá z několika datových vědců. Pro data vědecké účely jednotku s pouze malý počet datových vědců **správce skupiny** a **vést Team** může být stejná osoba nebo jejich úloh se může delegovat na náhradní. Ale samotné úkoly se nemění. Pracovní postup pro úkoly, které mají být dokončit týmů k nastavení tohoto prostředí je znázorněno na následujícím obrázku:

![1](./media/team-lead-tasks/team-leads-1-creating-teams.png)

>[AZURE.NOTE] Úlohy v blocích 1 a 2 na obrázku je potřeba, pokud používáte Visual Studio Team Services (VSTS) jako kód hostování platformy a budete chtít mít samostatné týmového projektu pro váš vlastní tým. Po dokončení těchto úloh, lze vytvořit všechny úložiště váš tým pod tímto týmovým projektem. 

Několik požadavků na úkoly uvedené v následující části jsou splněny správcem skupiny, existuje po pěti hlavní úlohy (některé volitelné), můžete dokončit v tomto kurzu. Tyto úlohy odpovídají hlavní číslované částech tohoto tématu:

1. Vytvoření **týmového projektu** na serveru služby VSTS skupiny skupiny a dvě team úložiště v projektu:
    - **ProjectTemplate úložiště** 
    - **TeamUtilities úložiště**
2. Počáteční hodnoty týmem **ProjectTemplate** úložiště z **GroupProjectTemplate** úložiště, která byla vytvořena správcem vaší skupiny. 
3. Vytvoření zdroje dat a analýzy týmu:
    - Přidat tým konkrétní nástroje, které **TeamUtilities** úložiště. 
    - (Volitelné) Vytvoření **úložiště Azure file** použije k uložení datové prostředky, které mohou být užitečné pro celý tým. 
4. (Volitelné) Připojte úložiště Azure file **datové vědy virtuální počítač** (DSVM) týmu vést a přidejte datové prostředky na něm.
5. Nastavit **řízení zabezpečení** nástrojem přidávání členů týmu a nakonfigurujte svá oprávnění.

>[AZURE.NOTE] Jsme popisují kroky potřebné k nastavení prostředí team TDSP pomocí služby VSTS v následujících pokynech. Určíme jak provést tyto úlohy s služby VSTS, protože se jedná, jak jsme implementovat TDSP ve společnosti Microsoft. Pokud jiný kód hostování platformy slouží pro vaši skupinu, úlohy, které je potřeba dokončit vedoucí týmu obecně se nezmění. Ale způsob k dokončení těchto úloh se bude lišit.

## <a name="repositories-and-directories"></a>Úložiště a adresáře

Toto téma používá zkrácený názvy pro úložiště a adresáře. Názvy těchto usnadňují následovat operace mezi úložiště a adresářů. Tento zápis (**R** pro úložiště Git a **D** pro místní adresáře na vaše DSVM) se používá v následujících částech:

- **R1**: **GroupProjectTemplate** úložiště na Git, který váš správce skupiny nastavený na váš server služby VSTS skupiny.
- **R3**: týmem **ProjectTemplate** úložiště na Git nastavíte.
- **R4**: **TeamUtilities** úložiště na Git nastavíte.
- **D1**: místní adresář naklonována ze R1 a zkopírovali do D3.
- **D3**: místní adresář naklonována ze R3, přizpůsobení a zkopírují se R3.
- **D4**: místní adresář naklonována ze R4, přizpůsobení a zkopírují se R4.

Názvy zadané pro úložiště a adresářů v tomto kurzu byly zadány na předpokladu, že váš cíl je k vytvoření samostatné týmového projektu pro váš vlastní tým v rámci skupiny větší vědecké účely data. Ale existují otevřené jako vedoucí týmu další možnosti:

- Celou skupinu můžete zvolit jeden týmového projektu. Všechny projekty od týmů vědecké účely všechna data, pak bude v tomto projektu jeden tým. Jak toho docílit, můžete určit git správcům postupujte podle těchto pokynů k vytvoření jedné týmového projektu. Tento scénář může být platný, například:
    -  skupinu vědecké účely malá data, která nemá více týmů data vědecké účely 
    -  větší vědecké účely skupinu dat s více dat vědecké účely týmy jasném chce optimalizovat mezi Týmová spolupráce s aktivity, například plánování sprintu úrovně seskupení. 
- Týmy můžete rozhodnout pro specifické pro týmový projekt šablony nebo specifické týmu nástroje v jednom týmového projektu pro celou skupinu. V takovém případě týmů měli vytvořit týmového projektu šablony úložiště nebo týmu nástroje úložiště v rámci stejné týmového projektu. Název tyto úložiště *< TeamName\>ProjectTemplate* a *< TeamName\>nástroje*, například *TeamJohnProjectTemplate*a *TeamJohnUtilities*. 

V každém případě týmů třeba dát členy týmu vědět, které šablony a nástroje pro úložiště přijmout při jejich nastavení a klonování úložiště projektů a nástroje. Projekt zájemců byste měli postupovat podle [projektu vést úlohy pro tým datové vědy](project-lead-tasks.md) k vytvoření projektu úložiště, jestli samostatné týmové projekty, nebo v rámci jedné týmového projektu. 


## <a name="0-prerequisites"></a>0. Požadavky

Požadavky jsou uspokojit dokončení úkolů, které správce vaší skupiny uvedených v [úlohy správce skupiny pro tým datové vědy](group-manager-tasks.md). To Shrneme zde, budou muset splnit před zahájením úloh realizace team následující požadavky: 

- Vaše **serveru služby VSTS skupiny** (nebo skupinový účet na některé další kód hostování platformy) byla nastavena správcem vaší skupiny.
- Vaše **GroupProjectTemplate úložiště** (R1) byla nastavena na vašem účtu skupiny správcem skupiny kódu hostování platformu, kterou plánujete použít.
- Byli jste **oprávnění** na vašem účtu skupiny k vytvoření úložiště pro váš tým.
- Git musí být nainstalován na váš počítač. Pokud používáte datové vědy virtuálního počítače (DSVM), předem nainstaloval Git a můžete se pustit do práce. Jinak, najdete v článku [platformy a nástroje příloha](platforms-and-tools.md#appendix).  
- Pokud používáte **Windows DSVM**, musíte mít [Git přihlašovacích údajů správce (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) nainstalovaný na počítači. V souboru README.md přejděte dolů k položce **stáhněte a nainstalujte** části a klikněte na tlačítko *nejnovější verzi instalačního programu*. Tím přejdete na stránku nejnovější instalační služby. Stažení instalačního programu .exe z tohoto umístění a spusťte ji. 
- Pokud používáte **Linux DSVM**, vytvořte veřejný klíč SSH na vaše DSVM a přidejte ho k serveru služby VSTS skupiny. Další informace o SSH naleznete v tématu **vytvořit veřejný klíč SSH** kapitoly [platformy a nástroje příloha](platforms-and-tools.md#appendix). 
    
## <a name="1-create-a-team-project-and-repositories"></a>1. Vytvoření týmového projektu a úložiště

Dokončení tohoto kroku, pokud používáte služby VSTS jako kód hostování platforma pro správu verzí a spolupráci. Tato část obsahuje, můžete vytvořit tři artefakty na serveru služby VSTS skupiny:

- **MyTeam** projekt v služby VSTS
- **MyProjectTemplate** úložiště (**R3**) na Git
- **MyTeamUtilities** úložiště (**R4**) na Git

### <a name="create-the-myteam-project"></a>Vytvoření projektu MyTeam

- Přejděte na domovskou stránku serveru služby VSTS vaší skupiny na adrese URL `https://<VSTS Server Name\>.visualstudio.com`. 
- Klikněte na tlačítko **nový** k vytvoření týmového projektu. 

    ![2](./media/team-lead-tasks/team-leads-2-create-new-team.png)

- Okno Vytvoření týmového projektu zobrazí výzvu k zadání názvu projektu (**MyTeam** v tomto příkladu). Ujistěte se, že jste vybrali **Agile** jako **šablony procesu** a **Git** jako **verzí**. 

    ![3](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)

- Klikněte na tlačítko **vytvořit projekt**. Týmový projekt **MyTeam** je vytvořen v menší než 1 minuta. 

- Po týmový projekt **MyTeam** je vytvořili, klikněte na **přejděte do projektu** tlačítko přesměrováni na domovské stránce týmového projektu. 

    ![4](./media/team-lead-tasks/team-leads-4-create-new-team-3.png)

- Pokud se zobrazí **Blahopřejeme!** automaticky otevíraném okně, klikněte **přidejte kód** (tlačítko v červeným rámečkem). Jinak, klikněte na tlačítko **kód** (v žlutý pole). To vás přesměruje na stránce úložiště Git týmového projektu. 

    ![5](./media/team-lead-tasks/team-leads-5-team-project-home.png)

### <a name="create-the-myprojecttemplate-repository-r3-on-git"></a>Vytvoření úložiště MyProjectTemplate (R3) na Git

- Na stránce úložiště Git týmového projektu, klikněte na šipku dolů vedle názvu úložiště **MyTeam**a vyberte **spravovat úložiště...** .

    ![6](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)

- Na **verzí** karta na ovládacím panelu týmového projektu, klikněte na tlačítko **MyTeam**, pak vyberte **přejmenování úložiště...** . 

    ![7](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)

- Zadejte nový název do úložiště v **přejmenovat úložiště MyTeam** okno. V tomto příkladu *MyTeamProjectTemplate*. Můžete zvolit něco podobného jako *< název vašeho týmu\>ProjectTemplate*. Klikněte na tlačítko **přejmenovat** pokračujte.

    ![8](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)

### <a name="create-the-myteamutilities-repository-r4-on-git"></a>Vytvoření úložiště MyTeamUtilities (R4) na Git

- Chcete-li vytvořit nové úložiště *< název vašeho týmu\>nástroje* v týmových projektech, klikněte na možnost **nového úložiště...**  na **verzí** kartě týmového projektu ovládací panely.  

    ![9](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)

- V **vytvořit nové úložiště** okna, která se objeví, zadejte název pro toto úložiště. V tomto příkladu jsme pojmenujte ji jako *MyTeamUtilities*, což je **R4** naše notaci. Zvolte něco podobného jako *< název vašeho týmu\>nástroje*. Ujistěte se, že jste vybrali **Git** pro **typu**. Potom klikněte na **vytvořit** pokračujte.

    ![10](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)

- Zkontrolujte, jestli dva nové úložiště Git se vytvořil v rámci týmový projekt **MyTeam**. V tomto příkladu: 

- **MyTeamProjectTemplate** (R3) 
- **MyTeamUtilities** (R4).

    ![11](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)


## <a name="2-seed-your-team-projecttemplate-and-teamutilities-repositories"></a>2. Počáteční hodnoty váš tým ProjectTemplate a TeamUtilities úložiště

Synchronizace replik indexů postup používá adresáře na vaše místní DSVM jako zprostředkující pracovní lokality. Pokud potřebujete nastavit vaše **ProjectTemplate** a **TeamUtilities** úložiště ke splnění některé konkrétní týmové vyžaduje, uděláte v předposlední kroku tohoto postupu. Zde je souhrn s postupem počáteční hodnoty obsah **MyTeamProjectTemplate** a **MyTeamUtilities** úložiště pro data vědecké účely tým. Jednotlivé kroky odpovídají témata v procesu synchronizace replik indexů:

- Klonování úložiště skupiny do místního adresáře: team R1 - na -> místní D1 klonovat
- Klonování úložiště váš tým do místního adresáře: team R3 & R4 - na -> místní D3 & D4 klonovat
- Zkopírujte obsah šablony projektu skupiny do složky místní team: D1 - obsah zkopírován do -> D3
- (Volitelné) přizpůsobení místní D3 & D4
- Odešlete obsah místního adresáře do úložiště team: D3 & D4 - na -> přidat obsah team R3 & R4


### <a name="initialize-the-team-repositories"></a>Inicializace úložiště team

V tomto kroku inicializaci úložiště týmového projektu šablony z úložiště šablony projektu skupiny:

- **MyTeamProjectTemplate** úložiště (**R3**) z vaší **GroupProjectTemplate** (**R1**) úložiště


### <a name="clone-group-repositories-into-local-directories"></a>Klonování skupiny úložišť do místního adresáře

Zahájíte tento postup:

- Vytváření adresářů na místním počítači:
    - Pro **Windows**: **C:\GitRepos\GroupCommon** a **C:\GitRepos\MyTeam**
    - Pro **Linux**: **GitRepos\GroupCommon** a **GitRepos\MyTeam** na domovský adresář 
- Přejděte do adresáře **GitRepos\GroupCommon**.
- Spusťte následující příkaz, podle potřeby, v operačním systému místního počítače.

**Windows**

    git clone https://<Your VSTS Server name>.visualstudio.com/GroupCommon/_git/GroupProjectTemplate
    

![12](./media/team-lead-tasks/team-leads-12-create-two-group-repos.png)

**Linux**
    
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/GroupCommon/_git/GroupProjectTemplate
    
    
![13](./media/team-lead-tasks/team-leads-13-clone_two_group_repos_linux.png)

Tyto příkazy klonovat vaše **GroupProjectTemplate** úložiště (R1) na serveru služby VSTS skupiny do místního adresáře v **GitRepos\GroupCommon** na místním počítači. Po klonování, adresář **GroupProjectTemplate** (D1) je vytvořen v adresáři **GitRepos\GroupCommon**. Zde jsme Předpokládejme, že váš správce skupiny vytvořili týmového projektu **GroupCommon**a **GroupProjectTemplate** úložiště je pod tímto týmovým projektem. 


### <a name="clone-your-team-repositories-into-local-directories"></a>Klonování úložiště váš tým do místního adresáře

Tyto příkazy klonovat vaše **MyTeamProjectTemplate** (R3) a **MyTeamUtilities** úložiště (R4) v týmových projektech **MyTeam** na server služby VSTS skupiny, abyste mohli  **MyTeamProjectTemplate** (D3) a **MyTeamUtilities** (D4) adresářů v **GitRepos\MyTeam** na místním počítači. 

- Přejděte do adresáře **GitRepos\MyTeam**
- Spusťte následující příkazy, podle potřeby, v operačním systému místního počítače. 

**Windows**

    git clone https://<Your VSTS Server name>.visualstudio.com/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone https://<Your VSTS Server name>.visualstudio.com/<Your Team Name>/_git/MyTeamUtilities

![14](./media/team-lead-tasks/team-leads-14-clone_two_empty_team_repos.png)
        
**Linux**
    
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamUtilities
    
![15](./media/team-lead-tasks/team-leads-15-clone_two_empty_team_repos_linux.png)

Po klonování, dva adresáře **MyTeamProjectTemplate** (D3) a **MyTeamUtilities** (D4) jsou vytvářeny v adresáři **GitRepos\MyTeam**. Můžeme mít předpokládá, že zde název týmového projektu šablony a nástroje pro úložiště **MyTeamProjectTemplate** a **MyTeamUtilities**. 

### <a name="copy-the-group-project-template-content-to-the-local-team-project-template-directory"></a>Kopírovat obsah šablony projektu skupiny k adresáři místní týmového projektu šablony

Kopírování obsahu místní **GroupProjectTemplate** (D1) složku pro místní **MyTeamProjectTemplate** (D3), spusťte jeden z následujících skriptů prostředí: 

#### <a name="from-the-powershell-command-line-for-windows"></a>Z příkazového řádku pro Windows PowerShell       

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 2

    
![16](./media/team-lead-tasks/team-leads-16-local_copy_team_lead_new.png)

#### <a name="from-the-linux-shell-for-the-linux-dsvm"></a>Z prostředí Linux pro **Linux DSVM**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 2
    
![17](./media/team-lead-tasks/team-leads-17-local-copy-team-lead-linux-new.png)

Skripty vyloučit obsah .git adresáře. Skripty vyzve k zadání **dokončení cesty** zdrojový adresář D1 a cílový adresář D3.
        

### <a name="customize-your-team-project-template-or-team-utilities-optional"></a>Přizpůsobení šablony týmového projektu nebo nástroje team (volitelné)

Přizpůsobení vaší **MyTeamProjectTemplate** (D3) a **MyTeamUtilities** (D4), v případě potřeby v této fázi procesu instalace. 

- Pokud chcete přizpůsobit obsah D3 pro konkrétní potřeby vašeho týmu, můžete upravit šablonu dokumenty nebo změnit strukturu adresáře.

- Pokud váš tým vyvíjí některé nástroje, které chcete sdílet s týmem celý, zkopírujte a vložte do adresáře D4 tyto nástroje. 


### <a name="push-local-directory-content-to-team-repositories"></a>Odešlete obsah místního adresáře do týmu úložiště

Pro přidání obsah místního adresáře (volitelně přizpůsobené) D3 a D4 do úložiště team R3 a R4, spusťte následující git bash příkazy z konzoly Windows Powershellu nebo z prostředí Linux. Spusťte příkazy z **GitRepos\MyTeam\MyTeamProjectTemplate** adresáře.

    git status
    git add .
    git commit -m"push from DSVM"
    git push
    
![18](./media/team-lead-tasks/team-leads-18-push-to-group-server-2.png)

Soubory v úložišti MyTeamProjectTemplate vaší skupině služby VSTS serveru se synchronizují téměř okamžitě při spuštění tohoto skriptu.

![19](./media/team-lead-tasks/team-leads-19-push-to-group-server-showed-up.png)

Teď spustit stejnou sadu čtyři příkazy gitu z **GitRepos\MyTeam\MyTeamUtilities** adresáře. 

> [AZURE.NOTE]Pokud je prvním potvrzení do úložiště Git, musíte nakonfigurovat globální parametry *user.name* a *user.email* před spuštěním `git commit` příkaz. Spusťte následující dva příkazy:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Pokud jsou přiřazeni více úložiště Git, použijte stejný název a e-mailovou adresu při potvrzení pro každé z nich. Pomocí stejné jméno a e-mailovou adresu prokáže pohodlný později při sestavování řídicí panely Power BI a sledovat aktivity Git na více úložiště.

![20](./media/team-lead-tasks/team-leads-20-git-config-name.png)


## <a name="3-create-team-data-and-analytics-resources-optional"></a>3. Vytvořit tým data a analýzy prostředky (volitelné)

Sdílení prostředků data a analýzy s týmem celý přináší výhody výkon a nákladová: členové týmu může provést jejich projektů na sdílené prostředky, uložte na rozpočty a efektivnější. V této části poskytujeme pokyny o tom, jak vytvořit úložiště Azure file. V další části poskytujeme pokyny o tom, jak úložiště Azure připojit soubor do místního počítače. Další informace o sdílení jiné prostředky, například Data vědecké účely virtuální počítače Azure, Azure clustery Spark HDInsight, naleznete v části [platformy a nástroje](platforms-and-tools.md). Toto téma poskytuje pokyny z hlediska vědecké účely data o výběru prostředky, které jsou vhodné pro vaše potřeby a odkazy na stránky produktu a další relevantní a užitečné kurzy, které jsme publikovali.

>[AZURE.NOTE] Aby se zabránilo přenosu mezi dat datových center, které může být pomalé a nákladná, ujistěte se, že skupinu prostředků, účet úložiště a virtuální počítač Azure (například DSVM) jsou ve stejném centru Azure data. 

Spusťte tyto skripty k vytvoření úložiště Azure pro váš tým. Úložiště Azure pro váš tým slouží k ukládání datových prostředků, které jsou užitečné pro celý tým. Skripty výzvu pro vaši Azure účet a předplatné informaci, takže mají připravení zadejte tyto přihlašovací údaje. 

### <a name="create-azure-file-storage-with-powershell-from-windows"></a>Vytvoření úložiště Azure file pomocí prostředí PowerShell ze systému Windows

Spusťte tento skript z prostředí PowerShell příkazového řádku:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
    .\CreateFileShare.ps1

![21](./media/team-lead-tasks/team-leads-21-create-fileshare-win.png)   

Přihlaste se ke svému účtu Microsoft Azure po zobrazení výzvy:

![22](./media/team-lead-tasks/team-leads-22-file-create-s1.png)

Vyberte předplatné Azure, kterou chcete použít:

![23](./media/team-lead-tasks/team-leads-23-file-create-s2.png)

Vyberte účtu úložiště, který se má použít nebo vytvořit novou v rámci svého vybraného předplatného:

![24](./media/team-lead-tasks/team-leads-24-file-create-s3.png)

Zadejte název pro úložiště Azure file k vytvoření. Pouze nižší případ písmen, číslic a - přijímají:

![25](./media/team-lead-tasks/team-leads-25-file-create-s4.png)

Pro usnadnění připojení a sdílení Toto úložiště po vytvoření, uložte informace o Azure file úložiště do textového souboru a poznamenejte si cestu k jeho umístění. Konkrétně je nutné tento soubor připojit úložiště Azure file na virtuálních počítačích Azure v další části. 

Je vhodné zkontrolovat v tomto souboru do úložiště ProjectTemplate týmu. Doporučujeme, abyste uvést v adresáři **Docs\DataDictionaries**. Proto tento datový prostředek přístupná pomocí všechny projekty ve vašem týmu. 

![26](./media/team-lead-tasks/team-leads-26-file-create-s5.png)


### <a name="create-azure-file-storage-with-a-linux-script"></a>Vytvoření úložiště Azure file pomocí skriptu Linux

Spusťte tento skript z prostředí Linux:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
    bash CreateFileShare.sh

Přihlaste se ke svému účtu Microsoft Azure, postupujte podle pokynů na této obrazovce:

![27](./media/team-lead-tasks/team-leads-27-file-create-linux-s1.png)

Vyberte předplatné Azure, kterou chcete použít:

![28](./media/team-lead-tasks/team-leads-28-file-create-linux-s2.png)

Vyberte účtu úložiště, který se má použít nebo vytvořit novou v rámci svého vybraného předplatného:

![29](./media/team-lead-tasks/team-leads-29-file-create-linux-s3.png)

Zadejte název úložiště Azure file k vytváření, jenom malá písmena, čísla a - přijímají:

![30](./media/team-lead-tasks/team-leads-30-file-create-linux-s4.png)

Pro usnadnění přístupu k toto úložiště po vytvoření, uložte informace o Azure file úložiště do textového souboru a poznamenejte si cestu k jeho umístění. Konkrétně je nutné tento soubor připojit úložiště Azure file na virtuálních počítačích Azure v další části.

Je vhodné zkontrolovat v tomto souboru do úložiště ProjectTemplate týmu. Doporučujeme, abyste uvést v adresáři **Docs\DataDictionaries**. Proto tento datový prostředek přístupná pomocí všechny projekty ve vašem týmu. 

![31](./media/team-lead-tasks/team-leads-31-file-create-linux-s5.png)


## <a name="4-mount-azure-file-storage-optional"></a>4. Úložiště Azure připojit soubor (volitelné)

Po úspěšném vytvoření úložiště Azure file může být připojen do místního počítače pomocí jedné z následujících skriptů prostředí PowerShell nebo Linux.

### <a name="mount-azure-file-storage-with-powershell-from-windows"></a>Soubor úložiště Azure připojit pomocí prostředí PowerShell ze systému Windows

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
    .\AttachFileShare.ps1
    
Zobrazí se výzva k přihlášení první, pokud není přihlášený. 

Klikněte na tlačítko **Enter** nebo **y** pokračovat po zobrazení dotazu, pokud máte informace úložiště Azure file souboru a poté zadejte ***dokončení název a cesta k** vytvoříte v souboru předchozí krok. Informace o připojení Azure file storage je číst přímo ze souboru a jste připraveni přejděte k dalšímu kroku.

![32](./media/team-lead-tasks/team-leads-32-attach-s1.png)

> [AZURE.NOTE] Pokud jste soubor obsahující informace o úložiště Azure file, kroky jako vstup z klávesnice informace jsou uvedeny na konci této části.

Potom budete vyzváni k zadání názvu disku, který se má přidat k virtuálnímu počítači. Seznam názvů stávající disk se vytiskne na obrazovce. Název jednotky, která ještě neexistuje v seznamu by měl poskytovat.

![33](./media/team-lead-tasks/team-leads-33-attach-s2.png)

Potvrďte, že na nový disk F úspěšně připojen k vašemu počítači.

![34](./media/team-lead-tasks/team-leads-34-attach-s3.png)

**Jak ručně zadat informace o úložiště Azure file:** Pokud nemáte vaše informace Azure file storage s textovým souborem, můžete podle pokynů na následující obrazovce zadejte požadované předplatné, účet úložiště a Azure informace o souboru úložiště:

![35](./media/team-lead-tasks/team-leads-35-attach-s4.png)

Zadejte předplatné Azure název, vyberte účet úložiště, kde se má vytvořit úložiště Azure file, a zadejte název úložiště Azure file:

![36](./media/team-lead-tasks/team-leads-36-attach-s5.png)

### <a name="mount-azure-file-storage-with-a-linux-script"></a>Úložiště souborů připojení Azure pomocí skriptu Linux

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
    bash AttachFileShare.sh

![37](./media/team-lead-tasks/team-leads-37-attach-s1-linux.png)

Zobrazí se výzva k přihlášení první, pokud není přihlášený. 

Klikněte na tlačítko **Enter** nebo **y** pokračovat po zobrazení dotazu, pokud máte informace úložiště Azure file souboru a poté zadejte ***dokončení název a cesta k** vytvoříte v souboru předchozí krok. Informace o připojení Azure file storage je číst přímo ze souboru a jste připraveni přejděte k dalšímu kroku.

![38](./media/team-lead-tasks/team-leads-38-attach-s2-linux.png)

Potom budete vyzváni k zadání názvu disku, který se má přidat k virtuálnímu počítači. Seznam názvů stávající disk se vytiskne na obrazovce. Název jednotky, která ještě neexistuje v seznamu by měl poskytovat.

![39](./media/team-lead-tasks/team-leads-39-attach-s3-linux.png)

Potvrďte, že na nový disk F úspěšně připojen k vašemu počítači.

![40](./media/team-lead-tasks/team-leads-40-attach-s4-linux.png)

**Jak ručně zadat informace o úložiště Azure file:** Pokud nemáte vaše informace Azure file storage s textovým souborem, můžete podle pokynů na následující obrazovce zadejte požadované předplatné, účet úložiště a Azure informace o souboru úložiště:

- Vstup **n**.
- Vyberte index název odběru, které bylo vytvořeno úložiště Azure file v předchozím kroku:

    ![41](./media/team-lead-tasks/team-leads-41-attach-s5-linux.png)

- Vyberte účet úložiště v rámci předplatného a typ v názvu úložiště Azure:

    ![42](./media/team-lead-tasks/team-leads-42-attach-s6-linux.png)

- Zadejte název jednotky pro přidání do vašeho počítače, které by měly být odlišný od všechny existující:

    ![43](./media/team-lead-tasks/team-leads-43-attach-s7-linux.png)


## <a name="5-set-up-security-control-policy"></a>5. Nastavte zásady zabezpečení ovládacího prvku 

Ze serveru vaší skupiny služby VSTS domovské stránky, klikněte na tlačítko **ozubené kolečko ikonu** vedle vašeho uživatelského jména v pravém horním rohu vyberte **zabezpečení** kartě. Můžete přidat členy si s týmem zde s různými oprávnění.

![44](./media/team-lead-tasks/team-leads-44-add-team-members.png)

## <a name="next-steps"></a>Další postup

Tady jsou odkazy na podrobnější popis jednotlivých rolí a úloh, které jsou definované procesu Team dat vědecké účely:

- [Úlohy správce skupiny pro data tým vědecké účely](group-manager-tasks.md)
- [Tým realizace úlohy pro data tým vědecké účely](team-lead-tasks.md)
- [Úlohy realizace projektu pro data tým vědecké účely](project-lead-tasks.md)
- [Jednotlivé přispěvatele projektu pro data tým vědecké účely](project-ic-tasks.md)