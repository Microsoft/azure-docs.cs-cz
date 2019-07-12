---
title: Vytvoření a Správa skupin akcí na webu Azure Portal
description: Zjistěte, jak vytvářet a spravovat skupiny akcí na webu Azure Portal.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 7/08/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 842965aa49ae4cd546fe9c107107d2a2ceebebbb
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705262"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Vytvoření a Správa skupin akcí na webu Azure Portal
Skupiny akcí je kolekce předvolby oznamování určené vlastník předplatného Azure. Upozornění Azure Monitor a stavy služeb použití skupin akcí uživatelům oznámit, že výstraha byla aktivována. Různé výstrahy použít stejnou skupinu akcí nebo skupiny různých akcí v závislosti na požadavcích uživatele. V rámci předplatného můžete nakonfigurovat až 2 000 skupin akcí.

Nakonfigurujete akci, kterou chcete upozornit osoby, e-mail nebo SMS, že dostanou oznamující, že byly přidány do skupiny akcí potvrzení.

V tomto článku se dozvíte, jak vytvořit a spravovat skupiny akcí na webu Azure Portal.

Každá akce se skládá z následujících vlastností:

* **Název**: Jedinečný identifikátor v rámci skupiny akcí.  
* **Typ akce**: Provést akci. Mezi příklady patří odesílání hlasový hovor, SMS, e-mailů; nebo aktivace různé automatizované akce. Zobrazit typy dále v tomto článku.
* **Podrobnosti o**: Související podrobnosti, které se liší podle *typ akce*.

