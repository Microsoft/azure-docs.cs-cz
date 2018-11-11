---
title: Použití Powershellu ke správě prostředků Azure Service Bus | Dokumentace Microsoftu
description: Modul prostředí PowerShell slouží k vytváření a správě prostředků služby Service Bus
services: service-bus-messaging
documentationcenter: .NET
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: spelluru
ms.openlocfilehash: 61869787304d8acaff00e13b52e557b878a795a4
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250369"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Použití Powershellu ke správě prostředků služby Service Bus

Prostředí Azure PowerShell je skriptovací prostředí, můžete použít k řízení a automatizaci nasazení a správy služeb Azure. Tento článek popisuje způsob použití [modulu Powershellu pro správce prostředků služby Service Bus](/powershell/module/azurerm.servicebus) ke zřízení a správě entit služby Service Bus (obory názvů, fronty, témata a odběry) pomocí skriptu nebo místní konzoly Azure Powershellu.

Můžete také spravovat entity služby Service Bus pomocí šablon Azure Resource Manageru. Další informace najdete v článku [vytvořit prostředky služby Service Bus pomocí šablon Azure Resource Manageru](service-bus-resource-manager-overview.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat následující:

* Předplatné Azure. Další informace o získání předplatného najdete v tématu [možnosti nákupu][purchase options], [nabídky pro členy][member offers], nebo [zdarma účet][free account].
* Počítače s prostředím Azure PowerShell. Pokyny najdete v tématu [Začínáme s rutinami Azure Powershellu](/powershell/azure/get-started-azureps).
* Obecné principy skripty prostředí PowerShell, balíčky NuGet a rozhraní .NET Framework.

## <a name="get-started"></a>Začínáme

Prvním krokem je použití Powershellu k přihlášení k účtu Azure a předplatné Azure. Postupujte podle pokynů v [Začínáme s rutinami Azure Powershellu](/powershell/azure/get-started-azureps) pro přihlášení ke svému účtu Azure a načíst a přístup k prostředkům ve vašem předplatném Azure.

## <a name="provision-a-service-bus-namespace"></a>Zřizování oboru názvů služby Service Bus

Při práci s obory názvů služby Service Bus, můžete použít [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace), [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace), [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace), a [Set-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespace) rutiny.

Tento příklad vytvoří několik lokální proměnné ve skriptu; `$Namespace` a `$Location`.

* `$Namespace` je název oboru názvů služby Service Bus, se kterým chcete pracovat.
* `$Location` Určuje datové centrum, ve kterém jsme zřízení oboru názvů.
* `$CurrentNamespace` uchovává odkaz na obor názvů, který jsme načítat (nebo vytvořit).

Ve skriptu skutečné `$Namespace` a `$Location` mohou být předány jako parametry.

Tato část souboru, který provede následující akce:

1. Pokusí se načíst oboru názvů služby Service Bus se zadaným názvem.
2. Pokud je nalezena oboru názvů, sestavy, co byl nalezen.
3. Pokud obor názvů není nalezen, vytvoří obor názvů a potom načte nově vytvořeného oboru názvů.
   
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

### <a name="create-a-namespace-authorization-rule"></a>Vytvořit autorizační pravidlo oboru názvů

Následující příklad ukazuje, jak spravovat autorizační pravidla oboru názvů pomocí [New-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespaceauthorizationrule), [Get-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespaceauthorizationrule), [Set-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespaceauthorizationrule), a [rutiny Remove-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespaceauthorizationrule).

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

Vytvoření fronty nebo tématu, proveďte kontrolu oboru názvů pomocí skriptu v předchozí části. Vytvořte frontu:

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

Po spuštění skriptu v předchozí části, můžete použít [Set-AzureRmServiceBusQueue](/powershell/module/azurerm.servicebus/set-azurermservicebusqueue) rutiny k aktualizaci vlastností fronty, jako v následujícím příkladu:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Zřizování jinými entitami služby Service Bus

Můžete použít [modul Powershellu pro Service Bus](/powershell/module/azurerm.servicebus) ke zřízení jinými entitami, jako jsou témata a odběry. Tyto rutiny jsou syntakticky podobně jako rutiny vytvoření fronty jsme vám ukázali v předchozí části.

## <a name="next-steps"></a>Další postup

- Zobrazit kompletní dokumentaci modulu Powershellu pro Service Bus Resource Manager [tady](/powershell/module/azurerm.servicebus). Tato stránka obsahuje seznam všech dostupných rutin.
- Informace o použití šablon Azure Resource Manageru, najdete v článku [vytvořit prostředky služby Service Bus pomocí šablon Azure Resource Manageru](service-bus-resource-manager-overview.md).
- Informace o [knihovny správy .NET služby Service Bus](service-bus-management-libraries.md).

Existují některé alternativní způsoby, jak spravovat entity služby Service Bus, jak je popsáno v těchto příspěvcích blogu:

* [Vytvoření fronty, témata a odběry pomocí skriptu prostředí PowerShell služby Service Bus](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Jak vytvořit Namespace služby Service Bus a centra událostí pomocí skriptu prostředí PowerShell](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Skripty prostředí PowerShell služby Service Bus](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/
