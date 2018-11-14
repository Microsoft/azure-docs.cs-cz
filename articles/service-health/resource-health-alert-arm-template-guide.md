---
title: Konfigurace výstrahy týkající se stavu prostředků Azure pomocí šablon Resource Manageru | Dokumentace Microsoftu
description: Vytvořte upozornění prostřednictvím kódu programu, které vás upozorní, když budou nedostupné prostředky Azure.
author: shawntabrizi
manager: scotthit
editor: ''
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/4/2018
ms.author: shtabriz
ms.openlocfilehash: ac1b9dbbb5739dd015c0bda5f1ea82fe26bb0c70
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51625942"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Konfigurace výstrahy týkající se stavu prostředků pomocí šablon Resource Manageru

Tento článek vám ukáže postup vytvoření Resource Health upozornění protokolu aktivit programově pomocí šablon Azure Resource Manageru a Azure Powershellu.

Azure Resource Health zachovat informována o aktuální a Historický stav svých prostředků Azure. Azure Resource Health můžete upozornění se zobrazí v téměř v reálném čase v případě těchto prostředků jste změny v jejich stav. Vytváří se Resource Health výstrahy prostřednictvím kódu programu povolit uživatelům vytvářet a upravovat výstrahy hromadně.

## <a name="prerequisites"></a>Požadavky

Postupujte podle pokynů na této stránce, budete muset předem zařídit pár věcí:

