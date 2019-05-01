---
title: Získání změn prostředků
description: Pochopit postup k zjištění, kdy se prostředek změnil a získat seznam vlastností, které se změnily.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/20/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 0ae85b45dfcd80056316ed5f2099aab4057d24c8
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720341"
---
# <a name="get-resource-changes"></a>Získání změn prostředků

Získejte prostředky změnit prostřednictvím kurzu denním, konfigurace a dokonce i opakované nasazení.
Změna mohou pocházet od jednotlivce nebo pomocí automatizovaného procesu. Většina změnit chování je záměrné, ale v některých případech tomu tak není. Posledních 14 dní historie změn Graph prostředků Azure umožňují:

- Zjistit, kdy byly změny detekovány vlastností Azure Resource Manageru.
- Podívat se, které vlastnosti se v rámci této změnové události změnily.

Detekce změn a podrobnosti jsou důležité pro následující ukázkové scénáře:

- Během správy incidentů pochopit _potenciálně_ jim podobných změn. Dotazování pro události změny během na konkrétní časové okno a vyhodnotit podrobnosti o změnách.
- Zachování databáze správy konfigurace, se označuje jako CMDB aktuální. Místo aktualizace všechny prostředky a jejich celou vlastnost nastaví na frekvenci naplánované pouze najdete, co se změnilo.
- Vysvětlení, jaké vlastnosti mohou být změněna při změně stavu dodržování předpisů prostředkem. Hodnocení produktu tyto další vlastnosti může poskytnout přehled o dalších vlastností, které možná bude nutné ke správě prostřednictvím Azure Policy definition.

Tento článek ukazuje, jak shromažďování těchto informací prostřednictvím grafu prostředků sady SDK. Tyto informace na webu Azure Portal najdete v tématu Azure Policy [historii změn](../../policy/how-to/determine-non-compliance.md#change-history-preview).

> [!NOTE]
> Podrobnosti o změnách v grafu prostředků jsou vlastností Resource Manageru. Sledování změn uvnitř virtuálního počítače, naleznete v tématu Azure Automation [Change tracking](../../../automation/automation-change-tracking.md) nebo Azure Policy [konfigurace hosta pro virtuální počítače](../../policy/concepts/guest-configuration.md).

> [!IMPORTANT]
> Historie změn v grafu prostředků Azure je ve verzi Public Preview.

## <a name="find-when-changes-were-detected"></a>Najít, když byly zjištěny změny

Prvním krokem vidět, co se změnilo na prostředek je najít události změn souvisejících s tímto prostředkem v rámci časové okno. Tento krok se provádí prostřednictvím **resourceChanges** koncového bodu REST.

**ResourceChanges** koncového bodu vyžaduje dva parametry v textu požadavku:

- **resourceId**: Prostředek Azure podívat změny.
- **interval**: Vlastnost s _start_ a _end_ data, kdy se mají kontrolovat změny události používal **Zulu časové pásmo (Z)**.

Text požadavku příkladu:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-03-28T00:00:00.000Z",
        "end": "2019-03-31T00:00:00.000Z"
    }
}
```

Pomocí výše uvedených tělo požadavku, identifikátor URI rozhraní API REST pro **resourceChanges** je:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Odpověď bude vypadat nějak takto:

```json
{
    "changes": [{
            "changeId": "2db0ad2d-f6f0-4f46-b529-5c4e8c494648",
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

Každou zjištěnou změnu událost pro **resourceId** má **changeId** , které jsou jedinečné pro daný prostředek. Zatímco **changeId** řetězec může někdy obsahovat další vlastnosti, ji má pouze musí být jedinečný. Obsahuje záznam změn časy, který před a po pořízení snímků.
Došlo k události změny v určitém okamžiku v tomto okně.

## <a name="see-what-properties-changed"></a>Zjistit, co změnil vlastnosti

S **changeId** z **resourceChanges** koncového bodu, **resourceChangeDetails** koncový bod REST se pak použije k získání podrobností o události změny.

**ResourceChangeDetails** koncového bodu vyžaduje dva parametry v textu požadavku:

- **resourceId**: Prostředek Azure podívat změny.
- **changeId**: Událost jedinečné změny pro **resourceId** shromážděných z **resourceChanges**.

Text požadavku příkladu:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "53dc0515-b86b-4bc2-979b-e4694ab4a556"
}
```

Pomocí výše uvedených tělo požadavku, identifikátor URI rozhraní API REST pro **resourceChangeDetails** je:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

Odpověď bude vypadat nějak takto:

```json
{
    "changeId": "53dc0515-b86b-4bc2-979b-e4694ab4a556",
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

**beforeSnapshot** a **afterSnapshot** každá v daném čase poskytnou čas pořízení snímku a vlastnosti. Tato změna se stalo v určitém okamžiku mezi tyto snímky. Výše uvedený příklad podíváme, vidíme, že byla vlastnost, která se změnila **supportsHttpsTrafficOnly**.

Chcete-li porovnat výsledky prostřednictvím kódu programu, porovnejte **obsah** část jednotlivých snímků k určení rozdílu. Pokud srovnáte celý snímek **časové razítko** vždy zobrazovat jako rozdíl bez ohledu na se očekává.

## <a name="next-steps"></a>Další postup

- Zobrazit jazyk v aplikaci [Starter dotazy](../samples/starter.md).
- Viz advanced používá v [upřesňujících dotazů](../samples/advanced.md).
- Zjistěte, jak [materiály](../concepts/explore-resources.md).
