---
title: Vytvoření nabídky virtuálních počítačů v Azure Marketplace | Dokumentace Microsoftu
description: Seznamy umožňují kroky potřebné k vytvoření nového virtuálního počítače (VM) pro Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 0a875b69a10f6305b4ffe32b63d3db5b508faf01
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639720"
---
# <a name="create-virtual-machine-offer"></a>Vytvoření nabídky virtuálních počítačů

Tato část uvádí kroky potřebné k vytvoření nové žádosti nabídky virtuálních počítačů (VM) pro Azure Marketplace.  Každé nabídky se zobrazí jako vlastní entity na webu Azure Marketplace a je přidružený nejmíň jeden skladové položky.  Nabídky virtuálních počítačů se skládá z následujících seskupení prostředků a služeb podpory: 

![Prostředky pro nabídky virtuálních počítačů](./media/publishvm_002.png)

kde:

|  **Skupiny prostředků**   |  **Popis**  |
|  ---------------   |  ---------------  |
|    Skladové položky            |  Nejmenší prodejných jednotka nabídky. Jediné nabídky (produktu třídy) může mít více jednotek SKU přidružený k rozlišení mezi podporované funkce, typy imagí virtuálních počítačů a modely fakturace. |
|  Marketplace       | Obsahuje marketing, právních i průběžné správy prostředků a specifikace.  <ul><li> Marketingové prostředky zahrnují název nabídky, popis a loga</li> <li> Právní prostředky zahrnují další právní dokumentace, zásady ochrany osobních údajů a podmínky použití</li>  <li> Zásada umožňuje průběžné správy můžete určit způsob zpracování vede z portálu Azure Marketplace s koncovým uživatelem.</li> </ul> |
| Podpora            | Obsahuje informace o podpoře kontaktní údaje a zásady |
| Testovací verze         | Definuje prostředky, které umožňují koncovým uživatelům otestujte předtím, než může zakoupit |
|  |  |


## <a name="new-offer-form"></a>Formulář nové nabídky

Jednou vaše Přihlaste se [portál partnerů cloudu](http://cloudpartner.azure.com/), klikněte na tlačítko **+ nová nabídka** položka na levém řádku nabídek. V nabídce výsledný klikněte na **virtuálních počítačů** zobrazíte **nová nabídka** formuláře a zahájit proces definice prostředků pro nové nabídky virtuálních počítačů. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Výběr nového virtuálního počítače nabídka uživatelského rozhraní](./media/publishvm_003.png)

> [!WARNING]
> Pokud **virtuálních počítačů** možnost není zobrazena, nebo není povoleno a pak váš účet nemá oprávnění vytvořit tento typ nabídky.  Zkontrolujte prosím, že jste splnili veškeré [požadavky](./cpp-prerequisites.md) pro tento typ nabídky, včetně registrace pro účet pro vývojáře.


## <a name="next-steps"></a>Další postup

Následující témata v této části odráží karet **nová nabídka** stránky (pro typ nabídky virtuálních počítačů).  Každý článek vysvětluje, jak používat kartě přidružené k definování skupin prostředků a služeb podpory pro nové nabídky virtuálních počítačů.

- [Karta Nastavení nabídky](./cpp-offer-settings-tab.md)
- [Karta Skladové položky](./cpp-skus-tab.md)
- [Karta testu jednotky](./cpp-test-drive-tab.md)
- [Karta Marketplace](./cpp-marketplace-tab.md)
- [Karta Podpora](./cpp-support-tab.md)
