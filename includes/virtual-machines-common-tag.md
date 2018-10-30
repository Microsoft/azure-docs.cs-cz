---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: ccc2b574ea054a1b0ecf32a1e59691050fb66fcf
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226667"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Označení virtuálního počítače pomocí šablony
Nejprve Podívejme se na označování prostřednictvím šablon. [Tato šablona](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) umístí na následujících odkazech: prostředky (virtuální počítač), úložiště (účet služby Storage) a sítí (veřejné IP adresy, virtuální sítě a síťové rozhraní). Tato šablona je pro virtuální počítač s Windows, ale můžete přizpůsobit pro virtuální počítače s Linuxem.

Klikněte na tlačítko **nasadit do Azure** tlačítko [odkaz na šablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Tím přejdete [webu Azure portal](https://portal.azure.com/) kde můžete nasadit tuto šablonu.

![Jednoduché nasazení se značkami](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Tato šablona obsahuje následující značky: *oddělení*, *aplikace*, a *vytvořil*. Je můžete přidat nebo upravit tyto značky přímo v šabloně Pokud byste o ni názvy různých značek.

![V šabloně Azure značky](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Jak je vidět, značky jsou definovány jako páry klíč hodnota oddělené dvojtečkou (:). Značky musí být definován v tomto formátu:

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

Po dokončení úprav se značkami podle vašeho výběru, uložte soubor šablony.

Vedle **upravit parametry** oddílu, můžete zadat hodnoty pro značky.

![Upravit značky na webu Azure portal](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Klikněte na tlačítko **vytvořit** k nasazení této šablony se vaše hodnoty značek.

## <a name="tagging-through-the-portal"></a>Označování pomocí portálu
Po vytvoření vašich prostředků pomocí značek, můžete zobrazit, přidat a odstranit značky na portálu.

Vyberte ikonu značky a zobrazit svá klíčová slova:

![Ikona značky na webu Azure portal](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Přidat novou značku přes portál tak, že definujete vlastní dvojice klíč/hodnota a uložte ho.

![Přidat novou značku na webu Azure portal](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Nová značka by měla teď budou zobrazovat v seznamu značek pro váš prostředek.

![Nová značka uložena na webu Azure portal](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

