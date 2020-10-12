---
title: Nasazení Azure Policy k delegovaným předplatným ve velkém měřítku
description: Přečtěte si, jak vám Azure Lighthouse umožňuje nasadit definici zásady a přiřazení zásad napříč více klienty.
ms.date: 08/12/2020
ms.topic: how-to
ms.openlocfilehash: 27d32a68c1a3806e514533efbae581aa97bc6d0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88167279"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Nasazení Azure Policy k delegovaným předplatným ve velkém měřítku

Jako poskytovatel služeb můžete mít k [Azure Lighthouse](../overview.md)k dispozici několik klientů pro zákazníky. Azure Lighthouse umožňuje poskytovatelům služeb provádět operace ve velkém měřítku napříč několika klienty najednou, což usnadňuje úlohy správy.

V tomto tématu se dozvíte, jak použít [Azure Policy](../../governance/policy/index.yml) k nasazení definice zásady a přiřazení zásad napříč více klienty pomocí příkazů PowerShellu. V tomto příkladu definice zásad zajišťuje zabezpečení účtů úložiště tím, že povoluje jenom přenosy HTTPS.

> [!TIP]
> I když v tomto tématu odkazujeme na poskytovatele služeb a zákazníky, můžou podniky, které [spravují víc tenantů](../concepts/enterprise.md) , používat stejné procesy.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Použití Azure Resource graphu k dotazování napříč klienty zákazníka

Pomocí [Azure Resource graphu](../../governance/resource-graph/index.yml) se můžete dotazovat napříč všemi předplatnými v klientech zákazníků, které spravujete. V tomto příkladu zjistíme všechny účty úložiště v těchto předplatných, které aktuálně nevyžadují provoz HTTPS.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Nasazení zásady napříč několika klienty zákazníka

Následující příklad ukazuje, jak použít [šablonu Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json) k nasazení definice zásady a přiřazení zásad v rámci delegovaných předplatných ve více zákaznických klientech. Tato definice zásady vyžaduje, aby všechny účty úložiště používaly přenosy protokolu HTTPS, což brání vytvoření jakýchkoli nových účtů úložiště, které nedodržují předpisy, a označení stávajících účtů úložiště bez nastavení jako nevyhovující.

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzSubscriptionDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>Ověření nasazení zásad

Po nasazení Azure Resource Manager šablony si můžete ověřit, že se definice zásady úspěšně použila při pokusu o vytvoření účtu úložiště s **EnableHttpsTrafficOnly** nastaveným na **hodnotu false** v jednom z vašich delegovaných předplatných. Vzhledem k přiřazení zásad by neměl být možné vytvořit tento účet úložiště.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete hotovi, odeberte definici zásady a přiřazení vytvořená nasazením.

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzSubscriptionDeployment -Name mgmt -AsJob

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

- Přečtěte si o [Azure Policy](../../governance/policy/index.yml).
- Přečtěte si o [prostředích pro správu mezi klienty](../concepts/cross-tenant-management-experience.md).
