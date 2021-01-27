---
title: Jak označit virtuální počítač pomocí Azure Portal
description: Přečtěte si informace o označení virtuálního počítače pomocí Azure Portal.
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 9c220814ae1f714e9eac0c0c11a50d9cf68a621d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897404"
---
# <a name="tagging-a-vm-using-the-portal"></a>Označení virtuálního počítače pomocí portálu

Tento článek popisuje, jak přidat značky k virtuálnímu počítači pomocí portálu. Značky jsou páry klíč/hodnota definované uživatelem, které lze umístit přímo do prostředku nebo skupiny prostředků. Azure v současné době podporuje až 50 značek na jeden prostředek a skupinu prostředků. Značky lze umístit na prostředek v době vytvoření nebo přidání do existujícího prostředku.


1. Na portálu přejděte na svůj virtuální počítač.
1. V nástroji **Essentials** vyberte **kliknutím sem přidejte značky**.

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="Snímek obrazovky s částí základy stránky virtuálního počítače":::

1. Přidejte hodnotu pro **název** a **hodnotu** a pak vyberte **Uložit**.

    :::image type="content" source="media/tag/key-value.png" alt-text="Snímek obrazovky stránky pro přidání páru klíč-hodnota jako značky":::

### <a name="next-steps"></a>Další kroky

- Další informace o označování vašich prostředků Azure najdete v tématu [přehled Azure Resource Manager přehledu](../azure-resource-manager/management/overview.md) a [použití značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md).
- Informace o tom, jak značky vám pomůžou při správě používání prostředků Azure, najdete v tématu [Princip fakturace Azure](../cost-management-billing/understand/review-individual-bill.md).
