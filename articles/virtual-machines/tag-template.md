---
title: Označení virtuálního počítače pomocí šablony
description: Přečtěte si informace o označení virtuálního počítače pomocí šablony.
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.author: cynthn
author: cynthn
ms.date: 10/26/2018
ms.openlocfilehash: e7dd75a025b76773a0bf1e3b4f752b5a77db6786
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897370"
---
# <a name="tagging-a-vm-using-a-template"></a>Označení virtuálního počítače pomocí šablony

Tento článek popisuje, jak označit virtuální počítač v Azure pomocí šablony Správce prostředků. Značky jsou páry klíč/hodnota definované uživatelem, které lze umístit přímo do prostředku nebo skupiny prostředků. Azure v současné době podporuje až 50 značek na jeden prostředek a skupinu prostředků. Značky lze umístit na prostředek v době vytvoření nebo přidání do existujícího prostředku.

[Tato šablona](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) umisťuje značky na následujících zdrojích: COMPUTE (virtuální počítač), Storage (účet úložiště) a síť (veřejná IP adresa, Virtual Network a síťové rozhraní). Tato šablona je určena pro virtuální počítače s Windows, ale dá se přizpůsobit pro virtuální počítače se systémem Linux.

Klikněte na tlačítko **nasadit do Azure** z [odkazu šablona](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Tím přejdete na [Azure Portal](https://portal.azure.com/) , kde můžete nasadit tuto šablonu.

![Jednoduché nasazení pomocí značek](./media/tag/deploy-to-azure-tags.png)

Tato šablona obsahuje následující značky: *oddělení*, *aplikace* a *vytvořil*. Tyto značky můžete přidat nebo upravit přímo v šabloně, pokud byste chtěli, aby byly názvy různých značek.

![Značky Azure v šabloně](./media/tag/azure-tags-in-a-template.png)

Jak vidíte, značky jsou definovány jako páry klíč/hodnota oddělené dvojtečkou (:). Značky musí být definovány v tomto formátu:

```config
"tags": {
    "Key1" : "Value1",
    "Key2" : "Value2"
}
```

Uložte soubor šablony po dokončení úprav pomocí značek podle vašeho výběru.

Dále můžete v části **Upravit parametry** vyplnit hodnoty pro vaše značky.

![Upravit značky v Azure Portal](./media/tag/edit-tags-in-azure-portal.png)

Kliknutím na **vytvořit** Nasaďte tuto šablonu s hodnotami značek.

### <a name="next-steps"></a>Další kroky

- Další informace o označování vašich prostředků Azure najdete v tématu [přehled Azure Resource Manager přehledu](../azure-resource-manager/management/overview.md) a [použití značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md).
- Informace o tom, jak značky vám pomůžou při správě používání prostředků Azure, najdete v tématu [Princip fakturace Azure](../cost-management-billing/understand/review-individual-bill.md).
