---
title: Označení virtuálního počítače pomocí PowerShellu
description: Další informace o označování virtuálního počítače pomocí PowerShellu
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 1efb512923caed97126bdb4ee6267c6a9b57f251
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594937"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>Jak označit virtuální počítač v Azure pomocí PowerShellu

Tento článek popisuje, jak označit virtuální počítač v Azure pomocí PowerShellu. Značky jsou páry klíč/hodnota definované uživatelem, které lze umístit přímo do prostředku nebo skupiny prostředků. Azure v současné době podporuje až 50 značek na jeden prostředek a skupinu prostředků. Značky lze umístit na prostředek v době vytvoření nebo přidání do existujícího prostředku. Pokud chcete označit virtuální počítač pomocí rozhraní příkazového řádku Azure, přečtěte si téma [jak označit virtuální počítač v Azure pomocí rozhraní příkazového řádku Azure CLI](tag-cli.md).


Pomocí `Get-AzVM` rutiny zobrazíte aktuální seznam značek pro váš virtuální počítač.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" | Format-List -Property Tags
```

Pokud váš virtuální počítač již obsahuje značky, zobrazí se všechny značky ve formátu seznamu.

K přidání značek použijte `Set-AzResource` příkaz. Při aktualizaci značek prostřednictvím PowerShellu se značky aktualizují jako celku. Pokud přidáváte jednu značku k prostředku, který již obsahuje značky, bude nutné zahrnout všechny značky, které chcete umístit do prostředku. Níže je uveden příklad přidání dalších značek k prostředku prostřednictvím rutin prostředí PowerShell.

Přiřaďte všechny aktuální značky pro virtuální počítač k `$tags` proměnné pomocí `Get-AzResource` `Tags` vlastnosti a.

```azurepowershell-interactive
$tags = (Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags
```

Chcete-li zobrazit aktuální značky, zadejte proměnnou.

```azurepowershell-interactive
$tags
```

Výstup by měl vypadat takto:

```output
Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

V následujícím příkladu přidáme značku nazvanou `Location` s hodnotou `myLocation` . Slouží `+=` k připojení nové dvojice klíč-hodnota k `$tags` seznamu.

```azurepowershell-interactive
$tags += @{Location="myLocation"}
```

Použijte `Set-AzResource` k nastavení všech značek definovaných v *$Tags* proměnné na virtuálním počítači.

```azurepowershell-interactive
Set-AzResource -ResourceGroupName myResourceGroup -Name myVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

Slouží `Get-AzResource` k zobrazení všech značek v prostředku.

```azurepowershell-interactive
(Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags

```

Výstup by měl vypadat nějak takto, který teď obsahuje novou značku:

```output

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```


**Další kroky**

- Další informace o označování vašich prostředků Azure najdete v tématu [přehled Azure Resource Manager přehledu](../azure-resource-manager/management/overview.md) a [použití značek k uspořádání prostředků Azure](../azure-resource-manager/management/tag-resources.md).
- Pokud chcete zjistit, jak vám značky pomůžou spravovat vaše používání prostředků Azure, přečtěte si článek [Princip fakturace Azure](../cost-management-billing/understand/review-individual-bill.md) a [Získejte přehled o využití prostředků Microsoft Azure](../cost-management-billing/manage/usage-rate-card-overview.md).
