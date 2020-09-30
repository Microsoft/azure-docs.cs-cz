---
title: Šablona pro vytvoření výstrah Resource Health
description: Vytvářejte výstrahy programově, které vás upozorní, když vaše prostředky Azure nebudou k dispozici.
ms.topic: conceptual
ms.date: 9/4/2018
ms.openlocfilehash: 3859bb0ce2497b1c1f547c3750e53745ef6d6f28
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91537425"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Konfigurace upozornění na stav prostředků s využitím šablon Resource Manageru

V tomto článku se dozvíte, jak pomocí šablon Azure Resource Manager a Azure PowerShell vytvářet výstrahy protokolu aktivit Resource Health programově.

Azure Resource Health vás informují o aktuálním a historickém stavu vašich prostředků Azure. Výstrahy Azure Resource Health vás můžou zobrazit téměř v reálném čase, když tyto prostředky mají změnu stavu. Vytváření výstrah Resource Health programově umožňuje uživatelům vytvářet a přizpůsobovat hromadnou výstrahu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady

Pokud chcete postupovat podle pokynů na této stránce, musíte předem nastavit pár věcí:

1. Je potřeba nainstalovat [modul Azure PowerShell](/powershell/azure/install-az-ps) .
2. Musíte [vytvořit nebo znovu použít skupinu akcí](../azure-monitor/platform/action-groups.md) nakonfigurovanou pro upozorňování

## <a name="instructions"></a>Pokyny
1. Pomocí PowerShellu se přihlaste k Azure pomocí svého účtu a vyberte předplatné, se kterým chcete pracovat.

    ```azurepowershell
    Login-AzAccount
    Select-AzSubscription -Subscription <subscriptionId>
    ```

    > Můžete použít `Get-AzSubscription` k vypsání předplatných, ke kterým máte přístup.

2. Vyhledejte a uložte úplné ID Azure Resource Manager pro vaši skupinu akcí.

    ```azurepowershell
    (Get-AzActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id
    ```

