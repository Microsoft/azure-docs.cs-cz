---
title: Správce skupiny týmu Data Science procesů úloh – Azure | Dokumentace Microsoftu
description: Přehled úloh pro správce skupiny v týmovém projektu datové vědy.
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
ms.openlocfilehash: a89c0f916f67de1bae81a5e1b3dcfc2cae41d248
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44304182"
---
# <a name="group-manager-tasks"></a>Správce skupiny úloh

Tohoto tématu jsou podrobněji popsány dále úlohy, které má správce skupiny pro provést hist / jeho organizace vědy data. Cílem je vytvořit prostředí pro spolupráci skupiny, který standardizuje na [vědecké zpracování týmových dat](overview.md) (TDSP). Přehled role pracovníky a jejich přidružených úloh, které jsou zpracovávány týmem datové vědy standardizovat o tomto procesu najdete v části [vědecké zpracování týmových dat role a úlohy](roles-tasks.md).

**Správce skupiny** je správce částí celé datové vědy v podniku. Datové vědy jednotky může mít několik týmů, z nichž každý pracuje na více projekty datových věd jednotlivou obchodní vertikálně. Správce skupiny může přenést své úkoly náhradní, ale úkoly spojené s rolí jsou stejné. Existuje šest hlavních úloh, jak je znázorněno v následujícím diagramu:

![0](./media/group-manager-tasks/tdsp-group-manager.png)


>[AZURE.NOTE] Uvádíme kroky potřebné k nastavení prostředí TDSP skupiny pomocí služby Azure DevOps v následujících pokynech. Můžeme určit způsob k provedení těchto úloh se službami Azure DevOps, protože to je, jak můžeme implementovat TDSP v Microsoftu. Pokud jiný kód hostování platformy se používá pro vaši skupinu, úlohy, které musíte provést správce skupiny obecně se nezmění. Ale způsob k provedení těchto úloh se bude lišit.

1. Nastavit **Azure DevOps služby** pro skupinu.
2. Vytvoření **projektu skupiny** na služby Azure DevOps (pro uživatele služby Azure DevOps)
3. Vytvořte **GroupProjectTemplate** úložiště
4. Vytvořte **GroupUtilities** úložiště
5. Počáteční hodnota **GroupProjectTemplate** a **GroupUtilities** úložišť pro služby Azure DevOps s obsahem z TDSP úložišť.
6. Nastavit **kontrolních mechanismů pro zabezpečení** členové týmu přístup k úložištím GroupProjectTemplate a GroupUtilities.

Každý z předchozích kroků je podrobně popsáno v. Ale nejprve jsme vám seznámit se s zkratky a diskutovat o požadavcích pro práci s úložišti.

### <a name="abbreviations-for-repositories-and-directories"></a>Zkratky pro úložiště a adresáře

Tento kurz používá zkrácené názvy pro úložiště a adresáře. Tyto definice usnadňují sledování operací mezi úložišť a adresáře. Tento typ notation se používá v následujících částech:

- **G1**: úložiště šablon projektu vyvinul a spravuje TDSP tým Microsoftu.
- **G2**: úložiště nástroje vyvinul a spravuje TDSP tým Microsoftu.
- **R1**: The GroupProjectTemplate úložiště v Gitu jste nastavili na váš server Azure DevOps skupiny.
- **R2**: The GroupUtilities úložiště v Gitu jste nastavili na váš server Azure DevOps skupiny.
- **Lg1** a **LG2**: místní adresáře na počítači, že klonování G1 a G2, v uvedeném pořadí.
- **LR1** a **LR2**: místní adresáře na počítači, že klonování R1 a R2, v uvedeném pořadí.

### <a name="pre-requisites-for-cloning-repositories-and-checking-code-in-and-out"></a>Předpoklady pro klonování úložišť a kontrola kódu dovnitř a ven
 
