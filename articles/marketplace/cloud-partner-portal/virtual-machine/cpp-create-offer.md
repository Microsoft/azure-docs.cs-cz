---
title: Vytvoření nabídky virtuálního počítače v Azure Marketplace
description: Seznam kroků požadovaných k vytvoření nové nabídky virtuálního počítače pro Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 1e10bd22b91b51fb811601e49fad06d8f8b30127
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73817104"
---
# <a name="create-virtual-machine-offer"></a>Vytvořit nabídku virtuálního počítače

V této části jsou uvedené kroky potřebné k vytvoření nového požadavku na nabídku pro virtuální počítač (VM) pro Azure Marketplace.  Každá nabídka se zobrazí jako vlastní entita v Azure Marketplace a je spojena s jednou nebo více SKU.  Nabídka virtuálních počítačů se skládá z následujících skupin prostředků a podpůrných služeb: 

![Prostředky pro nabídku virtuálních počítačů](./media/publishvm_002.png)

,

|  **Skupina prostředků**   |  **Popis**  |
|  ---------------   |  ---------------  |
|    SKU            |  Nejmenší jednotka kupní jednotky nabídky. K jedné nabídce (třídě produktu) může být přidruženo více SKU, aby bylo možné rozlišovat mezi podporovanými funkcemi, typy imagí virtuálních počítačů a modely fakturace. |
|  Marketplace       | Obsahuje prostředky a specifikace pro marketing, právní a vedoucí.  <ul><li> Marketingové prostředky zahrnují název nabídky, popis a loga.</li> <li> Právní prostředky zahrnují zásady ochrany osobních údajů, podmínek použití a další právní dokumentaci.</li>  <li> Zásady správy potenciálních zákazníků vám umožní určit, jak se mají zpracovávat potenciální zákazníky z portálu Azure Marketplace portál pro koncové uživatele.</li> </ul> |
| Podpora            | Obsahuje informace o kontaktech a zásadách podpory |
| Testovací verze         | Definuje prostředky, které koncovým uživatelům umožní otestovat vaši nabídku před jejich nákupem. |
|  |  |


## <a name="new-offer-form"></a>Formulář nové nabídky

Po přihlášení k [portál partnerů cloudu](https://cloudpartner.azure.com/)klikněte na tlačítko **+ Nová položka nabídky** na levém řádku nabídek. V výsledné nabídce kliknutím na **Virtual Machines** zobrazíte formulář **nové nabídky** a zahájíte proces definování prostředků pro novou nabídku virtuálních počítačů. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Výběr uživatelského rozhraní nabídky nového virtuálního počítače](./media/publishvm_003.png)

> [!WARNING]
> Pokud není zobrazená možnost **Virtual Machines** nebo není povolená, váš účet nemá oprávnění k vytvoření tohoto typu nabídky.  Zkontrolujte prosím, že jste splnili všechny [předpoklady](./cpp-prerequisites.md) pro tento typ nabídky, včetně registrace pro vývojářský účet.


## <a name="next-steps"></a>Další kroky

Následující témata v této části zrcadlí karty na nové stránce **nabídky** (pro typ nabídky VM).  Každý článek vysvětluje, jak pomocí přidružené karty definovat skupiny prostředků a podpůrné služby pro novou nabídku virtuálních počítačů.

- [Karta Nastavení nabídky](./cpp-offer-settings-tab.md)
- [Karta Skladové položky](./cpp-skus-tab.md)
- [Karta Testovací verze](./cpp-test-drive-tab.md)
- [Karta Marketplace](./cpp-marketplace-tab.md)
- [Karta Podpora](./cpp-support-tab.md)
