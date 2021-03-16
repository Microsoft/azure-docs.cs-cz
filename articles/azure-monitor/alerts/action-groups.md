---
title: Vytváření a správa skupin akcí na webu Azure Portal
description: Naučte se vytvářet a spravovat skupiny akcí v Azure Portal.
author: dkamstra
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: dukek
ms.openlocfilehash: 0771249e94d3e00cbeaff00406a0dbf33777a14d
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490326"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Vytváření a správa skupin akcí na webu Azure Portal
Skupina akcí je kolekce předvoleb oznámení definovaných vlastníkem předplatného Azure. Výstrahy Azure Monitor a Service Health pomocí skupin akcí upozorní uživatele na aktivaci výstrahy. Různé výstrahy můžou v závislosti na požadavcích uživatele používat stejnou skupinu akcí nebo různé skupiny akcí. 

V tomto článku se dozvíte, jak vytvořit a spravovat skupiny akcí v Azure Portal.

Každá akce se skládá z následujících vlastností:

* **Typ**: oznámení nebo akce provedena. Mezi příklady patří odesílání hlasových hovorů, SMS, e-mailu; nebo spouštějí různé typy automatizovaných akcí. Další informace najdete v části typy dále v tomto článku.
* **Název**: jedinečný identifikátor v rámci skupiny akcí.
* **Podrobnosti**: odpovídající podrobnosti, které se liší podle *typu*.

