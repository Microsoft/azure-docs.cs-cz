---
title: Označení virtuálního počítače pomocí šablony
description: Přečtěte si informace o označení virtuálního počítače pomocí šablony.
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.author: cynthn
author: cynthn
ms.date: 10/26/2018
ms.openlocfilehash: d1acbe82a086574a102e7897bbd3b99683c1185e
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594941"
---
# <a name="tagging-a-vm-using-a-template"></a>Označení virtuálního počítače pomocí šablony


Tento článek popisuje, jak označit virtuální počítač v Azure pomocí šablony Správce prostředků. Značky jsou páry klíč/hodnota definované uživatelem, které lze umístit přímo do prostředku nebo skupiny prostředků. Azure v současné době podporuje až 50 značek na jeden prostředek a skupinu prostředků. Značky lze umístit na prostředek v době vytvoření nebo přidání do existujícího prostředku.

[Tato šablona](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) umisťuje značky na následujících zdrojích: COMPUTE (virtuální počítač), Storage (účet úložiště) a síť (veřejná IP adresa, Virtual Network a síťové rozhraní). Tato šablona je určena pro virtuální počítače s Windows, ale dá se přizpůsobit pro virtuální počítače se systémem Linux.

Klikněte na tlačítko **nasadit do Azure** z [odkazu šablona](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Tím přejdete na [Azure Portal](https://portal.azure.com/) , kde můžete nasadit tuto šablonu.

![Jednoduché nasazení pomocí značek](./media/tag/deploy-to-azure-tags.png)

Tato šablona obsahuje následující značky: *oddělení* , *aplikace* a *vytvořil*. Tyto značky můžete přidat nebo upravit přímo v šabloně, pokud byste chtěli, aby byly názvy různých značek.

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


**Další kroky**

- Další informace o označování vašich prostředků Azure najdete v tématu [přehled Azure Resource Manager přehledu](../azure-resource-manager/management/overview.md) a [použití značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md).
- Pokud chcete zjistit, jak vám značky pomůžou spravovat vaše používání prostředků Azure, přečtěte si článek [Princip fakturace Azure](../cost-management-billing/understand/review-individual-bill.md) a [Získejte přehled o využití prostředků Microsoft Azure](../cost-management-billing/manage/usage-rate-card-overview.md).
