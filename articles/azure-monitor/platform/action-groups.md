---
title: Vytváření a Správa skupin akcí v Azure Portal
description: Naučte se vytvářet a spravovat skupiny akcí v Azure Portal.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 8/19/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 6b3d1ff76d4f7611da8e08dd4ce42293c805978e
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423851"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Vytváření a Správa skupin akcí v Azure Portal
Skupina akcí je kolekce předvoleb oznámení definovaných vlastníkem předplatného Azure. Výstrahy Azure Monitor a Service Health pomocí skupin akcí upozorní uživatele na aktivaci výstrahy. Různé výstrahy můžou v závislosti na požadavcích uživatele používat stejnou skupinu akcí nebo různé skupiny akcí. V rámci předplatného můžete nakonfigurovat až 2 000 skupin akcí.

Nakonfigurujete akci, která upozorní uživatele e-mailem nebo SMS, obdrží potvrzení oznamující, že byly přidány do skupiny akcí.

V tomto článku se dozvíte, jak vytvořit a spravovat skupiny akcí v Azure Portal.

Každá akce se skládá z následujících vlastností:

* **Název**: jedinečný identifikátor v rámci skupiny akcí.  
* **Typ akce**: akce provedena. Mezi příklady patří odesílání hlasových hovorů, SMS, e-mailu; nebo spouštějí různé typy automatizovaných akcí. Další informace najdete v části typy dále v tomto článku.
* **Podrobnosti**: odpovídající podrobnosti, které se liší podle *typu akce*.

