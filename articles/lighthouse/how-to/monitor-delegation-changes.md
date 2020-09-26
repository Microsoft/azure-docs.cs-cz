---
title: Sledování změn delegování ve vašem tenantovi pro správu
description: Přečtěte si, jak monitorovat aktivitu delegování z klientů zákazníka do vašeho spravovaného tenanta.
ms.date: 09/08/2020
ms.topic: how-to
ms.openlocfilehash: 15e96939d4115bd93260687f637143cc798a4331
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336594"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Sledování změn delegování ve vašem tenantovi pro správu

Jako poskytovatel služeb možná budete chtít vědět, že předplatná zákazníků nebo skupiny prostředků jsou delegovaná na vašeho tenanta prostřednictvím [Azure Lighthouse](../overview.md)nebo když se odebraly dřív delegované prostředky.

V rámci správy tenanta sleduje [Protokol aktivit Azure](../../azure-monitor/platform/platform-logs-overview.md) aktivitu delegování na úrovni tenanta. Tato zaznamenaná aktivita zahrnuje všechny přidané nebo odebrané delegace ze všech zákaznických klientů.

Toto téma vysvětluje oprávnění potřebná ke sledování aktivity delegování pro vašeho tenanta (u všech vašich zákazníků) a osvědčených postupů pro jejich účely. Obsahuje také ukázkový skript, který ukazuje jednu metodu pro dotazování a vytváření sestav s těmito daty.

> [!IMPORTANT]
> Všechny tyto kroky je třeba provést v tenantovi vašeho tenanta, nikoli v jakýchkoli klientech zákazníků.

## <a name="enable-access-to-tenant-level-data"></a>Povolit přístup k datům na úrovni tenanta