Informace o použití šablon Azure Resource Manager ke konfiguraci skupin akcí naleznete v tématu [Group action správce prostředků Templates](./action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Vytvoření skupiny akcí pomocí Azure Portal

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **monitor**. Podokno **monitorování** slučuje všechna nastavení monitorování a data v jednom zobrazení.

1. Vyberte **výstrahy** a pak vyberte **Spravovat akce**.

    ![Tlačítko pro správu akcí](./media/action-groups/manage-action-groups.png)
    
1. Vyberte **Přidat skupinu akcí** a vyplňte příslušná pole v prostředí průvodce.

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

    a. **Typ oznámení**: Vyberte typ oznámení, které chcete odeslat. Dostupné jsou následující možnosti:
      * Role e-mailu Azure Resource Manager – odešle e-mail uživatelům, kteří jsou přiřazeni k určitým rolím ARM na úrovni předplatného.
      * E-mail/SMS/nabízený/hlas – odešlete tyto typy oznámení konkrétním příjemcům.
    
    b. **Název**: Zadejte jedinečný název pro oznámení.

    c. **Podrobnosti**: na základě vybraného typu oznámení zadejte e-mailovou adresu, telefonní číslo atd.
    
    d. **Běžné schéma výstrah**: můžete si vybrat, že se má povolit [společné schéma výstrah](./alerts-common-schema.md), které poskytuje jedinou rozšiřitelnou a Sjednocenou datovou část pro všechny služby výstrah v Azure monitor.

    ![Karta oznámení](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>Konfigurace akcí

1. Kliknutím na tlačítko **Další: akce >** se přesuňte na kartu **Akce** nebo vyberte kartu **Akce** v horní části obrazovky.

1. Umožňuje definovat seznam akcí, které se aktivují při aktivaci výstrahy. Pro každou akci zadejte následující:

    a. **Typ akce**: vyberte Runbook automatizace, funkce Azure, ITSM, aplikaci logiky, zabezpečený Webhook, Webhook.
    
    b. **Název**: Zadejte jedinečný název pro akci.

    c. **Podrobnosti**: na základě typu akce zadejte identifikátor URI Webhooku, aplikaci Azure, připojení ITSM nebo Runbook Automation. Pro akci ITSM zadejte také **pracovní položku** a další pole, které nástroj ITSM vyžaduje.
    
    d. **Běžné schéma výstrah**: můžete si vybrat, že se má povolit [společné schéma výstrah](./alerts-common-schema.md), které poskytuje jedinou rozšiřitelnou a Sjednocenou datovou část pro všechny služby výstrah v Azure monitor.
    
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
Zapněte nabízená oznámení do [mobilní aplikace Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/) tím, že zadáte e-mailovou adresu, kterou používáte jako ID účtu při konfiguraci mobilní aplikace Azure.

Můžete mít omezený počet akcí aplikace Azure ve skupině akcí.

### <a name="email"></a>E-mail
E-maily budou odeslány z následujících e-mailových adres. Ujistěte se, že je správně nakonfigurováno filtrování e-mailů.
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Ve skupině akcí můžete mít omezený počet e-mailových akcí. Podívejte se na článek [o omezení rychlosti](./alerts-rate-limiting.md) .

### <a name="email-azure-resource-manager-role"></a>Poslat e-mail roli Azure Resource Manageru
Odešlete e-mail členům role předplatného. E-mail se pošle jenom do členů této role pro **uživatele Azure AD** . E-mail se nepošle skupinám ani instančním objektům Azure AD.

E-mail s oznámením se pošle jenom na *primární e-mailovou* adresu.

Pokud nepřijímáte oznámení v *primárním e-mailu*, můžete zkusit provést následující kroky:

1. V Azure Portal přejít na *Active Directory*.
2. Klikněte na všichni uživatelé (v levém podokně), zobrazí se seznam uživatelů (v pravém podokně).
3. Vyberte uživatele, u kterého chcete zkontrolovat *primární e-mailové* informace.

  :::image type="content" source="media/action-groups/active-directory-user-profile.png" alt-text="Příklad kontroly profilu uživatele." border="true":::

4. V profilu uživatele v části kontaktní informace pokud je karta E-mail prázdná, klikněte na tlačítko *Upravit* v horní části a v horní části přidejte svůj *primární e-mail* a tlačítko *Uložit* .

  :::image type="content" source="media/action-groups/active-directory-add-primary-email.png" alt-text="Příklad přidání primárního e-mailu" border="true":::

Ve skupině akcí můžete mít omezený počet e-mailových akcí. Podívejte se na článek [o omezení rychlosti](./alerts-rate-limiting.md) .

### <a name="function"></a>Funkce
Zavolá existující koncový bod HTTP triggeru v [Azure Functions](../../azure-functions/functions-get-started.md).

Ve skupině akcí můžete mít omezený počet akcí funkce.

### <a name="itsm"></a>ITSM
Akce ITSM vyžaduje připojení ITSM. Naučte se vytvořit [připojení ITSM](./itsmc-overview.md).

Ve skupině akcí můžete mít omezený počet ITSM akcí. 

### <a name="logic-app"></a>Aplikace logiky
Ve skupině akcí můžete mít omezený počet akcí aplikace logiky.

### <a name="secure-webhook"></a>Zabezpečený webhook

> [!NOTE]
> Použití akce Webhooku vyžaduje, aby cílový koncový bod Webhooku buď nevyžadoval úspěšné fungování výstrahy, nebo mohl analyzovat informace kontextu výstrahy, které jsou k dispozici jako součást operace POST. Pokud koncový bod Webhooku nemůže zpracovat informace kontextu výstrahy sám o sobě, můžete použít řešení jako [akci aplikace logiky](./action-groups-logic-app.md) pro vlastní manipulaci s informacemi v kontextu výstrahy, aby odpovídala očekávanému formátu dat Webhooku.
> Uživatel by měl být **vlastníkem** instančního objektu Webhooku, aby se zajistilo, že zabezpečení nebude narušeno. Protože každý zákazník Azure má přístup ke všem ID objektů prostřednictvím portálu bez kontroly jeho majitele, může kdokoli přidat zabezpečený Webhook do své vlastní skupiny akcí pro upozornění na upozornění služby Azure monitor, které porušuje zabezpečení.

Akce Webhooku skupin akcí vám umožní využít výhod Azure Active Directory k zabezpečení připojení mezi skupinou akcí a vaším chráněným webovým rozhraním API (koncový bod Webhooku). Celkový pracovní postup pro využití této funkce je popsaný níže. Přehled aplikací a instančních objektů služby Azure AD najdete v tématu [Přehled Microsoft Identity Platform (v 2.0)](../../active-directory/develop/v2-overview.md).

1. Vytvořte aplikaci Azure AD pro vaše chráněné webové rozhraní API. Viz [chráněné webové rozhraní API: registrace aplikace](../../active-directory/develop/scenario-protected-web-api-app-registration.md).
    - Nakonfigurujte chráněné rozhraní API, které bude [volat aplikace typu démon](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).
    
2. Povolením použití skupin akcí v aplikaci Azure AD.

    > [!NOTE]
    > Abyste mohli tento skript spustit, musíte být členem [role Správce aplikací Azure AD](../../active-directory/roles/permissions-reference.md#all-roles) .
    
    - Upravte volání Connect-AzureAD skriptu PowerShellu tak, aby používalo vaše ID tenanta Azure AD.
    - Upravte $myAzureADApplicationObjectId proměnné skriptu PowerShellu tak, aby používala ID objektu vaší aplikace služby Azure AD.
    - Spusťte upravený skript.
    
3. Nakonfigurujte akci zabezpečeného Webhooku skupiny akcí.
    - Zkopírujte hodnotu $myApp. ObjectId ze skriptu a zadejte ji do pole ID objektu aplikace v definici akce Webhooku.
    
    ![Akce zabezpečení Webhooku](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Zabezpečený skript prostředí PowerShell Webhooku

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object ID of your Azure AD Application>"
    
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
    
Write-Host "My Azure AD Application (ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>SMS
Další důležité informace najdete v tématu [omezení přenosové rychlosti](./alerts-rate-limiting.md) a [chování výstrah serveru SMS](./alerts-sms-behavior.md) . 

Ve skupině akcí můžete mít omezený počet akcí serveru SMS.

> [!NOTE]
> Pokud uživatelské rozhraní skupiny akcí Azure Portal neumožní vybrat kód země nebo oblasti, pak není pro vaši zemi nebo oblast podporován server SMS.  Pokud kód země nebo oblasti není k dispozici, můžete hlasovat, že jste přidali zemi nebo oblast v [uživatelském hlasu](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice). Do té doby je možné, že vaše skupina akcí volá Webhook k poskytovateli serveru SMS třetí strany s podporou ve vaší zemi nebo oblasti.  

Ceny pro podporované země nebo oblasti najdete na stránce s [cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

**Seznam zemí, ve kterých je podporováno oznámení serveru SMS**

| Kód země | Jméno země |
|:---|:---|
| 61 | Austrálie |
| 43 | Rakousko |
| 32 | Belgie |
| 55 | Brazílie |
| 1 |Kanada |
| 56 | Chile |
| 86 | Čína |
| 420 | Česká republika |
| 45 | Dánsko |
| 372 | Estonsko |
| 358 | Finsko |
| 33 | Francie |
| 49 | Německo |
| 852 | Hongkong |
| 91 | Indie |
| 353 | Irsko |
| 972 | Izrael |
| 39 | Itálie |
| 81 | Japonsko |
| 352 | Lucembursko |
| 60 | Malajsie |
| 52 | Mexiko |
| 31 | Nizozemsko |
| 64 | Nový Zéland |
| 47 | Norsko |
| 351 | Portugalsko |
| 1 | Portoriko |
| 40 | Rumunsko |
| 65 | Singapur |
| 27 | Jižní Afrika |
| 82 | Jižní Korea |
| 34 | Španělsko |
| 41 | Švýcarsko |
| 886 | Tchaj-wan |
| 44 | Spojené království |
| 1 | USA |

### <a name="voice"></a>Hlas
Další důležité chování najdete v článku [o omezení rychlosti](./alerts-rate-limiting.md) .

Ve skupině akcí můžete mít omezený počet hlasových akcí.

> [!NOTE]
> Pokud uživatelské rozhraní skupiny akcí Azure Portal neumožní vybrat kód země nebo oblasti, pak se hlasové hovory pro vaši zemi nebo oblast nepodporují. Pokud kód země nebo oblasti není k dispozici, můžete hlasovat, že jste přidali zemi nebo oblast v [uživatelském hlasu](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  Do té doby je možné, že vaše skupina akcí volá Webhook poskytovateli hlasových volání třetí strany s podporou ve vaší zemi nebo oblasti.  
> Pouze kód země podporovaný dnes v Azure Portal skupina akcí pro hlasové oznámení je + 1 (USA). 

Ceny pro podporované země nebo oblasti najdete na stránce s [cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="webhook"></a>Webhook

> [!NOTE]
> Použití akce Webhooku vyžaduje, aby cílový koncový bod Webhooku buď nevyžadoval úspěšné fungování výstrahy, nebo mohl analyzovat informace kontextu výstrahy, které jsou k dispozici jako součást operace POST. Pokud koncový bod Webhooku nemůže zpracovat informace kontextu výstrahy sám o sobě, můžete použít řešení jako [akci aplikace logiky](./action-groups-logic-app.md) pro vlastní manipulaci s informacemi v kontextu výstrahy, aby odpovídala očekávanému formátu dat Webhooku.

Webhooky se zpracovávají pomocí následujících pravidel.
- Volání Webhooku se podařilo maximálně třikrát.
- Volání se bude opakovat, pokud se odpověď neobdrží v rámci časového limitu, nebo se vrátí jeden z následujících kódů stavu HTTP: 408, 429, 503 nebo 504.
- První volání bude čekat 10 sekund na odpověď.
- Druhé a třetí pokusy budou čekat 30 sekund na odpověď.
- Po 3 pokusech o volání Webhooku se nezdařila žádná skupina akcí, která bude volat koncový bod po dobu 15 minut.

Podívejte se prosím na [IP adresy skupin akcí](../app/ip-addresses.md) pro zdrojové rozsahy IP adres.


## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [chování výstrah SMS](./alerts-sms-behavior.md).  
* Získejte [informace o schématu Webhooku upozornění protokolu aktivit](./activity-log-alerts-webhook.md).  
* Přečtěte si další informace o [konektoru ITSM](./itsmc-overview.md).
* Přečtěte si další informace o [omezování četnosti](./alerts-rate-limiting.md) výstrah.
* Získejte [Přehled výstrah protokolu aktivit](./alerts-overview.md)a Naučte se přijímat výstrahy.  
* Naučte se [konfigurovat výstrahy pokaždé, když se publikuje oznámení o stavu služby](../../service-health/alerts-activity-log-service-notifications-portal.md).