3. Vytvoření a uložení šablony Správce prostředků pro Resource Health výstrahy jako `resourcehealthalert.json` ([Viz podrobnosti níže](#resource-manager-template-options-for-resource-health-alerts))

4. Vytvořit nové nasazení Azure Resource Manager pomocí této šablony

    ```azurepowershell
    New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>
    ```

5. Zobrazí se výzva k zadání názvu výstrahy a ID prostředku skupiny akcí, které jste zkopírovali dříve:

    ```azurepowershell
    Supply values for the following parameters:
    (Type !? for Help.)
    activityLogAlertName: <Alert Name>
    actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>
    ```

6. Pokud vše úspěšně fungovalo, získáte potvrzení v PowerShellu.

    ```output
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
    ```

Pamatujte na to, že pokud plánujete plně automatizovat tento proces, stačí upravit šablonu Správce prostředků, aby se nezobrazovala výzva k zadání hodnot v kroku 5.

## <a name="resource-manager-template-options-for-resource-health-alerts"></a>Možnosti šablony Správce prostředků pro výstrahy Resource Health

Tuto základní šablonu můžete použít jako výchozí bod pro vytváření výstrah Resource Health. Tato šablona bude fungovat tak, jak bude zapsána, a pošle vám přihlašovací upozornění pro všechny nově aktivované události stavu prostředků ve všech prostředcích v rámci předplatného.

> V dolní části tohoto článku jsme také zahrnuli komplexnější šablonu výstrah, která by měla zvýšit poměr mezi signálem a Resource Health výstrahami v porovnání s touto šablonou.

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

Obecná výstraha, jako je tato jedna, se obecně nedoporučuje. Přečtěte si, jak můžeme nastavit rozsah těchto výstrah, aby se soustředit na události, které se vám zajímají níže.

### <a name="adjusting-the-alert-scope"></a>Úprava oboru výstrahy

Výstrahy Resource Health lze nakonfigurovat pro monitorování událostí ve třech různých oborech:

 * Úroveň předplatného
 * Úroveň skupiny prostředků
 * Úroveň prostředků

Šablona výstrahy je nakonfigurovaná na úrovni předplatného, ale pokud chcete, aby se vaše výstraha nakonfigurovala tak, aby vás upozornila jenom na určité prostředky, nebo prostředky v určité skupině prostředků, stačí změnit `scopes` část výše uvedené šablony.

V případě oboru na úrovni skupiny prostředků by měl oddíl rozsahy vypadat takto:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

A v rozsahu na úrovni prostředků by měl oddíl Scope vypadat takto:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Příklad: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Pokud chcete získat tento řetězec, můžete přejít na Azure Portal a podívat se na adresu URL při zobrazení prostředku Azure.

### <a name="adjusting-the-resource-types-which-alert-you"></a>Úprava typů prostředků, které vás upozorňují

Výstrahy na úrovni předplatného nebo skupiny prostředků mohou mít různé druhy prostředků. Pokud chcete omezit výstrahy jenom z určité podmnožiny typů prostředků, můžete to definovat v `condition` části šablony, například takto:

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

V tomto příkladu použijeme `anyOf` obálku, která umožňuje, aby výstraha o stavu prostředků odpovídala některé z podmínek, které zadáte, a umožňuje upozornění, která cílí na konkrétní typy prostředků.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>Úprava Resource Healthch událostí, které vás upozorňují
Když se prostředky dostanou události stavu, mohou projít řadou fází, které představují stav události stavu: `Active` , `In Progress` , `Updated` a `Resolved` .

Je možné, že budete chtít být upozorněni pouze v případě, že prostředek není v pořádku. v takovém případě budete chtít výstrahu nakonfigurovat tak, aby upozornila pouze v případě, že `status` je `Active` . Pokud ale chcete být také informováni o dalších fázích, můžete tyto podrobnosti přidat, například:

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

Pokud chcete být upozorněni na všechny čtyři fáze událostí stavu, můžete tuto podmínku odebrat dohromady a tato výstraha vás bude informovat bez ohledu na `status` vlastnost.

> [!NOTE]
> Každý oddíl "anyOf" by měl obsahovat pouze jednu hodnotu typu pole.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>Nastavení výstrah Resource Health, aby se předešlo neznámým událostem

Azure Resource Health vám může ohlásit nejnovější stav svých prostředků tím, že je průběžně monitoruje pomocí Test Runner. Příslušné nahlášené stavy stavu jsou: "dostupné", "nedostupné" a "snížené". V situacích, kdy spouštěč a prostředek Azure nemůže komunikovat, je pro prostředek hlášen stav "Neznámý", který je považován za "aktivní" událost stavu.

Pokud se ale zdroj ohlásí jako "Neznámý", je pravděpodobný, že se jeho stav od poslední přesné sestavy nezměnil. Pokud chcete eliminovat výstrahy na "neznámých" událostech, můžete tuto logiku zadat v šabloně:

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

V tomto příkladu oznamujeme jenom události, u kterých aktuální a předchozí stav nemá "Neznámý". Tato změna může být užitečná, pokud se vaše výstrahy odesílají přímo na váš mobilní telefon nebo e-mail. 

Všimněte si, že vlastnosti currentHealthStatus a previousHealthStatus v některých událostech mají hodnotu null. Například když dojde k aktualizované události, je pravděpodobný, že se stav prostředku od poslední sestavy nezměnil, jsou k dispozici pouze další informace o událostech (například příčina). Proto použití klauzule výše může vést k tomu, že se neaktivují některé výstrahy, protože hodnoty Properties. currentHealthStatus a Properties. previousHealthStatus budou nastavené na hodnotu null.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>Nastavení výstrahy, aby se předešlo událostem iniciované uživatelem

Události Resource Health mohou být aktivovány platformou iniciované uživatelem a událostmi iniciované uživatelem. Může být vhodné odeslat oznámení pouze v případě, že je událost stavu způsobena platformou Azure.

Výstrahu můžete snadno nakonfigurovat tak, aby vyfiltroval jenom tyto typy událostí:

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
Všimněte si, že v některých událostech může být pole Příčina null. To znamená, že dojde k přechodu na stav (například k dispozici k dispozici) a událost je protokolována okamžitě, aby nedocházelo k prodlevám při oznámení. Proto může použití klauzule výše způsobit, že se neaktivuje výstraha, protože hodnota vlastnosti Property. klauzule bude nastavena na hodnotu null.

## <a name="complete-resource-health-alert-template"></a>Dokončit šablonu výstrahy Resource Health

Pomocí různých úprav popsaných v předchozí části najdete ukázkovou šablonu, která je nakonfigurovaná tak, aby maximalizovala poměr hluku signálu. Pamatujte na to, že v některých událostech mohou být hodnoty vlastností currentHealthStatus, previousHealthStatus a způsobit hodnotu null.

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

Dozvíte se ale, co je pro vás nejvhodnější konfigurace, takže k vlastnímu přizpůsobení použijte výukové nástroje v této dokumentaci.

## <a name="next-steps"></a>Další kroky

Další informace o Resource Health:
-  [Přehled Azure Resource Health](Resource-health-overview.md)
-  [Typy prostředků a kontroly stavu dostupné prostřednictvím služby Azure Resource Health](resource-health-checks-resource-types.md)


Vytvořit výstrahy Service Health:
-  [Konfigurace upozornění pro Service Health](./alerts-activity-log-service-notifications-portal.md) 
-  [Schéma událostí protokolu aktivit Azure](../azure-monitor/platform/activity-log-schema.md)
