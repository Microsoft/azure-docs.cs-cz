---
title: Získání změn prostředků
description: Zjistěte, jak najít, kdy byl prostředek změněn, a získejte seznam vlastností, které se změnily.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 2027f56d44be14895a40550d78a79d9e9dda9d97
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980275"
---
# <a name="get-resource-changes"></a>Získání změn prostředků

Prostředky se v průběhu každodenního použití, změny konfigurace a dokonce opětovného nasazení mění.
Změna může pocházet z individuálního nebo automatizovaného procesu. Většina změn je záměrné, ale někdy ne. V posledních 14 dnech historie změn vám Azure Resource Graph umožní:

- Zjistit, kdy byly změny detekovány vlastností Azure Resource Manageru.
- Podívat se, které vlastnosti se v rámci této změnové události změnily.

Zjišťování změn a podrobnosti jsou užitečné v následujících ukázkových scénářích:

- Během správy incidentů získat informace o _potenciálně_ souvisejících změnách. Dotaz na události změny během konkrétního časového období a vyhodnocení podrobností o změně.
- Udržování databáze správy konfigurace označované jako CMDB, aktuální. Místo aktualizace všech prostředků a jejich úplných vlastností u naplánované četnosti Získejte jenom to, co se změnilo.
- Informace o tom, jaké další vlastnosti se mohly změnit, když prostředek změnil stav dodržování předpisů. Vyhodnocení těchto dalších vlastností může poskytnout přehled o dalších vlastnostech, které mohou být nutné ke správě pomocí definice Azure Policy.

Tento článek ukazuje, jak shromáždit tyto informace prostřednictvím sady SDK pro graf prostředků. Pokud chcete zobrazit tyto informace v Azure Portal, přečtěte si téma historie [změn](../../policy/how-to/determine-non-compliance.md#change-history-preview) v Azure Policy nebo [historie změn protokolu aktivit](../../../azure-monitor/platform/activity-log-view.md#azure-portal)Azure.

> [!NOTE]
> Podrobnosti o změně v grafu prostředků jsou pro Správce prostředků vlastnosti. Informace o sledování změn v rámci virtuálního počítače Azure Automation najdete v tématu Konfigurace služby [Change Tracking](../../../automation/automation-change-tracking.md) nebo [Host Azure Policy na virtuálních](../../policy/concepts/guest-configuration.md)počítačích.

> [!IMPORTANT]
> Historie změn v grafu prostředků Azure je Public Preview.

## <a name="find-when-changes-were-detected"></a>Najít, kdy byly zjištěny změny

Prvním krokem při zobrazení toho, co se změnilo u prostředku, je najít události změny týkající se tohoto prostředku v časovém intervalu. Tento krok se provádí prostřednictvím koncového bodu **resourceChanges** REST.

Koncový bod **resourceChanges** vyžaduje v textu žádosti dva parametry:

- **ResourceID**: prostředek Azure, na kterém se mají hledat změny.
- **interval**: vlastnost s _počátečním_ a _koncovým_ datem, kdy se má kontrolovat událost změny pomocí **časového pásma Zulu (Z)** .

Příklad textu žádosti:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-03-28T00:00:00.000Z",
        "end": "2019-03-31T00:00:00.000Z"
    }
}
```

S výše uvedeným textem žádosti je REST API identifikátor URI pro **resourceChanges** :

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Odpověď vypadá podobně jako v tomto příkladu:

```json
{
    "changes": [{
            "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
            "beforeSnapshot": {
                "timestamp": "2019-03-29T01:32:05.993Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-29T01:54:24.42Z"
            }
        },
        {
            "changeId": "9dc352cb-b7c1-4198-9eda-e5e3ed66aec8",
            "beforeSnapshot": {
                "timestamp": "2019-03-28T10:30:19.68Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-28T21:12:31.337Z"
            }
        }
    ]
}
```

Každá zjištěná událost změny pro **ResourceID** má **changeId** , který je pro tento prostředek jedinečný. I když řetězec **changeId** může někdy obsahovat jiné vlastnosti, je zaručený pouze jedinečný. Záznam změny zahrnuje časy, ve kterých byly provedeny snímky před a po.
K události změny došlo v určitém okamžiku v tomto časovém intervalu.

## <a name="see-what-properties-changed"></a>Zobrazit změněné vlastnosti

Pomocí **changeId** z koncového bodu **resourceChanges** se pak koncový bod **resourceChangeDetails** REST používá k získání specifických změn události změny.

Koncový bod **resourceChangeDetails** vyžaduje v textu žádosti dva parametry:

- **ResourceID**: prostředek Azure, na kterém se mají hledat změny.
- **changeId**: jedinečná událost změny pro ID služby **ResourceID** shromážděná z **resourceChanges**.

Příklad textu žádosti:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

S výše uvedeným textem žádosti je REST API identifikátor URI pro **resourceChangeDetails** :

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

Odpověď vypadá podobně jako v tomto příkladu:

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**beforeSnapshot** a **afterSnapshot** každý z nich poskytují čas pořízení snímku a vlastnosti v daném čase. Změna nastala v určitém bodě mezi těmito snímky. V předchozím příkladu vidíte, že vlastnost, která se změnila, byla **supportsHttpsTrafficOnly**.

Chcete-li porovnat výsledky prostřednictvím kódu programu, porovnejte část **obsahu** každého snímku a určete tak rozdíl. Pokud porovnáte celý snímek, **časové razítko** se vždycky zobrazí jako rozdíl, i když se očekává.

## <a name="next-steps"></a>Další kroky

- Podívejte se na jazyk používaný v [počátečních dotazech](../samples/starter.md).
- Viz rozšířená použití v [rozšířených dotazech](../samples/advanced.md).
- Naučte se [prozkoumat prostředky](../concepts/explore-resources.md).