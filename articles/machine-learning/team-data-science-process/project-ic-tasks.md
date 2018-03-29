---
title: Tým proces vědecké účely Data úlohy pro jednotlivé Přispěvatel - Azure | Microsoft Docs
description: Přehled úlohy pro jednotlivé přispěvatelem datové vědy týmového projektu.
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
ms.openlocfilehash: af60b188fe8db278297e1ece400bf4398e36094a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="individual-contributor-tasks"></a>Jednotlivé úlohy přispěvatele

Toto téma obsahuje přehled úlohy, které jednotlivé Přispěvatel je očekáván dokončete pro jejich tým vědecké účely data. Cílem je vytvoření prostředí pro spolupráci team, standardizující na [proces vědecké účely dat Team](overview.md) (TDSP). Přehled role pracovníky a jejich přidružených úloh, které zpracovávají datové vědy tým standardizace tohoto postupu najdete v tématu [proces vědecké účely dat Team rolí a úloh](roles-tasks.md).

Úlohy projektu jednotlivé přispěvatele (datových vědců) k nastavení TDSP prostředí pro projekt jsou použité v ukázkách následujícím způsobem: 

![1](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **GroupUtilities** je úložiště, které skupiny údržbu sdílet užitečné nástroje napříč celou skupinu. 
- **TeamUtilities** je úložiště, které váš tým údržbu speciálně pro váš tým. 

Pokyny o tom, jak provést projektu vědecké účely data v rámci TDSP najdete v tématu [provádění datové vědy projekty](project-execution.md). 

>[AZURE.NOTE] Jsme popisují kroky potřebné k nastavení prostředí TDSP týmu v aplikaci Visual Studio Team Services (VSTS) podle následujících pokynů. Určíme jak provést tyto úlohy s služby VSTS, protože se jedná, jak jsme implementovat TDSP ve společnosti Microsoft. Pokud jiné platformě hostování kódu slouží pro vaši skupinu, úlohy, které je potřeba dokončit vedoucí týmu obecně se nezmění. Ale způsob k dokončení těchto úloh se bude lišit.


## <a name="repositories-and-directories"></a>Úložiště a adresáře

Tento kurz používá zkrácený názvy pro úložiště a adresáře. Názvy těchto usnadňují následovat operace mezi úložiště a adresářů. Tento zápis (**R** pro úložiště Git a **D** pro místní adresáře na vaše DSVM) se používá v následujících částech:

- **R2**: GroupUtilities úložišti na Git, který nastavuje vaší skupiny správce na serveru služby VSTS skupiny.
- **R4**: TeamUtilities úložišti na Git, která nastavil vedoucí vašeho týmu.
- **R5**: úložiště projektu na Git, která byla vytvořena pomocí realizace vašeho projektu.
- **D2**: místní adresář klonovat z R2.
- **D4**: místní adresář naklonována ze R4.
- **D5**: místní adresář naklonována ze R5.


## <a name="step-0-prerequisites"></a>Krok-0: požadavky

Požadavky jsou uspokojit dokončení úkolů, které správce vaší skupiny uvedených v [úlohy správce skupiny pro tým datové vědy](group-manager-tasks.md). Souhrnu zde je nutné splnit před zahájením úloh realizace team následující požadavky: 
- Váš správce skupiny nastavený **GroupUtilities** úložiště (pokud existuje). 
- Vedoucí vašeho týmu nastavil **TeamUtilities** úložiště (pokud existuje).
- Váš projekt realizace nastavil úložiště projektu. 
- Můžete byly přidány do projektu úložiště podle vašeho projektu realizace s oprávněním a klonovat z push zpět do projektu úložiště.

Druhá s názvem **TeamUtilities** úložiště, požadovaných součástí je volitelné, v závislosti na tom, zda má váš tým nástroj team konkrétní úložiště. Pokud ještě nebylo dokončeno žádné další tři nezbytné součásti, obraťte se na váš tým realizace, váš projekt realizace nebo jejich delegátů k nastavení podle pokynů pro [Team vést úlohy pro tým datové vědy](team-lead-tasks.md) nebo [ Realizace úkoly pro tým datové vědy projektu](project-lead-tasks.md).

- Git musí být nainstalován na váš počítač. Pokud používáte datové vědy virtuálního počítače (DSVM), předem nainstaloval Git a můžete se pustit do práce. Jinak, najdete v článku [platformy a nástroje příloha](platforms-and-tools.md#appendix).  
- Pokud používáte **Windows DSVM**, musíte mít [Git přihlašovacích údajů správce (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) nainstalovaný na počítači. V souboru README.md přejděte dolů k položce **stáhněte a nainstalujte** části a klikněte na tlačítko *nejnovější verzi instalačního programu*. Tím přejdete na stránku nejnovější instalační služby. Stažení instalačního programu .exe z tohoto umístění a spusťte ji. 
- Pokud používáte **Linux DSVM**, vytvořte veřejný klíč SSH na vaše DSVM a přidejte ho k serveru služby VSTS skupiny. Další informace o SSH naleznete v tématu **vytvořit veřejný klíč SSH** kapitoly [platformy a nástroje příloha](platforms-and-tools.md#appendix). 
- Pokud váš tým nebo projektu realizace vytvořil některé úložiště Azure file, které budete muset připojit k vaší DSVM, měli byste obdržet informace o úložiště Azure file z nich. 

## <a name="step-1-3-clone-group-team-and-project-repositories-to-local-machine"></a>Krok 1 – 3: klonování skupiny, tým a úložiště projektu do místního počítače

Tato část obsahuje pokyny na dokončení první tři úkoly jednotlivé přispěvatele projektu: 

- Klon **GroupUtilities** úložiště R2 do D2
- Klon **TeamUtilities** úložiště R4 k D4 
- Klon **projektu** úložiště R5 k D5.

Na místním počítači, vytvořte adresář ***C:\GitRepos*** (pro Windows) nebo ***$home/GitRepos*** (forLinux) a poté změňte do tohoto adresáře. 

Spusťte jeden z následujících příkazů (jako je určený pro váš operační systém) klonovat vaše **GroupUtilities**, **TeamUtilities**, a **projektu** úložiště do adresáře na vaše místní počítač: 

**Windows**
    
    git clone <the URL of the GroupUtilities repository>
    git clone <the URL of the TeamUtilities repository>
    git clone <the URL of the Project repository>
    
![2](./media/project-ic-tasks/project-ic-2-clone-three-repo-to-ic.png)

Zkontrolujte, jestli tři složky v adresáři projektu.

![3](./media/project-ic-tasks/project-ic-3-three-repo-cloned-to-ic.png)

**Linux**
    
    git clone <the SSH URL of the GroupUtilities repository>
    git clone <the SSH URL of the TeamUtilities repository>
    git clone <the SSH URL of the Project repository>

![4](./media/project-ic-tasks/project-ic-4-clone-three-repo-to_ic-linux.png)

Zkontrolujte, jestli tři složky v adresáři projektu.

![5](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="step-4-5-mount-azure-file-storage-to-your-dsvm-optional"></a>Krok 4 – 5: úložiště souborů Azure připojit k vaší DSVM (volitelné)

K připojení Azure file storage k vaší DSVM, postupujte podle pokynů v části 4 [Team realizace úlohy pro data tým vědecké účely](team-lead-tasks.md)

## <a name="next-steps"></a>Další postup

Tady jsou odkazy na podrobnější popis jednotlivých rolí a úloh, které jsou definované procesu Team dat vědecké účely:

- [Úlohy správce skupiny pro data tým vědecké účely](group-manager-tasks.md)
- [Tým realizace úlohy pro data tým vědecké účely](team-lead-tasks.md)
- [Úlohy realizace projektu pro data tým vědecké účely](project-lead-tasks.md)
- [Jednotlivé přispěvatele projektu pro data tým vědecké účely](project-ic-tasks.md)

