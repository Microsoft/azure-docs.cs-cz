---
title: Jak označit prostředek virtuálního počítače s Windows v Azure
description: Informace o označování virtuálního počítače s Windows vytvořeného v Azure pomocí modelu nasazení Správce prostředků
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: b646b1a14d6cedcafa662192229daa570a0d2441
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616429"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Jak označit virtuální počítač s Windows v Azure
Tento článek popisuje různé způsoby označení virtuálního počítače s Windows v Azure prostřednictvím modelu nasazení Správce prostředků. Značky jsou uživatelem definované dvojice klíč/hodnota, které lze umístit přímo na prostředek nebo skupinu prostředků. Azure aktuálně podporuje až 50 značek na prostředek a skupinu prostředků. Značky mohou být umístěny na prostředek v době vytvoření nebo přidány do existujícího prostředku. Upozorňujeme, že značky jsou podporovány pouze pro prostředky vytvořené prostřednictvím modelu nasazení Správce prostředků. Pokud chcete označit virtuální počítač s Linuxem, přečtěte [si, jak označit virtuální počítač Linuxu v Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Označování pomocí PowerShellu
Chcete-li vytvářet, přidávat a odstraňovat značky prostřednictvím PowerShellu, musíte nejprve nastavit [prostředí PowerShellu pomocí Správce prostředků Azure][PowerShell environment with Azure Resource Manager]. Po dokončení instalace můžete umístit značky na výpočetní prostředky, sítě a prostředky úložiště při vytváření nebo po vytvoření prostředku prostřednictvím prostředí PowerShell. Tento článek se zaměří na prohlížení / úpravy značek umístěných na virtuálních počítačích.

 

Nejprve přejděte na virtuální `Get-AzVM` počítač prostřednictvím rutiny.

        PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Pokud váš virtuální počítač už značky obsahuje, uvidíte všechny značky ve vašem zdroji:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Pokud chcete přidat značky přes PowerShell, `Set-AzResource` můžete použít příkaz. Poznámka: Při aktualizaci značek prostřednictvím PowerShellu se značky aktualizují jako celek. Pokud tedy přidáváte jednu značku k prostředku, který již značky má, budete muset zahrnout všechny značky, které chcete umístit do prostředku. Níže je uveden příklad, jak přidat další značky k prostředku prostřednictvím rutin prostředí PowerShell.

Tato první rutina nastaví všechny značky umístěné na *MyTestVM* `Tags` na *proměnnou $tags* pomocí vlastnosti `Get-AzResource` a.

        PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Druhý příkaz zobrazí značky pro danou proměnnou.

```
    PS C:\> $tags
    
    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
```

Třetí příkaz přidá další značku do *proměnné $tags.* Všimněte si **+=** použití připojit nový klíč/hodnota dvojice do seznamu *$tags.*

        PS C:\> $tags += @{Location="MyLocation"}

Čtvrtý příkaz nastaví všechny značky definované v *proměnné $tags* na daný prostředek. V tomto případě je MyTestVM.

        PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Pátý příkaz zobrazí všechny značky na prostředek. Jak můžete vidět, *Umístění* je nyní definována jako značka s *MyLocation* jako hodnota.

```
    PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
    Location      MyLocation
```

Další informace o označování pomocí PowerShellu najdete v [rutinách prostředků Azure][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Další kroky
* Další informace o označování prostředků Azure najdete v [tématu Přehled Správce prostředků Azure][Azure Resource Manager Overview] a použití značek k uspořádání prostředků [Azure][Using Tags to organize your Azure Resources].
* Informace o tom, jak vám značky můžou pomoct se správou využití prostředků Azure, najdete [v tématu Principy účtu Azure][Understanding your Azure Bill] a získání [přehledu o spotřebě prostředků Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/management/manage-resources-powershell.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
