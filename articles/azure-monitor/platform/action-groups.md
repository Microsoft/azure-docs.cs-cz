---
title: Vytváření a správa skupin akcí na webu Azure Portal
description: Přečtěte si, jak vytvářet a spravovat skupiny akcí na webu Azure Portal.
author: dkamstra
ms.topic: conceptual
ms.date: 2/18/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 6ba48f3c40e45afa02e03a7589e968cca723118e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249513"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Vytváření a správa skupin akcí na webu Azure Portal
Skupina akcí je kolekce předvoleb oznámení definované vlastníkem předplatného Azure. Výstrahy Azure Monitor a Stav služby používají skupiny akcí k upozornění uživatelů, že se aktivovala výstraha. Různé výstrahy mohou používat stejnou skupinu akcí nebo různé skupiny akcí v závislosti na požadavcích uživatele. V předplatném můžete nakonfigurovat až 2 000 skupin akcí.

Nakonfigurujete akci, která upozorní osobu e-mailem nebo sms zprávou, obdrží potvrzení o tom, že byla přidána do skupiny akcí.

Tento článek ukazuje, jak vytvořit a spravovat skupiny akcí na webu Azure Portal.

Každá akce se skládá z následujících vlastností:

* **Název**: Jedinečný identifikátor v rámci skupiny akcí.  
* **Typ akce**: Provedená akce. Příklady zahrnují odeslání hlasového hovoru, SMS, e-mailu; nebo spouštění různých typů automatizovaných akcí. Viz typy dále v tomto článku.
* **Podrobnosti**: Odpovídající podrobnosti, které se liší podle *typu akce*.

