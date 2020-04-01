---
title: Sledování změn delegování ve vašem správě klienta
description: Přečtěte si, jak sledovat aktivitu delegování od klientů zákazníků až po správu klienta.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 82c41c392210e088c85af510b9698e0140f660e5
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421909"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Sledování změn delegování ve vašem správě klienta

Jako poskytovatel služeb můžete chtít vědět, kdy jsou odběry zákazníků nebo skupiny prostředků delegovány vašemu tenantovi prostřednictvím [správy delegovaných prostředků Azure](../concepts/azure-delegated-resource-management.md)nebo když jsou odebrány dříve delegované prostředky.

Ve správě tenanta [protokolu aktivit Azure](../../azure-monitor/platform/platform-logs-overview.md) sleduje aktivity delegování na úrovni klienta. Tato protokolovaná aktivita zahrnuje všechny přidané nebo odebrané delegace od všech klientů zákazníků.

Toto téma vysvětluje oprávnění potřebná ke sledování delegování aktivity do vašeho tenanta (ve všech vašich zákazníků) a osvědčené postupy pro to. Obsahuje také ukázkový skript, který zobrazuje jednu metodu pro dotazování a vytváření sestav na tato data.

> [!IMPORTANT]
> Všechny tyto kroky musí být provedeny ve vašem správě tenanta, nikoli v klientech zákazníka.

## <a name="enable-access-to-tenant-level-data"></a>Povolení přístupu k datům na úrovni klienta

