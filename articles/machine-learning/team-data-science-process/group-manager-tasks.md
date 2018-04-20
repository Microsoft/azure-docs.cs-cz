---
title: Úlohy správce skupiny pro proces Team Data vědecké účely – Azure | Microsoft Docs
description: Přehled úlohy pro skupiny Správci na datové vědě týmového projektu.
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
ms.author: bradsev
ms.openlocfilehash: 3c39ae865ec26567f70706b042dab2aa8e4939d3
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
---
# <a name="group-manager-tasks"></a>Správce skupiny úlohy

Toto téma obsahuje přehled úlohy, které správce skupiny musí být provedeny pro hist / jeho organizace vědecké účely data. Cílem je vytvoření spolupráce skupiny prostředí standardizující na [proces vědecké účely dat Team](overview.md) (TDSP). Přehled role pracovníky a jejich přidružených úloh, které zpracovávají datové vědy tým standardizace tohoto postupu najdete v tématu [proces vědecké účely dat Team rolí a úloh](roles-tasks.md).

**Správce skupiny** je správce celé datové vědy jednotky v podniku. Datové vědy jednotky může mít několik týmů, z nichž každý pracuje na více projektů pocházejícími jedinečných obchodních dat vědecké účely. Správce skupiny může delegovat na náhradní jejich úloh, ale úkoly spojené s rolí jsou stejné. Jak je znázorněno v následujícím diagramu jsou šesti hlavní úlohy:

![0](./media/group-manager-tasks/tdsp-group-manager.png)


>[AZURE.NOTE] Jsme popisují kroky potřebné k nastavení prostředí pro skupiny TDSP pomocí služby VSTS v následujících pokynech. Určíme jak provést tyto úlohy s služby VSTS, protože se jedná, jak jsme implementovat TDSP ve společnosti Microsoft. Pokud jiný kód hostování platformy slouží pro vaši skupinu, úlohy, které je třeba provést pomocí Správce skupiny obecně se nezmění. Ale způsob k dokončení těchto úloh se bude lišit.

1. Nastavit **serveru Visual Studio Team Services (VSTS)** pro skupinu.
2. Vytvoření **skupiny týmového projektu** na serveru pro Visual Studio Team Services (pro služby VSTS uživatelů)
3. Vytvořte **GroupProjectTemplate** úložiště
4. Vytvořte **GroupUtilities** úložiště
5. Počáteční hodnoty **GroupProjectTemplate** a **GroupUtilities** úložiště pro server služby VSTS s obsah z úložiště TDSP.
6. Nastavit **ovládacích prvků zabezpečení** pro členy týmu s přístupem k GroupProjectTemplate a GroupUtilities úložiště.

Je podrobně popsán všechny předchozí kroky. Ale nejprve jsme vám seznámit se s zkratky a popisují předpoklady pro práci s úložiště.

### <a name="abbreviations-for-repositories-and-directories"></a>Zkratky pro úložiště a adresáře

Tento kurz používá zkrácený názvy pro úložiště a adresáře. Tyto definice usnadňují následovat operace mezi úložiště a adresářů. Tento zápis se používá v následujících částech:

- **G1**: úložiště šablon projektu vyvíjí a spravuje TDSP týmu společnosti Microsoft.
- **G2**: úložiště nástroje vyvíjí a spravuje TDSP týmu společnosti Microsoft.
- **R1**: GroupProjectTemplate úložišti na Git nastavit na vašem serveru služby VSTS skupiny.
- **R2**: GroupUtilities úložišti na Git nastavit na vašem serveru služby VSTS skupiny.
- **Lg1** a **LG2**: místní adresáře na počítači, v uvedeném pořadí klonovat G1 a G2.
- **LR1** a **LR2**: místní adresáře na počítači, v uvedeném pořadí klonovat R1 a R2.

### <a name="pre-requisites-for-cloning-repositories-and-checking-code-in-and-out"></a>Předpoklady pro klonování úložiště a kontrola kódu a odhlášení
 
