---
title: Postup rozšíření výstrahy z protokolu Analytcs do Azure | Microsoft Docs
description: Tento článek popisuje nástroje a rozhraní API, pomocí kterého můžete rozšířit výstrahy z analýzy protokolů Azure výstrah.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2018
ms.author: vinagara
ms.openlocfilehash: 0dce6e6772b4efea90df2e095ac0041641d99061
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763523"
---
# <a name="how-to-extend-alerts-from-log-analytics-into-azure-alerts"></a>Postup rozšíření výstrahy z analýzy protokolů do Azure výstrahy
Výstrahy v analýzy protokolů pro nahrazení pomocí Azure výstrah a jako součást tento přechod, výstrahy, které jste nakonfigurovali v analýzy protokolů bude rozšířeno do Azure.  Pokud nechcete počkat na jejich automaticky přesunout do Azure, můžete zahájit proces následující jednu z možností:

1. Ručně z portálu OMS 
2. Programově pomocí rozhraní API AlertsVersion  

> [!NOTE]
> Společnost Microsoft automaticky rozšíří výstrahy vytvořené v analýzy protokolů Azure výstrah, spouštění na **14 2018 může** v rámci fázového přístupu, dokud nebude dokončena. Z tohoto dne Microsoft bude zahájeno při plánování migrace výstrahy na Azure a během tento přechod výstrahy můžete spravovat z portálu OMS a portálu Azure. Tento proces je nedestruktivní a není interruptive.  

## <a name="option-1---initiate-from-the-oms-portal"></a>Možnost 1 - zahájení na portálu OMS
Následující kroky popisují, jak rozšířit výstrahy pro pracovní prostor na portálu OMS.  

1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
2. V podokně analýzy protokolů předplatných, vyberte pracovní prostor a pak vyberte **portálu OMS** dlaždici.<br><br> ![Tlačítko Prohledávání protokolů](./media/monitor-alerts-extend/azure-portal-01.png)<br><br> 
3. Až budete přesměrováni na portálu OMS, klikněte na dlaždici nastavení na pravé straně horní části stránky.<br><br> ![Portál OMS možnosti nastavení](./media/monitor-alerts-extend/oms-portal-settings-option.png)<br><br> 
4. Z **nastavení** vyberte **výstrahy**.  
5. Klikněte na tlačítko **rozšířit do Azure**.<br><br> ![Stránka portálu nastavení výstrah OMS s možností rozšíření](./media/monitor-alerts-extend/ExtendInto.png)
6. Průvodce se zobrazí v podokně s první tři kroky, které umožní získat přehled o procesu.  Klikněte na tlačítko **Další** pokračovat.<br><br> ![Rozšířit výstrahy z analýzy protokolů Azure – krok 1](./media/monitor-alerts-extend/ExtendStep1.png)  
7. V druhém kroku, se zobrazí souhrn navrhované změny, výpis odpovídající [skupiny akcí](monitoring-action-groups.md) pro výstrahy. Pokud jsou podobné akce vidět napříč více než jednu výstrahu, službu navrhne přidružit všechny z nich skupinu jedné akce.  Akce skupiny navrhované, postupujte podle zásad vytváření názvů: *WorkspaceName_AG_ #Number*. Chcete-li pokračovat, klikněte na tlačítko **Další**.<br><br> ![Rozšiřte výstrahy z analýzy protokolů Azure – krok 2](./media/monitor-alerts-extend/ExtendStep2.png)  
8. V posledním kroku průvodce, klikněte na **Dokončit** a potvrďte po zobrazení výzvy k zahájení procesu.  Volitelně můžete zadat e-mailové adresy tak, aby se oznámení po dokončení procesu a všechny výstrahy byly úspěšně přesunuty do Azure výstrahy.<br><br> ![Rozšířit výstrahy z analýzy protokolů Azure – krok 3](./media/monitor-alerts-extend/ExtendStep3.png)

Po dokončení průvodce si všimnete na **nastavení výstrah** stránky, která možnost rozšířit výstrahy do Azure se odebere.  Na pozadí vaše výstrahy přesunou do Azure a to může chvíli trvat.  Během operace nebude možné provádět změny výstrahy z portálu OMS.  Z hlavičky v horní části portálu se zobrazí aktuální stav, a pokud jste e-mailovou adresu zadali dříve po úspěšném dokončení procesu se dostanete e-mailu.  


