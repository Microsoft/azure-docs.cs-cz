---
title: Pomocí prostředí PowerShell ke správě prostředků Azure Event Hubs | Microsoft Docs
description: Modul prostředí PowerShell slouží k vytváření a správě služby Event Hubs
services: event-hubs
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: de86b8241166d4e0bd03beb22550464457e3db5e
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2017
ms.locfileid: "26782213"
---
# <a name="use-powershell-to-manage-event-hubs-resources"></a>Pomocí prostředí PowerShell ke správě prostředků služby Event Hubs

Microsoft Azure PowerShell je skriptovací prostředí, které můžete řídit a automatizovat nasazení a správu služeb Azure. Tento článek popisuje způsob použití [modulu PowerShell Resource Manager centra událostí](/powershell/module/azurerm.eventhub) a zřizovat a spravovat služby Event Hubs entity (obory názvů, jednotlivé služba event hubs a skupiny uživatelů) pomocí místní konzoly Azure PowerShell nebo skriptu.

Můžete také spravovat služby Event Hubs prostředků pomocí šablony Azure Resource Manager. Další informace najdete v článku [vytvoření oboru názvů Event Hubs s skupina rozbočovače a příjemce událostí pomocí šablony Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat následující:

* Předplatné Azure. Další informace o získání předplatného najdete v tématu [možností nákupu][purchase options], [nabízí člen][member offers], nebo [bezplatný účet][free account].
* Počítač s prostředím Azure PowerShell. Pokyny najdete v tématu [Začínáme pomocí rutin prostředí Azure PowerShell](/powershell/azure/get-started-azureps).
* Obecné znalosti skriptů prostředí PowerShell, balíčků NuGet a rozhraní .NET Framework.

## <a name="get-started"></a>Začínáme

Prvním krokem je pomocí prostředí PowerShell pro přihlášení k účtu Azure a předplatné Azure. Postupujte podle pokynů v [Začínáme pomocí rutin prostředí Azure PowerShell](/powershell/azure/get-started-azureps) k přihlášení k účtu Azure, pak načíst a přístup k prostředkům ve vašem předplatném Azure.

## <a name="provision-an-event-hubs-namespace"></a>Zřídit na obor názvů služby Event Hubs

Při práci se službou Event Hubs obory názvů, můžete použít [Get-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/get-azurermeventhubnamespace), [New-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/new-azurermeventhubnamespace), [odebrat AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/remove-azurermeventhubnamespace), a [Set-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/set-azurermeventhubnamespace) rutiny.

Tento příklad vytvoří pár lokální proměnné ve skriptu; `$Namespace` a `$Location`.

* `$Namespace`je název oboru názvů služby Event Hubs, se kterým chcete pracovat.
* `$Location`Určuje datové centrum, ve kterém bude jsme zřízení oboru názvů.
* `$CurrentNamespace`ukládá odkaz na obor názvů, který jsme načíst (nebo ji vytvořte).

Ve skriptu skutečné `$Namespace` a `$Location` lze předat jako parametry.

Tato část skript provede následující akce:

1. Pokusí se načíst na obor názvů služby Event Hubs se zadaným názvem.
2. Pokud je nalezen obor názvů, sestavy, co byl nalezen.
3. Pokud není nalezen obor názvů, vytvoří obor názvů a pak načte nově vytvořený obor názvů.

    ```powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
    }
    ```

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

Vytvoření centra událostí, proveďte kontrolu oboru názvů pomocí skriptu v předchozí části. Potom použít [New-AzureRmEventHub](/powershell/module/azurerm.eventhub/new-azurermeventhub) rutiny pro vytvoření centra událostí:

```powershell
# Check if event hub already exists
$CurrentEH = Get-AzureRMEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName

if($CurrentEH)
{
    Write-Host "The event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $EventHubName event hub does not exist."
    Write-Host "Creating the $EventHubName event hub in the $Location region..."
    New-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -Location $Location -MessageRetentionInDays 3
    $CurrentEH = Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $EventHubName event hub in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="create-a-consumer-group"></a>Vytvořte skupinu uživatelů

Vytvořte skupinu uživatelů v rámci centra událostí, provádět obor názvů a události rozbočovače kontrol pomocí skriptů v předchozí části. Potom použít [New-AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/new-azurermeventhubconsumergroup) vytvořte skupiny uživatelů v rámci centra událostí. Příklad:

```powershell
# Check if consumer group already exists
$CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -ErrorAction Ignore

if($CurrentCG)
{
    Write-Host "The consumer group $ConsumerGroupName in event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $ConsumerGroupName consumer group does not exist."
    Write-Host "Creating the $ConsumerGroupName consumer group in the $Location region..."
    New-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
    $CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $ConsumerGroupName consumer group in event hub $EventHubName in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

#### <a name="set-user-metadata"></a>Metadata uživatele sady

Po provedení skripty v předchozí části, můžete použít [Set-AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/set-azurermeventhubconsumergroup) rutiny aktualizovat vlastnosti skupiny příjemců, jako v následujícím příkladu:

```powershell
# Set some user metadata on the CG
Write-Host "Setting the UserMetadata field to 'Testing'"
Set-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -UserMetadata "Testing"
# Show result
Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
```

## <a name="remove-event-hub"></a>Odebrat centra událostí

Chcete-li odebrat centra událostí, který jste vytvořili, můžete použít `Remove-*` rutin, jako v následujícím příkladu:

```powershell
# Clean up
Remove-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
Remove-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
Remove-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
```

## <a name="next-steps"></a>Další kroky

- Naleznete v dokumentaci k dokončení Powershellu Resource Manager centra událostí modulu [zde](/powershell/module/azurerm.eventhub). Tato stránka obsahuje seznam všech dostupných rutin.
- Informace o používání šablon Azure Resource Manageru, najdete v článku [vytvoření oboru názvů Event Hubs s skupina rozbočovače a příjemce událostí pomocí šablony Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub.md).
- Informace o [knihovny .NET centra událostí správy](event-hubs-management-libraries.md).

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/