- Git musí být nainstalován na váš počítač. Pokud používáte datové vědy virtuálního počítače (DSVM), předem nainstaloval Git a můžete se pustit do práce. Jinak, najdete v článku [platformy a nástroje příloha](platforms-and-tools.md#appendix).  
- Pokud používáte **Windows DSVM**, musíte mít [Git přihlašovacích údajů správce (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) nainstalovaný na počítači. V souboru README.md přejděte dolů k položce **stáhněte a nainstalujte** části a klikněte na tlačítko *nejnovější verzi instalačního programu*. Tento krok přejdete na stránku nejnovější instalační služby. Stažení instalačního programu .exe z tohoto umístění a spusťte ji. 
- Pokud používáte **Linux DSVM**, vytvořte veřejný klíč SSH na vaše DSVM a přidejte ho k serveru služby VSTS skupiny. Další informace o SSH naleznete v tématu **vytvořit veřejný klíč SSH** kapitoly [platformy a nástroje příloha](platforms-and-tools.md#appendix). 


## <a name="1-create-account-on-vsts-server"></a>1. Vytvoření účtu na serveru služby VSTS

Služby VSTS server hostuje následující úložiště:

- **Skupina úložišť běžné**: univerzální úložiště, které můžete používat více týmů v rámci skupiny pro více projektů vědecké účely data. Například *GroupProjectTemplate* a *GroupUtilities* úložiště.
- **tým úložiště**: úložiště pro konkrétní týmy v rámci skupiny. Tyto úložiště jsou specifické pro potřeby týmových a může být přijatý více projektů provedený tento tým, ale není dostatečně obecné být užitečné pro více týmů v rámci skupiny vědecké účely data. 
- **Projekt úložiště**: úložiště k dispozici pro konkrétní projekty. Takové úložiště nemusí být dostatečně obecné být užitečné pro více projektů provádí tým a do více týmů ve skupině vědecké účely data.


### <a name="setting-up-the-vsts-server-sign-into-your-microsoft-account"></a>Nastavení služby VSTS přihlašovací Server k účtu Microsoft
    
Přejděte na [sadou Visual Studio](https://www.visualstudio.com/), klikněte na tlačítko **přihlášení** v pravém horním rohu a přihlaste se k účtu Microsoft. 
    
![1](./media/group-manager-tasks/login.PNG)

Pokud nemáte účet Microsoft, klikněte na tlačítko **zaregistrujte si teď** k vytvoření účtu Microsoft a potom se přihlaste pomocí tohoto účtu. 

Pokud má vaše organizace předplatné Visual Studio nebo MSDN, klikněte na tlačítko zeleným **přihlaste pomocí svého pracovního nebo školního účtu** pole a přihlaste se pomocí přihlašovacích údajů spojených s tímto odběrem. 
        
![2](./media/group-manager-tasks/signin.PNG)


        
Po přihlášení, klikněte na tlačítko **vytvořit nový účet** v pravém horním rohu jak je znázorněno na následujícím obrázku:
        
![3](./media/group-manager-tasks/create-account-1.PNG)
        
Zadejte informace pro služby VSTS server, který chcete vytvořit v **vytvoření účtu** průvodce s následujícími hodnotami: 

- **Adresa URL serveru**: Nahraďte *mysamplegroup* vlastními *název serveru*. Adresa URL serveru bude: *https://\<servername\>. visualstudio.com*. 
- **Spravovat pomocí kódu:** vyberte  **_Git_**.
- **Název projektu:** Enter *GroupCommon*. 
- **Uspořádání pomocí pracovní:** zvolte *Agile*.
- **Hostování vašich projektů v:** zvolte geografické umístění. V tomto příkladu vybereme možnost *jihu USA*. 
        
![4](./media/group-manager-tasks/fill-in-account-information.png)

>[AZURE.NOTE] Pokud se zobrazí následující místním okně po kliknutí na tlačítko **vytvořit nový účet**, pak je třeba kliknout na **změnit podrobnosti o** zobrazíte všechna pole uvedeno.

![5](./media/group-manager-tasks/create-account-2.png)


Klikněte na tlačítko **pokračovat**. 

## <a name="2-groupcommon-team-project"></a>2. GroupCommon týmového projektu

**GroupCommon** stránky (*https://\<servername\>.visualstudio.com/GroupCommon*) otevře po vytvoření služby VSTS serveru.
                            
![6](./media/group-manager-tasks/server-created-2.PNG)

## <a name="3-create-the-grouputilities-r2-repository"></a>3. Vytvoření úložiště GroupUtilities (R2)

Chcete-li vytvořit **GroupUtilities** úložiště (R2) v rámci služby VSTS serveru:

- Otevřete **vytvořit nové úložiště** průvodce, klikněte na tlačítko **nového úložiště** na **verzí** kartě týmového projektu. 

![7](./media/group-manager-tasks/create-grouputilities-repo-1.png) 

- Vyberte *Git* jako **typ**a zadejte *GroupUtilities* jako **název**a potom klikněte na **vytvořit**. 

![8](./media/group-manager-tasks/create-grouputilities-repo-2.png)
                
Teď byste měli vidět dvě úložiště Git **GroupProjectTemplate** a **GroupUtilities** v levém sloupci **verzí** stránky: 

![9](./media/group-manager-tasks/two-repo-under-groupCommon.PNG)


## <a name="4-create-the-groupprojecttemplate-r1-repository"></a>4. Vytvoření úložiště GroupProjectTemplate (R1)

Instalační program pro server služby VSTS skupiny úložišť se skládá z dvě úlohy:

- Přejmenování výchozí **GroupCommon** úložiště***GroupProjectTemplate***.
- Vytvořte **GroupUtilities** úložiště na serveru služby VSTS pod týmového projektu **GroupCommon**. 

Pokyny pro první úlohou jsou obsažené v této části poznámky na zásady vytváření názvů nebo naše úložiště a adresářů. Pokyny v druhé úloze jsou obsažené v kroku 4 v následující části.

### <a name="rename-the-default-groupcommon-repository"></a>Přejmenování výchozí GroupCommon úložiště

Chcete-li přejmenovat výchozí **GroupCommon** úložiště jako *GroupProjectTemplate* (označované jako **R1** v tomto kurzu):
    
- Klikněte na tlačítko **spolupráce na kód** na **GroupCommon** stránce týmového projektu. Tím přejdete na stránce úložiště Git výchozí týmový projekt **GroupCommon**. Toto úložiště Git v současné době je prázdný. 

![10](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
        
- Klikněte na tlačítko **GroupCommon** v levém horním rohu (zvýrazněné s červeným rámečkem na následujícím obrázku) na stránce úložiště Git **GroupCommon** a vyberte **spravovat úložiště**(zvýrazněné zelená pole na následujícím obrázku). Tento postup se vyvolá **ovládací panely**. 
- Vyberte **verzí** kartě týmového projektu. 

![11](./media/group-manager-tasks/rename-groupcommon-repo-4.png)

- Klikněte **...**  napravo **GroupCommon** úložiště na levém panelu a vyberte **přejmenování úložiště**. 

![12](./media/group-manager-tasks/rename-groupcommon-repo-5.png)
        
- V **přejmenovat úložiště GroupCommon** průvodce, který bodů POP, zadejte *GroupProjectTemplate* v **název Repozitáře** pole a pak klikněte na **přejmenovat** . 

![13](./media/group-manager-tasks/rename-groupcommon-repo-6.png)



## <a name="5-seed-the-r1--r2-repositories-on-the-vsts-server"></a>5. Počáteční hodnoty R1 & R2 úložiště na serveru služby VSTS

V této fázi procesu, můžete počáteční hodnoty *GroupProjectTemplate* (R1) a *GroupUtilities* úložiště (R2), které jste vytvořili v předchozí části. Tyto úložiště osadit ***ProjectTemplate*** (**G1**) a ***nástroje*** (**G2**) úložiště, které jsou spravovány nástrojem Microsoft pro proces Team Data vědecké účely. Když je dokončení této synchronizace replik indexů:

- R1 úložiště bude mít stejnou sadu adresářů a šablony dokumentů, které nemá G1
- R2 úložiště bude obsahovat sadu nástrojů vědecké účely data vyvinuté společností Microsoft.

Synchronizace replik indexů postup používá adresáře na vaše místní DSVM jako zprostředkující pracovní lokality. Tady jsou kroky, a potom v této části:

- G1 & G2 - na -> LG1 & LG2 klonovat
- R1 & R2 - na -> LR1 & LR2 klonovat
- Lg1 & LG2 – soubory zkopírují do -> LR1 & LR2
- (Volitelné) přizpůsobení LR1 & LR2
- LR1 & LR2 - obsah přidat do -> R1 & R2


### <a name="clone-g1--g2-repositories-to-your-local-dsvm"></a>Klonování G1 & G2 úložiště na vaše místní DSVM

V tomto kroku klonovat úložiště ProjectTemplate tým datové vědy procesu (TDSP) (G1) a nástroje (G2) z úložiště githubu TDSP do složek ve vaší místní DSVM jako LG1 a LG2:

- Vytvořte adresář, který bude sloužit jako kořenový adresář pro hostování vaší klony úložišť. 
    -  V systému Windows DSVM, vytvořte adresář *C:\GitRepos\TDSPCommon*. 
    -  V systému Linux DSVM, vytvořte adresář *GitRepos\TDSPCommon* v domovském adresáři. 

- Spusťte následující sadu příkazů z *GitRepos\TDSPCommon* adresáře.

    `git clone https://github.com/Azure/Azure-TDSP-ProjectTemplate`<br>
    `git clone https://github.com/Azure/Azure-TDSP-Utilities`
        
![14](./media/group-manager-tasks/two-folder-cloned-from-TDSP-windows.PNG)

- Pomocí názvů naše zkrácené úložiště, to je, co jste dosáhli tyto skripty: 
    - G1 - klonovat do -> LG1
    - G2 - klonovat do -> LG2
- Po dokončení klonování byste měli vidět dva adresáře _ProjectTemplate_ a _nástroje_v části **GitRepos\TDSPCommon** adresáře. 

### <a name="clone-r1--r2-repositories-to-your-local-dsvm"></a>Klonování úložiště R1 & R2 k vaší místní DSVM

V tomto kroku klonovat úložiště GroupProjectTemplate (R1) a úložišti GroupUtilities (R2) na místní adresáře (označované jako LR1 a LR2,) pod **GitRepos\GroupCommon** na vaše DSVM.

- K získání adres URL R1 a R2 úložišť, přejděte na vaše **GroupCommon** na služby VSTS domovskou stránku. To obvykle obsahuje adresu URL *https://\<název vašeho serveru služby VSTS\>.visualstudio.com/GroupCommon*. 
- Klikněte na tlačítko **kód**. 
- Vyberte **GroupProjectTemplate** a **GroupUtilities** úložiště. Zkopírujte a uložte každou z adres URL (HTTPS pro systém Windows; SSH pro Linux) z **adresa URL klonování** element naopak pro použití v následujících skriptů:  

![15](./media/group-manager-tasks/find_https_ssh_2.PNG)

- Přejděte do **GitRepos\GroupCommon** v systému Windows nebo Linux DSVM a spusťte jeden z následující sady příkazů klonovat R1 a R2 do tohoto adresáře.
        
Zde jsou skripty Windows a Linux:

    # Windows DSVM

    git clone <the HTTPS URL of the GroupProjectTemplate repository>
    git clone <the HTTPS URL of the GroupUtilities repository>

![16](./media/group-manager-tasks/clone-two-empty-group-reo-windows-2.PNG)

    # Linux DSVM

    git clone <the SSH URL of the GroupProjectTemplate repository>
    git clone <the SSH URL of the GroupUtilities repository>

![17](./media/group-manager-tasks/clone-two-empty-group-reo-linux-2.PNG)

>[AZURE.NOTE] Očekávají, že se zobrazí upozornění, že LR1 a LR2 jsou prázdné.    

- Pomocí názvů naše zkrácené úložiště, to je, co jste dosáhli tyto skripty: 
    - R1 - klonovat do -> LR1
    - R2 - klonovat do -> LR2   


### <a name="seed-your-groupprojecttemplate-lr1-and-grouputilities-lr2"></a>Počáteční hodnoty GroupProjectTemplate (LR1) a GroupUtilities (LR2)

V místním počítači, zkopírujte obsah ProjectTemplate a nástrojů adresáře (s výjimkou metadat v adresáři .git) v části GitRepos\TDSPCommon do vašich adresářů GroupProjectTemplate a GroupUtilities pod **GitRepos\ GroupCommon**. Zde jsou dvě úlohy k dokončení v tomto kroku:

- Zkopírujte soubory v GitRepos\TDSPCommon\ProjectTemplate (**LG1**) k GitRepos\GroupCommon\GroupProjectTemplate (**LR1**) 
- Zkopírujte soubory v GitRepos\TDSPCommon\Utilities (**LG2** k GitRepos\GroupCommon\Utilities (**LR2**). 

K dosažení tyto dvě úlohy, spusťte následující skripty v konzole PowerShell (Windows) nebo konzole skriptu prostředí (Linux). Zobrazí se výzva k dokončení cesty LG1, LR1, LG2 a LR2 vstup. Ověření cesty, které můžete vložit. Pokud můžete vložit do adresáře, který neexistuje, budete vyzváni k vstupnímu ho znovu. 

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
        
Nyní uvidíte, že jsou soubory ve dvou složek (s výjimkou souborů v adresáři .git) zkopírovány do GroupProjectTemplate a GroupUtilities v uvedeném pořadí.
    
![21](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux.PNG)

- Pomocí názvů naše zkrácené úložiště, to je, co jste dosáhli tyto skripty:
    - Lg1 - LR1 -> zkopírovaných do souborů
    - Lg2 - LR2 -> zkopírovaných do souborů

### <a name="option-to-customize-the-contents-of-lr1--lr2"></a>Možnost přizpůsobit obsah LR1 & LR2
    
Pokud chcete přizpůsobit obsah LR1 a LR2 splnili specifické požadavky vaší skupiny, je to fáze procesu, kde to je vhodné. Můžete upravit šablonu dokumentů, změnit strukturu adresáře a přidejte existující nástroje, která vytvořena, skupiny nebo které jsou užitečné pro celou skupinu. 

### <a name="add-the-contents-in-lr1--lr2-to-r1--r2-on-group-server"></a>Přidat obsah v LR1 & LR2 do R1 & R2 na serveru skupiny

Teď je potřeba přidat obsah v LR1 a LR2 do úložiště R1 a R2. Tady jsou git bash příkazy, které můžete spustit v prostředí Windows PowerShell nebo Linux. 

Spusťte následující příkazy z adresáře GitRepos\GroupCommon\GroupProjectTemplate:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

-M možnost umožňuje nastavit zprávu pro vaše potvrzení git.

![22](./media/group-manager-tasks/push-to-group-server-2.PNG)

Uvidíte, že ve vaší skupině služby VSTS serveru v úložišti GroupProjectTemplate soubory se synchronizují okamžitě.

![23](./media/group-manager-tasks/push-to-group-server-showed-up-2.PNG)

Nakonec změnit na **GitRepos\GroupCommon\GroupUtilities** adresáře a spusťte stejnou sadu git bash příkazy:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

>[AZURE.NOTE] Pokud je prvním potvrzení do úložiště Git, musíte nakonfigurovat globální parametry *user.name* a *user.email* před spuštěním `git commit` příkaz. Spusťte následující dva příkazy:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
>Pokud jsou přiřazeni více úložiště Git, použijte stejný název a e-mailovou adresu při potvrzení pro každé z nich. Pomocí stejné jméno a e-mailovou adresu prokáže pohodlný později při sestavování řídicí panely Power BI a sledovat aktivity Git na více úložiště.


- Pomocí názvů naše zkrácené úložiště, to je, co jste dosáhli tyto skripty:
    - Přidat obsah k LR1 - na -> R1
    - LR2 - obsahu přidat k R2 ->

## <a name="6-add-group-members-to-the-group-server"></a>6. Přidání členů do skupiny na serveru skupiny

Ze serveru vaší skupiny služby VSTS domovské stránky, klikněte na tlačítko **ozubené kolečko ikonu** vedle vašeho uživatelského jména v pravém horním rohu vyberte **zabezpečení** kartě. Můžete přidat členy do skupiny zde s různými oprávnění.

![24](./media/group-manager-tasks/add_member_to_group.PNG) 


## <a name="next-steps"></a>Další postup

Tady jsou odkazy na podrobnější popis jednotlivých rolí a úloh, které jsou definované procesu Team dat vědecké účely:

- [Úlohy správce skupiny pro data tým vědecké účely](group-manager-tasks.md)
- [Tým realizace úlohy pro data tým vědecké účely](team-lead-tasks.md)
- [Úlohy realizace projektu pro data tým vědecké účely](project-lead-tasks.md)
- [Jednotlivé přispěvatele projektu pro data tým vědecké účely](project-ic-tasks.md)