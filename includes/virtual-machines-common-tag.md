---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: ccc2b574ea054a1b0ecf32a1e59691050fb66fcf
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "67174947"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Označení virtuálního počítače prostřednictvím šablon
Nejprve se podívejme na označování prostřednictvím šablon. [Tato šablona](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) umisťuje značky na následujících zdrojích: COMPUTE (virtuální počítač), Storage (účet úložiště) a síť (veřejná IP adresa, Virtual Network a síťové rozhraní). Tato šablona je určena pro virtuální počítače s Windows, ale dá se přizpůsobit pro virtuální počítače se systémem Linux.

Klikněte na tlačítko **nasadit do Azure** z [odkazu šablona](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Tím přejdete na [Azure Portal](https://portal.azure.com/) , kde můžete nasadit tuto šablonu.

![Jednoduché nasazení pomocí značek](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Tato šablona obsahuje následující značky: *Oddělení*, *aplikace*a *vytvořil*. Tyto značky můžete přidat nebo upravit přímo v šabloně, pokud byste chtěli, aby byly názvy různých značek.

![Značky Azure v šabloně](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Jak vidíte, značky jsou definovány jako páry klíč/hodnota oddělené dvojtečkou (:). Značky musí být definovány v tomto formátu:

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

Uložte soubor šablony po dokončení úprav pomocí značek podle vašeho výběru.

Dále můžete v části **Upravit parametry** vyplnit hodnoty pro vaše značky.

![Upravit značky v Azure Portal](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Kliknutím na **vytvořit** Nasaďte tuto šablonu s hodnotami značek.

## <a name="tagging-through-the-portal"></a>Označování prostřednictvím portálu
Po vytvoření prostředků pomocí značek můžete zobrazit, přidat a odstranit značky na portálu.

Vyberte ikonu značek pro zobrazení značek:

![Ikona značek v Azure Portal](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Přidejte novou značku prostřednictvím portálu tak, že definujete vlastní dvojici klíč/hodnota a uložíte ji.

![Přidat novou značku v Azure Portal](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Nová značka by se teď měla zobrazit v seznamu značek pro váš prostředek.

![Nová značka uložená v Azure Portal](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

