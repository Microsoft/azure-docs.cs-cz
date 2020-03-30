---
title: Vytvoření nabídky virtuálních strojů na Azure Marketplace
description: Uvádí kroky potřebné k vytvoření nabídky nového virtuálního počítače (VM) pro Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: bc1cf7a839307e65bd91eb29531663141e521472
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278037"
---
# <a name="create-virtual-machine-offer"></a>Vytvořit nabídku virtuálního počítače

V této části jsou uvedeny kroky potřebné k vytvoření nového virtuálního počítače (VM) žádost o nabídku pro Azure Marketplace.  Každá nabídka se na Azure Marketplace zobrazuje jako vlastní entita a je přidružená k jedné nebo více skum.  Nabídka virtuálních her se skládá z následujících seskupení aktiv a podpůrných služeb: 

![Prostředky pro nabídku virtuálních mís](./media/publishvm_002.png)

kde:

|  **Skupina majetku**   |  **Popis**  |
|  ---------------   |  ---------------  |
|    SKU            |  Nejmenší prodejná jednotka nabídky. Jedna nabídka (třída produktu) může mít více sku s ykusařské systémy, které jsou k ní přidruženy, aby bylo možné rozlišovat mezi podporovanými funkcemi, typy bitových bitových bitových bitových propojení virtuálních počítačů a fakturačními modely. |
|  Marketplace       | Obsahuje marketingová, právní a vedoucí aktiva a specifikace správy.  <ul><li> Marketingové prostředky zahrnují název nabídky, popis a loga</li> <li> Právní majetek zahrnuje zásady ochrany osobních údajů, podmínky použití a další právní dokumentaci</li>  <li> Zásady správy potenciálních zákazníků umožňují určit, jak zpracovat zájemce z portálu koncového uživatele Azure Marketplace.</li> </ul> |
| Podpora            | Obsahuje informace o kontaktech podpory a zásadách. |
| Testovací verze         | Definuje datové zdroje, které koncovým uživatelům umožňují otestovat vaši nabídku před zakoupením. |
|  |  |


## <a name="new-offer-form"></a>Formulář nové nabídky

Po přihlášení na [portál pro partnery cloudu](https://cloudpartner.azure.com/)klikněte na položku **+ Nová nabídka** na levém panelu menu. Ve výsledné nabídce klikněte na **virtuální počítače,** chcete-li zobrazit formulář **Nová nabídka** a spustit proces definování datových zdrojů pro novou nabídku virtuálního počítače. 
<!-- not all publishers see corevm or azure apps test, you need to be whitelisted to see them. we should hide those in these images. -->

![Nový virtuální stroj nabízí výběr uživatelského rozhraní](./media/publishvm_003.png)

> [!WARNING]
> Pokud možnost **Virtuální počítače** není zobrazena nebo není povolená, váš účet nemá oprávnění k vytvoření tohoto typu nabídky.  Zkontrolujte, zda jste splnili všechny [požadavky](./cpp-prerequisites.md) pro tento typ nabídky, včetně registrace vývojářského účtu.


## <a name="next-steps"></a>Další kroky

Následující témata v této části zrcadlí karty na stránce **Nová nabídka** (pro typ nabídky virtuálního soudu).  Každý článek vysvětluje, jak pomocí přidružené karty definovat skupiny prostředků a podpůrné služby pro vaši novou nabídku virtuálních her.

- [Karta Nastavení nabídky](./cpp-offer-settings-tab.md)
- [Karta Skladové položky](./cpp-skus-tab.md)
- [Karta Testovací verze](./cpp-test-drive-tab.md)
- [Karta Marketplace](./cpp-marketplace-tab.md)
- [Karta Podpora](./cpp-support-tab.md)
