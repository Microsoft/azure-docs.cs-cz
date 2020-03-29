---
title: Šablona pro vytváření výstrah stavu prostředků
description: Vytvářejte výstrahy programově, které vás upozorní, když vaše prostředky Azure nebudou k dispozici.
ms.topic: conceptual
ms.date: 9/4/2018
ms.openlocfilehash: c01934cc88dc29d0503abfafc203ab0f04bf1761
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062897"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Konfigurace výstrah stavu prostředků pomocí šablon Správce prostředků

Tento článek vám ukáže, jak programově vytvářet výstrahy protokolu aktivit o stavu prostředků pomocí šablon Azure Resource Manager a Azure PowerShellu.

Azure Resource Health vás informuje o aktuálním a historickém stavu vašich prostředků Azure. Výstrahy Azure Resource Health vás můžou upozornit téměř v reálném čase, když se tyto prostředky změní ve svém stavu. Vytváření výstrah stavu prostředků programově umožňuje uživatelům vytvářet a upravovat výstrahy hromadně.

> [!NOTE]
> Výstrahy stavu prostředků jsou aktuálně ve verzi Preview.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle pokynů na této stránce, budete muset předem nastavit několik věcí:

1. Je třeba nainstalovat [modul Azure PowerShellu](https://docs.microsoft.com/powershell/azure/install-Az-ps)
2. Je třeba [vytvořit nebo znovu použít skupinu akcí](../azure-monitor/platform/action-groups.md) nakonfigurovanou tak, aby vás upozorňovala

## <a name="instructions"></a>Pokyny
1. Pomocí PowerShellu se přihlaste k Azure pomocí svého účtu a vyberte předplatné, se kterým chcete pracovat.

        Login-AzAccount
        Select-AzSubscription -Subscription <subscriptionId>

    > Můžete použít `Get-AzSubscription` k zobrazení seznamu předplatných, ke které máte přístup.

2. Vyhledání a uložení úplného ID Správce prostředků Azure pro vaši skupinu akcí

        (Get-AzActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id

3. Vytvoření a uložení šablony Správce prostředků `resourcehealthalert.json` pro výstrahy stavu zdrojů jako ([viz podrobnosti níže](#resource-manager-template-options-for-resource-health-alerts))

4. Vytvoření nového nasazení Azure Resource Manageru pomocí této šablony

        New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>

5. Budete vyzváni k zadání ID prostředku Název výstrahy a skupiny akcí, které jste zkopírovali dříve:

        Supply values for the following parameters:
        (Type !? for Help.)
        activityLogAlertName: <Alert Name>
        actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>

6. Pokud vše fungovalo úspěšně, dostanete potvrzení v PowerShellu

        DeploymentName          : ExampleDeployment
        ResourceGroupName       : <resourceGroup>
        ProvisioningState       : Succeeded
        Timestamp               : 11/8/2017 2:32:00 AM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                Name                     Type       Value
                                ===============          =========  ==========
                                activityLogAlertName     String     <Alert Name>
                                activityLogAlertEnabled  Bool       True
                                actionGroupResourceId    String     /...
        
        Outputs                 :
        DeploymentDebugLogLevel :

Všimněte si, že pokud plánujete plně automatizovat tento proces, stačí upravit šablonu Správce prostředků, abyste nezobrazovali výzvu k zadání hodnot v kroku 5.

## <a name="resource-manager-template-options-for-resource-health-alerts"></a>Možnosti šablony Správce prostředků pro výstrahy stavu prostředků

Tuto základní šablonu můžete použít jako výchozí bod pro vytváření výstrah stavu prostředků. Tato šablona bude fungovat tak, jak je napsáno a zaregistruje vás k přijímání výstrah pro všechny nově aktivované události stavu prostředků napříč všemi prostředky v předplatném.

> V dolní části tohoto článku jsme také zahrnuli složitější šablonu výstrahy, která by měla zvýšit poměr signálu k šumu pro výstrahy stavu prostředků ve srovnání s touto šablonou.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": true,
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ResourceHealth"
            },
            {
              "field": "status",
              "equals": "Active"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

Nicméně, široká výstraha, jako je tento, se obecně nedoporučuje. Zjistěte, jak můžeme tuto výstrahu zaměřit tak, abychom se zaměřili na události, na kterých nám záleží níže.

### <a name="adjusting-the-alert-scope"></a>Úprava rozsahu výstrah

Výstrahy stavu prostředků lze nakonfigurovat tak, aby monitorovaly události ve třech různých oborech:

 * Úroveň předplatného
 * Úroveň skupiny zdrojů
 * Úroveň zdroje

Šablona výstrahje nakonfigurována na úrovni předplatného, ale pokud chcete nakonfigurovat výstrahu tak, aby vás upozorňovala `scopes` pouze na určité prostředky nebo prostředky v rámci určité skupiny prostředků, stačí upravit oddíl ve výše uvedené šabloně.

Pro obor úrovně skupiny prostředků by měl oddíl oborů vypadat takto:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

A pro obor na úrovni prostředků by měla vypadat část oboru:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Příklad: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Můžete přejít na portál Azure Portal a podívejte se na adresu URL při zobrazení prostředku Azure získat tento řetězec.

### <a name="adjusting-the-resource-types-which-alert-you"></a>Úprava typů prostředků, které vás upozorní

Výstrahy na úrovni předplatného nebo skupiny prostředků mohou mít různé druhy prostředků. Pokud chcete omezit výstrahy pouze z určité podmnožinu typů prostředků, `condition` můžete definovat, že v části šablony, jako je takto:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.COMPUTE/VIRTUALMACHINES",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.STORAGE/STORAGEACCOUNTS",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

Zde použijeme `anyOf` obálku, aby výstraha stavu prostředku odpovídala libovolné z podmínek, které zadáme, což umožňuje výstrahy, které cílí na konkrétní typy prostředků.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>Úprava událostí stavu prostředků, které vás upozorní
Když zdroje podstoupí zdravotní událost, mohou projít řadou fází, které `Active`představují `In Progress` `Updated`stav `Resolved`události stavu: , , , a .

Můžete chtít být upozorněni pouze v případě, že prostředek přestane být v pořádku, `status` v `Active`takovém případě chcete nakonfigurovat výstrahu tak, aby upozorňovala pouze v případě, že je . Pokud však chcete být upozorněni i na ostatní fáze, můžete tyto podrobnosti přidat takto:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "status",
                    "equals": "Active"
                },
                {
                    "field": "status",
                    "equals": "In Progress"
                },
                {
                    "field": "status",
                    "equals": "Resolved"
                },
                {
                    "field": "status",
                    "equals": "Updated"
                }
            ]
        }
    ]
}
```

Pokud chcete být upozorněni na všechny čtyři fáze událostí stavu, můžete tuto podmínku odebrat `status` společně a výstraha vás upozorní bez ohledu na vlastnost.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>Úprava výstrah stavu prostředků tak, aby se zabránilo "neznámým" událostem

Azure Resource Health vám může hlásit nejnovější stav vašich prostředků neustálým sledováním pomocí testovacích běhů. Příslušné hlášené stavové stavy jsou: "K dispozici", "Nedostupné" a "Degradováno". Však v situacích, kdy runner a prostředek Azure nejsou schopny komunikovat, "Neznámý" stav je hlášena pro prostředek a který je považován za "aktivní" událost stavu.

Pokud však zdroj hlásí "Neznámý", je pravděpodobné, že se jeho stav od poslední přesné sestavy nezměnil. Pokud chcete odstranit výstrahy na události "Neznámé", můžete určit tuto logiku v šabloně:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
        {
            "anyOf": [
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
    ]
},
```

