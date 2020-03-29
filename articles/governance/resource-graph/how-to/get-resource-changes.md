---
title: Získání změn prostředků
description: Zjistěte, jak najít, kdy byl prostředek změněn, získat seznam vlastností, které se změnily a vyhodnotit rozdíly.
ms.date: 10/09/2019
ms.topic: how-to
ms.openlocfilehash: 9504ac77fc4a3b03434912cc65284e2001df6e03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873025"
---
# <a name="get-resource-changes"></a>Získání změn prostředků

Zdroje se mění v průběhu každodenního používání, rekonfigurace a dokonce i opětovného nasazení.
Změna může pocházet od jednotlivce nebo automatizovaným procesem. Většina změn je záměrná, ale někdy není. Díky historii změn za posledních 14 dní umožňuje Azure Resource Graph:

- Zjištění, kdy byly zjištěny změny ve vlastnosti Azure Resource Manager
- Pro každou změnu prostředků, viz podrobnosti o změně vlastnosti
- Zobrazit úplné porovnání prostředku před a po zjištěné změně

Zjišťování změn a podrobnosti jsou cenné pro následující ukázkové scénáře:

- Během správy incidentů pochopit _potenciálně_ související změny. Dotaz na události změny během určitého časového období a vyhodnotit podrobnosti o změně.
- Udržování databáze správy konfigurace, známé jako CMDB, aktuální. Namísto aktualizace všech prostředků a jejich úplné sady vlastností na naplánované frekvenci, jen získat to, co se změnilo.
- Pochopení, jaké další vlastnosti mohly být změněny, když prostředek změnil stav dodržování předpisů. Vyhodnocení těchto dalších vlastností může poskytnout přehled o dalších vlastnostech, které může být nutné spravovat pomocí definice zásad Azure.

