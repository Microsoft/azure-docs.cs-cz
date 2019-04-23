---
title: Vytvořte objekt pro vytváření bitové kopie ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit objekt pro vytváření vlastní image ve službě Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: e51c56f54a4e30b5e9094388ed92aa0a62ba0840
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59796306"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Vytvořte objekt pro vytváření vlastní image ve službě Azure DevTest Labs
V tomto článku se dozvíte, jak vytvořit objekt pro vytváření vlastní image pomocí dostupných v ukázkových skriptech [úložiště Git](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>Co je objekt pro vytváření image?
Objekt pro vytváření bitové kopie je konfigurace jako kódu řešení, která sestavuje a distribuuje imagí automaticky v pravidelných intervalech s požadovanou konfigurací. Obrázky v objektu pro vytváření bitové kopie jsou vždy aktuální a průběžnou údržbu není téměř nulové po je celý proces automatizovat. A vzhledem k tomu, že všechny požadované konfigurace jsou již v bitové kopii, šetří čas z ručně po vytvoření virtuálního počítače s operačním systémem základní konfigurace systému.

Významné akcelerátoru zobrazíte developer desktop do připraveného stavu ve službě DevTest Labs je použití vlastní Image. Z vlastních imagí nevýhod je, že je něco navíc udržovat v testovacím prostředí. Například časem vypršení platnosti zkušební verze produktů (nebo) se nepoužijí nově vydané aktualizace zabezpečení, která vynutí nám pravidelně aktualizují vlastní image. Pomocí objektu pro vytváření bitové kopie máte definici image vráceny se změnami do zdrojového kódu a mít automatizovaný proces pro vytvoření vlastní Image na základě definice.

Toto řešení umožňuje rychlost vytváření virtuálních počítačů z vlastních imagí a nemusíte průběžnou údržbu další náklady. S tímto řešením můžete automaticky vytvořit vlastní Image, distribuovat na další DevTest Labs a vyřazení staré Image. V následujícím videu se dozvíte o objekt pro vytváření bitové kopie a jak je implementováno s DevTest Labs.  Všechny skripty Azure Powershellu jsou volně k dispozici a je umístěn zde: [ https://aka.ms/dtlimagefactory ](https://aka.ms/dtlimagefactory).

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Souhrnný přehled řešení
Toto řešení umožňuje rychlost vytváření virtuálních počítačů z vlastních imagí a nemusíte průběžnou údržbu další náklady. S tímto řešením můžete automaticky vytvořit vlastní Image a distribuovat na další DevTest Labs. Použití Azure DevOps (dřív Visual Studio Team Services) jako orchestrační modul pro automatizaci všechny operace ve službě DevTest Labs.

![Souhrnný přehled řešení](./media/create-image-factory/high-level-view-of-solution.png)

Je [rozšíření VSTS pro DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) , která umožňuje spustit tyto jednotlivé kroky: 

- Vytvoření vlastní image
- Vytvoření virtuálního počítače
- Odstranění virtuálního počítače
- Vytvoření prostředí
- Odstranění prostředí
- Naplnění prostředí

Použití rozšíření DevTest Labs je snadný způsob, jak začít pracovat s automaticky vytváření vlastních imagí ve službě DevTest Labs.

Není k dispozici alternativní implementace pomocí Powershellového skriptu pro složitější scénář. Pomocí Powershellu, lze plně automatizovat objekt pro vytváření bitové kopie založené na službě DevTest Labs, který lze použít v průběžnou integraci a průběžné doručování (CI/CD) nástrojů. Se zásadami, a potom v tomto alternativní řešení:

- Běžné aktualizace by měla nevyžadují žádné změny k objektu pro vytváření bitové kopie. (například přidání nového typu z vlastní image, automatické vyřazení staré Image, přidáte nový "koncový bod' DevTest Labs pro příjem vlastních imagí a tak dále.)
- Společné změny jsou zajišťované zdrojového kódu (infrastruktura jako kód)
- DevTest Labs přijímání vlastních imagí nemusí být ve stejném předplatném Azure (labs span předplatných)
- Powershellové skripty musí být opakovaně použitelné, proto jsme aktivovat další objekty pro vytváření podle potřeby

## <a name="next-steps"></a>Další postup
Přejít na další článek v této části: [Spustit objekt pro vytváření image z Azure DevOps](image-factory-set-up-devops-lab.md)
