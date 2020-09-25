---
title: Vytváření a správa skupin akcí na webu Azure Portal
description: Naučte se vytvářet a spravovat skupiny akcí v Azure Portal.
author: dkamstra
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 7937b412b1eb3f311f0212f19c4eb9fc7782459d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327727"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Vytváření a správa skupin akcí na webu Azure Portal
Skupina akcí je kolekce předvoleb oznámení definovaných vlastníkem předplatného Azure. Výstrahy Azure Monitor a Service Health pomocí skupin akcí upozorní uživatele na aktivaci výstrahy. Různé výstrahy můžou v závislosti na požadavcích uživatele používat stejnou skupinu akcí nebo různé skupiny akcí. V rámci předplatného můžete nakonfigurovat až 2 000 skupin akcí.

V tomto článku se dozvíte, jak vytvořit a spravovat skupiny akcí v Azure Portal.

Každá akce se skládá z následujících vlastností:

* **Typ**: oznámení nebo akce provedena. Mezi příklady patří odesílání hlasových hovorů, SMS, e-mailu; nebo spouštějí různé typy automatizovaných akcí. Další informace najdete v části typy dále v tomto článku.
* **Název**: jedinečný identifikátor v rámci skupiny akcí.
* **Podrobnosti**: odpovídající podrobnosti, které se liší podle *typu*.

