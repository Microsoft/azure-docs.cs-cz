---
title: Jak označit virtuální počítač pomocí Azure Portal
description: Přečtěte si informace o označení virtuálního počítače pomocí Azure Portal.
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 7c3e779c152a967452f86b55f06d38076102085c
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594940"
---
# <a name="tagging-a-vm-using-the-portal"></a>Označení virtuálního počítače pomocí portálu

Tento článek popisuje, jak přidat značky k virtuálnímu počítači pomocí portálu. Značky jsou páry klíč/hodnota definované uživatelem, které lze umístit přímo do prostředku nebo skupiny prostředků. Azure v současné době podporuje až 50 značek na jeden prostředek a skupinu prostředků. Značky lze umístit na prostředek v době vytvoření nebo přidání do existujícího prostředku.


1. Na portálu přejděte na svůj virtuální počítač.
1. V nástroji **Essentials** vyberte **kliknutím sem přidejte značky**.

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="Snímek obrazovky s částí základy stránky virtuálního počítače":::

1. Přidejte hodnotu pro **název** a **hodnotu** a pak vyberte **Uložit**.

    :::image type="content" source="media/tag/key-value.png" alt-text="Snímek obrazovky stránky pro přidání páru klíč-hodnota jako značky":::



**Další kroky**

- Další informace o označování vašich prostředků Azure najdete v tématu [přehled Azure Resource Manager přehledu](../azure-resource-manager/management/overview.md) a [použití značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md).
- Pokud chcete zjistit, jak vám značky pomůžou spravovat vaše používání prostředků Azure, přečtěte si článek [Princip fakturace Azure](../cost-management-billing/understand/review-individual-bill.md) a [Získejte přehled o využití prostředků Microsoft Azure](../cost-management-billing/manage/usage-rate-card-overview.md).