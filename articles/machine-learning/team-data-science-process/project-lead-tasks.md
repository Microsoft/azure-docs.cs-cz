---
title: Tým datové vědy proces projektu vést úlohy – Azure | Microsoft Docs
description: Přehled úloh pro projekt vést na datové vědě týmového projektu.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 3cc938e7d573c146b688acabdcfcb8e674e185cf
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="project-lead-tasks"></a>Úkoly realizace projektu

Tento kurz obsahuje přehled o úlohách, které je realizace projektu očekává dokončete pro jejich projektový tým. Cílem je vytvoření prostředí pro spolupráci team, standardizující na [proces vědecké účely dat Team](overview.md) (TDSP). TDSP je architektura vyvinuté společností Microsoft, která poskytuje strukturovaných posloupnost aktivit k úspěšnému provedení řešení založená na cloudu, prediktivní analýzy. Přehled role pracovníky a jejich přidružených úloh, které zpracovávají datové vědy tým standardizace tohoto postupu najdete v tématu [proces vědecké účely dat Team rolí a úloh](roles-tasks.md).

A **projektu vést** spravuje denní aktivity jednotlivých datových vědců na konkrétní datové vědy projektu. Pracovní postup pro úkoly, které mají za následek projektu nastavení tohoto prostředí dokončit je znázorněno na následujícím obrázku:

