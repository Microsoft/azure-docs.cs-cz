---
title: Distribuované spolupráce na vývoji zdrojů službě Azure DevTest Labs | Dokumentace Microsoftu
description: Poskytuje osvědčené postupy pro nastavení prostředí pro vývoj distribuovaných a spolupráci vypracovává materiály DevTest Labs.
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
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: 8ffc8ed3f84284ff69e9515cba0982790b823a37
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543771"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Osvědčené postupy pro vývoj distribuovaných a spolupráci zdrojů službě Azure DevTest Labs
Distribuované spolupráce na vývoji umožňuje různé týmy nebo osoby pro vývoj a údržbu základní kód. K dosažení úspěchu procesu vývoje závisí na schopnost vytvářet, sdílet a integrovat informace. Tento princip vývoje klíče je možné v rámci Azure DevTest Labs. Existuje několik typů prostředků v rámci testovacího prostředí, které jsou obvykle rozdělena mezi různé testovací prostředí v rámci organizace. Různé typy prostředků se zaměřuje na dvě oblasti:

- Prostředky, které jsou uloženy interně v rámci testovacího prostředí (založený na prostředí)
- Prostředky, které jsou uloženy v [externí úložiště, které jsou připojeny k testovacím prostředí](devtest-lab-add-artifact-repo.md) (kódu na základě úložiště). 

Tento dokument popisuje některé osvědčené postupy, které umožňují spolupráci a distribuci napříč několika týmy přitom zajistit přizpůsobení a kvalitu na všech úrovních.

## <a name="lab-based-resources"></a>Testovací prostředí podle prostředků

### <a name="custom-virtual-machine-images"></a>Vlastní Image virtuálních počítačů
Můžete mít společný zdroj vlastních imagí, které jsou nasazené do testovacích prostředí přes noc. Podrobné informace najdete v tématu [objekt pro vytváření bitové kopie](image-factory-create.md).    

### <a name="formulas"></a>Vzorce
[Vzorce](devtest-lab-manage-formulas.md) jsou specifické pro testovací prostředí a nemáte mechanismus distribuce. Členové testovacího prostředí provádět všechny vývoj vzorce. 

## <a name="code-repository-based-resources"></a>Prostředky založené na úložiště kódu
Existují dva různé funkce, které jsou založeny na úložiště kódu, artefakty a prostředí. Tento článek prochází přes prvky a efektivní nastavení úložiště a pracovní postup umožňuje přizpůsobit prostředí na úrovni organizace nebo na úrovni týmu a k dispozici artefakty.  Tento pracovní postup je založen na standardní [zdrojového kódu řízení strategii větvení](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops). 

### <a name="key-concepts"></a>Klíčové koncepty
Informace o zdroji artefaktů zahrnuje jejich metadata, skripty. Informace o zdroji pro prostředí obsahuje metadata a šablon Resource Manageru s všechny podpůrné soubory, jako jsou skripty prostředí PowerShell, DSC, skripty, soubory Zip a tak dále.  

### <a name="repository-structure"></a>Struktura úložiště  
Nejběžnější konfigurací pro správu zdrojového kódu (SCC) je nastavení úrovně struktury pro soubory kódu (šablony Resource Manageru, metadata a skripty), které se používají k ukládání v Tato praktická cvičení. Konkrétně vytvořte různých úložištích se budou uchovávat prostředky, které se spravují přes různé úrovně firmy:   

- Prostředky pořádaného microsoftem.
- Obchodní jednotky nebo divize celou prostředky
- Prostředky specifické pro tým.

Každá z těchto úrovní odkaz na jiném úložišti, ve kterém je musí být produkční kvality v hlavní větvi. [Větví](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) v každém úložišti, které budou platit pro vývoj tyto konkrétní prostředky (artefakty nebo šablony). Tato struktura správně zarovnaná s DevTest Labs, jak můžete snadno připojit více úložišť a několika větví ve stejnou dobu k testovacím prostředím pro organizaci. Název úložiště je součástí uživatelského rozhraní (UI), aby nedocházelo k záměně po stejný název, popis a vydavatele.
     
Následující diagram znázorňuje dvě úložišť: společnosti úložiště, který je spravován oddělení IT a dělení úložiště udržuje dělení výzkum a vývoj.

![Ukázka distributivních a spolupráci vývojové prostředí](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Tato struktura vrstvami umožňuje vývoj přitom vyšší úroveň kvality v hlavní větvi, zatímco s více úložišť připojené do testovacího prostředí umožňuje větší flexibilitu.

## <a name="next-steps"></a>Další postup    
Viz následující články:

- Přidání úložiště do testovacího prostředí pomocí [webu Azure portal](devtest-lab-add-artifact-repo.md) nebo prostřednictvím [šablony Azure Resource Manageru](add-artifact-repository.md)
- [Artefakty DevTest Labs](devtest-lab-artifact-author.md)
- [Prostředí DevTest Labs](devtest-lab-create-environment-from-arm.md).