- Git musí být nainstalován na počítači. Pokud používáte Data virtuálního počítače VĚDY, Git je předem nainstalovaný a jste připravení. V opačném případě najdete v článku [platformami a nástroji příloha](platforms-and-tools.md#appendix).  
- Pokud používáte **Windows DSVM**, musíte mít [Git Credential Manageru (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) na vašem počítači nainstalovaný. V souboru README.md přejděte dolů k položce **stáhněte a nainstalujte** a klikněte *nejnovější instalační program*. Tento krok vás přesměruje na stránku pro nejnovější instalační program. Stažení instalačního programu .exe odsud a spustíme ji. 
- Pokud používáte **Linux DSVM**vytvořte veřejný klíč SSH na vaše DSVM a přidejte k vašim službám Azure DevOps skupiny. Další informace o SSH najdete v tématu **vytvořit veřejný klíč SSH** tématu [platformami a nástroji příloha](platforms-and-tools.md#appendix). 


## <a name="1-create-account-on-azure-devops-services"></a>1. Vytvoření účtu na službách Azure DevOps

Hostitelem služeb Azure DevOps následující úložiště:

- **Skupina úložišť běžné**: úložiště pro obecné účely, které může být osvojena více týmů v rámci skupiny pro více projekty datových věd. Například *GroupProjectTemplate* a *GroupUtilities* úložišť.
- **týmových úložišť**: úložiště pro konkrétní týmy v rámci skupiny. Tato úložiště jsou specifické pro potřeby týmu a může být více projektech provést daným týmem přijatý, ale není dostatečně obecný být užitečné pro více týmy v rámci datové vědy skupiny. 
- **Projekt úložišť**: úložiště nejsou k dispozici pro konkrétní projekty. Taková úložiště nemusí být dostatečně obecná, být užitečné pro více projektů, tým provádět a několik týmů v datové vědě skupiny.


### <a name="setting-up-the-azure-devops-services-sign-into-your-microsoft-account"></a>Nastavení přihlašování služby Azure DevOps do svého účtu Microsoft
    
Přejděte na [Visual Studio online](https://www.visualstudio.com/), klikněte na tlačítko **přihlášení** v pravém horním rohu a přihlaste se k účtu Microsoft. 
    
![1](./media/group-manager-tasks/login.PNG)

Pokud nemáte účet Microsoft, klikněte na tlačítko **zaregistrujte** k vytvoření účtu Microsoft a pak se přihlaste pomocí tohoto účtu. 

Pokud má vaše organizace předplatné sady Visual Studio/MSDN, klikněte na zelené **přihlaste pomocí svého pracovního nebo školního účtu** pole a přihlaste se pomocí přihlašovacích údajů spojených s tímto předplatným. 
        
![2](./media/group-manager-tasks/signin.PNG)


        
Jakmile se přihlásíte, klikněte na tlačítko **Create New Account** v pravém horním rohu jak je znázorněno na následujícím obrázku:
        
![3](./media/group-manager-tasks/create-account-1.PNG)
        
Zadejte informace pro služby Azure DevOps, které chcete vytvořit v **vytvoření účtu** průvodce s použitím následujících hodnot: 

- **Adresa URL serveru**: nahradit *mysamplegroup* vlastními *název serveru*. Adresa URL serveru bude: *https://\<servername\>. visualstudio.com*. 
- **Spravovat kód pomocí:** vyberte  **_Git_**.
- **Název projektu:** Enter *GroupCommon*. 
- **Uspořádat práci pomocí pracovních:** zvolit *Agile*.
- **Hostovat projekty v::** zvolte geografické polohy. V tomto příkladu jsme zvolili *střed USA – jih*. 
        
![4](./media/group-manager-tasks/fill-in-account-information.png)

>[AZURE.NOTE] Pokud následující automaticky otevírané okno zobrazí po kliknutí na **vytvořit nový účet**, pak budete muset kliknout na **změnit podrobnosti** zobrazíte všechna pole, které jsou oddělené.

![5](./media/group-manager-tasks/create-account-2.png)


Klikněte na tlačítko **pokračovat**. 

## <a name="2-groupcommon-project"></a>2. GroupCommon projektu

**GroupCommon** stránky (*https://\<servername\>.visualstudio.com/GroupCommon*) se otevře po vytvoření služby Azure DevOps.
                            
![6](./media/group-manager-tasks/server-created-2.PNG)

## <a name="3-create-the-grouputilities-r2-repository"></a>3. Vytvořit úložiště GroupUtilities (R2)

Chcete-li vytvořit **GroupUtilities** úložišti (R2) v rámci služby Azure DevOps:

- Otevřete **vytvořit nové úložiště** průvodce, klikněte na tlačítko **nové úložiště** na **verzí** kartu vašeho projektu. 

![7](./media/group-manager-tasks/create-grouputilities-repo-1.png) 

- Vyberte *Git* jako **typ**a zadejte *GroupUtilities* jako **název**a potom klikněte na tlačítko **vytvořit**. 

![8](./media/group-manager-tasks/create-grouputilities-repo-2.png)
                
Teď byste měli vidět dvě úložiště Git **GroupProjectTemplate** a **GroupUtilities** v levém sloupci **verzí** stránky: 

![9](./media/group-manager-tasks/two-repo-under-groupCommon.PNG)


## <a name="4-create-the-groupprojecttemplate-r1-repository"></a>4. Vytvořit úložiště GroupProjectTemplate (R1)

Nastavení úložiště pro skupiny server Azure DevOps se skládá ze dvou úloh:

- Přejmenujte výchozí **GroupCommon** úložiště***GroupProjectTemplate***.
- Vytvořte **GroupUtilities** úložiště ve službě Azure DevOps v rámci projektu **GroupCommon**. 

Pokyny pro první úkol jsou obsaženy v této části po poznámky na zásady vytváření názvů nebo našich úložištích a adresáře. Pokyny pro druhý úkol jsou obsaženy v kroku 4 v následující části.

### <a name="rename-the-default-groupcommon-repository"></a>Přejmenujte výchozí GroupCommon úložiště

Přejmenování výchozí **GroupCommon** úložiště jako *GroupProjectTemplate* (označované jako **R1** v tomto kurzu):
    
- Klikněte na tlačítko **spolupráce na kódu** na **GroupCommon** stránce projektu. Tím přejdete na výchozí stránce úložiště Git projektu **GroupCommon**. V současné době toto úložiště Git je prázdný. 

![10](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
        
- Klikněte na tlačítko **GroupCommon** na levého horního rohu (zvýrazněno s červeným rámečkem na následujícím obrázku) na stránce úložiště Git **GroupCommon** a vyberte **spravovat úložiště**(zvýrazněná zeleného pole na následujícím obrázku). Tento postup vyvolá **ovládací panely**. 
- Vyberte **verzí** kartu vašeho projektu. 

![11](./media/group-manager-tasks/rename-groupcommon-repo-4.png)

- Klikněte na tlačítko **...**  napravo od **GroupCommon** úložiště na levém panelu a vyberte **přejmenovat úložiště**. 

![12](./media/group-manager-tasks/rename-groupcommon-repo-5.png)
        
- V **přejmenovat úložiště GroupCommon** průvodce, který bodů POP, zadejte *GroupProjectTemplate* v **název úložiště** pole a potom klikněte na tlačítko **přejmenovat** . 

![13](./media/group-manager-tasks/rename-groupcommon-repo-6.png)



## <a name="5-seed-the-r1--r2-repositories-on-the-azure-devops-services"></a>5. Počáteční hodnoty R1 & R2 úložišť ve službě Azure DevOps

V této fázi procedury naplnit *GroupProjectTemplate* (R1) a *GroupUtilities* úložišť (R2), které jste vytvořili v předchozí části. Tato úložiště se nasadí se ***ProjectTemplate*** (**G1**) a ***nástroje*** (**G2**) úložiště, která se spravují přes Microsoft pro vědecké zpracování týmových dat Tato synchronizace replik indexů dokončení:

- R1 úložiště bude mít stejnou sadu adresářů a šablony dokumentů, jako G1
- R2 úložiště bude obsahovat sadu nástrojů vědy dat vyvinutých společností Microsoft.

Osazení postup používá adresáře na místním DSVM jako zprostředkující pracovní lokality. Tady jsou kroky, a potom v této části:

- Klonovat G1 & G2 - do -> LG1 & LG2
- R1 & R2 - do -> LR1 & LR2 klonovat
- Lg1 & LG2 - LR1 & LR2 -> zkopírovat do souborů
- (Volitelné) přizpůsobení LR1 & LR2
- LR1 & LR2 - do -> R1 & R2 přidat obsah


### <a name="clone-g1--g2-repositories-to-your-local-dsvm"></a>Klonovat G1 & G2 úložiště na místním DSVM

V tomto kroku naklonujete ProjectTemplate vědecké zpracování týmových dat (TDSP) úložiště (G1) a nástrojů (G2) z úložišť github TDSP do složky ve vaší místní DSVM jako LG1 a LG2:

- Vytvořte adresář, který slouží jako kořenový adresář pro hostování všech duplicit úložišť. 
    -  Ve Windows datové VĚDY, vytvořte adresář *C:\GitRepos\TDSPCommon*. 
    -  V systému Linux datové VĚDY, vytvořte adresář *GitRepos\TDSPCommon* ve svém domovském adresáři. 

- Spusťte následující sady příkazů z *GitRepos\TDSPCommon* adresáře.

    `git clone https://github.com/Azure/Azure-TDSP-ProjectTemplate`<br>
    `git clone https://github.com/Azure/Azure-TDSP-Utilities`
        
![14](./media/group-manager-tasks/two-folder-cloned-from-TDSP-windows.PNG)

- Pomocí našich názvy zkrácený úložiště, to je, co tyto skripty dosáhli: 
    - -G1 – klonovat do > LG1
    - G2 - klonovat do -> LG2
- Po dokončení klonování byste měli vidět dva adresáře _ProjectTemplate_ a _nástroje_v části **GitRepos\TDSPCommon** adresáře. 

### <a name="clone-r1--r2-repositories-to-your-local-dsvm"></a>Naklonujte úložiště R1 & R2 k vaší místní DSVM

V tomto kroku naklonujete úložiště GroupProjectTemplate (R1) a úložišti GroupUtilities (R2) na místní adresáře (označované jako LR1 a LR2,) v části **GitRepos\GroupCommon** na vaše DSVM.

- Adresy URL úložiště R1 a R2, přejděte k vaší **GroupCommon** domovskou stránku na služby Azure DevOps. Tato adresa URL má obvykle *https://\<Azure DevOps služby jméno\>.visualstudio.com/GroupCommon*. 
- Klikněte na tlačítko **kód**. 
- Zvolte **GroupProjectTemplate** a **GroupUtilities** úložišť. Zkopírujte a uložte každou z adres URL (protokol HTTPS pro Windows; SSH pro Linux) z **adresa URL klonu** element v důsledku pro použití v těchto skriptů:  

![15](./media/group-manager-tasks/find_https_ssh_2.PNG)

- Přejděte do **GitRepos\GroupCommon** ve vaší Windows nebo Linux DSVM a spusťte jeden z následující sady příkazů naklonujte R1 a R2 do tohoto adresáře.
        
Tady se o skripty Windows a Linux:

    # Windows DSVM

    git clone <the HTTPS URL of the GroupProjectTemplate repository>
    git clone <the HTTPS URL of the GroupUtilities repository>

![16](./media/group-manager-tasks/clone-two-empty-group-reo-windows-2.PNG)

    # Linux DSVM

    git clone <the SSH URL of the GroupProjectTemplate repository>
    git clone <the SSH URL of the GroupUtilities repository>

![17](./media/group-manager-tasks/clone-two-empty-group-reo-linux-2.PNG)

>[AZURE.NOTE] By se měl zobrazit upozornění, že LR1 a LR2 jsou prázdné.    

- Pomocí našich názvy zkrácený úložiště, to je, co tyto skripty dosáhli: 
    - R1 - klonovat do -> LR1
    - LR2-R2 - klonovat do >   


### <a name="seed-your-groupprojecttemplate-lr1-and-grouputilities-lr2"></a>Počáteční hodnoty GroupProjectTemplate (LR1) a GroupUtilities (LR2)

V dalším kroku v místním počítači, zkopírujte obsah ProjectTemplate a nástroje adresáře (s výjimkou metadata v adresářích .git) v části GitRepos\TDSPCommon k adresářům GroupProjectTemplate a GroupUtilities pod **GitRepos\ GroupCommon**. Tady jsou dva úkoly k dokončení v tomto kroku:

- Zkopírujte soubory v GitRepos\TDSPCommon\ProjectTemplate (**LG1**) k GitRepos\GroupCommon\GroupProjectTemplate (**LR1**) 
- Zkopírujte soubory v GitRepos\TDSPCommon\Utilities (**LG2** k GitRepos\GroupCommon\Utilities (**LR2**). 

K dosažení těchto dvou úloh, spuštěním následujících skriptů v konzole Powershellu (Windows) nebo prostředí skriptu konzoly (Linux). Zobrazí se výzva k zadání úplné cesty k LG1, LR1, LG2 a LR2. Cesty, které zadáte, se ověří. Zadejte adresář, který neexistuje, budete vyzváni k zadání znovu. 

    # Windows DSVM      
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 1

![18](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows-2.PNG)

Nyní uvidíte, že soubory v adresářích LG1 a LG1 (s výjimkou souborů v adresáři .git) byly zkopírovány do LR1 a LR2, v uvedeném pořadí.

![19](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows.PNG)

    # Linux DSVM

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 1

![20](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux-2.PNG)
        
Teď vidíte, že se soubory ve dvou složkách (s výjimkou souborů v adresáři .git) zkopírují do GroupProjectTemplate a GroupUtilities v uvedeném pořadí.
    
![21](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux.PNG)

- Pomocí našich názvy zkrácený úložiště, to je, co tyto skripty dosáhli:
    - Lg1 - LR1 -> zkopírovat do souborů
    - Lg2 - LR2 -> zkopírovat do souborů

### <a name="option-to-customize-the-contents-of-lr1--lr2"></a>Možnost upravit obsah LR1 & LR2
    
Pokud chcete upravit obsah LR1 a LR2 splnili specifické požadavky vaší skupiny, je to fáze procedury, kde, která je vhodná. Můžete upravit šablony dokumentů, změnit strukturu adresáře a přidat stávající nástroje, který byl vyvinut vaší skupiny nebo které jsou užitečné pro celou skupinu. 

### <a name="add-the-contents-in-lr1--lr2-to-r1--r2-on-group-server"></a>Přidejte obsah v LR1 & LR2 R1 & R2 na serveru skupiny

Teď budete muset přidat obsah v LR1 a LR2 k úložištím R1 a R2. Tady je git příkazy prostředí bash, které můžete spustit v prostředí Windows PowerShell nebo Linux. 

Spusťte následující příkazy z adresáře GitRepos\GroupCommon\GroupProjectTemplate:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

Možnost -m umožňuje nastavit zprávu o potvrzení git.

![22](./media/group-manager-tasks/push-to-group-server-2.PNG)

Uvidíte, že ve vaší skupině služby Azure DevOps, v úložišti GroupProjectTemplate soubory jsou synchronizovány okamžitě.

![23](./media/group-manager-tasks/push-to-group-server-showed-up-2.PNG)

Nakonec změňte **GitRepos\GroupCommon\GroupUtilities** adresáře a spustit stejnou sadu git příkazy bash:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

>[AZURE.NOTE] Pokud to je poprvé, kdy jste se zavázali k úložišti Git, musíte nakonfigurovat globální parametry *user.name* a *user.email* předtím, než spustíte `git commit` příkazu. Spusťte následující dva příkazy:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
>Pokud jsou přiřazeni k více úložišť Git, použijte stejný název a e-mailovou adresu když jste se zavázali k každý z nich. Pomocí stejného jména a e-mailovou adresu prokáže pohodlný později při vytváření řídicích panelů Power BI ke sledování aktivit Git na více úložišť.


- Pomocí našich názvy zkrácený úložiště, to je, co tyto skripty dosáhli:
    - LR1 - obsah přidat -> R1
    - LR2 - přidat obsah do R2 ->

## <a name="6-add-group-members-to-the-group-server"></a>6. Přidání členů do skupiny na serveru skupiny

Ze skupiny Azure DevOps služby na domovskou stránku, klikněte na tlačítko **ikonu ozubeného kola** vedle vaše uživatelské jméno v pravém horním rohu vyberte **zabezpečení** kartu. Přidat členy do skupiny tady s různými oprávněními.

![24](./media/group-manager-tasks/add_member_to_group.PNG) 


## <a name="next-steps"></a>Další postup

Tady jsou odkazy na podrobnější popis role a úlohy určené vědecké zpracování týmových dat:

- [Úlohy správce skupiny pro datové vědy tým](group-manager-tasks.md)
- [Úkoly vedoucí týmu pro datové vědy tým](team-lead-tasks.md)
- [Vedoucí úkoly pro datové vědy tým projektu](project-lead-tasks.md)
- [Jednotlivé přispěvatelé projektu týmu datové vědy](project-ic-tasks.md)