---
title: Vytvoření objektu pro vytváření imagí v Azure DevTest Labs | Microsoft Docs
description: V tomto článku se dozvíte, jak nastavit vlastní objekt pro vytváření imagí pomocí ukázkových skriptů dostupných v úložišti Git (Azure DevTest Labs).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 14c8338d6a5ae21847da3a9c774ea6dcdac33ae4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85482067"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Vytvoření vlastní továrny imagí v Azure DevTest Labs
V tomto článku se dozvíte, jak nastavit vlastní objekt pro vytváření imagí pomocí ukázkových skriptů dostupných v [úložišti Git](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>Co je to objekt pro vytváření obrázků?
Objekt pro vytváření imagí je řešení pro konfiguraci, které vytváří a distribuuje image automaticky v pravidelných intervalech s použitím všech požadovaných konfigurací. Obrázky v továrně imagí jsou vždycky aktuální a Průběžná údržba je po automatizaci celého procesu skoro nula. A vzhledem k tomu, že všechny požadované konfigurace už jsou v imagi, šetří čas od ruční konfigurace systému po vytvoření virtuálního počítače se základním operačním systémem.

Významný akcelerátor, jak získat Desktop pro vývojáře do připraveného stavu v DevTest Labs, používá vlastní image. Nevýhodou vlastních imagí je ještě něco dalšího, co je potřeba v testovacím prostředí udržovat. Například zkušební verze produktů, jejichž platnost vyprší v čase (nebo nově vydané aktualizace zabezpečení, se nepoužijí, což nám vynutí pravidelnou aktualizaci vlastní image). S objektem pro vytváření imagí máte definici image vrácenou se správou zdrojového kódu a máte automatizovaný proces, který vytváří vlastní image na základě definice.

Řešení umožňuje zrychlit vytváření virtuálních počítačů z vlastních imagí a zároveň eliminovat náklady na další probíhající údržbu. Pomocí tohoto řešení můžete automaticky vytvářet vlastní image, distribuovat je do jiných DevTest Labs a vyřadit staré image. V následujícím videu se dozvíte o továrně imagí a způsobu jejich implementace pomocí DevTest Labs.  Všechny skripty Azure PowerShellu jsou volně dostupné a nacházejí se tady:  [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory) .

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Pohled na vysoké úrovni řešení
Řešení umožňuje zrychlit vytváření virtuálních počítačů z vlastních imagí a zároveň eliminovat náklady na další probíhající údržbu. Pomocí tohoto řešení můžete automaticky vytvářet vlastní image a distribuovat je do jiných DevTest Labs. Jako modul orchestrace pro automatizaci všech operací v DevTest Labs používáte Azure DevOps (dříve Visual Studio Team Services).

![Pohled na vysoké úrovni řešení](./media/create-image-factory/high-level-view-of-solution.png)

K dispozici je [rozšíření VSTS pro DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) , které umožňuje provádět tyto jednotlivé kroky:

- Vytvoření vlastní image
- Vytvoření virtuálního počítače
- Odstranění virtuálního počítače
- Vytvořit prostředí
- Odstranit prostředí
- Naplnit prostředí

Použití rozšíření DevTest Labs představuje snadný způsob, jak začít s automatickým vytvářením vlastních imagí v DevTest Labs.

Existuje alternativní implementace pomocí skriptu PowerShellu pro složitější scénář. Pomocí prostředí PowerShell můžete plně automatizovat továrnu imagí založenou na DevTest Labs, kterou je možné použít při průběžné integraci a průběžném doručování (CI/CD) sada nástrojů. Principy, které následují v tomto alternativním řešení:

- Běžné aktualizace by neměly vyžadovat žádné změny továrny imagí. (například přidání nového typu vlastní image, automatické vyřazení starých imagí, přidání nového ' Endpoint ' DevTest Labs pro příjem vlastních imagí atd.)
- Běžné změny jsou zajištěny prostřednictvím správy zdrojového kódu (infrastruktura jako kód).
- DevTest Labs, které přijímají vlastní image, nemusí být ve stejném předplatném Azure (předplatná prostředí Labs).
- Skripty PowerShellu je potřeba znovu použít, abychom mohli podle potřeby vystavovat další továrny.

## <a name="next-steps"></a>Další kroky
Přejděte k dalšímu článku v této části: spuštění objektu pro [vytváření imagí z Azure DevOps](image-factory-set-up-devops-lab.md)
