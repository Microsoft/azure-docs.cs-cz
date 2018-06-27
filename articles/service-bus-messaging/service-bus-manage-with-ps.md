---
title: Pomocí prostředí PowerShell ke správě prostředků Azure Service Bus | Microsoft Docs
description: Modul prostředí PowerShell slouží k vytvoření a Správa prostředků služby Service Bus
services: service-bus-messaging
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: 962134c0c71ac0a251f8adf1f0f067d6067cb808
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37018617"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Pomocí prostředí PowerShell pro správu prostředků služby Service Bus

Microsoft Azure PowerShell je skriptovací prostředí, které můžete řídit a automatizovat nasazení a správu služeb Azure. Tento článek popisuje postup použití [modulu Powershellu pro Service Bus Resource Manager](/powershell/module/azurerm.servicebus) zřizují a spravují entit služby Service Bus (obory názvů, fronty, témata a odběry) pomocí místní konzoly Azure PowerShell nebo skriptu.

Můžete také spravovat entit služby Service Bus pomocí šablony Azure Resource Manager. Další informace najdete v článku [vytvořit Service Bus prostředků pomocí šablony Azure Resource Manager](service-bus-resource-manager-overview.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat následující požadavky:

* Předplatné Azure. Další informace o získání předplatného najdete v tématu [možností nákupu][purchase options], [nabízí člen][member offers], nebo [volné účet][free account].
* Počítač s prostředím Azure PowerShell. Pokyny najdete v tématu [Začínáme pomocí rutin prostředí Azure PowerShell](/powershell/azure/get-started-azureps).
* Obecné znalosti skriptů prostředí PowerShell, balíčků NuGet a rozhraní .NET Framework.

## <a name="get-started"></a>Začínáme

Prvním krokem je pomocí prostředí PowerShell pro přihlášení k účtu Azure a předplatné Azure. Postupujte podle pokynů v [Začínáme pomocí rutin prostředí Azure PowerShell](/powershell/azure/get-started-azureps) se přihlaste k účtu Azure a načtení a přístup k prostředkům ve vašem předplatném Azure.

## <a name="provision-a-service-bus-namespace"></a>Zřízení oboru názvů Service Bus

Při práci s obory názvů Service Bus, můžete použít [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace), [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace), [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace), a [Set-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespace) rutiny.

Tento příklad vytvoří pár lokální proměnné ve skriptu; `$Namespace` a `$Location`.

* `$Namespace` je název oboru názvů Service Bus, se kterým chcete pracovat.
* `$Location` Určuje datové centrum, ve kterém jsme zřízení oboru názvů.
* `$CurrentNamespace` ukládá odkaz na obor názvů, který jsme načíst (nebo ji vytvořte).

Ve skriptu skutečné `$Namespace` a `$Location` lze předat jako parametry.

Tato část skript provede následující akce:

1. Pokusí se načíst oboru názvů Service Bus se zadaným názvem.
2. Pokud je nalezen obor názvů, sestavy, co byl nalezen.
3. Pokud není nalezen obor názvů, vytvoří obor názvů a pak načte nově vytvořený obor názvů.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Vytvoření oboru názvů autorizační pravidla

Následující příklad ukazuje, jak spravovat pravidla autorizace oboru názvů pomocí [New-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespaceauthorizationrule), [Get-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespaceauthorizationrule), [Set-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespaceauthorizationrule), a [rutiny Remove-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespaceauthorizationrule).

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzureRmServiceBusKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
}
```

## <a name="create-a-queue"></a>Vytvoření fronty

Pokud chcete vytvořit fronta nebo téma, proveďte kontrolu oboru názvů pomocí skriptu v předchozí části. Pak vytvořte fronty:

```powershell
# Check if queue already exists
$CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Úprava vlastností fronty.

Po provedení skript v předchozí části, můžete použít [Set-AzureRmServiceBusQueue](/powershell/module/azurerm.servicebus/set-azurermservicebusqueue) rutiny aktualizovat vlastnosti fronty, jako v následujícím příkladu:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Zřizování dalšími subjekty, Service Bus

Můžete použít [modulu Service Bus PowerShell](/powershell/module/azurerm.servicebus) ke zřízení jinými entitami, jako jsou témata a odběry. Tyto rutiny jsou syntakticky podobná rutiny vytvoření fronty ukázáno v předchozí části.

## <a name="next-steps"></a>Další postup

- Najdete kompletní dokumentaci modulu Powershellu pro Service Bus Resource Manager [zde](/powershell/module/azurerm.servicebus). Tato stránka obsahuje seznam všech dostupných rutin.
- Informace o používání šablon Azure Resource Manageru, najdete v článku [vytvořit Service Bus prostředků pomocí šablony Azure Resource Manager](service-bus-resource-manager-overview.md).
- Informace o [knihovny Service Bus .NET správy](service-bus-management-libraries.md).

Existují některé alternativní způsoby správy entit služby Service Bus, jak je popsáno v těchto příspěvcích na blogu:

* [Postup vytvoření fronty, témata a odběry pomocí skriptu prostředí PowerShell služby Service Bus](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Postup vytvoření Namespace Service Bus a centra událostí pomocí skriptu prostředí PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Skripty prostředí PowerShell služby Service Bus](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/
