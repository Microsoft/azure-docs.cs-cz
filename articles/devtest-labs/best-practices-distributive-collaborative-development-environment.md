---
title: Distribuované vývojové prostředí pro Azure DevTest Labs prostředky
description: Poskytuje osvědčené postupy pro nastavení distribuovaného prostředí pro vývoj a spolupráci pro vývoj prostředků DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c18bbbfd3ad727811cc28c424381e5caf32b1cfc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85483801"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Osvědčené postupy pro distribuování a spolupráci s vývojem Azure DevTest Labsch prostředků
Distribuovaný vývoj pro spolupráci umožňuje různým týmům nebo lidem vyvíjet a udržovat základ kódu. Aby byl proces vývoje úspěšný, závisí na možnosti vytváření, sdílení a integraci informací. Tento klíčový princip vývoje lze použít v rámci Azure DevTest Labs. V rámci testovacího prostředí je několik typů prostředků, které jsou často distribuované mezi různými cvičeními v rámci podniku. Různé typy prostředků jsou zaměřené na dvě oblasti:

- Prostředky, které jsou interně uloženy v rámci testovacího prostředí (založené na testovacím prostředí)
- Prostředky, které jsou uloženy v [externích úložištích, které jsou připojeny k testovacímu prostředí](devtest-lab-add-artifact-repo.md) (založené na úložišti kódu). 

Tento dokument popisuje některé osvědčené postupy, které umožňují spolupráci a distribuci napříč několika týmy a současně zajišťuje přizpůsobení a kvalitu na všech úrovních.

## <a name="lab-based-resources"></a>Prostředky založené na testovacím prostředí

### <a name="custom-virtual-machine-images"></a>Vlastní image virtuálních počítačů
Můžete mít společný zdroj vlastních imagí, které jsou nasazené na cvičení na noční bázi. Podrobné informace najdete v tématu [objekt pro vytváření imagí](image-factory-create.md).    

### <a name="formulas"></a>Vzorce
[Vzorce](devtest-lab-manage-formulas.md) jsou specifické pro testovací prostředí a nemají distribuční mechanismus. Členové testovacího prostředí mají všechny vývojové vzorce. 

## <a name="code-repository-based-resources"></a>Prostředky založené na úložišti kódu
Existují dvě různé funkce, které jsou založeny na úložištích kódu, artefaktech a prostředích. Tento článek přechází na funkce a jak efektivně nastavit úložiště a pracovní postup, aby bylo možné přizpůsobit dostupné artefakty a prostředí na úrovni organizace nebo týmu.  Tento pracovní postup je založený na standardní [strategii pro větvení správy zdrojového kódu](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops). 

### <a name="key-concepts"></a>Klíčové koncepty
Zdrojové informace pro artefakty zahrnují metadata, skripty. Zdrojové informace pro prostředí zahrnují šablony metadat a Správce prostředků s libovolnými podpůrnými soubory, jako jsou skripty PowerShellu, skripty DSC, soubory zip atd.  

### <a name="repository-structure"></a>Struktura úložiště  
Nejběžnější konfigurací pro řízení zdrojového kódu (SCC) je nastavení vícevrstvé struktury pro ukládání souborů kódu (Správce prostředků šablon, metadat a skriptů), které se používají v laboratořích. Konkrétně vytvořte různá úložiště pro ukládání prostředků, které jsou spravovány různými úrovněmi firmy:   

- Prostředky pro nejrůznější společnosti.
- Prostředky obchodní jednotky/oddělení – celá
- Prostředky specifické pro tým.

Každá z těchto úrovní odkazuje na jiné úložiště, kde je hlavní větev nutná k produkční kvalitě. [Větve](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) v každém úložišti by byly pro vývoj těchto specifických prostředků (artefakty nebo šablony). Tato struktura je dobře zarovnaná s DevTest Labs, protože můžete snadno propojit více úložišť a více větví současně s laboratořemi organizace. Název úložiště je obsažen v uživatelském rozhraní (UI), aby nedocházelo k záměně, pokud jsou k dispozici stejné názvy, popis a vydavatel.
     
Následující diagram ukazuje dvě úložiště: firemní úložiště, které je udržováno oddělením IT, a prostorové úložiště, které je udržováno v rámci oddílu R&D.

![Ukázkové a vývojové prostředí pro spolupráci](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Tato vrstvená struktura umožňuje vývoj a udržuje vyšší úroveň kvality v hlavní větvi, zatímco když máte víc úložišť připojených k testovacímu prostředí, umožníte větší flexibilitu.

## <a name="next-steps"></a>Další kroky    
Viz následující články:

- Přidání úložiště do testovacího prostředí pomocí [Azure Portal](devtest-lab-add-artifact-repo.md) nebo pomocí [šablony správy prostředků Azure](add-artifact-repository.md)
- [Artefakty DevTest Labs](devtest-lab-artifact-author.md)
- [Prostředí DevTest Labs](devtest-lab-create-environment-from-arm.md).