Výstrahy dál uvedené na portálu OMS i poté, co jsou úspěšně přesunuta do Azure.<br><br> ![Po přesunutí výstrahy v analýzy protokolů Azure](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2---using-the-alertsversion-api"></a>Možnost 2 – pomocí AlertsVersion rozhraní API
Log Analytics AlertsVersion API můžete rozšířit výstrahy z analýzy protokolů do Azure výstrahy z libovolného klienta, který můžete volat rozhraní REST API. Máte přístup z prostředí PowerShell pomocí [ARMClient](https://github.com/projectkudu/ARMClient), nástroje příkazového řádku open source, který zjednodušuje volání rozhraní API služby Azure Resource Manager. Použití ARMClient a prostředí PowerShell je jedním z mnoha možností pro přístup k rozhraní API.  Pomocí rozhraní API bude výstup výsledků ve formátu JSON.  

Chcete-li použít rozhraní API, nejdřív vytvořit požadavek GET, které vyhodnotí a vrátí souhrn navrhované změny před dalším pokusem o ve skutečnosti rozšířit do Azure pomocí požadavek POST. Výsledky seznam výstrah a navrhované seznam [skupiny akcí](monitoring-action-groups.md) ve formátu JSON.  Podobně jako akce jsou vidět napříč více než jednu výstrahu, službu navrhne přidružení všechny z nich se skupinou jedné akce.  Akce skupiny navrhované postupujte podle zásad vytváření názvů: *WorkspaceName_AG_ #Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Pokud požadavek GET je úspěšné, vrátí se společně s seznam výstrah a navrhovanou akci skupiny v datech JSON stavový kód HTTP 200. Toto je odpověď příklad:

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
Pokud zadaný prostoru nemá definovaný pravidla výstrah, včetně stavu HTTP 200 OK kódu pro operaci GET JSON data vrátí:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Pokud již byla všechna pravidla výstrahy v pracovním prostoru zadaný rozšířili k Azure – je odpověď na požadavek GET:

```json
{
    "version": 2
}
```

K zahájení migrace výstrahy na Azure, inicializujte odpověď POST. Odpověď POST potvrzuje vašeho záměr a také přijetí tak, aby měl výstrahy rozšířené z analýzy protokolů Auzre výstrah.  Je naplánováno aktivity a zpracování výstrah, které je uvedené podle výsledků, pokud jste provedli dříve odpovědi GET.  Volitelně můžete zadat seznam e-mailové adresy, na které analýzy protokolů bude Poštovní sestavu po úspěšném dokončení procesu naplánované pozadí migrace výstrahy.  To se provádí pomocí v následujícím příkladu žádost:

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Výsledek migrace výstrahy do Azure výstrahy může lišit v závislosti na souhrn poskytované GET odpovědi.  Jakmile naplánováno, bude výstrahy v Log Analytics není dočasně k dispozici pro úpravy nebo modifikace na portálu OMS.  Však mohou být vytvořeny nové výstrahy. 

Pokud je požadavek POST úspěšné, vrátí stavem HTTP 200 OK společně s následující odpověď:

```json
{
    "version": 2
}
```

Tato odpovědi vyplývá, že výstrahy se úspěšně rozšířily do Azure výstrahy. Vlastnost verze je určena pouze pro ověřování, zda výstrahy se rozšířily do Azure a mít žádný vztah k [Log Analytics vyhledávání API](../log-analytics/log-analytics-api-alerts.md). Jakmile Azure jsou rozšířené výstrahy úspěšně, všechny e-mailových adres, zadaný v příspěvku jsou požadavek odeslán sestavu s podrobnostmi provést změny.  Pokud jsou všechny výstrahy v pracovním prostoru zadaný již naplánována rozšířit, je odpověď na žádost POST 403 stav kód význam, které bylo zakázáno pokus. K zobrazení všech chybová zpráva nebo pochopit, že pokud proces se zasekne, můžete odeslat požadavek GET a chybovou zprávu, pokud existuje, bude vrácen společně s souhrnné informace.

```json
{
    "version": 1,
    "message": "OMS was unable to extend your alerts into Azure, Error: The subscription is not registered to use the namespace 'microsoft.insights'. OMS will schedule extending your alerts, once remediation steps illustrated in the troubleshooting guide are done.",
    "recipients": [
       "john.doe@email.com",
       "jane.doe@email.com"
     ],
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}              

```


## <a name="option-3---using-custom-powershell-script"></a>Možnost 3 – pomocí vlastního skriptu prostředí PowerShell
 Po 14 může 2018 – Pokud Microsoft nebylo rozšířeno úspěšně upozornění z portálu OMS do Azure; potom dokud **5 července 2018** -uživatele můžete ručně provést stejný prostřednictvím [možnost 1 - prostřednictvím grafického uživatelského rozhraní](#option-1---initiate-from-the-oms-portal) nebo [možnost 2 – prostřednictvím rozhraní API](#option-2---using-the-alertsversion-api).

Po **5 července 2018** – všechny výstrahy z portálu OMS bude rozšířeno do Azure. Uživatelé, kteří nebyla trvat [navrhované kroky nezbytné nápravy](#troubleshooting), bude mít výstrahy spustíte bez ohlásí akce nebo oznámení z důvodu nedostatku přidružené [akce skupiny](monitoring-action-groups.md). 

Ruční vytvoření [skupiny akcí](monitoring-action-groups.md) pro výstrahy v analýzy protokolů, uživatelé mohou používat následující ukázka skriptu.
```PowerShell
########## Input Parameters Begin ###########


$subscriptionId = ""
$resourceGroup = ""
$workspaceName = "" 


########## Input Parameters End ###########

armclient login

try
{
    $workspace = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/"$workspaceName"?api-version=2015-03-20 | ConvertFrom-Json
    $workspaceId = $workspace.properties.customerId
    $resourceLocation = $workspace.location
}
catch
{
    "Please enter valid input parameters i.e. Subscription Id, Resource Group and Workspace Name !!"
    exit
}

# Get Extend Summary of the Alerts
"`nGetting Extend Summary of Alerts for the workspace...`n"
try
{

    $value = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview

    "Extend preview summary"
    "=========================`n"

    $value

    $result = $value | ConvertFrom-Json
}
catch
{

    $ErrorMessage = $_.Exception.Message
    "Error occured while fetching/parsing Extend summary: $ErrorMessage"
    exit 
}

if ($result.version -eq 2)
{
    "`nThe alerts in this workspace have already been extended to Azure."
    exit
}

$in = Read-Host -Prompt "`nDo you want to continue extending the alerts to Azure? (Y/N)"

if ($in.ToLower() -ne "y")
{
    exit
} 


# Check for resource provider registration
try
{
    $val = armclient get subscriptions/$subscriptionId/providers/microsoft.insights/?api-version=2017-05-10 | ConvertFrom-Json
    if ($val.registrationState -eq "NotRegistered")
    {
        $val = armclient post subscriptions/$subscriptionId/providers/microsoft.insights/register/?api-version=2017-05-10
    }
}
catch
{
    "`nThe user does not have required access to register the resource provider. Please try with user having Contributor/Owner role in the subscription"
    exit
}

$actionGroupsMap = @{}
try
{
    "`nCreating new action groups for alerts extension...`n"
    foreach ($actionGroup in $result.migrationSummary.actionGroups)
    {
        $actionGroupName = $actionGroup.actionGroupName
        $actions = $actionGroup.actions
        if ($actionGroupsMap.ContainsKey($actionGroupName))
        {
            continue
        } 
        
        # Create action group payload
        $shortName = $actionGroupName.Substring($actionGroupName.LastIndexOf("AG_"))
        $properties = @{"groupShortName"= $shortName; "enabled" = $true}
        $emailReceivers = New-Object Object[] $actions.emailIds.Count
        $webhookReceivers = New-Object Object[] $actions.webhookActions.Count
        
        $count = 0
        foreach ($email in $actions.emailIds)
        {
            $emailReceivers[$count] = @{"name" = "Email$($count+1)"; "emailAddress" = "$email"}
            $count++
        }

        $count = 0
        foreach ($webhook in $actions.webhookActions)
        {
            $webhookReceivers[$count] = @{"name" = "$($webhook.name)"; "serviceUri" = "$($webhook.serviceUri)"}
            $count++
        }

        $itsmAction = $actions.itsmAction
        if ($itsmAction.connectionId -ne $null)
        {
            $val = @{
            "name" = "ITSM"
            "workspaceId" = "$subscriptionId|$workspaceId"
            "connectionId" = "$($itsmAction.connectionId)"
            "ticketConfiguration" = $itsmAction.templateInfo
            "region" = "$resourceLocation"
            }
            $properties["itsmReceivers"] = @($val)  
        }

        $properties["emailReceivers"] = @($emailReceivers)
        $properties["webhookReceivers"] = @($webhookReceivers)
        $armPayload = @{"properties" = $properties; "location" = "Global"} | ConvertTo-Json -Compress -Depth 4

    
        # ARM call to create action group
        $response = $armPayload | armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroupName/?api-version=2017-04-01

        "Created Action Group with name $actionGroupName" 
        $actionGroupsMap[$actionGroupName] = $actionGroup.actionGroupResourceId.ToLower()
        $index++
    }

    "`nSuccessfully created all action groups!!"
}
catch
{
    $ErrorMessage = $_.Exception.Message

    #Delete all action groups in case of failure
    "`nDeleting newly created action groups if any as some error happened..."
    
    foreach ($actionGroup in $actionGroupsMap.Keys)
    {
        $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
    }

    "`nError: $ErrorMessage"
    "`nExiting..."
    exit
}

# Update all alerts configuration to the new version
"`nExtending OMS alerts to Azure...`n"

try
{
    $index = 1
    foreach ($alert in $result.migrationSummary.alerts)
    {
        $uri = $alert.alertId + "?api-version=2015-03-20"
        $config = armclient get $uri | ConvertFrom-Json
        $aznsNotification = @{
            "GroupIds" = @($actionGroupsMap[$alert.actionGroupName])
        }
        if ($alert.customWebhookPayload)
        {
            $aznsNotification.Add("CustomWebhookPayload", $alert.customWebhookPayload)
        }
        if ($alert.customEmailSubject)
        {
            $aznsNotification.Add("CustomEmailSubject", $alert.customEmailSubject)
        }      

        # Update alert version
        $config.properties.Version = 2

        $config.properties | Add-Member -MemberType NoteProperty -Name "AzNsNotification" -Value $aznsNotification
        $payload = $config | ConvertTo-Json -Depth 4
        $response = $payload | armclient put $uri
    
        "Extended alert with name $($alert.alertName)"
        $index++
    }
}
catch
{
    $ErrorMessage = $_.Exception.Message   
    if ($index -eq 1)
    {
        "`nDeleting all newly created action groups as no alerts got extended..."
        foreach ($actionGroup in $actionGroupsMap.Keys)
        {
            $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
        }
        "`nDeleted all action groups."  
    }
    
    "`nError: $ErrorMessage"
    "`nPlease resolve the issue and try extending again!!"
    "`nExiting..."
    exit
}

"`nSuccessfully extended all OMS alerts to Azure!!" 

# Update version of workspace to indicate extension
"`nUpdating alert version information in OMS workspace..." 

$response = armclient post "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview&iversion=2"

"`nExtension complete!!"
```


**Pomocí vlastního skriptu prostředí PowerShell** 
- Instalace je nezbytné [ARMclient](https://github.com/projectkudu/ARMClient), nástroje příkazového řádku open source, který zjednodušuje volání rozhraní API služby Azure Resource Manager
- Uživatel, který spouští skript uvedené musí mít roli Přispěvatel nebo vlastníka v rámci předplatného Azure
- Tady jsou parametry, které je třeba zadat pro skript:
    - $subscriptionId: ID předplatného Azure přidružené pracovní prostor OMS/LA
    - $resourceGroup: Skupina prostředků Azure kde je pracovní prostor OMS/LA
    - $workspaceName: název pracovního prostoru OMS/LA

**Výstup skriptu prostředí PowerShell, vlastní** skript je podrobné a bude výstup kroky, jak se provede. 
- Zobrazí souhrn, který obsahuje informace o existující OMS/LA výstrahy v pracovním prostoru a skupiny Azure akcí pro akce spojené s nimi. 
- Uživatel se vyzve k pokračujte s příponou nebo ukončit po zobrazení souhrnu.
- Pokud se uživatel vyzve k pokračujte s příponou, vytvoří se nové skupiny Azure akce a všechny existující výstrahy bude spojený s nimi. 
- V části end ukončí skript tím, že zobrazuje zpráva "rozšíření dokončení!." V případě jakékoli zprostředkující selhání se zobrazí následující chyby.

## <a name="troubleshooting"></a>Řešení potíží 
Během procesu rozšíří výstrahy od OMS do Azure, může být občasné problém, který zabrání vytvoření nezbytných systému [skupiny akcí](monitoring-action-groups.md). V takovém případě se zobrazí na portálu OMS prostřednictvím banner v části výstrah a volání GET provádí API chybovou zprávu.

> [!WARNING]
> Pokud uživatel neberou v postupu nápravy precribed uvedeného níže, před **5 července 2018** - pak výstrahy se spustí v Azure, ale bez aktivuje všechny akce nebo oznámení. Dostávat oznámení pro výstrahy, uživatelé musí ručně upravit a přidat [skupiny akcí](monitoring-action-groups.md) nebo použít [vlastní skript prostředí PowerShell](#option-3---using-custom-powershell-script) výše uvedeného.

Postup nápravy pro jednotlivé chyby v následujícím seznamu jsou:
1. **Chyba: Obor zámku nachází na úrovni předplatného nebo prostředků skupiny pro operace zápisu**: ![stránky portálu nastavení výstrah OMS s ScopeLock chybová zpráva](./media/monitor-alerts-extend/ErrorScopeLock.png)

    a. Pokud obor uzamknout je povoleno, omezení žádné nové změny v předplatné nebo skupinu prostředků obsahující pracovní prostor analýzy protokolů (OMS); v systému se nepodařilo rozšířit výstrahy (kopie) do Azure a vytvoření skupin potřebné akce.
    
    b. Chcete-li vyřešit, odstraňte *jen pro čtení* zámku na vaše předplatné nebo prostředek skupiny obsahující pracovním prostoru; pomocí portálu Azure, Powershell, rozhraní příkazového řádku Azure nebo rozhraní API. Chcete-li další informace najdete podívejte se na článek [využití prostředků zámku](../azure-resource-manager/resource-group-lock-resources.md). 
    
    c. Po vyřešení podle pokynů v článku kroky OMS rozšíří upozornění do Azure v rámci naplánované spuštění následujícího dne; bez nutnosti jakéhokoli akce nebo spuštění.

2. **Chyba: Zásady nachází na úrovni předplatného nebo prostředků skupiny**: ![stránky portálu nastavení výstrah OMS s zásad chybová zpráva](./media/monitor-alerts-extend/ErrorPolicy.png)

    a. Když [zásad Azure](../azure-policy/azure-policy-introduction.md) se použije, omezení nové prostředky v předplatné nebo skupinu prostředků obsahující pracovní prostor analýzy protokolů (OMS); systému se nepodařilo rozšířit výstrahy (kopie) do Azure a vytvoření skupin potřebné akce.
    
    b. Vyřešíte upravit zásady způsobuje *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* chybu, která brání vytváření nových prostředků na vaše předplatné nebo prostředek skupiny obsahující pracovním prostoru. Pomocí portálu Azure, Powershell, rozhraní příkazového řádku Azure nebo rozhraní API; můžete auditovat akce k vyhledání příslušné zásady způsobující selhání. Chcete-li další informace najdete podívejte se na článek [prohlížení protokolů aktivity akce](../azure-resource-manager/resource-group-audit.md). 
    
    c. Po vyřešení podle pokynů v článku kroky OMS rozšíří upozornění do Azure v rámci naplánované spuštění následujícího dne; bez nutnosti jakéhokoli akce nebo spuštění.


## <a name="next-steps"></a>Další postup

* Další informace o nové [prostředí Azure výstrahy](monitoring-overview-unified-alerts.md).
* Další informace o [protokolu výstrahy ve výstrahách Azure](monitor-alerts-unified-log.md).