Tento článek ukazuje, jak shromažďovat tyto informace prostřednictvím sady SDK graph prostředků. Tyto informace najdete na webu Azure Portal najdete v tématu [Historie změn](../../policy/how-to/determine-non-compliance.md#change-history-preview) zásad Azure nebo [Historie změn](../../../azure-monitor/platform/activity-log-view.md#azure-portal)protokolu aktivit Azure .
Podrobnosti o změnách vašich aplikací z vrstvy infrastruktury až po nasazení aplikací najdete v tématu [Použití analýzy změn aplikací (preview)](../../../azure-monitor/app/change-analysis.md) v Azure Monitoru.

> [!NOTE]
> Podrobnosti o změně v grafu prostředků jsou určeny pro vlastnosti Správce prostředků. Sledování změn uvnitř virtuálního počítače najdete v tématu Azure Automation [sledování změn](../../../automation/automation-change-tracking.md) nebo [konfigurace hosta](../../policy/concepts/guest-configuration.md)zásad Azure pro virtuální počítače .

> [!IMPORTANT]
> Historie změn v Azure Resource Graph je ve veřejné verzi Preview.

## <a name="find-detected-change-events-and-view-change-details"></a>Vyhledání zjištěných událostí změn a zobrazení podrobností o změně

Prvním krokem při zobrazení změny na prostředku je najít události změny související s tímto zdrojem v časovém období. Každá událost změny také obsahuje podrobnosti o tom, co se změnilo na prostředku. Tento krok se provádí prostřednictvím **resourceChanges** REST koncový bod.

Koncový bod **resourceChanges** přijímá následující parametry v těle požadavku:

- **resourceId** \[\]povinné : Prostředek Azure hledat změny na.
- **interval** \[Požadovaný\]interval : Vlastnost s _počátečním_ a _koncovým_ datem, kdy se má zkontrolovat událost změny pomocí **časového pásma Zulu (Z)**.
- **fetchPropertyChanges** (volitelné): Logická vlastnost, která nastaví, pokud objekt odpovědi obsahuje změny vlastností.

Příklad textu požadavku:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-09-28T00:00:00.000Z",
        "end": "2019-09-29T00:00:00.000Z"
    },
    "fetchPropertyChanges": true
}
```

S výše uvedené tělo požadavku ROZHRANÍ REST API pro **resourceChanges** je:

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

Odpověď vypadá podobně jako v tomto příkladu:

```json
{
    "changes": [
        {
            "changeId": "{\"beforeId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"beforeTime\":\"2019-09-28T00:45:35.012Z\",\"afterId\":\"6178968e-981e-4dac-ac37-340ee73eb577\",\"afterTime\":\"2019-09-28T00:52:53.371Z\"}",
            "beforeSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "afterSnapshot": {
                "snapshotId": "6178968e-981e-4dac-ac37-340ee73eb577",
                "timestamp": "2019-09-28T00:52:53.371Z"
            },
            "changeType": "Create"
        },
        {
            "changeId": "{\"beforeId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"beforeTime\":\"2019-09-28T00:43:38.366Z\",\"afterId\":\"3262e382-9f73-4866-a2e9-9d9dbee6a796\",\"afterTime\":\"2019-09-28T00:45:35.012Z\"}",
            "beforeSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "afterSnapshot": {
                "snapshotId": "3262e382-9f73-4866-a2e9-9d9dbee6a796",
                "timestamp": "2019-09-28T00:45:35.012Z"
            },
            "changeType": "Delete"
        },
        {
            "changeId": "{\"beforeId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"beforeTime\":\"2019-09-28T00:43:15.518Z\",\"afterId\":\"a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c\",\"afterTime\":\"2019-09-28T00:43:38.366Z\"}",
            "beforeSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "afterSnapshot": {
                "snapshotId": "a00f5dac-86a1-4d86-a1c5-a9f7c8147b7c",
                "timestamp": "2019-09-28T00:43:38.366Z"
            },
            "propertyChanges": [
                {
                    "propertyName": "tags.org",
                    "afterValue": "compute",
                    "changeCategory": "User",
                    "changeType": "Insert"
                },
                {
                    "propertyName": "tags.team",
                    "afterValue": "ARG",
                    "changeCategory": "User",
                    "changeType": "Insert"
                }
            ],
            "changeType": "Update"
        },
        {
            "changeId": "{\"beforeId\":\"19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268\",\"beforeTime\":\"2019-09-28T00:42:46.839Z\",\"afterId\":\"b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c\",\"afterTime\":\"2019-09-28T00:43:15.518Z\"}",
            "beforeSnapshot": {
                "snapshotId": "19d12ab1-6ac6-4cd7-a2fe-d453a8e5b268",
                "timestamp": "2019-09-28T00:42:46.839Z"
            },
            "afterSnapshot": {
                "snapshotId": "b37a90d1-7ebf-41cd-8766-eb95e7ee4f1c",
                "timestamp": "2019-09-28T00:43:15.518Z"
            },
            "propertyChanges": [{
                "propertyName": "tags.cgtest",
                "afterValue": "hello",
                "changeCategory": "User",
                "changeType": "Insert"
            }],
            "changeType": "Update"
        }
    ]
}
```

Každá zjištěná událost změny pro **id resourceId** má následující vlastnosti:

- **changeId** - Tato hodnota je jedinečná pro tento prostředek. Zatímco **řetězec changeId** může někdy obsahovat jiné vlastnosti, je pouze zaručeno, že je jedinečný.
- **beforeSnapshot** - Obsahuje **snapshotId** a **časové razítko** snímku prostředku, který byl pořízen před zjištěním změny.
- **afterSnapshot** - Obsahuje **snapshotId** a **časové razítko** snímku prostředku, který byl pořízen po zjištění změny.
- **changeType** - Popisuje typ změny zjištěna pro celý záznam změny mezi **beforeSnapshot** a **afterSnapshot**. Hodnoty jsou: _Vytvořit_, _Aktualizovat_a _Odstranit_. Pole vlastnosti **propertyChanges** je zahrnuto pouze v **případě, že changeType** je _Update_.
- **propertyChanges** - Toto pole vlastností podrobně popisuje všechny vlastnosti prostředku, které byly aktualizovány mezi **beforeSnapshot** a **afterSnapshot**:
  - **propertyName** - Název vlastnosti prostředku, která byla změněna.
  - **changeCategory** - Popisuje, co provedlzměnu. Hodnoty jsou: _Systém_ a _uživatel_.
  - **changeType** - Popisuje typ změny zjištěné pro jednotlivé vlastnosti prostředku.
    Hodnoty jsou: _Vložit_, _Aktualizovat_, _Odebrat_.
  - **beforeValue** - Hodnota vlastnosti prostředku v **beforeSnapshot**. Nezobrazí se, když je **changeType** _Insert_.
  - **afterValue** - Hodnota vlastnosti prostředku v **afterSnapshot**. Nezobrazí se, když je **changeType** _Odebrat_.

## <a name="compare-resource-changes"></a>Porovnat změny zdrojů

S **changeId** z **resourceChanges** koncového **bodu, prostředekChangeDetails** REST koncový bod se pak používá k získání před a po snímky prostředku, který byl změněn.

Koncový bod **resourceChangeDetails** vyžaduje dva parametry v těle požadavku:

- **resourceId**: Prostředek Azure porovnat změny na.
- **changeId**: Jedinečná událost změny pro **resourceId** shromážděné z **resourceChanges**.

Příklad textu požadavku:

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"afterTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

S výše uvedeným tělem požadavku je identifikátor URI rozhraní REST API pro **resourceChangeDetails:**

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

**beforeSnapshot** a **afterSnapshot** každý dát čas snímek byl pořízen a vlastnosti v té době. Ke změně došlo v určitém okamžiku mezi těmito snímky. Při pohledu na výše uvedený příklad můžeme vidět, že vlastnost, která se změnila byla **supportsHttpsTrafficOnly**.

Chcete-li porovnat výsledky, použijte **vlastnost changes** v **resourceChanges** nebo vyhodnoťte **obsahovou** část každého snímku v **resourceChangeDetails** k určení rozdílu. Pokud porovnáte snímky, **časové razítko** se vždy zobrazí jako rozdíl, přestože se očekává.

## <a name="next-steps"></a>Další kroky

- Podívejte se na jazyk, který se používá v [dotazech Starter](../samples/starter.md).
- Zobrazení pokročilých použití v [rozšířených dotazech](../samples/advanced.md).
- Přečtěte si další informace o tom, jak [prozkoumat zdroje](../concepts/explore-resources.md).