Informace o použití šablon Azure Resource Manager ke konfiguraci skupin akcí naleznete v tématu [Group action správce prostředků Templates](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Vytvoření skupiny akcí pomocí Azure Portal

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **monitor**. Podokno **monitorování** slučuje všechna nastavení monitorování a data v jednom zobrazení.

1. Vyberte **Upozornění** a pak vyberte **Spravovat akce**.

    ![Tlačítko pro správu akcí](./media/action-groups/manage-action-groups.png)
    
1. Vyberte **Přidat skupinu akcí**a vyplňte pole.

    ![Příkaz Přidat skupinu akcí](./media/action-groups/add-action-group.png)
    
1. Do pole **název skupiny akcí** zadejte název a zadejte název do pole **krátký název** . Krátký název se použije místo úplného názvu skupiny akcí při odesílání oznámení pomocí této skupiny.

      ![Dialogové okno Přidat skupinu akcí](./media/action-groups/action-group-define.png)

1. Pole **předplatné** se vyplní vaším aktuálním předplatným. Toto předplatné je ten, ve kterém je skupina akcí uložená.

1. Vyberte **skupinu prostředků** , ve které se skupina akcí uloží.

1. Definujte seznam akcí. Pro každou akci zadejte následující:

    1. **Název**: Zadejte jedinečný identifikátor pro tuto akci.

    1. **Typ akce**: vyberte E-mail/SMS/Push/Voice, Logic Apps, Webhook, ITSM nebo Automation Runbook.

    1. **Podrobnosti**: na základě typu akce zadejte telefonní číslo, e-mailovou adresu, identifikátor URI Webhooku, aplikaci Azure, připojení ITSM nebo Runbook Automation. Pro akci ITSM zadejte také **pracovní položku** a další pole, které nástroj ITSM vyžaduje.
    
    1. **Běžné schéma výstrah**: můžete si vybrat, že se má povolit [společné schéma výstrah](https://aka.ms/commonAlertSchemaDocs), které poskytuje jedinou rozšiřitelnou a Sjednocenou datovou část pro všechny služby výstrah v Azure monitor.

1. Vyberte **OK** a vytvořte skupinu akcí.

## <a name="manage-your-action-groups"></a>Správa skupin akcí

Po vytvoření skupiny akcí je tato skupina zobrazená v části **skupiny akcí** v podokně **monitorování** . Vyberte skupinu akcí, pro kterou chcete spravovat:

* Přidat, upravit nebo odebrat akce.
* Odstraňte skupinu akcí.

## <a name="action-specific-information"></a>Informace specifické pro akci

> [!NOTE]
> V tématu [omezení služby předplatného pro monitorování](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-monitor-limits) pro číselná omezení u každé z níže uvedených položek.  

### <a name="automation-runbook"></a>Runbook služby Automation
Omezení pro datové části sady Runbook najdete v části [omezení služby předplatného Azure](../../azure-subscription-service-limits.md) .

Ve skupině akcí můžete mít omezený počet akcí sady Runbook. 

### <a name="azure-app-push-notifications"></a>Nabízená oznámení aplikace Azure
Můžete mít omezený počet akcí aplikace Azure ve skupině akcí.

### <a name="email"></a>Email
E-maily budou odeslány z následujících e-mailových adres. Ujistěte se, že je správně nakonfigurováno filtrování e-mailů.
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Ve skupině akcí můžete mít omezený počet e-mailových akcí. Podívejte se na článek [o omezení rychlosti](./../../azure-monitor/platform/alerts-rate-limiting.md) .

### <a name="email-azure-resource-manager-role"></a>Role e-mailové Azure Resource Manager
Odešlete e-mail členům role předplatného.

Ve skupině akcí můžete mít omezený počet e-mailových akcí. Podívejte se na článek [o omezení rychlosti](./../../azure-monitor/platform/alerts-rate-limiting.md) .

### <a name="function"></a>Funkce
Klíče funkcí pro aplikace Function App nakonfigurované jako akce jsou čteny prostřednictvím rozhraní API Functions, které aktuálně vyžaduje aplikace funkce v2 ke konfiguraci nastavení aplikace "AzureWebJobsSecretStorageType" na "soubory". Další informace najdete v tématu [změny správy klíčů ve Functions v2]( https://aka.ms/funcsecrets).

Ve skupině akcí můžete mít omezený počet akcí funkce.

### <a name="itsm"></a>ITSM
Akce ITSM vyžaduje připojení ITSM. Naučte se vytvořit [připojení ITSM](../../azure-monitor/platform/itsmc-overview.md).

Ve skupině akcí můžete mít omezený počet ITSM akcí. 

### <a name="logic-app"></a>Aplikace logiky
Ve skupině akcí můžete mít omezený počet akcí aplikace logiky.

### <a name="secure-webhook"></a>Zabezpečený Webhook
**Funkce zabezpečeného Webhooku je teď ve verzi Preview.**

Akce Webhooku skupin akcí vám umožní využít výhod Azure Active Directory k zabezpečení připojení mezi skupinou akcí a vaším chráněným webovým rozhraním API (koncový bod Webhooku). Celkový pracovní postup pro využití této funkce je popsaný níže. Přehled aplikací a instančních objektů služby Azure AD najdete v tématu [Přehled Microsoft Identity Platform (v 2.0)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Vytvořte aplikaci Azure AD pro vaše chráněné webové rozhraní API. Viz https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Nakonfigurujte chráněné rozhraní API, které bude volat aplikace typu démon.
    
1. Povolením použití skupin akcí v aplikaci Azure AD.

    > [!NOTE]
    > Abyste mohli tento skript spustit, musíte být členem [role Správce aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) .
    
    - Pokud chcete použít ID tenanta Azure AD, upravte volání Connect-AzureAD skriptu PowerShellu.
    - Úprava proměnné $myAzureADApplicationObjectId skriptu PowerShellu pro použití ID objektu vaší aplikace Azure AD
    - Spusťte upravený skript.
    
1. Nakonfigurujte akci zabezpečeného Webhooku skupiny akcí.
    - Zkopírujte hodnotu $myApp. ObjectId ze skriptu a zadejte ji do pole ID objektu aplikace v definici akce Webhooku.
    
    ![Akce zabezpečení Webhooku](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Zabezpečený skript prostředí PowerShell Webhooku

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>SMS
Další důležité informace najdete v tématu [omezení přenosové rychlosti](./../../azure-monitor/platform/alerts-rate-limiting.md) a [chování výstrah serveru SMS](../../azure-monitor/platform/alerts-sms-behavior.md) .

Ve skupině akcí můžete mít omezený počet akcí serveru SMS.  

### <a name="voice"></a>Hlas
Podívejte se na článek [o omezení rychlosti](./../../azure-monitor/platform/alerts-rate-limiting.md) .

Ve skupině akcí můžete mít omezený počet hlasových akcí.

### <a name="webhook"></a>Webhook
Webhooky se zopakují pomocí následujících pravidel. Volání Webhooku se v případě vrácení následujících stavových kódů HTTP znovu pokusí o maximum 2 časy: 408, 429, 503, 504 nebo koncový bod HTTP nereaguje. První opakování se provede po 10 sekundách. Druhý pokus proběhne po 100 sekundách. Po dvou selháních nebude žádná skupina akcí volat koncový bod na 30 minut. 

Zdrojové rozsahy IP adres
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 13.106.57.197
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Chcete-li dostávat aktualizace o změnách těchto IP adres, doporučujeme nakonfigurovat Service Health výstrahu, která sleduje informační oznámení o službě skupin akcí.

Ve skupině akcí můžete mít omezený počet akcí Webhooku.



## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [chování výstrah SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Získejte [informace o schématu Webhooku upozornění protokolu aktivit](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Další informace o [konektoru ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Přečtěte si další informace o [omezování četnosti](../../azure-monitor/platform/alerts-rate-limiting.md) výstrah.
* Získejte [Přehled výstrah protokolu aktivit](../../azure-monitor/platform/alerts-overview.md)a Naučte se přijímat výstrahy.  
* Naučte se [konfigurovat výstrahy pokaždé, když se publikuje oznámení o stavu služby](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