1. Je potřeba nainstalovat [modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (`AzureRm`)
2. Je potřeba [vytvořit nebo znovu použít skupiny akcí](../monitoring-and-diagnostics/monitoring-action-groups.md) nakonfigurovaný tak, aby upozornění

## <a name="instructions"></a>Pokyny
1. Pomocí Powershellu, připojte se k Azure pomocí svého účtu a vyberte předplatné, které chcete pracovat s

        Login-AzureRmAccount
        Select-AzureRmSubscription -Subscription <subscriptionId>

    > Můžete použít `Get-AzureRmSubscription` seznam předplatných, budete mít přístup k.

2. Najít a uložit úplné ID Azure Resource Manageru pro vaši skupinu akcí

        (Get-AzureRmActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id

3. Vytvoříte a uložíte šablonu Resource Manageru pro výstrahy týkající se stavu prostředků jako `resourcehealthalert.json` ([viz podrobnosti níže](#resource-manager-template-for-resource-health-alerts))

4. Vytvořte nové nasazení Azure Resource Manageru pomocí této šablony.

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>

5. Budete vyzváni k zadání názvu upozornění a jste si zkopírovali dříve ID prostředku skupiny akcí:

        Supply values for the following parameters:
        (Type !? for Help.)
        activityLogAlertName: <Alert Name>
        actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>

6. Pokud vše funguje úspěšně, dostanete potvrzení v prostředí PowerShell

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

Všimněte si, že pokud plánujete plné automatizaci tohoto procesu, stačí jednoduše upravte šablonu Resource Manageru pro výzvu pro hodnoty v kroku 5.

## <a name="resource-manager-template-for-resource-health-alerts"></a>Šablona Resource Manageru pro výstrahy týkající se stavu prostředků

Tuto základní šablonu můžete použít jako výchozí bod pro vytvoření výstrahy týkající se stavu prostředků. Tato šablona bude fungovat, jak je uvedená, bude registraci a přihlašování budete chcete obdržet výstrahy pro všechny události stavu prostředků nově aktivovaného všech prostředků v předplatném.

> V dolní části tohoto článku jsme zahrnuli také mnohem složitější šablonu oznámení, což by měl zvýšit signál od šumu pro výstrahy týkající se stavu prostředků porovnání s touto šablonou.

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

Však není doporučeno široké upozornění podobný následujícímu. Zjistěte, jak můžeme nastavit dolů toto upozornění a zaměřte se na události, které jsme vám jde o níže.

### <a name="adjusting-the-alert-scope"></a>Úprava oboru výstrah

Výstrahy týkající se stavu prostředků lze nakonfigurovat monitorování událostí na třech různých oborech:

 * Úroveň předplatného
 * Úroveň skupiny prostředků
 * Zdrojem pro úroveň

Šablona upozornění je nakonfigurována na úrovni předplatného, ale pokud chcete konfigurovat upozornění na pouze oznámení o některých prostředků nebo prostředky v rámci určité skupiny prostředků, stačí jednoduše změnit `scopes` v výše uvedené části Šablona.

Pro úrovně oboru skupiny prostředků by měl vypadat části obory:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

A pro prostředek úrovni oboru, část oboru by měl vypadat takto:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Příklad: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Můžete přejít na web Azure Portal a podívejte se na adresu URL při prohlížení vašich prostředků Azure, chcete-li získat tento řetězec.

### <a name="adjusting-the-resource-types-which-alert-you"></a>Nastavení prostředku typy, které vás upozorní

Upozornění na úrovni skupiny prostředků nebo předplatné může mít různé druhy prostředků. Pokud chcete omezit výstrahy, které může přicházet pouze ze podmnožinu typů prostředků, můžete definovat, že `condition` část šablony takto:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "Microsoft.Compute/virtualMachines",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "Microsoft.Storage/storageAccounts",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

Tady používáme `anyOf` obálky umožňující upozornění stavu prostředků tak, aby odpovídaly některá z podmínek, určíme, což pro výstrahy, které se zaměřují na konkrétní typy prostředků.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>Nastavení s upozorněním, že události stavu prostředků
Pokud prostředky událost stavu, které procházejí řadu fází, která představuje stav události stavu: `Active`, `InProgress`, `Updated`, a `Resolved`.

Můžete chtít pouze oznámení, když se prostředek stane není v pořádku, v takovém případě chcete nakonfigurovat výstrahy jenom oznámit, kdy `status` je `Active`. Pokud chcete také upozorněni v dalších fázích, můžete však přidat tyto podrobnosti takto:

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
                    "equals": "InProgress"
                },
                        {
                    "field": "status",
                    "equals": "Resolved"
                }
            ]
        }
    ]
}
```

Pokud chcete být upozornění pro všechny čtyři fází událostí stavu, tuto podmínku můžete odebrat všechno dohromady a upozornění vám oznámí bez ohledu `status` vlastnost.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>Nastavení výstrah Resource Health, aby "Neznámý" události

Azure Resource Health můžete nahlásit vám nejnovější stav svých prostředků nepřetržitým monitorováním jejich pomocí nástrojům test Runner. Relevantní ohlášené stavu stavy jsou: "K dispozici", "Není k dispozici" a "Snížený". Ale v situacích, kdy jsou schopné komunikovat spouštěče a prostředků Azure, "Neznámý" stav nahlašuje prostředku a bude se považovat za událost "Aktivním" stavu.

Ale pokud prostředek ohlásí "Neznámá", je pravděpodobné, že jeho stav nebyl změněn od posledního hlášení o přesné. Pokud chcete odstranit oznámení o událostech "Neznámý", můžete zadat tuto logiku v šabloně:

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

V tomto příkladu jsme jsou se pouze oznámení o událostech, kde aktuální a předchozí stav nemá "Neznámý". Tato změna může být záležitostí užitečné, pokud vaše výstrahy se odesílají přímo na váš mobilní telefon nebo e-mailu.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>Úprava výstrahu, kterou chcete vyhnout, kterou inicioval uživatel události

Události stavu prostředků může být aktivační událost iniciované platformou a události, kterou inicioval uživatel. Může být vhodné, pouze odeslat oznámení, když událost stavu se způsobené platformou Azure.

Je snadno konfigurovatelné upozornění můžete filtrovat pouze tyto typy událostí:

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

## <a name="recommended-resource-health-alert-template"></a>Doporučené výstrahy šablonu Resource Health

Pomocí různých nastavení popsané v předchozí části, můžeme vytvořit komplexní šablonu oznámení, který je nakonfigurován pro maximalizaci signál od šumu.

Zde je, co, doporučujeme použít:
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
                                    "equals": "InProgress",
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

Však budete nejlíp vědět, jaký konfigurace platí pro vás, takže používejte nástroje, výukové vám v této dokumentaci, aby vlastní přizpůsobení.

## <a name="next-steps"></a>Další postup

Další informace o službě Resource Health:
-  [Přehled Azure Resource Health](Resource-health-overview.md)
-  [Typy prostředků a kontroly stavu dostupné prostřednictvím služby Azure Resource Health](resource-health-checks-resource-types.md)

Vytvoření upozornění na stav služby:
-  [Konfigurace výstrah pro Service Health](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) 
