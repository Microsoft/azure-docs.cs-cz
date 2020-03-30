---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: f6bd574c83d309ce6d6f54fdb1c7d23cb713420d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73182193"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Označení virtuálního počítače pomocí šablon
Nejprve se podívejme na označování šablon. [Tato šablona](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) umísťuje značky na následující prostředky: Výpočetní prostředky (Virtuální počítač), Úložiště (účet úložiště) a Síť (veřejná IP adresa, virtuální síť a síťové rozhraní). Tato šablona je pro virtuální počítač se systémem Windows, ale může být přizpůsobena pro virtuální počítače s Linuxem.

Klikněte na tlačítko **Nasadit do Azure** z [odkazu šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Tím přejdete na [portál Azure,](https://portal.azure.com/) kde můžete tuto šablonu nasadit.

![Jednoduché nasazení se značkami](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Tato šablona obsahuje následující značky: *Oddělení*, *Aplikace*a *Vytvořeno .* Tyto značky můžete přidat nebo upravit přímo v šabloně, pokud chcete jiné názvy značek.

![Značky Azure v šabloně](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Jak můžete vidět, značky jsou definovány jako dvojice klíč/hodnota, oddělené dvojtečkou (:). Značky musí být definovány v tomto formátu:

        "tags": {
            "Key1" : "Value1",
            "Key2" : "Value2"
        }

Po dokončení úprav souboru šablony uložte pomocí značek, které si vyberete.

Dále v části **Upravit parametry** můžete vyplnit hodnoty značek.

![Úpravy značek na webu Azure Portal](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Kliknutím na **Vytvořit** nasadíte tuto šablonu s hodnotami značek.

## <a name="tagging-through-the-portal"></a>Označování prostřednictvím portálu
Po vytvoření prostředků pomocí značek můžete značky na portálu zobrazit, přidat a odstranit.

Vyberte ikonu značek pro zobrazení značek:

![Ikona značek na webu Azure Portal](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Přidejte novou značku prostřednictvím portálu definováním vlastního páru klíč/hodnota a uložte ji.

![Přidání nové značky na webu Azure Portal](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Nová značka by se nyní měla zobrazit v seznamu značek pro váš prostředek.

![Nová značka uložená na webu Azure Portal](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