![1](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Toto téma popisuje aktuálně úlohy 1, 2 a 6 tento pracovní postup pro projekt zájemce.

>[AZURE.NOTE] Jsme popisují kroky potřebné k nastavení prostředí team TDSP pro projekt pomocí Visual Studio Team Services (VSTS) v následujících pokynech. Určíme jak provést tyto úlohy s služby VSTS, protože se jedná, jak jsme implementovat TDSP ve společnosti Microsoft. Pokud jiné platformě hostování kódu slouží pro vaši skupinu, úlohy, které je potřeba dokončit vedoucí týmu obecně se nezmění. Ale způsob k dokončení těchto úloh se bude lišit.


## <a name="repositories-and-directories"></a>Úložiště a adresáře

Tento kurz používá zkrácený názvy pro úložiště a adresáře. Názvy těchto usnadňují následovat operace mezi úložiště a adresářů. Tento zápis (R pro úložiště Git) a D pro místní adresáře na vaše DSVM se používá v následujících částech:

- **R3**: týmem **ProjectTemplate** úložiště na Git nastavil vedoucí vašeho týmu.
- **R5**: úložiště projektu na Git nastavení pro svůj projekt.
- **D3**: místní adresář naklonována ze R3.
- **D5**: místní adresář naklonována ze R5.


## <a name="0-prerequisites"></a>0. Požadavky

Požadavky jsou uspokojit dokončení úkolů, které správce vaší skupiny uvedených v [úlohy správce skupiny pro tým datové vědy](group-manager-tasks.md) a na team realizace uvedených v [Team realizace úlohy pro tým datové vědy](team-lead-tasks.md). 

To Shrneme zde, budou muset splnit před zahájením úloh realizace team následující požadavky: 

- Vaše **serveru služby VSTS skupiny** (nebo skupinový účet na některé další hostování kódu platformy) byla nastavena správcem vaší skupiny.
- Vaše **TeamProjectTemplate úložiště** (R3) byl nastaven v rámci účtu skupiny podle vašeho týmu realizace na platformě hostování kód budete používat.
- Byli jste **oprávnění** podle vašeho týmu realizace vytvořit úložiště na vašem účtu skupiny pro váš tým.
- Git musí být nainstalován na váš počítač. Pokud používáte datové vědy virtuálního počítače (DSVM), předem nainstaloval Git a můžete se pustit do práce. Jinak, najdete v článku [platformy a nástroje příloha](platforms-and-tools.md#appendix).  
- Pokud používáte **Windows DSVM**, musíte mít [Git přihlašovacích údajů správce (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) nainstalovaný na počítači. V souboru README.md přejděte dolů k položce **stáhněte a nainstalujte** části a klikněte na tlačítko *nejnovější verzi instalačního programu*. Tím přejdete na stránku nejnovější instalační služby. Stažení instalačního programu .exe z tohoto umístění a spusťte ji. 
- Pokud používáte **Linux DSVM**, vytvořte veřejný klíč SSH na vaše DSVM a přidejte ho k serveru služby VSTS skupiny. Další informace o SSH naleznete v tématu **vytvořit veřejný klíč SSH** kapitoly [platformy a nástroje příloha](platforms-and-tools.md#appendix). 


## <a name="1-create-a-project-repository-r5"></a>1. Vytvoření projektu úložiště (R5)

- Přihlaste se k serveru služby VSTS skupiny v *https://\<název serveru služby VSTS\>. visualstudio.com*. 
- V části **poslední projekty & týmy**, klikněte na tlačítko **Procházet**. Okno, které se zobrazí, jsou uvedeny všechny týmové projekty, na serveru služby VSTS. 

    ![2](./media/project-lead-tasks/project-leads-2-create-project-repo.png)

- Klikněte na název týmového projektu, ve kterém chcete vytvořit projekt úložiště. V tomto příkladu, klikněte na tlačítko **MyTeam**. 
- Potom klikněte na **přejděte** přesměrovat na domovskou stránku týmový projekt **MyTeam**:

    ![3](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)

- Klikněte na tlačítko **spolupráce na kód** přesměrovat na domovskou stránku git týmového projektu.  

    ![4](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

- Klikněte na šipku dolů na levého horního rohu a vyberte **+ nové úložiště**. 
    
    ![5](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)

- V **vytvořit nové úložiště** okno, zadejte název pro úložiště git projektu. Ujistěte se, že jste vybrali **Git** jako typ úložiště. V tomto příkladu používáme název *DSProject1*. 

    ![6](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

- Chcete-li vytvořit vaše ***DSProject1*** projektu úložiště git, klikněte na tlačítko **vytvořit**.


## <a name="2-seed-the-dsproject1-project-repository"></a>2. Počáteční hodnoty úložišti DSProject1 projektu

Úlohu zde má počáteční hodnotu **DSProject1** projektu úložiště (R5) z úložiště šablony týmového projektu (R3). Synchronizace replik indexů postup používá adresářů D3 a D5 na vaše místní DSVM jako zprostředkující pracovní lokality. V souhrnu, je cesta k synchronizace replik indexů: R3 -> D3 -> D5 -> R5.

Pokud potřebujete nastavit vaše **DSProject1** projektu úložiště ke splnění některé konkrétní projekt vyžaduje, uděláte v předposlední kroku tohoto postupu. Zde je souhrn s postupem počáteční hodnoty obsah **DSProject1** projektu úložiště. Jednotlivé kroky odpovídají témata v procesu synchronizace replik indexů:

- Klon týmového projektu šablony úložiště do místního adresáře: team R3 - na -> místní D3 klonovat.
- Klonování úložiště DSProject1 do místního adresáře: team R5 - na -> místní D5 klonovat.
- Zkopírujte obsahu šablony klonovaný týmového projektu do místního klon DSProject1 úložiště: D3 - obsah zkopírován do -> D5.
- (Volitelné) Místní D5 přizpůsobení.
- Nabízená místní DSProject1 obsahu úložiště team: D5 - obsah přidat -> team R5.


### <a name="clone-your-team-project-template-repository-r3-to-a-directory-d3-on-your-local-machine"></a>Klonování týmového projektu šablony úložiště (R3) do adresáře (D3) na místním počítači.

Na místním počítači vytvořte adresář:

- *C:\GitRepos\MyTeamCommon* pro Windows 
- *$home/GitRepos/MyTeamCommon* pro Linux

Změnit do tohoto adresáře. Potom spusťte následující příkaz, který klonovat úložiště šablony týmového projektu do místního počítače. 

**Windows**
            
    git clone <the HTTPS URL of the TeamProjectTemplate repository>
    
Pokud používáte služby VSTS jako hostování kódu platformy, obvykle *adresy URL HTTPS úložiště šablony týmového projektu* je:

 ***https://\<název serveru služby VSTS\>.visualstudio.com/\<název týmového projektu\>/_git/\<úložiště název týmového projektu šablony\>***. 

V tomto příkladu máme:

***https://mysamplegroup.visualstudio.com/MyTeam/_git/MyTeamProjectTemplate***. 

![7](./media/project-lead-tasks/project-leads-7-clone-team-project-template.png)
            
**Linux**

    git clone <the SSH URL of the TeamProjectTemplate repository>
        
![8](./media/project-lead-tasks/project-leads-8-clone-team-project-template-linux.png)

Pokud používáte služby VSTS jako hostování kódu platformy, obvykle *SSH URL úložiště šablony týmového projektu* je:

***SSH: / /\<název serveru služby VSTS\>@\<název serveru služby VSTS\>.visualstudio.com:22/\<si název týmového projektu > /_git/\<název úložiště šablony týmového projektu \>.*** 

V tomto příkladu máme:

***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/MyTeamProjectTemplate***. 

### <a name="clone-dsproject1-repository-r5-to-a-directory-d5-on-your-local-machine"></a>Klonování úložiště DSProject1 (R5) do adresáře (D5) na místním počítači

Změnit adresář **GitRepos**, a spusťte následující příkaz klonovat úložiště projektu do místního počítače. 

**Windows**
            
    git clone <the HTTPS URL of the Project repository>

![9](./media/project-lead-tasks/project-leads-9-clone-project-repository.png)

Pokud používáte služby VSTS jako hostování kódu platformy, obvykle _adresy URL HTTPS úložiště projektu_ je ***https://\<název serveru služby VSTS\>.visualstudio.com/\<váš tým Název projektu > /_git/ < název projektu úložiště\>***. V tomto příkladu máme ***https://mysamplegroup.visualstudio.com/MyTeam/_git/DSProject1***.

**Linux**

    git clone <the SSH URL of the Project repository>

![10](./media/project-lead-tasks/project-leads-10-clone-project-repository-linux.png)

Pokud používáte služby VSTS jako hostování kódu platformy, obvykle _SSH URL projektu úložiště_ je _ssh: / / < název serveru služby VSTS\>@< název serveru služby VSTS\>.visualstudio.com:22/<Your Team Project Name> / \_git / < název projektu úložiště\>. V tomto příkladu máme ***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/DSProject1***.

### <a name="copy-contents-of-d3-to-d5"></a>Zkopírujte obsah D3 do D5 

Nyní v místním počítači, je nutné zkopírovat obsah _D3_ k _D5_, s výjimkou git metadata v adresáři .git. Tyto skripty se provést úlohu. Zajistěte, aby ve správné a úplné cesty k adresáři. Zdrojová složka je pro váš tým (_D3_); cílová složka je pro váš projekt (_D5_).    

**Windows**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 -role 3
    
![11](./media/project-lead-tasks/project-leads-11-local-copy-project-lead-new.png)

Nyní vidíte v _DSProject1_ složky, všechny soubory (s výjimkou .git) jsou kopírovány z _MyTeamProjectTemplate_.

![12](./media/project-lead-tasks/project-leads-12-teamprojectTemplate_copied_to_local.png)

**Linux**
            
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 3
        
![13](./media/project-lead-tasks/project-leads-13-local_copy_project_lead_linux_new.png)

Nyní vidíte v _DSProject1_ složky, všechny soubory (s výjimkou metadat v .git) jsou kopírovány z _MyTeamProjectTemplate_.

![14](./media/project-lead-tasks/project-leads-14-teamprojectTemplate_copied_to_local_linux_new.png)


### <a name="customize-d5-if-you-need-to-optional"></a>Přizpůsobení D5, pokud potřebujete (volitelné)

Pokud váš projekt vyžaduje některé konkrétní adresáře nebo dokumenty, než ty, které můžete získat z šablony projektu team (zkopírován do adresáře D5 v předchozím kroku), můžete přizpůsobit obsah D5 teď. 

### <a name="add-contents-of-dsproject1-in-d5-to-r5-on-your-group-vsts-server"></a>Přidat obsah DSProject1 v D5 R5 na serveru služby VSTS skupiny

Nyní je třeba push obsah **_DSProject1_** k _R5_ úložiště v týmových projektech na serveru služby VSTS vaší skupině. 


- Přejděte do adresáře **D5**. 
- Pomocí následujících příkazů git přidejte obsah **D5** k **R5**. Příkazy jsou stejné pro systémy Windows a Linux. 
    
    Přidejte Git stav git.
    Git potvrzení -m "push z win DSVM" git push
    
- Potvrďte změnu a posílejte nabízená oznámení. 

>[AZURE.NOTE] Pokud je prvním potvrzení do úložiště Git, musíte nakonfigurovat globální parametry *user.name* a *user.email* před spuštěním `git commit` příkaz. Spusťte následující dva příkazy:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Pokud jsou přiřazeni více úložiště Git, použijte stejný název a e-mailovou adresu pro všechny z nich. Pomocí stejné jméno a e-mailovou adresu prokáže pohodlný později při sestavování řídicí panely Power BI a sledovat aktivity Git na více úložiště.

![15](./media/project-lead-tasks/project-leads-15-git-config-name.png)


## <a name="6-create-and-mount-azure-file-storage-as-project-resources-optional"></a>6. Vytvořte a připojte úložiště Azure file jako prostředky projektu (volitelné)

Pokud chcete vytvořit úložiště Azure file sdílet data, jako je například projektu nezpracovaná data nebo funkce generované pro svůj projekt tak, aby všechny projektu členové mají přístup do stejné datové sady z více DSVMs, postupujte podle pokynů v částech 3 a 4 [ Tým realizace úlohy pro tým datové vědy](team-lead-tasks.md). 


## <a name="next-steps"></a>Další postup

Tady jsou odkazy na podrobnější popis jednotlivých rolí a úloh, které jsou definované procesu Team dat vědecké účely:

- [Úlohy správce skupiny pro data tým vědecké účely](group-manager-tasks.md)
- [Tým realizace úlohy pro data tým vědecké účely](team-lead-tasks.md)
- [Úlohy realizace projektu pro data tým vědecké účely](project-lead-tasks.md)
- [Jednotlivé přispěvatele projektu pro data tým vědecké účely](project-ic-tasks.md)