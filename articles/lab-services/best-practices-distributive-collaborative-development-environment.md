---
title: Distribuovaný vývoj spolupráce s prostředky Azure DevTest Labs
description: Poskytuje osvědčené postupy pro nastavení distribuovaného vývojového prostředí pro spolupráci s vývojem prostředků DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9469591b1945adaffca973828d619d5d06655262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170109"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Doporučené postupy pro distribuovaný a kolaborativní vývoj prostředků Azure DevTest Labs
Distribuovaný vývoj spolupráce umožňuje různým týmům nebo lidem vyvíjet a udržovat základ kódu. Chcete-li být úspěšný, proces vývoje závisí na schopnosti vytvářet, sdílet a integrovat informace. Tento princip vývoje klíčů lze použít v rámci Azure DevTest Labs. Existuje několik typů prostředků v rámci testovacího prostředí, které jsou běžně distribuovány mezi různých testovacích prostředí v rámci rozlehlé sítě. Různé typy zdrojů jsou rozděleny do dvou oblastí:

- Prostředky, které jsou interně uloženy v testovacím prostředí (na základě testovacího prostředí)
- Prostředky, které jsou uloženy v [externích úložištích, které jsou připojeny k testovacímu prostředí](devtest-lab-add-artifact-repo.md) (založené na úložišti kódu). 

Tento dokument popisuje některé osvědčené postupy, které umožňují spolupráci a distribuci mezi více týmy a současně zajišťují přizpůsobení a kvalitu na všech úrovních.

## <a name="lab-based-resources"></a>Prostředky založené na laboratoři

### <a name="custom-virtual-machine-images"></a>Vlastní image virtuálních strojů
Můžete mít společný zdroj vlastních ibi, které jsou nasazeny do testovacích prostředí na noční bázi. Podrobné informace naleznete v tématu [Image factory](image-factory-create.md).    

### <a name="formulas"></a>Vzorce
[Vzorce](devtest-lab-manage-formulas.md) jsou specifické pro testovací prostředí a nemají distribuční mechanismus. Členové laboratoře dělají veškerý vývoj vzorců. 

## <a name="code-repository-based-resources"></a>Prostředky založené na úložišti kódu
Existují dvě různé funkce, které jsou založeny na úložiště kódu, artefakty a prostředí. Tento článek jde přes funkce a jak co nejefektivněji nastavit úložiště a pracovní postupy, aby bylo možné přizpůsobit dostupné artefakty a prostředí na úrovni organizace nebo týmu.  Tento pracovní postup je založen na standardní [strategii větvení správy zdrojového kódu](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops). 

### <a name="key-concepts"></a>Klíčové koncepty
Zdrojové informace pro artefakty zahrnují metadata, skripty. Zdrojové informace pro prostředí zahrnují metadata a šablony Správce prostředků se všemi podpůrnými soubory, jako jsou skripty prostředí PowerShell, skripty DSC, soubory ZIP a tak dále.  

### <a name="repository-structure"></a>Struktura úložiště  
Nejběžnější konfigurace pro správu zdrojového kódu (SCC) je nastavení vícevrstvé struktury pro ukládání souborů kódu (šablony Správce prostředků, metadata a skripty), které se používají v testovacích prostředích. Konkrétně vytvořte různé repozitáře pro ukládání prostředků, které jsou spravovány různými úrovněmi podnikání:   

- Zdroje pro celou společnost.
- Obchodní jednotka/zdroje pro celou divizi
- Zdroje specifické pro tým.

Každá z těchto úrovní se propojuje s jiným úložištěm, kde je hlavní větev povinna mít kvalitu výroby. Větve v každém úložišti by byly [určeny](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) pro vývoj těchto konkrétních prostředků (artefaktů nebo šablon). Tato struktura je dobře zarovnána s DevTest Labs, protože můžete snadno připojit více úložišť a více větví současně k laboratořím organizace. Název úložiště je součástí uživatelského rozhraní (UI), aby nedošlo k záměně, pokud existují identické názvy, popis a vydavatel.
     
Následující diagram znázorňuje dva repozitáře: repozitář společnosti, který je spravován divizí IT, a úložiště dělení spravované divizí R&D.

![Ukázkové distribuční a kolaborativní vývojové prostředí](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Tato vrstvená struktura umožňuje vývoj při zachování vyšší úrovně kvality v hlavní větvi a zároveň více úložišť připojených k testovacímu prostředí umožňuje větší flexibilitu.

## <a name="next-steps"></a>Další kroky    
Viz následující články:

- Přidání úložiště do testovacího prostředí pomocí [portálu Azure nebo](devtest-lab-add-artifact-repo.md) pomocí [šablony Azure Resource Management](add-artifact-repository.md)
- [Artefakty DevTest Labs](devtest-lab-artifact-author.md)
- [Prostředí DevTest Labs](devtest-lab-create-environment-from-arm.md).