Informace o tom, jak pomocí šablon Azure Resource Manageru nakonfigurovat skupiny akcí, najdete v [tématu Šablony Správce prostředků skupiny akcí](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Vytvoření skupiny akcí pomocí portálu Azure

1. Na [webu Azure Portal](https://portal.azure.com)vyhledejte a vyberte **sledovat**. Podokno **Monitorování** sloučí všechna nastavení monitorování a data v jednom zobrazení.

1. Vyberte **Upozornění** a pak vyberte **Spravovat akce**.

    ![Tlačítko Spravovat akce](./media/action-groups/manage-action-groups.png)
    
1. Vyberte **Přidat skupinu akcí**a vyplňte pole.

    ![Příkaz Přidat skupinu akcí](./media/action-groups/add-action-group.png)
    
1. Zadejte název do pole **Název skupiny akcí** a do pole **Krátký název** zadejte název. Krátký název se použije místo úplného názvu skupiny akcí při odesílání oznámení pomocí této skupiny.

      ![Dialogové okno Přidat skupinu akcí](./media/action-groups/action-group-define.png)

1. Pole **Odběr** se automaticky vyplní s aktuálním předplatným. Toto předplatné je ve kterém je skupina akcí uložena.

1. Vyberte **skupinu Prostředků,** do které je skupina akcí uložena.

1. Definujte seznam akcí. Pro každou akci uveďte následující:

    1. **Název**: Zadejte jedinečný identifikátor této akce.

    1. **Typ akce:** Vyberte e-mail/SMS/Push/Voice, aplikace logiky, webhook, ITSM nebo automatizační sada Runbook.

    1. **Podrobnosti:** Na základě typu akce zadejte telefonní číslo, e-mailovou adresu, identifikátor URI webhooku, aplikaci Azure, připojení ITSM nebo runbook Automation. Pro akci ITSM dále zadejte **pracovní položku** a další pole, která nástroj ITSM vyžaduje.
    
    1. **Obecné schéma výstrah**: Můžete povolit [společné schéma výstrah](https://aka.ms/commonAlertSchemaDocs), které poskytuje výhodu, že máte jednu rozšiřitelnou a jednotnou datovou část výstrahy ve všech službách výstrah v Azure Monitoru.

1. Chcete-li vytvořit skupinu akcí, vyberte **OK.**

## <a name="manage-your-action-groups"></a>Správa skupin akcí

Po vytvoření skupiny akcí můžete zobrazit **skupiny akcí** výběrem **možnosti Spravovat akce** ze vstupní stránky **Výstrahy** v podokně **Monitor.** Vyberte skupinu akcí, kterou chcete spravovat:

* Přidejte, upravte nebo odeberte akce.
* Odstraňte skupinu akcí.

## <a name="action-specific-information"></a>Informace specifické pro konkrétní akce

> [!NOTE]
> Viz [Limity předplacených služeb pro monitorování](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-monitor-limits) pro číselné limity pro každou z níže uvedených položek.  

### <a name="automation-runbook"></a>Automatizační kniha Runbook
Omezení datových částí sady Runbook najdete v [limitech předplacených služeb Azure.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

Ve skupině akcí může být omezený počet akcí runbooku. 

### <a name="azure-app-push-notifications"></a>Nabízená oznámení aplikací Azure
Ve skupině akcí můžete mít omezený počet akcí aplikací Azure.

### <a name="email"></a>E-mail
E-maily budou zasílány z následujících e-mailových adres. Ujistěte se, že filtrování e-mailů je správně nakonfigurováno
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

V akční skupině může být omezený počet e-mailových akcí. Viz informace o [omezení rychlosti.](./../../azure-monitor/platform/alerts-rate-limiting.md)

### <a name="email-azure-resource-manager-role"></a>E-mailová role Správce prostředků Azure
Pošlete e-mail členům role předplatného. E-mail se bude odesílat jenom členům role **pro uživatele Azure AD.** E-mail se nebude odesílat do skupin Azure AD nebo instančních objektů.

V akční skupině může být omezený počet e-mailových akcí. Viz informace o [omezení rychlosti.](./../../azure-monitor/platform/alerts-rate-limiting.md)

### <a name="function"></a>Funkce
Volá existující koncový bod aktivační události HTTP v [Azure Functions](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

V akční skupině může být omezený počet akcí funkce.

### <a name="itsm"></a>ITSM
Akce ITSM vyžaduje připojení ITSM. Přečtěte si, jak vytvořit [připojení ITSM](../../azure-monitor/platform/itsmc-overview.md).

V akční skupině může být omezený počet akcí ITSM. 

### <a name="logic-app"></a>Aplikace logiky
Můžete mít omezený počet akcí aplikace logiky ve skupině akcí.

### <a name="secure-webhook"></a>Zabezpečený webový hák
Akce Webhook skupiny akcí umožňuje využít výhod služby Azure Active Directory k zabezpečení připojení mezi skupinou akcí a chráněným webovým rozhraním API (koncový bod webhooku). Celkový pracovní postup pro využití této funkce je popsán níže. Přehled aplikací Azure AD a instancí najdete v tématu [Microsoft identity platformy (v2.0) přehled](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Vytvořte aplikaci Azure AD pro chráněné webové rozhraní API. Viz třída https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Nakonfigurujte chráněné rozhraní API tak, aby bylo voláno aplikací pro daemon.
    
1. Povolte skupinám akcí používat aplikaci Azure AD.

    > [!NOTE]
    > Chcete-li tento skript spustit, musíte být členem [role správce aplikací Azure AD.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles)
    
    - Upravte volání Connect-AzureAD skriptu PowerShellu tak, aby používalo id klienta Azure AD.
    - Úprava proměnné skriptu PowerShellu $myAzureADApplicationObjectId tak, aby používala ID objektu aplikace Azure AD
    - Spusťte upravený skript.
    
1. Nakonfigurujte akci Zabezpečené webové háky skupiny akcí akce Action Group.
    - Zkopírujte hodnotu $myApp.ObjectId ze skriptu a zadejte ji do pole ID aplikačního objektu v definici akce Webhook.
    
    ![Akce Zabezpečené webové háky](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Zabezpečený skript prostředí PowerShell zabezpečeného webu

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
Další důležité informace naleznete v tématu [omezení rychlosti informací](./../../azure-monitor/platform/alerts-rate-limiting.md) a [chování upozornění serveru SMS.](../../azure-monitor/platform/alerts-sms-behavior.md)

V akční skupině může být omezený počet akcí sms.  

### <a name="voice"></a>Hlas
Viz informace o [omezení rychlosti.](./../../azure-monitor/platform/alerts-rate-limiting.md)

V akční skupině je pravděpodobně omezený počet hlasových akcí.

### <a name="webhook"></a>Webhook
Webhooky jsou opakovány pomocí následujících pravidel. Volání webhooku je opakováno maximálně 2krát, když jsou vráceny následující stavové kódy HTTP: 408, 429, 503, 504 nebo koncový bod HTTP nereaguje. První opakování se provede po 10 sekundách. Druhý pokus se stane po 100 sekundách. Po dvou selháních žádná skupina akcí bude volat koncový bod po dobu 30 minut. 

Rozsahy zdrojových IP adres
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

Chcete-li dostávat aktualizace o změnách těchto ADRES IP, doporučujeme nakonfigurovat výstrahu Stavu služby, která monitoruje informační oznámení o službě Skupiny akcí.

V akční skupině může být omezený počet akcí Webhooku.



## <a name="next-steps"></a>Další kroky
* Další informace o [chování upozornění serveru SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Získejte přehled o [schématu webhooku výstrahy protokolu aktivit](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Další informace o [konektoru ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Přečtěte si další informace o [omezení rychlosti](../../azure-monitor/platform/alerts-rate-limiting.md) u výstrah.
* Získejte [přehled upozornění protokolu aktivit](../../azure-monitor/platform/alerts-overview.md)a zjistěte, jak přijímat výstrahy.  
* Přečtěte si, jak [nakonfigurovat výstrahy při každém zaúčtování oznámení o stavu služby](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
