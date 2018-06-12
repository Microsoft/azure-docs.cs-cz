---
title: Rozšířit výstrahy z protokolu Analytcs Azure
description: Tento článek popisuje nástroje a rozhraní API, pomocí kterého můžete rozšířit výstrahy z analýzy protokolů Azure výstrah.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 21ba95a7b3efff177afe63d22da3f6ba9848ded2
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301027"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>Rozšíření výstrahy z analýzy protokolů do Azure výstrahy
Funkce oznámení v Azure Log Analytics je nahrazován Azure výstrahy. Jako součást tento přechod bude rozšířeno výstrahy, které jste nakonfigurovali v analýzy protokolů do Azure. Pokud nechcete počkat na jejich automaticky přesunout do Azure, můžete zahájit proces:

- Ručně z portálu služby Operations Management Suite. 
- Programově pomocí rozhraní API AlertsVersion.  

> [!NOTE]
> Microsoft bude automaticky rozšířit výstrahy vytvořené v analýzy protokolů Azure výstrah na 14 může 2018 počínaje řadu opakovaných, dokud nebude dokončena. Plány Microsoft Azure a během tento přechod migrace výstrahy, výstrahy můžete spravovat z portálu služby Operations Management Suite a portálu Azure. Tento proces není škodlivý nebo interruptive.  

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>Možnost 1: Zahájení z portálu služby Operations Management Suite
Následující kroky popisují, jak rozšířit výstrahy pro pracovní prostor z portálu služby Operations Management Suite.  

1. Na portálu Azure vyberte **všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
2. V podokně analýzy protokolů předplatných, vyberte pracovní prostor a potom vyberte **portálu OMS** dlaždici.
![Snímek obrazovky analýzy protokolů předplatné podokně se zvýrazněnou dlaždice portálu OMS](./media/monitor-alerts-extend/azure-portal-01.png) 
3. Když budete přesměrováni na portál Operations Management Suite, vyberte **nastavení** ikonu.
![Snímek obrazovky Operations Management Suite portálu, se zvýrazněnou ikonou nastavení](./media/monitor-alerts-extend/oms-portal-settings-option.png) 
4. Z **nastavení** vyberte **výstrahy**.  
5. Vyberte **rozšířit do Azure**.
![Snímek obrazovky Operations Management Suite nastavení výstrah stránky portálu, s rozšířením do Azure zvýrazněná](./media/monitor-alerts-extend/ExtendInto.png)
6. Tří kroků průvodce se zobrazí v **výstrahy** podokně. Naleznete v přehledu a vyberte **Další**.
![Snímek obrazovky kroku 1 Průvodce](./media/monitor-alerts-extend/ExtendStep1.png)  
7. V druhém kroku, můžete zobrazit souhrn navrhované změny, výpis odpovídající [skupiny akcí](monitoring-action-groups.md) pro výstrahy. Pokud jsou podobné akce vidět napříč více než jednu výstrahu, Průvodce navrhuje pro všechny z nich přidružení skupiny jedné akce.  Zásady vytváření názvů vypadá takto: *WorkspaceName_AG_ #Number*. Chcete-li pokračovat, vyberte **Další**.
![Snímek obrazovky krok 2 Průvodce](./media/monitor-alerts-extend/ExtendStep2.png)  
8. V posledním kroku průvodce, vyberte **Dokončit**a potvrďte po zobrazení výzvy k zahájení procesu. Volitelně můžete zadat e-mailovou adresu, tak, aby se oznámení po dokončení procesu a všechny výstrahy byly úspěšně přesunuty do Azure výstrahy.
![Snímek obrazovky krok 3 Průvodce](./media/monitor-alerts-extend/ExtendStep3.png)

Pokud je na dokončení průvodce **nastavení výstrah** stránku, možností rozšíření výstrahy do Azure je odstranit. Na pozadí upozornění přesunou do Azure, a to může chvíli trvat. Při operaci nelze provádět změny pro výstrahy z portálu služby Operations Management Suite. Zobrazí aktuální stav pomocí hlavičky v horní části portálu. Pokud jste dříve zadali e-mailovou adresu, obdržíte e-mail, když se proces úspěšně dokončil.  