Chcete-li získat přístup k datům protokolu aktivit na úrovni klienta, musí být účtu přiřazena předdefinovaná role [čtečky monitorování](../../role-based-access-control/built-in-roles.md#monitoring-reader) v kořenovém oboru (/). Toto přiřazení musí provést uživatel, který má roli globálního správce s dalším zvýšeným přístupem.

### <a name="elevate-access-for-a-global-administrator-account"></a>Zvýšení přístupu pro účet globálního správce

Chcete-li přiřadit roli v kořenovém oboru (/), budete muset mít roli globálního správce se zvýšeným přístupem. Tento zvýšený přístup by měl být přidán pouze v případě, že potřebujete přiřazení role a po dokončení je odebrán.

Podrobné pokyny k přidání a odebrání zvýšení oprávnění najdete v [tématu Zvýšení přístupu ke správě všech předplatných azure a skupin pro správu](../../role-based-access-control/elevate-access-global-admin.md).

Po zvýšení přístupu bude mít váš účet roli správce přístupu uživatelů v Azure v kořenovém oboru. Toto přiřazení role umožňuje zobrazit všechny prostředky a přiřadit přístup v libovolném předplatném nebo skupině pro správu v adresáři a také provádět přiřazení rolí v kořenovém oboru. 

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>Vytvoření nového účtu instančního objektu pro přístup k datům na úrovni klienta

Po zvýšení oprávnění můžete účtu přiřadit příslušná oprávnění, aby mohl dotazovat na data protokolu aktivit na úrovni klienta. Tento účet bude muset mít integrovanou roli [monitorovací čtečky](../../role-based-access-control/built-in-roles.md#monitoring-reader) přiřazenou v kořenovém oboru vašeho klienta pro správu.

> [!IMPORTANT]
> Udělení přiřazení role v kořenovém oboru znamená, že stejná oprávnění se budou vztahovat na všechny prostředky v tenantovi.

Vzhledem k tomu, že se jedná o širokou úroveň přístupu, doporučujeme přiřadit tuto roli účtu instančního objektu, nikoli jednotlivému uživateli nebo skupině. Dále doporučujeme následující doporučené postupy:

- [Vytvořte nový účet instančního objektu,](../../active-directory/develop/howto-create-service-principal-portal.md) který se použije pouze pro tuto funkci, místo toho, abyste tuto roli přiřadili existujícímu instančnímu objektu používanému pro jinou automatizaci.
- Ujistěte se, že tento instanční objekt nemá přístup k žádné delegované prostředky zákazníka.
- [Pomocí certifikátu jej můžete bezpečně ověřit](../../active-directory/develop/howto-create-service-principal-portal.md#certificates-and-secrets) a bezpečně uložit v [trezoru klíčů Azure](../../key-vault/key-vault-best-practices.md).
- Omezte uživatele, kteří mají přístup k jednání jménem instančního objektu.

K provedení přiřazení kořenového oboru použijte jednu z následujících metod.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ApplicationId $servicePrincipal.ApplicationId 
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Odebrání zvýšeného přístupu pro účet globálního správce

Po vytvoření účtu instančního objektu a přiřazení role Čtečka monitorování v kořenovém oboru nezapomeňte [odebrat zvýšený přístup](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) pro účet globálního správce, protože tato úroveň přístupu již nebude potřeba.

## <a name="query-the-activity-log"></a>Dotaz na protokol aktivit

Jakmile vytvoříte nový účet instančního objektu s přístupem čtečky monitorování ke kořenovému oboru vašeho klienta pro správu, můžete ho použít k dotazování a vykazování aktivity delegování ve vašem tenantovi. 

[Tento skript Azure PowerShell](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/monitor-delegation-changes) umíte dotaz na poslední 1 den aktivity a sestavy na všechny přidané nebo odebrané delegování (nebo pokusy, které nebyly úspěšné). Dotazuje se dat [protokolu aktivit klienta](https://docs.microsoft.com/rest/api/monitor/TenantActivityLogs/List) a pak vytvoří následující hodnoty pro hlášení o delegacích, které jsou přidány nebo odebrány:

- **DelegatedResourceId**: ID delegovaného předplatného nebo skupiny prostředků
- **CustomerTenantId:** ID klienta zákazníka
- **CustomerSubscriptionId**: ID předplatného, který byl delegován nebo který obsahuje skupinu prostředků, která byla delegována
- **CustomerDelegationStatus**: Změna stavu delegovaného prostředku (úspěšné nebo neúspěšné)
- **EventTimeStamp**: Datum a čas, kdy byla zaznamenána změna delegování

Při dotazování na tato data mějte na paměti:

- Pokud je v jednom nasazení delegováno více skupin prostředků, budou pro každou skupinu prostředků vráceny samostatné položky.
- Změny provedené v předchozím delegování (například aktualizace struktury oprávnění) budou zaznamenány jako přidané delegování.
- Jak je uvedeno výše, účet musí mít integrovanou roli monitorovací čtečky v kořenovém oboru (/), aby bylo možné získat přístup k těmto datům na úrovni klienta.
- Tato data můžete použít ve vlastních pracovních postupech a sestavách. Například můžete použít [rozhraní API shromažďování dat HTTP (public preview)](../../azure-monitor/platform/data-collector-api.md) k protokolování dat do Azure Monitor z klienta rozhraní REST API a potom pomocí [skupin akcí](../../azure-monitor/platform/action-groups.md) vytvářet oznámení nebo výstrahy.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the past 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method = 'GET'
}
$list = Invoke-RestMethod @listOperations
$showOperations = $list.value

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action")
{
    $registerOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action"}
    foreach ($registerOutput in $registerOutputs)
    {
    $registerOutputdata = [pscustomobject]@{
        Event = "An Azure customer has delegated resources to your tenant";
        DelegatedResourceId = $registerOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $registerOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $registerOutput.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") 
{
    $unregisterOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action"}
    foreach ($unregisterOutput in $unregisterOutputs)
    {
    $unregisterOutputdata = [pscustomobject]@{
        Event = "An Azure customer has removed delegated resources from your tenant";
        DelegatedResourceId = $unregisterOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $unregisterOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $unregisterOutput.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else 
{
    Write-Output "No new delegation changes."
}   


```

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak zavést zákazníky do [azure delegované správy prostředků](../concepts/azure-delegated-resource-management.md).
- Další informace o [Azure Monitor](../../azure-monitor/index.yml) a protokolu [aktivit Azure](../../azure-monitor/platform/platform-logs-overview.md).