V tomto příkladu upozorňujeme pouze na události, kde aktuální a předchozí stav nemá "Neznámý". Tato změna může být užitečným doplňkem, pokud jsou upozornění odesílána přímo na váš mobilní telefon nebo e-mail. 

Všimněte si, že je možné pro currentHealthStatus a previousHealthStatus vlastnosti mají být null v některých událostech. Například když dojde k aktualizované události je pravděpodobné, že stav prostředku se od poslední sestavy nezměnil, pouze jsou k dispozici další informace o události (např. příčina). Proto použití výše uvedené klauzule může mít za následek některé výstrahy nejsou spuštěny, protože vlastnosti properties.currentHealthStatus a properties.previousHealthStatus hodnoty budou nastaveny na hodnotu null.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>Úprava výstrahy tak, aby se zabránilo událostem iniciovaných uživatelem

Události stavu prostředků mohou být spuštěny iniciované platformou a událostmi iniciované uživatelem. Může mít smysl odeslat oznámení pouze v případě, že událost stavu je způsobena platformou Azure.

Výstrahu lze snadno nakonfigurovat tak, aby filtrovala pouze tyto druhy událostí:

```json
"condition": {
    "allOf": [
        ...,
        {
            "field": "properties.cause",
            "equals": "PlatformInitiated",
            "containsAny": null
        }
    ]
}
```
Všimněte si, že je možné pro příčinu pole má být null v některých událostech. To znamená, že dojde k přechodu na stav (např. k dispozici k dispozici) a událost je okamžitě zaznamenána, aby se zabránilo zpoždění oznámení. Proto použití výše uvedené klauzule může mít za následek výstrahy není aktivována, protože hodnota vlastnosti vlastnostvlastnosti vlastnosti bude nastavena na hodnotu null.

## <a name="complete-resource-health-alert-template"></a>Kompletní šablona výstrahy Stav zdroje

Pomocí různých úprav popsaných v předchozí části je zde ukázková šablona, která je nakonfigurována tak, aby maximalizovala poměr signálu k šumu. Mějte na paměti výše uvedené upozornění, kde currentHealthStatus, previousHealthStatus a způsobit hodnoty vlastností může být null v některých událostech.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "activityLogAlertName": {
            "type": "string",
            "metadata": {
                "description": "Unique name (within the Resource Group) for the Activity log alert."
            }
        },
        "actionGroupResourceId": {
            "type": "string",
            "metadata": {
                "description": "Resource Id for the Action group."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/activityLogAlerts",
            "apiVersion": "2017-04-01",
            "name": "[parameters('activityLogAlertName')]",
            "location": "Global",
            "properties": {
                "enabled": true,
                "scopes": [
                    "[subscription().id]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ResourceHealth",
                            "containsAny": null
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.cause",
                                    "equals": "PlatformInitiated",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "status",
                                    "equals": "Active",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Resolved",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "In Progress",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Updated",
                                    "containsAny": null
                                }
                            ]
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[parameters('actionGroupResourceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

Budete však vědět nejlépe, jaké konfigurace jsou pro vás efektivní, proto pomocí nástrojů, které jste v této dokumentaci naučili, proveďte vlastní přizpůsobení.

## <a name="next-steps"></a>Další kroky

Další informace o stavu zdrojů:
-  [Přehled stavu prostředků Azure](Resource-health-overview.md)
-  [Typy prostředků a kontroly stavu dostupné prostřednictvím služby Azure Resource Health](resource-health-checks-resource-types.md)


Vytvořit výstrahy stavu služby:
-  [Konfigurace výstrah pro stav služby](../azure-monitor/platform/alerts-activity-log-service-notifications.md) 
-  [Schéma událostí protokolu aktivit Azure](../azure-monitor/platform/activity-log-schema.md)
