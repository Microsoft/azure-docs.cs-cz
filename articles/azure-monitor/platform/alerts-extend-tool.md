---
title: Rozšíření upozornění z Log Analytics do cloudu Azure Government
description: Tento článek popisuje nástroje a rozhraní API, pomocí kterého můžete rozšíření upozornění z Log Analytics do Azure Alerts.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 9d734f74c4e12b369e46c15dcb9d01a8185dddd6
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103373"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>Rozšíření upozornění z Log Analytics do upozornění Azure
Funkce oznámení na portálu OMS se nahrazuje pomocí výstrah služby Azure v cloudu Azure Government. Jako součást tohoto přechodu je výstrahy, které jste nakonfigurovali v Log Analytics se rozšířit do Azure. Pokud nechcete čekat na jejich automaticky přesunou na Azure, můžete zahájit proces:

- Ručně z portálu Operations Management Suite. 
- Programově pomocí rozhraní AlertsVersion API.  

> [!NOTE]
> Microsoft automaticky rozšíří upozornění vytvořená na portálu instancí Azure Government OMS Log Analytics pro Azure Alerts, od 1. března 2019, systematicky. Pokud máte potíže s vytvořením [skupiny akcí](../../azure-monitor/platform/action-groups.md), použijte [tyto kroky nápravy](alerts-extend-tool.md#troubleshooting) získat skupiny akcí, které jsou vytvořeny automaticky. Na portálu Azure Government OMS můžete pomocí těchto kroků až do 15. března 2019.

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>Option 1: Zahájení z portálu Operations Management Suite
Následující kroky popisují, jak rozšířit výstrahy pro pracovní prostor z portálu Operations Management Suite pro cloud Azure Government.  

1. Na webu Azure Portal vyberte **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
2. V podokně předplatná Log Analytics vyberte pracovní prostor a pak vyberte **portál OMS** dlaždici.
![Snímek obrazovky služby Log Analytics předplatné podokně s zvýrazněnou dlaždicí portál OMS](media/alerts-extend-tool/azure-portal-01.png) 
3. Po dokončení budete přesměrování na portál Operations Management Suite, vyberte **nastavení** ikonu.
![Snímek obrazovky s Operations Management Suite portal se zvýrazněnou ikonou nastavení](media/alerts-extend-tool/oms-portal-settings-option.png) 
4. Z **nastavení** stránce **výstrahy**.  
5. Vyberte **rozšířit do Azure**.
![Snímek obrazovky s Operations Management Suite nastavení výstrah stránka portálu, se rozšířit do Azure zvýrazněnou](media/alerts-extend-tool/ExtendInto.png)
6. Krok 3 průvodce se zobrazí v **výstrahy** podokně. Přečtěte si přehled a vyberte **Další**.
![Snímek obrazovky krok 1 Průvodce](media/alerts-extend-tool/ExtendStep1.png)  
7. V druhém kroku, zobrazí se souhrn navrhovaných změn, výpis příslušné [skupiny akcí](../../azure-monitor/platform/action-groups.md) pro výstrahy. Pokud jsou podobné akcí napříč více než jedna výstraha, Průvodce navrhuje přidružte skupinu jednu akci všechny z nich.  Zásady vytváření názvů je následující: *WorkspaceName_AG_#Number*. Chcete-li pokračovat, vyberte **Další**.
![Snímek obrazovky z kroku 2 Průvodce](media/alerts-extend-tool/ExtendStep2.png)  
8. V posledním kroku průvodce vyberte **Dokončit**a potvrďte výzvu k zahájení procesu. Volitelně můžete zadat e-mailovou adresu, tak, aby se zobrazí oznámení po dokončení procesu a všechny výstrahy byly úspěšně přesunuty do upozornění Azure.
![Snímek obrazovky krok 3 Průvodce](media/alerts-extend-tool/ExtendStep3.png)

Pokud je na dokončení průvodce **nastavení výstrah** odebrání stránky, možnost rozšíření upozornění do Azure. Na pozadí přesunou se vaše upozornění do Azure a to může nějakou dobu trvat. Během operace nemůžete provádět změny výstrah z portálu Operations Management Suite. Můžete zobrazit aktuální stav z banner v horní části portálu. Pokud jste dříve zadali e-mailovou adresu, dostanete e-mail, když se proces úspěšně dokončil.  


Upozornění dál zobrazovat na portálu Operations Management Suite, i když jsou úspěšně přesunuta do Azure.
![Stránka nastavení výstrah portálu snímek obrazovky s Operations Management Suite](media/alerts-extend-tool/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>Option 2: Použití rozhraní AlertsVersion API
Rozhraní AlertsVersion API Log Analytics můžete použít k rozšíření upozornění z Log Analytics do upozornění Azure z libovolného klienta, která může volat rozhraní REST API. Rozhraní API můžete přistupovat z Powershellu pomocí [ARMClient](https://github.com/projectkudu/ARMClient), je open source nástroj příkazového řádku. Můžete výstup výsledků ve formátu JSON.  

Pokud chcete používat rozhraní API, je nejprve vytvořit požadavek GET. To vyhodnotí a vrátí přehled navrhovaných změn, než zkusíte skutečně rozšířit do Azure s použitím požadavek POST. Seznam výsledků obsahuje upozornění a seznam navrhovaných [skupiny akcí](../../azure-monitor/platform/action-groups.md), ve formátu JSON. Pokud jsou podobné akcí napříč více než jedna výstraha, služba navrhuje všechny z nich přidružit ke skupině jedné akce. Zásady vytváření názvů je následující: *WorkspaceName_AG_#Number*.

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
                            "serviceUri": "https://test.com"
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

Tato odpověď označuje, že výstrahy se úspěšně rozšířily do upozornění Azure. Vlastnost verze je pouze pro kontrolu, pokud upozornění rozšířit do Azure a mít žádný vztah k položce [rozhraní API služby Log Analytics Search](../../azure-monitor/platform/api-alerts.md). Jakmile výstrahy se rozšíří do Azure úspěšně, některé e-mailové adresy zadaná v příspěvku požadavku se odesílají sestavy. Pokud všechna upozornění v zadaný pracovní prostor se už naplánované rozšíření, odpověď na váš požadavek POST je, že tento pokus bylo zakázáno (403 stavový kód). Zobrazit všechny chybové zprávy nebo pochopit, pokud se zablokuje a procesu, můžete odeslat požadavek GET. Pokud dojde k chybě, bude vrácen, spolu s souhrnné informace.

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
                            "serviceUri": "https://test.com"
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

## <a name="troubleshooting"></a>Řešení potíží 
Během procesu rozšiřování upozornění, problémů můžete zabránit v systému vytváření potřebných [skupiny akcí](../../azure-monitor/platform/action-groups.md). V takovém případě se zobrazí chybová zpráva v informační zprávě v **výstrah** části portálu Operations Management Suite, nebo GET volání Hotovo rozhraní API.

> [!IMPORTANT]
> Založené na cloudu Azure Government uživatelé portálu OMS není před 15. března 2019 proveďte následující kroky nápravy, upozornění se spustí v Azure, ale nepřidají žádné akce ani oznámení. Chcete-li získat oznámení pro výstrahy, musíte ručně upravit pravidla výstrah v Azure a přidat [skupiny akcí](../../azure-monitor/platform/action-groups.md)

Tady jsou kroky nápravy u každé chyby:
- **Chyba: Rozsah zámku je k dispozici na úrovni předplatného nebo prostředek skupiny pro operace zápisu**:   ![Snímek obrazovky s Operations Management Suite nastavení výstrah stránky portálu, se zvýrazněným obor zámku chybová zpráva](media/alerts-extend-tool/ErrorScopeLock.png)

    Pokud je povoleno uzamčení oboru, omezuje funkci žádné nové změny v předplatné nebo skupinu prostředků, který obsahuje tento pracovní prostor Log Analytics (Operations Management Suite). Systém se nemůže rozšířit upozornění do Azure a vytvořte potřebné skupiny akcí.
    
    Pokud chcete vyřešit, odstraňte *jen pro čtení* zámek na vaše předplatné nebo skupinu prostředků, který obsahuje tento pracovní prostor. Můžete to provést pomocí webu Azure portal, Powershellu, rozhraní příkazového řádku Azure nebo rozhraní API. Další informace najdete v tématu [využití prostředků Zámek](../../azure-resource-manager/resource-group-lock-resources.md). 
    
    Jakmile vyřešíte chybu pomocí popsané postupy v tomto článku, Operations Management Suite rozšiřuje vaše upozornění do Azure během plánovaného spuštění dalšího dne. Není nutné provádět žádnou další akci, nebo můžete spustit cokoli.

- **Chyba: Zásady jsou k dispozici na úrovni předplatného nebo prostředek skupiny**:   ![Snímek obrazovky s Operations Management Suite nastavení výstrah stránky portálu, se zvýrazněným zásad chybová zpráva](media/alerts-extend-tool/ErrorPolicy.png)

    Když [Azure Policy](../../governance/policy/overview.md) je použita, omezuje nový prostředek ve skupině předplatné nebo prostředek, který obsahuje tento pracovní prostor Log Analytics (Operations Management Suite). Systém se nemůže rozšířit upozornění do Azure a vytvořte potřebné skupiny akcí.
    
    Pokud chcete vyřešit, upravit zásadu, která je příčinou *[RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)* chybu, která brání vytváření nových prostředků na vaše předplatné nebo skupinu prostředků, který obsahuje tento pracovní prostor. Můžete to provést pomocí webu Azure portal, Powershellu, rozhraní příkazového řádku Azure nebo rozhraní API. Můžete auditovat akce najít příslušnou zásadu, která je příčinou selhání. Další informace najdete v tématu [zobrazení protokolů aktivit pro auditování akcí](../../azure-resource-manager/resource-group-audit.md). 
    
    Jakmile vyřešíte chybu pomocí popsané postupy v tomto článku, Operations Management Suite rozšiřuje vaše upozornění do Azure během plánovaného spuštění dalšího dne. Není nutné provádět žádnou další akci, nebo můžete spustit cokoli.


## <a name="next-steps"></a>Další postup

* Další informace o novém [prostředí Azure Alerts](../../azure-monitor/platform/alerts-overview.md).
* Další informace o [upozornění protokolů ve službě Azure Alerts](alerts-unified-log.md).

