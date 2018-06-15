---
title: Jak k označování prostředků virtuálního počítače s Windows v Azure | Microsoft Docs
description: Další informace o označování Windows virtuální počítač vytvořený v Azure pomocí modelu nasazení Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: 5044bda855b6ac88eb5784f257686bf8a1838222
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30917495"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Postup označení virtuálního počítače s Windows v Azure
Tento článek popisuje různé způsoby, jak označit virtuálního počítače s Windows v Azure pomocí modelu nasazení Resource Manager. Značky jsou páry klíč – hodnota definovaný uživatelem, které mohou být umístěny přímo na prostředek nebo skupina zdrojů. Azure aktuálně podporuje až 15 značky pro každý prostředků a skupina prostředků. Značky mohou umístit na prostředku v době vytvoření nebo přidat do existující prostředek. Upozorňujeme, že značky jsou podporovány pro prostředky vytvořené pomocí modelu nasazení Resource Manager jenom. Pokud chcete označit virtuální počítač s Linuxem, přečtěte si téma [jak k označování virtuální počítač s Linuxem v Azure](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Označování pomocí prostředí PowerShell
Pokud chcete vytvořit, přidat a odstranit značky pomocí prostředí PowerShell, nejprve musíte nastavit vaše [prostředí PowerShell s Azure Resource Manager][PowerShell environment with Azure Resource Manager]. Po dokončení instalace můžete umístit značky na výpočty, síť a úložiště prostředků při vytváření nebo po vytvoření prostředku pomocí prostředí PowerShell. Tento článek se zaměří na zobrazení nebo úpravu značky umístěny na virtuální počítače.

První, přejděte k virtuálnímu počítači prostřednictvím `Get-AzureRmVM` rutiny.

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Pokud virtuální počítač už obsahuje značky, zobrazí se všechny značky na prostředek:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Pokud chcete přidat značky pomocí prostředí PowerShell, můžete použít `Set-AzureRmResource` příkaz. Poznámka: při aktualizaci značky pomocí prostředí PowerShell, značky jsou aktualizovány jako celek. Proto pokud přidáváte jednu značku k prostředku, který již má značky, musíte zahrnout všechny značky, které chcete umístit do prostředku. Dole je příklad toho, jak přidat další značky prostředku pomocí rutin prostředí PowerShell.

Tato první rutina nastaví všechny uvedené značky, umístit na *MyTestVM* k *$tags* proměnné, pomocí `Get-AzureRmResource` a `Tags` vlastnost.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Druhý příkaz zobrazí značek pro daný proměnnou.

        PS C:\> $tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment

Třetí příkaz přidá značku další na *$tags* proměnné. Všimněte si použití **+=** připojit nový pár klíč/hodnota do *$tags* seznamu.

        PS C:\> $tags += @{Name="Location";Value="MyLocation"}

Čtvrtý příkaz nastaví všechny značky definované v *$tags* proměnnou pro daný prostředek. V takovém případě je MyTestVM.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Páté příkaz zobrazí všechny značek u prostředku. Jak vidíte, *umístění* je nyní definována jako značka *MyLocation* jako hodnotu.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment
        Value        MyLocation
        Name        Location

Další informace o označování pomocí prostředí PowerShell, podívejte se [rutiny Azure Resource][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Další postup
* Další informace o označování prostředků Azure, najdete v části [přehled Azure Resource Manageru] [ Azure Resource Manager Overview] a [pomocí značek k uspořádání prostředků Azure][Using Tags to organize your Azure Resources].
* Jak značky vám může pomoci spravovat používání prostředků Azure, najdete v tématu [pochopení vaše faktura Azure] [ Understanding your Azure Bill] a [proniknout do vaší spotřeby prostředků Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/powershell-azure-resource-manager.md
[Azure Resource Cmdlets]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