Informace o použití šablon Azure Resource Manager ke konfiguraci skupin akcí naleznete v tématu [Group action správce prostředků Templates](./action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Vytvoření skupiny akcí pomocí Azure Portal

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **monitor**. Podokno **monitorování** slučuje všechna nastavení monitorování a data v jednom zobrazení.

1. Vyberte **výstrahy**a pak vyberte **Spravovat akce**.

    ![Tlačítko pro správu akcí](./media/action-groups/manage-action-groups.png)
    
1. Vyberte **Přidat skupinu akcí**a vyplňte příslušná pole v prostředí průvodce.

    ![Příkaz Přidat skupinu akcí](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>Konfigurovat základní nastavení skupiny akcí

V části **Podrobnosti o projektu**:

Vyberte **předplatné** a **skupinu prostředků** , ve které se skupina akcí uloží.

V části **Podrobnosti instance**:

1. Zadejte **název skupiny akcí**.

1. Zadejte **Zobrazovaný název**. Zobrazovaný název se používá místo úplného názvu skupiny akcí při odeslání oznámení pomocí této skupiny.

      ![Dialogové okno Přidat skupinu akcí](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>Konfigurace oznámení

1. Kliknutím na tlačítko **Další: oznámení >** přejděte na kartu **oznámení** nebo vyberte kartu **oznámení** v horní části obrazovky.

1. Umožňuje definovat seznam oznámení, která se mají odeslat, když se aktivuje výstraha. Pro každé oznámení zadejte následující:

    a. **Typ oznámení**: Vyberte typ oznámení, které chcete odeslat. Dostupné jsou tyto možnosti:
      * Role e-mailu Azure Resource Manager – odešle e-mail uživatelům, kteří jsou přiřazeni k určitým rolím ARM na úrovni předplatného.
      * E-mail/SMS/nabízený/hlas – odešlete tyto typy oznámení konkrétním příjemcům.
    
    b. **Název**: Zadejte jedinečný název pro oznámení.

    c. **Podrobnosti**: na základě vybraného typu oznámení zadejte e-mailovou adresu, telefonní číslo atd.
    
    d. **Běžné schéma výstrah**: můžete si vybrat, že se má povolit [společné schéma výstrah](https://aka.ms/commonAlertSchemaDocs), které poskytuje jedinou rozšiřitelnou a Sjednocenou datovou část pro všechny služby výstrah v Azure monitor.

    ![Karta oznámení](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>Konfigurace akcí

1. Kliknutím na tlačítko **Další: akce >** se přesuňte na kartu **Akce** nebo vyberte kartu **Akce** v horní části obrazovky.

1. Umožňuje definovat seznam akcí, které se aktivují při aktivaci výstrahy. Pro každou akci zadejte následující:

    a. **Typ akce**: vyberte Runbook automatizace, funkce Azure, ITSM, aplikaci logiky, zabezpečený Webhook, Webhook.
    
    b. **Název**: Zadejte jedinečný název pro akci.

    c. **Podrobnosti**: na základě typu akce zadejte identifikátor URI Webhooku, aplikaci Azure, připojení ITSM nebo Runbook Automation. Pro akci ITSM zadejte také **pracovní položku** a další pole, které nástroj ITSM vyžaduje.
    
    d. **Běžné schéma výstrah**: můžete si vybrat, že se má povolit [společné schéma výstrah](https://aka.ms/commonAlertSchemaDocs), které poskytuje jedinou rozšiřitelnou a Sjednocenou datovou část pro všechny služby výstrah v Azure monitor.
    
    ![Karta akce](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>Vytvoření skupiny akcí

1. Pokud chcete, můžete prozkoumat nastavení **Značky**. To vám umožní přidružit páry klíč/hodnota ke skupině akcí pro svou kategorizaci a je dostupná funkce pro libovolný prostředek Azure.

    ![Karta značky](./media/action-groups/action-group-4-tags.png)
    
1. Klikněte na **Zkontrolovat a vytvořit** a zkontrolujte nastavení. To provede rychlé ověření vašich vstupů, abyste měli jistotu, že jsou vybraná všechna povinná pole. Případné problémy zobrazí se zde. Po kontrole nastavení klikněte na **vytvořit** a zřiďte skupinu akcí.
    
    ![Karta Revize + vytvořit](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> Když nakonfigurujete akci, která upozorní uživatele e-mailem nebo SMS, obdrží potvrzení oznamující, že byly přidány do skupiny akcí.

## <a name="manage-your-action-groups"></a>Správa skupin akcí

Po vytvoření skupiny akcí můžete zobrazit **skupiny akcí** tak, že vyberete **Spravovat akce** na úvodní stránce **výstrahy** v podokně **monitorování** . Vyberte skupinu akcí, pro kterou chcete spravovat:

* Přidat, upravit nebo odebrat akce.
* Odstraňte skupinu akcí.

## <a name="action-specific-information"></a>Informace specifické pro akci

> [!NOTE]
> V tématu [omezení služby předplatného pro monitorování](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits) pro číselná omezení u každé z níže uvedených položek.  

### <a name="automation-runbook"></a>Runbook služby Automation
Omezení pro datové části sady Runbook najdete v části [omezení služby předplatného Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) .

Ve skupině akcí můžete mít omezený počet akcí sady Runbook. 

### <a name="azure-app-push-notifications"></a>Nabízená oznámení aplikace Azure
Můžete mít omezený počet akcí aplikace Azure ve skupině akcí.

### <a name="email"></a>E-mail
E-maily budou odeslány z následujících e-mailových adres. Ujistěte se, že je správně nakonfigurováno filtrování e-mailů.
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Ve skupině akcí můžete mít omezený počet e-mailových akcí. Podívejte se na článek [o omezení rychlosti](./alerts-rate-limiting.md) .

### <a name="email-azure-resource-manager-role"></a>Poslání e-mailu roli Azure Resource Manageru
Odešlete e-mail členům role předplatného. E-mail se pošle jenom do členů této role pro **uživatele Azure AD** . E-mail se nepošle skupinám ani instančním objektům Azure AD.

E-mail s oznámením se pošle jenom na *primární e-mailovou* adresu.

Ve skupině akcí můžete mít omezený počet e-mailových akcí. Podívejte se na článek [o omezení rychlosti](./alerts-rate-limiting.md) .

### <a name="function"></a>Funkce
Zavolá existující koncový bod HTTP triggeru v [Azure Functions](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

Ve skupině akcí můžete mít omezený počet akcí funkce.

### <a name="itsm"></a>ITSM
Akce ITSM vyžaduje připojení ITSM. Naučte se vytvořit [připojení ITSM](./itsmc-overview.md).

Ve skupině akcí můžete mít omezený počet ITSM akcí. 

### <a name="logic-app"></a>Aplikace logiky
Ve skupině akcí můžete mít omezený počet akcí aplikace logiky.

### <a name="secure-webhook"></a>Zabezpečený webhook
Akce Webhooku skupin akcí vám umožní využít výhod Azure Active Directory k zabezpečení připojení mezi skupinou akcí a vaším chráněným webovým rozhraním API (koncový bod Webhooku). Celkový pracovní postup pro využití této funkce je popsaný níže. Přehled aplikací a instančních objektů služby Azure AD najdete v tématu [Přehled Microsoft Identity Platform (v 2.0)](../../active-directory/develop/v2-overview.md).

1. Vytvořte aplikaci Azure AD pro vaše chráněné webové rozhraní API. Viz [chráněné webové rozhraní API: registrace aplikace](../../active-directory/develop/scenario-protected-web-api-app-registration.md).
    - Nakonfigurujte chráněné rozhraní API, které bude [volat aplikace typu démon](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).
    
2. Povolením použití skupin akcí v aplikaci Azure AD.

    > [!NOTE]
    > Abyste mohli tento skript spustit, musíte být členem [role Správce aplikací Azure AD](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#available-roles) .
    
    - Pokud chcete použít ID tenanta Azure AD, upravte volání Connect-AzureAD skriptu PowerShellu.
    - Upravte $myAzureADApplicationObjectId proměnné skriptu PowerShellu tak, aby používala ID objektu vaší aplikace služby Azure AD.
    - Spusťte upravený skript.
    
3. Nakonfigurujte akci zabezpečeného Webhooku skupiny akcí.
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
Další důležité informace najdete v tématu [omezení přenosové rychlosti](./alerts-rate-limiting.md) a [chování výstrah serveru SMS](./alerts-sms-behavior.md) . 

Ve skupině akcí můžete mít omezený počet akcí serveru SMS.

> [!NOTE]
> Pokud uživatelské rozhraní skupiny akcí Azure Portal neumožní vybrat kód země nebo oblasti, pak není pro vaši zemi nebo oblast podporován server SMS.  Pokud kód země nebo oblasti není k dispozici, můžete hlasovat, že jste přidali zemi nebo oblast v [uživatelském hlasu](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice). Do té doby je možné, že vaše skupina akcí volá Webhook k poskytovateli serveru SMS třetí strany s podporou ve vaší zemi nebo oblasti.  

Ceny pro podporované země nebo oblasti najdete na stránce s [cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).
  

### <a name="voice"></a>Hlas
Další důležité chování najdete v článku [o omezení rychlosti](./alerts-rate-limiting.md) .

Ve skupině akcí můžete mít omezený počet hlasových akcí.

> [!NOTE]
> Pokud uživatelské rozhraní skupiny akcí Azure Portal neumožní vybrat kód země nebo oblasti, pak se hlasové hovory pro vaši zemi nebo oblast nepodporují. Pokud kód země nebo oblasti není k dispozici, můžete hlasovat, že jste přidali zemi nebo oblast v [uživatelském hlasu](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  Do té doby je možné, že vaše skupina akcí volá Webhook poskytovateli hlasových volání třetí strany s podporou ve vaší zemi nebo oblasti.  

Ceny pro podporované země nebo oblasti najdete na stránce s [cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="webhook"></a>Webhook
Webhooky se zpracovávají pomocí následujících pravidel.
- Volání Webhooku se podařilo maximálně třikrát.
- Volání se bude opakovat, pokud se odpověď neobdrží v rámci časového limitu, nebo se vrátí jeden z následujících kódů stavu HTTP: 408, 429, 503 nebo 504.
- První volání bude čekat 10 sekund na odpověď.
- Druhé a třetí pokusy budou čekat 30 sekund na odpověď.
- Po 3 pokusech o volání Webhooku se nezdařila žádná skupina akcí, která bude volat koncový bod po dobu 15 minut.

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
* Přečtěte si další informace o [chování výstrah SMS](./alerts-sms-behavior.md).  
* Získejte [informace o schématu Webhooku upozornění protokolu aktivit](./activity-log-alerts-webhook.md).  
* Přečtěte si další informace o [konektoru ITSM](./itsmc-overview.md).
* Přečtěte si další informace o [omezování četnosti](./alerts-rate-limiting.md) výstrah.
* Získejte [Přehled výstrah protokolu aktivit](./alerts-overview.md)a Naučte se přijímat výstrahy.  
* Naučte se [konfigurovat výstrahy pokaždé, když se publikuje oznámení o stavu služby](../../service-health/alerts-activity-log-service-notifications-portal.md).