Výstrahy dál uvedené na portál Operations Management Suite, i poté, co jsou úspěšně přesunuta do Azure.
![Nastavení výstrah stránky portálu snímek Operations Management Suite](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>Možnost 2: Použijte AlertsVersion rozhraní API
Log Analytics AlertsVersion API můžete rozšířit výstrahy z analýzy protokolů do Azure výstrahy z libovolného klienta, který můžete volat rozhraní REST API. Můžete získat přístup k rozhraní API z prostředí PowerShell pomocí [ARMClient](https://github.com/projectkudu/ARMClient), nástroj příkazového řádku na open source. Výstup můžete výsledky ve formátu JSON.  

Chcete-li použít rozhraní API, nejdřív vytvořit požadavek GET. To vyhodnotí a vrátí souhrn navrhované změny, před dalším pokusem o ve skutečnosti rozšířit do Azure pomocí požadavek POST. Seznam výsledků upozornění a navrhované seznam [skupiny akcí](monitoring-action-groups.md), ve formátu JSON. Pokud jsou podobné akce vidět napříč více než jednu výstrahu, službu navrhuje všechny z nich přidružit skupinu jedné akce. Zásady vytváření názvů vypadá takto: *WorkspaceName_AG_ #Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Pokud požadavek GET úspěšné, je vrácen, společně s seznam výstrah a navrhované stavový kód HTTP 200 skupiny akcí v datech JSON. Toto je odpověď příklad:

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
Pokud zadaný prostoru nemá definovaný pravidla výstrah, vrátí následující JSON data:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Pokud všechna pravidla výstrahy v pracovním prostoru zadaný se rozšířily již do Azure, je odpověď na požadavek GET:

```json
{
    "version": 2
}
```

K zahájení migrace výstrahy na Azure, inicializujte odpověď POST. Odpověď POST potvrzuje vašeho záměr, jakož i přijetí, tak, aby měl výstrahy rozšířené z analýzy protokolů Azure výstrah. Je naplánované aktivity a výstrahy se zpracovávají, jak je uvedeno, na základě výsledků Pokud jste provedli dříve GET odpovědi. Volitelně můžete zadat seznam e-mailové adresy, na které analýzy protokolů odešle zprávu po úspěšném dokončení procesu naplánované pozadí migrace výstrahy. Můžete použít následující příklad žádost:

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Výsledek migrace výstrahy do Azure výstrahy může lišit v závislosti na souhrn poskytované GET odpovědi. Při plánování, výstrahy v analýzy protokolů jsou dočasně nedostupná pro úpravu na portálu služby Operations Management Suite. Můžete však vytvořit nové výstrahy. 

Pokud je požadavek POST úspěšné, vrátí stavem HTTP 200 OK společně s následující odpověď:

```json
{
    "version": 2
}
```

Tato odpovědi vyplývá, že výstrahy se úspěšně rozšířily do Azure výstrahy. Vlastnost verze je určena pouze pro ověřování, zda výstrahy se rozšířily do Azure a mít žádný vztah k [Log Analytics vyhledávání API](../log-analytics/log-analytics-api-alerts.md). Pokud výstrahy jsou rozšířené Azure a úspěšně, některé e-mailové adresy zadaný v příspěvku jsou požadavek odeslán sestavy. Pokud všechny výstrahy v pracovním prostoru zadaný jsou již naplánována potřeba rozšířit, je odpověď na žádost POST, že pokus bylo zakázáno (403 stavový kód). Pokud chcete zobrazit všechny chybová zpráva nebo pochopit, pokud je zablokované proces, můžete odeslat požadavek GET. Pokud dojde k chybě, bude vrácen, společně s souhrnné informace.

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


## <a name="option-3-use-a-custom-powershell-script"></a>Možnost 3: Použít vlastní skript prostředí PowerShell
 Pokud je Microsoft nebyl rozšířené úspěšně upozornění z portálu služby Operations Management Suite do Azure, můžete ručně provést až 5 července 2018. Dvě možnosti pro ruční rozšíření jsou popsané v předchozí dva oddíly.

Všechny výstrahy z portálu služby Operations Management Suite se po 5 července 2018 rozšířit do Azure. Uživatelé, kteří nebyla trvat [navrhované kroky nezbytné nápravy](#troubleshooting) bude související výstrahy spustíte bez ohlásí akce nebo oznámení z důvodu nedostatku [skupiny akcí](monitoring-action-groups.md). 

Chcete-li vytvořit [skupiny akcí](monitoring-action-groups.md) pro výstrahy ručně v analýzy protokolů, použijte následující ukázkový skript:
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

    
        # Azure Resource Manager call to create action group
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


### <a name="about-the-custom-powershell-script"></a>O vlastních skriptů prostředí PowerShell 
Toto je důležité informace o používání skriptu:
- Předpokladem je, instalace [ARMclient](https://github.com/projectkudu/ARMClient), nástroje příkazového řádku open source, který zjednodušuje volání rozhraní API služby Azure Resource Manager.
- Pokud chcete spustit skript, musíte mít roli Přispěvatel nebo vlastníka v rámci předplatného Azure.
- Je nutné zadat následující parametry:
    - $subscriptionId: ID předplatného Azure přidružené k pracovním prostoru Operations Management Suite Log Analytics.
    - $resourceGroup: Skupina prostředků Azure pro pracovní prostor analýzy protokolů aplikace Operations Management Suite.
    - $workspaceName: název pracovního prostoru Operations Management Suite Log Analytics.

### <a name="output-of-the-custom-powershell-script"></a>Výstup vlastního skriptu prostředí PowerShell
Skript je podrobné a výstupy kroky při jeho spuštění: 
- Zobrazí souhrn, který obsahuje informace o existující Operations Management Suite Log Analytics výstrahy v pracovním prostoru. Souhrn také obsahuje informace o skupinách Azure akce pro akce spojené s nimi. 
- Zobrazí se výzva k pokračujte s příponou, nebo ukončete po zobrazení souhrnu.
- Pokud jste pokračujte s příponou, vytvoří se nové skupiny Azure akce a všechny existující výstrahy jsou spojeny s nimi. 
- Ukončí skript tím, že zobrazuje zpráva "Rozšíření bylo dokončeno!" V případě jakékoli zprostředkující selhání skript zobrazí další chyby.

## <a name="troubleshooting"></a>Řešení potíží 
Během procesu rozšíření výstrahy, problémy můžete zabránit v systému vytváření nezbytné [skupiny akcí](monitoring-action-groups.md). V takových případech se zobrazí chybová zpráva v hlavičce v **výstrahy** části portálu Operations Management Suite, nebo v GET volání done rozhraní API.

> [!IMPORTANT]
> Pokud nemáte proveďte následující kroky nápravy před 5 července 2018 výstrahy se spustí v Azure, ale nebude fire všechny akce nebo oznámení. Dostávat oznámení pro výstrahy, musíte ručně upravit a přidat [skupiny akcí](monitoring-action-groups.md), nebo použijte předchozím [vlastní skript prostředí PowerShell](#option-3---using-custom-powershell-script).

Tady jsou kroky nápravy pro jednotlivé chyby:
- **Chyba: Obor zámku nachází na úrovni předplatného nebo prostředků skupiny pro operace zápisu**: ![snímek obrazovky Operations Management Suite nastavení výstrah stránky portálu, s chybovou zprávou oboru zámku zvýrazněná](./media/monitor-alerts-extend/ErrorScopeLock.png)

    Pokud je povoleno uzamčení oboru, omezuje funkci žádné nové změny ve skupině předplatné nebo prostředek, který obsahuje pracovní prostor analýzy protokolů (Operations Management Suite). V systému se nepodařilo rozšířit výstrahy do Azure a vytvoření skupin potřebné akce.
    
    Chcete-li vyřešit, odstraňte *jen pro čtení* zámku na vaše předplatné nebo prostředek skupiny, která obsahuje pracovním prostoru. To provedete pomocí portálu Azure, PowerShell, rozhraní příkazového řádku Azure nebo rozhraní API. Další informace najdete v tématu [využití prostředků zámku](../azure-resource-manager/resource-group-lock-resources.md). 
    
    Pomocí kroků v článku vyřešíte chyba Operations Management Suite rozšiřuje upozornění do Azure v rámci naplánované spuštění následujícího dne. Nemusíte provádět žádnou další akci nebo nic zahájení.

- **Chyba: Zásady nachází na úrovni předplatného nebo prostředků skupiny**: ![snímek obrazovky Operations Management Suite nastavení výstrah stránky portálu, se zvýrazněnou zásad chybová zpráva](./media/monitor-alerts-extend/ErrorPolicy.png)

    Když [zásad Azure](../azure-policy/azure-policy-introduction.md) je použita, omezuje žádné nové prostředků ve skupině předplatné nebo prostředek, který obsahuje pracovní prostor analýzy protokolů (Operations Management Suite). V systému se nepodařilo rozšířit výstrahy do Azure a vytvoření skupin potřebné akce.
    
    Pokud chcete vyřešit, upravte zásadu, která je příčinou *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* chybu, která brání vytváření nových prostředků na vaše předplatné nebo prostředek skupiny, která obsahuje pracovním prostoru. To provedete pomocí portálu Azure, PowerShell, rozhraní příkazového řádku Azure nebo rozhraní API. Můžete auditovat akce najít na příslušnou zásadu, která je příčinou selhání. Další informace najdete v tématu [prohlížení protokolů aktivity akce](../azure-resource-manager/resource-group-audit.md). 
    
    Pomocí kroků v článku vyřešíte chyba Operations Management Suite rozšiřuje upozornění do Azure v rámci naplánované spuštění následujícího dne. Nemusíte provádět žádnou další akci nebo nic zahájení.


## <a name="next-steps"></a>Další postup

* Další informace o nové [prostředí Azure výstrahy](monitoring-overview-unified-alerts.md).
* Další informace o [protokolu výstrahy ve výstrahách Azure](monitor-alerts-unified-log.md).
