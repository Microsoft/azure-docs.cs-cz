---
title: Použití PowerShellu ke správě prostředků Azure Service Bus | Dokumenty společnosti Microsoft
description: Tento článek vysvětluje, jak pomocí modulu Azure PowerShell vytvořit a spravovat entity Service Bus (obory názvů, fronty, témata, předplatná).
services: service-bus-messaging
documentationcenter: .NET
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: e333dfb109840538fd5dec8110e1c32adedce989
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759257"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Správa prostředků service bus pomocí prostředí PowerShell

Microsoft Azure PowerShell je skriptovací prostředí, které můžete použít k řízení a automatizaci nasazení a správy služeb Azure. Tento článek popisuje, jak pomocí [modulu PowerShell Service Bus Resource Manager](/powershell/module/az.servicebus) zřídit a spravovat entity Service Bus (obory názvů, fronty, témata a odběry) pomocí místní konzoly nebo skriptu Azure PowerShellu.

Můžete také spravovat entity Service Bus pomocí šablon Azure Resource Manager. Další informace najdete v článku [Vytvoření prostředků service bus pomocí šablon Azure Resource Manager](service-bus-resource-manager-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat následující požadavky:

* Předplatné Azure. Další informace o získání předplatného naleznete v [tématu možnosti nákupu][purchase options], [nabídky členů][member offers]nebo [bezplatný účet][free account].
* Počítač s Azure PowerShellem. Pokyny najdete [v tématu Začínáme s rutinami Prostředí Azure PowerShell](/powershell/azure/get-started-azureps).
* Obecné pochopení skriptů prostředí PowerShell, balíčků NuGet a rozhraní .NET Framework.

## <a name="get-started"></a>Začínáme

Prvním krokem je použití PowerShellu k přihlášení k účtu Azure a předplatnému Azure. Podle pokynů v [části Začínáme s rutinami Azure PowerShellu,](/powershell/azure/get-started-azureps) přihlaste se ke svému účtu Azure a načtěte a získejte přístup k prostředkům ve vašem předplatném Azure.

## <a name="provision-a-service-bus-namespace"></a>Zřízení oboru názvů service bus

Při práci s obory názvů service bus můžete použít rutiny [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace), [New-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace), [Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace)a [Set-AzServiceBusNamespace.](/powershell/module/az.servicebus/set-azservicebusnamespace)

Tento příklad vytvoří několik místních proměnných ve skriptu; `$Namespace` a `$Location`.

* `$Namespace`je název oboru názvů Service Bus, se kterým chceme pracovat.
* `$Location`identifikuje datové centrum, ve kterém zřídíme obor názvů.
* `$CurrentNamespace`ukládá referenční obor názvů, který načteme (nebo vytvoříme).

Ve skutečném `$Namespace` skriptu a `$Location` mohou být předány jako parametry.

Tato část skriptu provádí následující:

1. Pokusí se načíst obor názvů service bus se zadaným názvem.
2. Pokud je nalezen obor názvů, hlásí, co bylo nalezeno.
3. Pokud obor názvů nebyl nalezen, vytvoří obor názvů a potom načte nově vytvořený obor názvů.
   
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

### <a name="create-a-namespace-authorization-rule"></a>Vytvoření pravidla autorizace oboru názvů

Následující příklad ukazuje, jak spravovat pravidla autorizace oboru názvů pomocí rutin [New-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule), [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule), [Set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule)a [Remove-AzServiceBusAuthorizationRule.](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule)

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

Chcete-li vytvořit frontu nebo téma, proveďte kontrolu oboru názvů pomocí skriptu v předchozí části. Potom vytvořte frontu:

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

### <a name="modify-queue-properties"></a>Úprava vlastností fronty

Po spuštění skriptu v předchozí části můžete pomocí rutiny [Set-AzServiceBusQueue](/powershell/module/az.servicebus/set-azservicebusqueue) aktualizovat vlastnosti fronty, jako v následujícím příkladu:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Zřizování dalších entit service bus

[Modul PowerShell service bus](/powershell/module/az.servicebus) můžete použít ke zřízení jiných entit, jako jsou témata a odběry. Tyto rutiny jsou syntakticky podobné rutinám vytváření front, které byly demonstrovány v předchozí části.

## <a name="next-steps"></a>Další kroky

- Kompletní dokumentaci modulu powershellu Service Bus Resource Manager [naleznete zde](/powershell/module/az.servicebus). Na této stránce jsou uvedeny všechny dostupné rutiny.
- Informace o používání šablon Azure Resource Manageru najdete v článku [Vytvoření prostředků služby Service Bus pomocí šablon Azure Resource Manager](service-bus-resource-manager-overview.md).
- Informace o [knihovnách správy service bus .NET](service-bus-management-libraries.md).

Existují některé alternativní způsoby správy entit Service Bus, jak je popsáno v těchto příspěvcích blogu:

* [Jak vytvořit fronty, témata a odběry služby Service Bus pomocí skriptu PowerShellu](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Jak vytvořit obor názvů service bus a centrum událostí pomocí skriptu Prostředí PowerShell](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Skripty prostředí PowerShell service bus](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: https://azure.microsoft.com/pricing/purchase-options/
[member offers]: https://azure.microsoft.com/pricing/member-offers/
[free account]: https://azure.microsoft.com/pricing/free-trial/
