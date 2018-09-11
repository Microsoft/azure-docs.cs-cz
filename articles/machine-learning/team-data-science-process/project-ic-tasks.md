---
title: Týmu úlohy vědeckého zpracování dat pro jednotlivý Přispěvatel – Azure | Dokumentace Microsoftu
description: Přehled úloh pro jednotlivé přispěvatelů v týmovém projektu datové vědy.
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
ms.openlocfilehash: f21098381d75a4843e9300beaae687adc6ec107d
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44303757"
---
# <a name="individual-contributor-tasks"></a>Jednotlivé úlohy přispěvatele

Tohoto tématu jsou podrobněji popsány dále úlohy, které je jednotlivý Přispěvatel očekává pro jejich tým datové vědy. Cílem je vytvořit prostředí pro spolupráci týmu, který standardizuje na [vědecké zpracování týmových dat](overview.md) (TDSP). Přehled role pracovníky a jejich přidružených úloh, které jsou zpracovávány týmem datové vědy standardizovat o tomto procesu najdete v části [vědecké zpracování týmových dat role a úlohy](roles-tasks.md).

Úlohy jednotlivých přispěvatelů projektu (odborníci přes data) k nastavení prostředí TDSP pro projekt jsou znázorněné následujícím způsobem: 

![1](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **GroupUtilities** je úložiště, které vaše skupina za údržbu v celé skupině sdílet užitečné nástroje. 
- **TeamUtilities** je úložiště, které váš tým za údržbu speciálně pro váš tým. 

Pokyny o tom, jak spustit projekt vědeckého zpracování dat v rámci TDSP najdete v tématu [projekty spuštění z datových věd](project-execution.md). 

>[AZURE.NOTE] Uvádíme kroky potřebné k nastavení prostředí TDSP týmu pomocí Azure DevOps v následujících pokynech. Můžeme určit způsob k provedení těchto úloh s Azure DevOps, protože to je, jak můžeme implementovat TDSP v Microsoftu. Pokud je pro vaši skupinu pro jinou platformu pro hosting kódu, úlohy, které musíte provést vedoucí týmu obecně se nezmění. Ale způsob k provedení těchto úloh se bude lišit.


## <a name="repositories-and-directories"></a>Úložiště a adresáře

Tento kurz používá zkrácené názvy pro úložiště a adresáře. Tyto názvy usnadňují sledování operací mezi úložišť a adresáře. Tato notace (**R** pro úložiště Git a **D** pro místní adresáře na vaše DSVM) se používá v následujících částech:

- **R2**: The GroupUtilities úložiště v Gitu, která správce skupiny je nastavená na vašem serveru Azure DevOps skupiny.
- **R4**: The TeamUtilities úložiště v Gitu, které nastavil váš vedoucí týmu.
- **R5**: projekt úložiště v Gitu, který je nastavený podle váš vedoucí projektu.
- **D2**: místní adresář naklonovali z R2.
- **D4**: místní adresář naklonovali z R4.
- **D5**: místní adresář naklonovali z R5.


## <a name="step-0-prerequisites"></a>Krok 0: požadavky

Požadavky splněny dokončení úkolů přiřazena vedoucímu skupiny uvedených v [úkoly správce skupiny pro datové vědy tým](group-manager-tasks.md). Slouží ke shrnutí tady, třeba tyto požadavky splnit, předtím, než začnete s úkoly vedoucí týmu: 
- Vaše skupina správce nastavil **GroupUtilities** úložiště (pokud existuje). 
- Váš vedoucí týmu mají nastavený **TeamUtilities** úložiště (pokud existuje).
- V úložišti projektů nastavil váš vedoucí projektu. 
- Byli jste přidáni do vašeho projektu úložiště podle vašich vedoucí projektu s oprávněními k zkopírovat a vložit zpět do úložiště projektu.

Druhá s názvem **TeamUtilities** úložiště, požadovaných součástí je volitelný, v závislosti na tom, jestli má váš tým nástroj team konkrétní úložiště. Pokud některá z požadovaných součástí další tři nebyla dokončena, obraťte se na váš vedoucí týmu, váš vedoucí projektu nebo jejich delegáty pro nastavení podle pokynů pro [vedoucí týmu úkoly pro datové vědy tým](team-lead-tasks.md) nebo [ Vedoucí úkoly pro datové vědy tým projektu](project-lead-tasks.md).

- Git musí být nainstalován na počítači. Pokud používáte Data virtuálního počítače VĚDY, Git je předem nainstalovaný a jste připravení. V opačném případě najdete v článku [platformami a nástroji příloha](platforms-and-tools.md#appendix).  
- Pokud používáte **Windows DSVM**, musíte mít [Git Credential Manageru (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) na vašem počítači nainstalovaný. V souboru README.md přejděte dolů k položce **stáhněte a nainstalujte** a klikněte *nejnovější instalační program*. Tím přejdete na stránku pro nejnovější instalační program. Stažení instalačního programu .exe odsud a spustíme ji. 
- Pokud používáte **Linux DSVM**vytvořte veřejný klíč SSH na vaše DSVM a přidejte k vašim službám Azure DevOps skupiny. Další informace o SSH najdete v tématu **vytvořit veřejný klíč SSH** tématu [platformami a nástroji příloha](platforms-and-tools.md#appendix). 
- Pokud váš vedoucí týmu a/nebo projekt byl vytvořen některé Azure file storage, které potřebujete k připojení k vaší DSVM, by měl získat informace o Azure file storage z nich. 

## <a name="step-1-3-clone-group-team-and-project-repositories-to-local-machine"></a>Krok 1 – 3: klonování úložišť projektu do místního počítače, týmu a skupiny

Tato část obsahuje informace o dokončení první tři úkoly jednotlivých přispěvatelů projektu: 

- Klonování **GroupUtilities** úložiště R2 D2
- Klonování **TeamUtilities** úložiště R4 až D4 
- Klonování **projektu** úložiště R5 D5.

Na místním počítači vytvořte adresář ***C:\GitRepos*** (pro Windows) nebo ***$home/GitRepos*** (forLinux) a potom změňte do tohoto adresáře. 

Spusťte jeden z následujících příkazů (jako je určený pro váš operační systém) ke klonování vaší **GroupUtilities**, **TeamUtilities**, a **projektu** úložiště k adresářům na vaše místní počítač: 

**Windows**
    
    git clone <the URL of the GroupUtilities repository>
    git clone <the URL of the TeamUtilities repository>
    git clone <the URL of the Project repository>
    
![2](./media/project-ic-tasks/project-ic-2-clone-three-repo-to-ic.png)

Zkontrolujte, jestli se tři složky v adresáři projektu.

![3](./media/project-ic-tasks/project-ic-3-three-repo-cloned-to-ic.png)

**Linux**
    
    git clone <the SSH URL of the GroupUtilities repository>
    git clone <the SSH URL of the TeamUtilities repository>
    git clone <the SSH URL of the Project repository>

![4](./media/project-ic-tasks/project-ic-4-clone-three-repo-to_ic-linux.png)

Zkontrolujte, jestli se tři složky v adresáři projektu.

![5](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="step-4-5-mount-azure-file-storage-to-your-dsvm-optional"></a>Krok 4 – 5: připojení Azure file storage pro vaše DSVM (volitelné)

K připojení Azure file storage pro vaše DSVM, postupujte podle pokynů v části 4 [úkoly vedoucí týmu pro datové vědy tým](team-lead-tasks.md)

## <a name="next-steps"></a>Další postup

Tady jsou odkazy na podrobnější popis role a úlohy určené vědecké zpracování týmových dat:

- [Úlohy správce skupiny pro datové vědy tým](group-manager-tasks.md)
- [Úkoly vedoucí týmu pro datové vědy tým](team-lead-tasks.md)
- [Vedoucí úkoly pro datové vědy tým projektu](project-lead-tasks.md)
- [Jednotlivé přispěvatelé projektu týmu datové vědy](project-ic-tasks.md)