Aby bylo možné získat přístup k datům protokolu aktivit na úrovni tenanta, musí být k účtu přiřazená integrovaná role Azure [monitoring Reader](../../role-based-access-control/built-in-roles.md#monitoring-reader) v kořenovém oboru (/). Toto přiřazení musí provést uživatel, který má roli globálního správce s dalšími oprávněními vyšší úrovně přístupu.

### <a name="elevate-access-for-a-global-administrator-account"></a>Zvýšení přístupu pro účet globálního správce

Pokud chcete přiřadit roli v kořenovém oboru (/), budete muset mít roli globálního správce se zvýšeným přístupem. Tento přístup se zvýšenými oprávněními by se měl přidat jenom v případě, že je potřeba provést přiřazení role a pak ji po dokončení odebrat.

Podrobné pokyny k přidání a odebrání zvýšení oprávnění najdete v tématu [zvýšení přístupu ke správě všech předplatných Azure a skupin pro správu](../../role-based-access-control/elevate-access-global-admin.md).

Po zvýšení úrovně přístupu bude mít váš účet roli správce přístupu uživatele v Azure v kořenovém oboru. Toto přiřazení role vám umožní zobrazit všechny prostředky a přiřadit přístup v rámci předplatného nebo skupiny pro správu v adresáři a taky dělat přiřazení rolí v kořenovém oboru.

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>Vytvořit nový účet instančního objektu pro přístup k datům na úrovni tenanta

Po zvýšení úrovně přístupu můžete účtu přiřadit příslušná oprávnění, aby se mohl dotazovat na data protokolu aktivit na úrovni tenanta. Tento účet bude muset mít integrovanou roli Azure [monitoring Reader](../../role-based-access-control/built-in-roles.md#monitoring-reader) , která je přiřazená v kořenovém oboru vašeho tenanta správy.

> [!IMPORTANT]
> Udělení přiřazení role v kořenovém oboru znamená, že stejná oprávnění budou platit pro všechny prostředky v tenantovi.

Vzhledem k tomu, že se jedná o širokou úroveň přístupu, doporučujeme přiřadit tuto roli k hlavnímu účtu služby a nikoli jednotlivým uživatelům nebo skupinám.

 Kromě toho doporučujeme následující osvědčené postupy:

- [Vytvoří nový účet instančního objektu](../../active-directory/develop/howto-create-service-principal-portal.md) , který se bude používat jenom pro tuto funkci, a ne přiřazení této role k existujícímu instančnímu objektu, který se používá pro jiné služby Automation.
- Ujistěte se, že tento instanční objekt nemá přístup k žádným delegovaným zákaznickým prostředkům.
- [Použijte certifikát k ověření](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) a [bezpečnému jeho uložení v Azure Key Vault](../../key-vault/general/best-practices.md).
- Omezte uživatele, kteří mají přístup ke službě Act jménem instančního objektu.

> [!NOTE]
> Můžete také přiřadit integrovanou roli Azure Monitoring Reader v kořenovém oboru pro jednotlivé uživatele nebo skupiny uživatelů. To může být užitečné, pokud chcete, aby uživatel mohl [Zobrazit informace o delegování přímo v Azure Portal](#view-delegation-changes-in-the-azure-portal). Pokud to uděláte, uvědomte si, že se jedná o širokou úroveň přístupu, která by se měla omezit na nejmenší možný počet uživatelů.

Pomocí jedné z následujících metod proveďte přiřazení kořenového oboru.

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

Až vytvoříte účet instančního objektu a přiřadíte roli Čtenář monitorování v kořenovém oboru, nezapomeňte [Odebrat oprávnění vyšší](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) úrovně přístupu pro účet globálního správce, protože tato úroveň přístupu už nebude potřeba.

## <a name="query-the-activity-log"></a>Dotazování protokolu aktivit

Po vytvoření nového účtu instančního objektu s přístupem ke čtečce monitorování ke kořenovému oboru spravovaného tenanta ho můžete použít k dotazování a hlášení aktivity delegování ve vašem tenantovi.

[Tento skript Azure PowerShell](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) lze použít k dotazování na poslední 1 den aktivity a sestavy na všech přidaných nebo odebraných delegováních (nebo pokusůch, které nebyly úspěšné). Dotazuje data [protokolu aktivit tenanta](/rest/api/monitor/TenantActivityLogs/List) a pak vytvoří následující hodnoty, které se budou hlásit na přidaných nebo odebraných delegováních:

- **DelegatedResourceId**: ID delegovaného předplatného nebo skupiny prostředků
- **CustomerTenantId**: ID tenanta zákazníka
- **CustomerSubscriptionId**: ID předplatného, které bylo delegované nebo které obsahuje delegovanou skupinu prostředků.
- **CustomerDelegationStatus**: Změna stavu delegovaného prostředku (úspěšné nebo neúspěšné)
- **EventTimeStamp**: datum a čas, kdy byla změna delegování protokolována

Při dotazování na tato data Pamatujte:

- Pokud je v jednom nasazení delegovaných víc skupin prostředků, pro každou skupinu prostředků se vrátí samostatné položky.
- Změny provedené v předchozím delegování (například aktualizace struktury oprávnění) budou protokolovány jako přidané delegování.
- Jak je uvedeno výše, účet musí mít integrovanou roli Azure Monitoring Reader v kořenovém oboru (/), aby bylo možné získat přístup k těmto datům na úrovni tenanta.
- Tato data můžete použít ve svých vlastních pracovních postupech a vytváření sestav. Můžete například použít [rozhraní API kolekce dat http (Public Preview)](../../azure-monitor/platform/data-collector-api.md) k protokolování dat do Azure monitor z klienta REST API a pak pomocí [skupin akcí](../../azure-monitor/platform/action-groups.md) vytvořit oznámení nebo výstrahy.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the last 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri     = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization  = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method  = 'GET'
}
$list = Invoke-RestMethod @listOperations

# First link can be empty - and point to a next link (or potentially multiple pages)
# While you get more data - continue fetching and add result
while($list.nextLink){
    $list2 = Invoke-RestMethod $list.nextLink -Headers $listOperations.Headers -Method Get
    $data+=$list2.value;
    $list.nextLink = $list2.nextlink;
}

$showOperations = $data;

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action") {
    $registerOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action" }
    foreach ($registerOutput in $registerOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $registerOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has registered delegated resources to your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId; 
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp           = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") {
    $unregisterOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action" }
    foreach ($unregisterOutput in $unregisterOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $unregisterOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has unregistered delegated resources from your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId;
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp           = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else {
    Write-Output "No new delegation events for tenant: $($currentContext.Tenant.TenantId)"
}
```

> [!TIP]
> I když v tomto tématu odkazujeme na poskytovatele služeb a zákazníky, můžou podniky, které [spravují víc tenantů](../concepts/enterprise.md) , používat stejné procesy.

## <a name="view-delegation-changes-in-the-azure-portal"></a>Zobrazit změny delegování v Azure Portal

Uživatelé, kterým byla přiřazena integrovaná role Azure Monitoring Reader v kořenovém oboru, mohou zobrazit změny delegování přímo v Azure Portal.

1. Přejděte na stránku **moji zákazníci** a potom v navigační nabídce vlevo vyberte **Protokol aktivit** .
1. Ujistěte se, že je ve filtru v horní části obrazovky vybraná **aktivita adresáře** .

Zobrazí se seznam změn delegování. Můžete vybrat **Upravit sloupce** a zobrazit nebo skrýt **stav**, **kategorii události**, **čas**, **časové razítko**, **předplatné**, **událost iniciované**, **skupinu prostředků**, **typ prostředku**a hodnoty **prostředků** .

## <a name="next-steps"></a>Další kroky

- Naučte se začlenit zákazníky do [Azure Lighthouse](../concepts/azure-delegated-resource-management.md).
- Přečtěte si o [Azure monitor](../../azure-monitor/index.yml) a [protokolu aktivit Azure](../../azure-monitor/platform/platform-logs-overview.md).
