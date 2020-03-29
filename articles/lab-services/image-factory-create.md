---
title: Vytvoření továrny na image v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak nastavit vlastní image factory pomocí ukázkové skripty k dispozici v úložišti Git (Azure DevTest Labs).
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 2c5a44a9505d4a312be521cdc3219c5e4ce95a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759444"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Vytvoření vlastní továrny na image v azure devtest labs
Tento článek ukazuje, jak nastavit vlastní továrně obrázků pomocí ukázkových skriptů dostupných v [úložišti Git](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>Co je továrna na obrázky?
Továrna bitových kopií je řešení konfigurace jako kód, které vytváří a distribuuje bitové kopie automaticky v pravidelných intervalech se všemi požadovanými konfiguracemi. Obrázky v továrně na obrázky jsou vždy aktuální a průběžná údržba je téměř nulová, jakmile je celý proces automatizován. A protože všechny požadované konfigurace jsou již v bitové kopii, šetří čas z ruční konfigurace systému po vytvoření virtuálního počítače se základním os.

Významný akcelerátor, který může dostat plochu vývojáře do stavu připravenosti v devTest Labs, používá vlastní image. Nevýhodou vlastních obrázků je, že je tu něco navíc k údržbě v laboratoři. Například zkušební verze produktů vyprší v průběhu času (nebo) nově vydané aktualizace zabezpečení nejsou použity, což nás nutí pravidelně aktualizovat vlastní obrázek. S továrně bitových kopií máte definici bitové kopie se změnami do správy zdrojového kódu a máte automatizovaný proces vytváření vlastních bitových kopií na základě definice.

Řešení umožňuje rychlost vytváření virtuálních počítačů z vlastních bitových kopií a zároveň eliminuje další průběžné náklady na údržbu. Pomocí tohoto řešení můžete automaticky vytvářet vlastní image, distribuovat je do jiných laboratoří DevTest A vyřadit staré bitové kopie. V následujícím videu se dozvíte o továrně obrázků a o tom, jak je implementována pomocí devTest Labs.  Všechny skripty Azure Powershellu jsou volně [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory)dostupné a umístěné tady: .

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Pohled na řešení na vysoké úrovni
Řešení umožňuje rychlost vytváření virtuálních počítačů z vlastních bitových kopií a zároveň eliminuje další průběžné náklady na údržbu. Pomocí tohoto řešení můžete automaticky vytvářet vlastní image a distribuovat je do jiných laboratoří DevTest. Azure DevOps (dříve Visual Studio Team Services) jako modul orchestrace pro automatizaci všech operací v DevTest Labs.

![Pohled na řešení na vysoké úrovni](./media/create-image-factory/high-level-view-of-solution.png)

K dispozici je [rozšíření VSTS pro DevTest Labs,](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) které umožňuje provádět tyto jednotlivé kroky:

- Vytvoření vlastní image
- Vytvoření virtuálního počítače
- Odstranění virtuálního počítače
- Vytvořit prostředí
- Odstranit prostředí
- Naplnit prostředí

Použití rozšíření DevTest Labs je snadný způsob, jak začít s automatickým vytvářením vlastních ibi v DevTest Labs.

Existuje alternativní implementace pomocí skriptu Prostředí PowerShell pro složitější scénář. Pomocí prostředí PowerShell můžete plně automatizovat továrnu na bitové kopie založené na laboratořích DevTest, kterou lze použít v řetězci nástrojů průběžné integrace a průběžného doručování (CI/CD). Principy uplatní v tomto alternativním řešení jsou:

- Běžné aktualizace by neměly vyžadovat žádné změny v továrně bitové kopie. (například přidání nového typu vlastní image, automatické vyřazení starých obrázků, přidání nového 'koncového bodu' DevTest Labs pro příjem vlastních obrázků a tak dále.)
- Běžné změny jsou podpořeny spravovanou položkou zdrojového kódu (infrastruktura jako kód)
- DevTest Labs příjem vlastních bitových kopií nemusí být ve stejném předplatném Azure (testovací prostředí span předplatné)
- Skripty prostředí PowerShell musí být opakovaně použitelné, abychom mohli podle potřeby roztáčet další továrny

## <a name="next-steps"></a>Další kroky
Přechod na další článek v této části: [Spuštění továrny bitových bitů z Azure DevOps](image-factory-set-up-devops-lab.md)
