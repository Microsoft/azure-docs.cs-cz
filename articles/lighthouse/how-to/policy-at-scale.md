---
title: Nasazení zásad Azure na delegovaná předplatná ve velkém měřítku
description: Zjistěte, jak správa delegovaných prostředků Azure umožňuje nasadit definici zásad a přiřazení zásad napříč více klienty.
ms.date: 11/8/2019
ms.topic: conceptual
ms.openlocfilehash: 9e061995b728e2864d1bd33a32d530634ab794d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456838"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Nasazení zásad Azure na delegovaná předplatná ve velkém měřítku

Jako poskytovatel služeb můžete mít na palubě více klienty zákazníků pro správu delegovaných prostředků Azure. [Azure Lighthouse](../overview.md) umožňuje poskytovatelům služeb provádět operace ve velkém měřítku napříč několika tenanty najednou, což zefektivňuje úlohy správy.

Toto téma ukazuje, jak pomocí [zásad Azure](../../governance/policy/index.yml) nasadit definici zásad a přiřazení zásad napříč více klienty pomocí příkazů PowerShellu. V tomto příkladu definice zásad zajišťuje, že účty úložiště jsou zabezpečeny povolením pouze přenosy HTTPS.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Použití Azure Resource Graph k dotazování napříč klienty zákazníků

[Azure Resource Graph](../../governance/resource-graph/index.yml) můžete použít k dotazování napříč všemi předplatnými v klientech zákazníků, které spravujete. V tomto příkladu identifikujeme všechny účty úložiště v těchto předplatných, které aktuálně nevyžadují provoz HTTPS.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Nasazení zásad y pro více klientských klientů zákazníků

Následující příklad ukazuje, jak použít [šablonu Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/policy-enforce-https-storage/enforceHttpsStorage.json) k nasazení definice zásad a přiřazení zásad napříč delegovanými předplatnými ve více klientech zákazníků. Tato definice zásad vyžaduje, aby všechny účty úložiště používaly přenosy HTTPS, což brání vytváření nových účtů úložiště, které nesplňují požadavky, a označení existujících účtů úložiště bez nastavení jako nekompatibilní.

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>Ověření nasazení zásad

Po nasazení šablony Azure Resource Manageru můžete potvrdit, že definice zásad byla úspěšně použita pokusem o vytvoření účtu úložiště s **EnableHttpsTrafficPouze** nastavena na **false** v jednom z vašich delegovaných předplatných. Z důvodu přiřazení zásad byste neměli být schopni vytvořit tento účet úložiště.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení odeberte definici zásad a přiřazení vytvořené nasazením.

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>Další kroky

- Další informace o [zásadách Azure](../../governance/policy/index.yml).
- Další informace o [prostředích správy mezi tenanty](../concepts/cross-tenant-management-experience.md).
