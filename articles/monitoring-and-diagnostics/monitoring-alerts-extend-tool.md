---
title: Rozšíření upozornění z Analytcs protokolu do Azure
description: Tento článek popisuje nástroje a rozhraní API, pomocí kterého můžete rozšíření upozornění z Log Analytics do Azure Alerts.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: d70eecb6a5d6bafbfa6507dbe8b1bcb1cad67191
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990231"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>Rozšíření upozornění z Log Analytics do upozornění Azure
Funkce výstrah ve službě Azure Log Analytics teď nahrazuje Azure Alerts. Jako součást tohoto přechodu je výstrahy, které jste nakonfigurovali v Log Analytics se rozšířit do Azure. Pokud nechcete čekat na jejich automaticky přesunou na Azure, můžete zahájit proces:

- Ručně z portálu Operations Management Suite. 
- Programově pomocí rozhraní AlertsVersion API.  

> [!NOTE]
> Microsoft automaticky rozšíří upozornění vytvořená v instancích veřejné cloudové služby Log Analytics pro Azure Alerts, od 14. května 2018, opakovala, dokud nebude dokončena. Pokud máte potíže s vytvořením [skupiny akcí](monitoring-action-groups.md), použijte [tyto kroky nápravy](monitoring-alerts-extend-tool.md#troubleshooting) získat skupiny akcí, které jsou vytvořeny automaticky. Pomocí těchto kroků 5. července 2018. *Není k dispozici pro uživatele Soveriegn cloudové služby Log Analytics a Azure Goverment*. 

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>Možnost 1: Zahájení z portálu Operations Management Suite
Následující kroky popisují, jak rozšířit výstrahy pro pracovní prostor z portálu Operations Management Suite.  

1. Na webu Azure Portal, vyberte **všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
2. V podokně předplatná Log Analytics vyberte pracovní prostor a pak vyberte **portál OMS** dlaždici.
![Snímek obrazovky služby Log Analytics předplatné podokně s zvýrazněnou dlaždicí portál OMS](./media/monitor-alerts-extend/azure-portal-01.png) 
3. Po dokončení budete přesměrování na portál Operations Management Suite, vyberte **nastavení** ikonu.
![Snímek obrazovky s Operations Management Suite portal se zvýrazněnou ikonou nastavení](./media/monitor-alerts-extend/oms-portal-settings-option.png) 
4. Z **nastavení** stránce **výstrahy**.  
5. Vyberte **rozšířit do Azure**.
![Snímek obrazovky s Operations Management Suite nastavení výstrah stránka portálu, se rozšířit do Azure zvýrazněnou](./media/monitor-alerts-extend/ExtendInto.png)
6. Krok 3 průvodce se zobrazí v **výstrahy** podokně. Přečtěte si přehled a vyberte **Další**.
![Snímek obrazovky krok 1 Průvodce](./media/monitor-alerts-extend/ExtendStep1.png)  
7. V druhém kroku, zobrazí se souhrn navrhovaných změn, výpis příslušné [skupiny akcí](monitoring-action-groups.md) pro výstrahy. Pokud jsou podobné akcí napříč více než jedna výstraha, Průvodce navrhuje přidružte skupinu jednu akci všechny z nich.  Zásady vytváření názvů je následující: *WorkspaceName_AG_ #Number*. Chcete-li pokračovat, vyberte **Další**.
![Snímek obrazovky z kroku 2 Průvodce](./media/monitor-alerts-extend/ExtendStep2.png)  
8. V posledním kroku průvodce vyberte **Dokončit**a potvrďte výzvu k zahájení procesu. Volitelně můžete zadat e-mailovou adresu, tak, aby se zobrazí oznámení po dokončení procesu a všechny výstrahy byly úspěšně přesunuty do upozornění Azure.
![Snímek obrazovky krok 3 Průvodce](./media/monitor-alerts-extend/ExtendStep3.png)

Pokud je na dokončení průvodce **nastavení výstrah** odebrání stránky, možnost rozšíření upozornění do Azure. Na pozadí přesunou se vaše upozornění do Azure a to může nějakou dobu trvat. Během operace nemůžete provádět změny výstrah z portálu Operations Management Suite. Můžete zobrazit aktuální stav z banner v horní části portálu. Pokud jste dříve zadali e-mailovou adresu, dostanete e-mail, když se proces úspěšně dokončil.  


Upozornění dál zobrazovat na portálu Operations Management Suite, i když jsou úspěšně přesunuta do Azure.
![Stránka nastavení výstrah portálu snímek obrazovky s Operations Management Suite](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>Možnost 2: Použití rozhraní AlertsVersion API
Rozhraní AlertsVersion API Log Analytics můžete použít k rozšíření upozornění z Log Analytics do upozornění Azure z libovolného klienta, která může volat rozhraní REST API. Rozhraní API můžete přistupovat z Powershellu pomocí [ARMClient](https://github.com/projectkudu/ARMClient), je open source nástroj příkazového řádku. Můžete výstup výsledků ve formátu JSON.  

Pokud chcete používat rozhraní API, je nejprve vytvořit požadavek GET. To vyhodnotí a vrátí přehled navrhovaných změn, než zkusíte skutečně rozšířit do Azure s použitím požadavek POST. Seznam výsledků obsahuje upozornění a seznam navrhovaných [skupiny akcí](monitoring-action-groups.md), ve formátu JSON. Pokud jsou podobné akcí napříč více než jedna výstraha, služba navrhuje všechny z nich přidružit ke skupině jedné akce. Zásady vytváření názvů je následující: *WorkspaceName_AG_ #Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Pokud požadavek na získání úspěšné, je stavový kód HTTP 200 vrátil spolu se seznamem výstrah a navrhované skupiny akcí v datech JSON. Následuje příklad odpovědi:

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
Pokud zadaný pracovní prostor nemá definovaná nějaká pravidla upozornění, vrátí následující JSON data:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Pokud všechna pravidla výstrah ve službě zadaný pracovní prostor se rozšířily již do Azure, bude odpověď na požadavek na získání:

```json
{
    "version": 2
}
```

Chcete-li zahájit migraci upozornění do Azure, inicializovat odpověď na příspěvek. Odpověď na příspěvek potvrdí vaše záměr, jakož i přijetí, aby upozornění rozšířit z Log Analytics na Azure Alerts. Naplánované aktivity a upozornění se zpracovávají, jak je uvedeno, na základě výsledků při provádění odezvy GET dříve. Volitelně můžete zadat seznam e-mailové adresy, ke kterým Log Analytics odesílá sestavy po úspěšném dokončení plánované pozadí proces migrace výstrahy. Můžete použít následující příklad požadavku:

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Výsledek migrace výstrahy do upozornění Azure může lišit v závislosti na souhrn poskytované získat odpověď. Při plánování, upozornění v Log Analytics jsou dočasně nedostupné pro úpravy na portálu Operations Management Suite. Můžete však vytvořit nové výstrahy. 

Pokud je požadavek POST úspěšná, vrátí stav HTTP 200 OK, spolu s následující odpověď:

```json
{
    "version": 2
}
```

Tato odpověď označuje, že výstrahy se úspěšně rozšířily do upozornění Azure. Vlastnost verze je pouze pro kontrolu, pokud upozornění rozšířit do Azure a mít žádný vztah k položce [rozhraní API služby Log Analytics Search](../log-analytics/log-analytics-api-alerts.md). Jakmile výstrahy se rozšíří do Azure úspěšně, některé e-mailové adresy zadaná v příspěvku požadavku se odesílají sestavy. Pokud všechna upozornění v zadaný pracovní prostor se už naplánované rozšíření, odpověď na váš požadavek POST je, že tento pokus bylo zakázáno (403 stavový kód). Zobrazit všechny chybové zprávy nebo pochopit, pokud se zablokuje a procesu, můžete odeslat požadavek GET. Pokud dojde k chybě, bude vrácen, spolu s souhrnné informace.

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
 Pokud Microsoft není rozšířené úspěšně upozornění z portálu Operations Management Suite do Azure, které můžou provádět ručně do 5. července 2018. Dvě možnosti pro ruční rozšíření jsou popsaná v předchozích dvou částech.

Všechny výstrahy z portálu Operations Management Suite se po 5. července 2018 se rozšíří do Azure. Uživatelé, kteří se nepovedlo provést [navrhované kroky k nápravě nezbytné](#troubleshooting) bude spojená výstrah, spouštění bez aktivaci akce nebo oznámení, protože by chyběla [skupiny akcí](monitoring-action-groups.md). 

Chcete-li vytvořit [skupiny akcí](monitoring-action-groups.md) upozornění v Log Analytics ručně, použijte následující ukázkový skript:
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


### <a name="about-the-custom-powershell-script"></a>O vlastní skript prostředí PowerShell 
Toto je důležité informace o používání skriptu:
- Předpokladem je instalace [ARMclient](https://github.com/projectkudu/ARMClient), nástroje příkazového řádku open source, který zjednodušuje volání rozhraní API Azure Resource Manageru.
- Pro spuštění skriptu, musíte mít roli Přispěvatel nebo vlastník v rámci předplatného Azure.
- Je nutné zadat následující parametry:
    - $subscriptionId: ID předplatného Azure přidružené k pracovnímu prostoru Operations Management Suite Log Analytics.
    - $resourceGroup: Skupina prostředků Azure pro pracovní prostor Operations Management Suite Log Analytics.
    - $workspaceName: název pracovního prostoru Operations Management Suite Log Analytics.

### <a name="output-of-the-custom-powershell-script"></a>Výstup vlastního skriptu prostředí PowerShell
Skript je verbose a vypíše kroky při jejím spuštění: 
- Zobrazí souhrn, který obsahuje informace o existujících výstrah Operations Management Suite Log Analytics v pracovním prostoru. Souhrn obsahuje také informace o skupinách Azure akcí, které mají být vytvořeny pro akce související s nimi. 
- Zobrazí se výzva k pokračujte s rozšíření nebo ukončete po zobrazení souhrnu.
- Pokud vám pokračujte s příponou, vytvoří se nové skupiny akcí Azure a všechny existující výstrahy jsou s nimi spojeny. 
- Ukončí skript tím, že zobrazuje zpráva "Rozšíření bylo dokončeno!" V případě všechny zprostředkující chyby skript zobrazí další chyby.

## <a name="troubleshooting"></a>Řešení potíží 
Během procesu rozšiřování upozornění, problémů můžete zabránit v systému vytváření potřebných [skupiny akcí](monitoring-action-groups.md). V takovém případě se zobrazí chybová zpráva v informační zprávě v **výstrah** části portálu Operations Management Suite, nebo GET volání Hotovo rozhraní API.

> [!IMPORTANT]
> Na základě veřejného cloudu Azure Log Analytics uživatele není proveďte následující kroky nápravy před 5. července 2018, upozornění se spustí v Azure, ale nepřidají žádné akce ani oznámení. Chcete-li získat oznámení pro výstrahy, musíte ručně upravit a přidat [skupiny akcí](monitoring-action-groups.md), nebo použít předchozí [vlastní skript prostředí PowerShell](#option-3---using-custom-powershell-script).

Tady jsou kroky nápravy u každé chyby:
- **Chyba: Zámek oboru je k dispozici na úrovni předplatného nebo prostředek skupiny pro operace zápisu**: ![snímek obrazovky s Operations Management Suite nastavení výstrah stránky portálu, se zvýrazněným obor zámku chybová zpráva](./media/monitor-alerts-extend/ErrorScopeLock.png)

    Pokud je povoleno uzamčení oboru, omezuje funkci žádné nové změny v předplatné nebo skupinu prostředků, který obsahuje tento pracovní prostor Log Analytics (Operations Management Suite). Systém se nemůže rozšířit upozornění do Azure a vytvořte potřebné skupiny akcí.
    
    Pokud chcete vyřešit, odstraňte *jen pro čtení* zámek na vaše předplatné nebo skupinu prostředků, který obsahuje tento pracovní prostor. Můžete to provést pomocí webu Azure portal, Powershellu, rozhraní příkazového řádku Azure nebo rozhraní API. Další informace najdete v tématu [využití prostředků Zámek](../azure-resource-manager/resource-group-lock-resources.md). 
    
    Jakmile vyřešíte chybu pomocí popsané postupy v tomto článku, Operations Management Suite rozšiřuje vaše upozornění do Azure během plánovaného spuštění dalšího dne. Není nutné provádět žádnou další akci, nebo můžete spustit cokoli.

- **Chyba: Zásady nachází na úrovni předplatného nebo prostředek skupiny**: ![snímek obrazovky s Operations Management Suite nastavení výstrah stránky portálu, se zvýrazněným zásad chybová zpráva](./media/monitor-alerts-extend/ErrorPolicy.png)

    Když [Azure Policy](../azure-policy/azure-policy-introduction.md) je použita, omezuje nový prostředek ve skupině předplatné nebo prostředek, který obsahuje tento pracovní prostor Log Analytics (Operations Management Suite). Systém se nemůže rozšířit upozornění do Azure a vytvořte potřebné skupiny akcí.
    
    Pokud chcete vyřešit, upravit zásadu, která je příčinou *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* chybu, která brání vytváření nových prostředků na vaše předplatné nebo skupinu prostředků, který obsahuje tento pracovní prostor. Můžete to provést pomocí webu Azure portal, Powershellu, rozhraní příkazového řádku Azure nebo rozhraní API. Můžete auditovat akce najít příslušnou zásadu, která je příčinou selhání. Další informace najdete v tématu [zobrazení protokolů aktivit pro auditování akcí](../azure-resource-manager/resource-group-audit.md). 
    
    Jakmile vyřešíte chybu pomocí popsané postupy v tomto článku, Operations Management Suite rozšiřuje vaše upozornění do Azure během plánovaného spuštění dalšího dne. Není nutné provádět žádnou další akci, nebo můžete spustit cokoli.


## <a name="next-steps"></a>Další postup

* Další informace o novém [prostředí Azure Alerts](monitoring-overview-unified-alerts.md).
* Další informace o [upozornění protokolů ve službě Azure Alerts](monitor-alerts-unified-log.md).