Informace o tom, jak nakonfigurovat skupiny akcí pomocí šablon Azure Resource Manageru najdete v tématu [šablon Resource Manageru skupiny akcí](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Vytvoření skupiny akcí s využitím webu Azure portal

1. V [webu Azure portal](https://portal.azure.com)vyberte **monitorování**. **Monitorování** podokně konsoliduje všechny vaše monitorování nastavení a data v jednom zobrazení.

    !["Monitor" service](./media/action-groups/home-monitor.png)
    
1. Vyberte **výstrahy** vyberte **spravovat akce**.

    ![Správa tlačítko akce](./media/action-groups/manage-action-groups.png)
    
1. Vyberte **přidat skupinu akcí**a přejít k vyplnění polí.

    ![Příkaz "Přidat skupinu akcí"](./media/action-groups/add-action-group.png)
    
1. Zadejte název do pole **název skupiny akcí** pole a zadejte název do pole **krátký název** pole. Krátký název se použije místo úplného názvu skupiny akcí při odesílání oznámení pomocí této skupiny.

      ![Dialogové okno Přidat skupinu akcí"](./media/action-groups/action-group-define.png)

1. **Předplatné** pole autofills s vaším aktuálním předplatným. Toto předplatné je ten, ve kterém se uloží skupiny akcí.

1. Vyberte **skupiny prostředků** v skupinu akcí se uloží.

1. Definujte seznam akcí. Zadejte následující úkony:

    1. **Název**: Zadejte jedinečný identifikátor pro tuto akci.

    1. **Typ akce**: Vyberte e-mailu/SMS nebo nabízená/hlasové, aplikace logiky, Webhooku, ITSM nebo Runbooku Automation.

    1. **Podrobnosti o**: Závislosti na typu akce, zadejte telefonní číslo, e-mailovou adresu, webhooku identifikátor URI, aplikace Azure, připojení ITSM nebo runbooku Automation. Akce ITSM, kromě určit **pracovní položku** a ostatní pole ITSM nástroj vyžaduje.
    
    1. **Společné schéma produktu výstrah**: Můžete povolit [společné schéma produktu výstrah](https://aka.ms/commonAlertSchemaDocs), která nabízí výhodu v podobě jediného rozšiřitelné a sjednocené výstrah datová část mezi všechny výstrahy služby ve službě Azure Monitor.

1. Vyberte **OK** k vytvoření skupiny akcí.

## <a name="manage-your-action-groups"></a>Spravovat skupiny akcí

Po vytvoření skupiny akcí je viditelná ve **skupiny akcí** část **monitorování** podokně. Vyberte skupinu akcí, které chcete spravovat:

* Přidání, úprava nebo odebrání akce.
* Odstraňte skupinu akcí.

## <a name="action-specific-information"></a>Informace o konkrétní akci

> [!NOTE]
> Zobrazit [limity předplatného služby pro monitorování](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-monitor-limits) pro číselné limity na každé z níže uvedených položek.  

### <a name="azure-app-push-notifications"></a>Nabízená oznámení aplikace Azure
Může mít omezený počet akcí Azure aplikace do skupiny akcí.

### <a name="email"></a>Email
E-maily se budou odesílat z těchto e-mailových adres. Ujistěte se, že filtrování e-mailu je správně nakonfigurována
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Může mít omezený počet e-mailových akcí do skupiny akcí. Zobrazit [tady informace](./../../azure-monitor/platform/alerts-rate-limiting.md) článku.

### <a name="itsm"></a>ITSM
Akce ITSM vyžaduje připojení ITSM. Zjistěte, jak vytvořit [připojení ITSM](../../azure-monitor/platform/itsmc-overview.md).

Do skupiny akcí může mít omezený počet akce ITSM. 

### <a name="logic-app"></a>Aplikace logiky
Může mít omezený počet akce aplikace logiky do skupiny akcí.

### <a name="function"></a>Funkce
Prostřednictvím rozhraní API funkce, které aktuálně vyžaduje v2 aplikace function App ke konfiguraci nastavení "AzureWebJobsSecretStorageType" k "files" aplikace jsou pro čtení funkční klávesy pro aplikace Function App nakonfigurovaný jako akce. Další informace najdete v tématu [změní na správu klíčů ve V2 funkce]( https://aka.ms/funcsecrets).

Může mít omezený počet akcí funkce v skupiny akcí.

### <a name="automation-runbook"></a>Runbook služby Automation
Odkazovat [limity předplatného Azure](../../azure-subscription-service-limits.md) pro omezení datových částí sady Runbook.

Do skupiny akcí může mít omezený počet sad Runbook akcí. 

### <a name="sms"></a>SMS
Zobrazit [tady informace](./../../azure-monitor/platform/alerts-rate-limiting.md) a [chování výstrah SMS](../../azure-monitor/platform/alerts-sms-behavior.md) další důležité informace.

Může mít omezený počet SMS akce v skupiny akcí.  

### <a name="voice"></a>Hlas
Zobrazit [tady informace](./../../azure-monitor/platform/alerts-rate-limiting.md) článku.

Může mít omezený počet hlasové akce v skupiny akcí.

### <a name="webhook"></a>Webhook
Webhooky jsou spuštěna znovu s použitím následujících pravidel. Volání webhooku je opakovat maximálně 2 při následující stavové kódy HTTP vrácené časy jsou časy: 408, 429, 503, 504 nebo když koncový bod HTTP neodpovídá. První opakování se provede po 10 sekundách. Druhý opakování se stane po 100 sekund. Žádné skupiny akcí po dvou selháních, zavolá koncový bod po dobu 30 minut. 

Zdrojové rozsahy IP adres
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Pokud chcete dostávat informace o změnách na tyto IP adresy, doporučujeme, abyste že konfigurace upozornění Service Health, která monitoruje Informační oznámení týkající se služby skupiny akcí.

Může mít omezený počet akce Webhooku v skupiny akcí.

#### <a name="secure-webhook"></a>Secure Webhook
**Funkce zabezpečení Webhooku je aktuálně ve verzi Preview.**

Akce Webhooku skupiny akcí můžete využít Azure Active Directory pro zabezpečené připojení mezi vaší skupiny akcí a chráněné webové rozhraní API (koncový bod webhooku). Níže je popsána celkového pracovního postupu pro využití výhod této funkce. Přehled aplikace Azure AD a instanční objekty, naleznete v tématu [Microsoft identity platform (v2.0) přehled](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Vytvořte aplikaci Azure AD pro chráněné webové rozhraní API. Viz https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Konfigurace vašeho chráněné rozhraní API volat aplikace pro proces démon.
    
1. Povolte skupiny akcí používat aplikaci Azure AD.

    > [!NOTE]
    > Musíte být členem skupiny [role správce Azure AD aplikace](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) ke spuštění tohoto skriptu.
    
    - Upravit skript Powershellu Connect-AzureAD volání použijte ID vašeho Tenanta služby Azure AD.
    - Upravit skript Powershellu proměnnou $myAzureADApplicationObjectId použít ID objektu Azure AD aplikace
    - Spusťte upravený skript.
    
1. Konfigurace akce Webhooku skupiny akcí.
    - Zkopírujte hodnotu $myApp.ObjectId ze skriptu a zadejte ho v poli ID objektu aplikace v definici akce Webhooku.
    
    ![Zabezpečené akce Webhooku](./media/action-groups/action-groups-secure-webhook.png)

##### <a name="secure-webhook-powershell-script"></a>Zabezpečení skriptů prostředí PowerShell Webhooku

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


## <a name="next-steps"></a>Další kroky
* Další informace o [chování výstrah SMS](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Získat [pochopení schéma webhooku v upozornění protokolu aktivit](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Další informace o [konektor ITSM](../../azure-monitor/platform/itsmc-overview.md)
* Další informace o [rychlosti](../../azure-monitor/platform/alerts-rate-limiting.md) na výstrahy.
* Získat [přehled upozornění protokolu aktivit](../../azure-monitor/platform/alerts-overview.md)a zjistěte, jak dostávat upozornění.  
* Zjistěte, jak [konfigurace oznámení pokaždé, když se pošle oznámení o stavu služby](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
