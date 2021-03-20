---
title: Použití PowerShellu ke správě Azure Service Busch prostředků | Microsoft Docs
description: Tento článek vysvětluje, jak používat modul Azure PowerShell k vytváření a správě entit Service Bus (obory názvů, fronty, témata, odběry).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: b6439deb2b86c2ea5b50fe3bdbad89a0875b2acc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88065739"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Použití PowerShellu ke správě Service Busch prostředků

Microsoft Azure PowerShell je skriptovací prostředí, které můžete použít k řízení a automatizaci nasazení a správy služeb Azure. Tento článek popisuje, jak pomocí [modulu Service Bus správce prostředků PowerShellu](/powershell/module/az.servicebus) zřídit a spravovat Service Bus entit (obory názvů, fronty, témata a odběry) pomocí místní konzoly nebo skriptu pro Azure PowerShell.

Service Bus entit můžete také spravovat pomocí šablon Azure Resource Manager. Další informace najdete v článku [vytvoření Service Busch prostředků pomocí šablon Azure Resource Manager](service-bus-resource-manager-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady

Než začnete, budete potřebovat následující požadavky:

* Předplatné Azure. Další informace o získání předplatného najdete v tématu [možnosti nákupu][purchase options], [nabídky členů][member offers]nebo [bezplatný účet][free account].
* Počítač se Azure PowerShell. Pokyny najdete v tématu [Začínáme s rutinami Azure PowerShell](/powershell/azure/get-started-azureps).
* Obecné porozumění skriptům PowerShellu, balíčkům NuGet a .NET Framework.

## <a name="get-started"></a>Začínáme

Prvním krokem je použití PowerShellu pro přihlášení k účtu Azure a předplatnému Azure. Podle pokynů v tématu [Začínáme s rutinami Azure PowerShell](/powershell/azure/get-started-azureps) se přihlaste ke svému účtu Azure a načtěte a získejte přístup k prostředkům ve vašem předplatném Azure.

## <a name="provision-a-service-bus-namespace"></a>Zřízení Service Busového oboru názvů

Při práci s oborem názvů Service Bus můžete použít rutiny [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace), [New-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace), [Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace)a [set-AzServiceBusNamespace](/powershell/module/az.servicebus/set-azservicebusnamespace) .

Tento příklad vytvoří ve skriptu několik místních proměnných. `$Namespace` a `$Location` .

* `$Namespace` je název oboru názvů Service Bus, se kterým chceme pracovat.
* `$Location` Určuje datové centrum, ve kterém zřizujeme obor názvů.
* `$CurrentNamespace` ukládá referenční obor názvů, který načteme (nebo vytvoříte).

Ve vlastním skriptu `$Namespace` a `$Location` lze předat jako parametry.

Tato část skriptu provede následující akce:

1. Pokusí se načíst obor názvů Service Bus se zadaným názvem.
2. Pokud je obor názvů nalezen, oznamuje, co byl nalezen.
3. Pokud obor názvů nebyl nalezen, vytvoří obor názvů a následně načte nově vytvořený obor názvů.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Vytvoření autorizačního pravidla oboru názvů

Následující příklad ukazuje, jak spravovat autorizační pravidla oboru názvů pomocí rutin [New-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule), [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule), [set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule)a [Remove-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule) .

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzServiceBusKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
}
```

## <a name="create-a-queue"></a>Vytvoření fronty

Chcete-li vytvořit frontu nebo téma, proveďte kontrolu oboru názvů pomocí skriptu v předchozí části. Pak vytvořte frontu:

```powershell
# Check if queue already exists
$CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Upravit vlastnosti fronty

Po spuštění skriptu v předchozí části můžete pomocí rutiny [set-AzServiceBusQueue](/powershell/module/az.servicebus/set-azservicebusqueue) aktualizovat vlastnosti fronty, jak je znázorněno v následujícím příkladu:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Zřizování dalších entit Service Bus

K zřizování jiných entit, jako jsou témata a předplatná, můžete použít [modul Service Bus PowerShell](/powershell/module/az.servicebus) . Tyto rutiny jsou syntakticky podobné rutinám vytváření fronty, které jsou znázorněné v předchozí části.

## <a name="next-steps"></a>Další kroky

- Další [informace najdete v](/powershell/module/az.servicebus)dokumentaci k modulu PowerShellu pro Service Bus správce prostředků. Tato stránka obsahuje seznam všech dostupných rutin.
- Informace o používání šablon Azure Resource Manager najdete v článku [vytvoření prostředků Service Bus pomocí Azure Resource Managerch šablon](service-bus-resource-manager-overview.md).
- Informace o [Service Bus knihovny pro správu .NET](service-bus-management-libraries.md).

Existuje několik alternativních způsobů, jak spravovat Service Bus entit, jak je popsáno v těchto blogových příspěvcích:

* [Postup vytvoření Service Bus front, témat a odběrů pomocí skriptu PowerShellu](/archive/blogs/paolos/how-to-create-service-bus-queues-topics-and-subscriptions-using-a-powershell-script)
* [Postup vytvoření oboru názvů Service Bus a centra událostí pomocí skriptu PowerShellu](/archive/blogs/paolos/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script)
* [Service Bus skripty PowerShellu](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: https://azure.microsoft.com/pricing/purchase-options/
[member offers]: https://azure.microsoft.com/pricing/member-offers/
[free account]: https://azure.microsoft.com/pricing/free-trial/