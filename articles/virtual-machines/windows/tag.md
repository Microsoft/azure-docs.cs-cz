---
title: Jak označit prostředek virtuálního počítače s Windows v Azure
description: Přečtěte si informace o označení virtuálního počítače s Windows vytvořeného v Azure pomocí modelu nasazení Správce prostředků.
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
ms.openlocfilehash: b17a126ac870e96c79c4c391f5f8381ce70f8d41
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462304"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Jak označit virtuální počítač s Windows v Azure
Tento článek popisuje různé způsoby, jak označit virtuální počítač s Windows v Azure pomocí modelu nasazení Správce prostředků. Značky jsou páry klíč/hodnota definované uživatelem, které lze umístit přímo do prostředku nebo skupiny prostředků. Azure v současné době podporuje až 15 značek na jeden prostředek a skupinu prostředků. Značky lze umístit na prostředek v době vytvoření nebo přidání do existujícího prostředku. Upozorňujeme, že značky se podporují jenom u prostředků vytvořených pomocí modelu nasazení Správce prostředků. Pokud chcete označit virtuální počítač se systémem Linux, přečtěte si téma [jak označit virtuální počítač se systémem Linux v Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Označování pomocí PowerShellu
Pokud chcete vytvořit, přidat a odstranit značky přes PowerShell, musíte nejdřív nastavit [prostředí PowerShell pomocí Azure Resource Manager][PowerShell environment with Azure Resource Manager]. Po dokončení instalace můžete značky umístit na výpočetní, síťové a úložné prostředky při vytváření nebo po vytvoření prostředku přes PowerShell. Tento článek se soustředí na zobrazení nebo úpravu značek umístěných na Virtual Machines.

 

Nejprve přejděte k virtuálnímu počítači pomocí rutiny `Get-AzVM`.

        PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Pokud váš virtuální počítač už obsahuje značky, zobrazí se vám všechny značky v prostředku:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Pokud chcete přidat značky přes PowerShell, můžete použít příkaz `Set-AzResource`. Poznámka: při aktualizaci značek prostřednictvím PowerShellu se značky aktualizují jako celek. Takže pokud přidáváte jednu značku k prostředku, který již obsahuje značky, bude nutné zahrnout všechny značky, které chcete umístit do prostředku. Níže je uveden příklad přidání dalších značek k prostředku prostřednictvím rutin prostředí PowerShell.

Tato první rutina nastaví všechny značky, které jsou umístěny na *MyTestVM* , do proměnné *$tags* pomocí vlastnosti `Get-AzResource` a `Tags`.

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

Třetí příkaz přidá do proměnné *$Tags* další značku. Všimněte si použití **+=** k připojení nové dvojice klíč-hodnota k seznamu *$Tags* .

        PS C:\> $tags += @{Location="MyLocation"}

Čtvrtý příkaz nastaví všechny značky definované v *$Tags* proměnnou na daný prostředek. V tomto případě je to MyTestVM.

        PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Pátý příkaz zobrazí všechny značky na prostředku. Jak vidíte, *umístění* je nyní definováno jako značka s *MyLocation* jako hodnotou.

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

Další informace o označování značek prostřednictvím PowerShellu najdete v části [rutiny prostředků Azure][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Další kroky
* Další informace o označování vašich prostředků Azure najdete v tématu [přehled Azure Resource Manager přehledu][Azure Resource Manager Overview] a [použití značek k uspořádání prostředků Azure][Using Tags to organize your Azure Resources].
* Pokud chcete zjistit, jak vám značky pomůžou spravovat vaše používání prostředků Azure, přečtěte si článek [Princip fakturace Azure][Understanding your Azure Bill] a [Získejte přehled o využití prostředků Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/manage-resources-powershell.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
