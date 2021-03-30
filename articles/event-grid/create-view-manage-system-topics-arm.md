---
title: Použití šablon Azure Resource Manager k vytváření systémových témat v Azure Event Grid
description: V tomto článku se dozvíte, jak pomocí šablon Azure Resource Manager vytvořit systémová témata v Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 1c8881a2d9dfca43084cc537b106e84b050a18d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86115158"
---
# <a name="create-system-topics-in-azure-event-grid-using-resource-manager-templates"></a>Vytváření systémových témat v Azure Event Grid pomocí šablon Správce prostředků
V tomto článku se dozvíte, jak vytvářet a spravovat systémová témata pomocí Správce prostředků šablon. Přehled systémových témat najdete v tématu [Systémová témata](system-topics.md).

## <a name="create-system-topic-first-and-then-create-event-subscription"></a>Nejdřív vytvořte systémové téma a pak vytvořte odběr událostí.
Chcete-li nejprve vytvořit systémové téma na zdroji Azure a pak vytvořit odběr události pro toto téma, můžete použít šablonu jako: 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string",
            "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Provide a unique name for the Blob Storage account."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Provide a location for the Blob Storage account that supports Event Grid."
            }
        },
        "eventSubName": {
            "type": "string",
            "defaultValue": "subToStorage",
            "metadata": {
                "description": "Provide a name for the Event Grid subscription."
            }
        },
        "endpoint": {
            "type": "string",
            "metadata": {
                "description": "Provide the URL for the WebHook to receive events. Create your own endpoint for events."
            }
        },
        "systemTopicName": {
            "type": "String",
            "defaultValue": "mystoragesystemtopic",
            "metadata": {
                "description": "Provide a name for the system topic."
            }
        }
    },
    "resources": [
        {
            "name": "[parameters('storageName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2017-10-01",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "accessTier": "Hot"
            }
        },
        {
            "name": "[parameters('systemTopicName')]",
            "type": "Microsoft.EventGrid/systemTopics",
            "apiVersion": "2020-04-01-preview",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[parameters('storageName')]"
            ],
            "properties": {
                "source": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageName'))]",
                "topicType": "Microsoft.Storage.StorageAccounts"
            }
        },
        {
            "type": "Microsoft.EventGrid/systemTopics/eventSubscriptions",
            "apiVersion": "2020-04-01-preview",
            "name": "[concat(parameters('systemTopicName'), '/', parameters('eventSubName'))]",
            "dependsOn": [
                "[resourceId('Microsoft.EventGrid/systemTopics', parameters('systemTopicName'))]"
            ],
            "properties": {
                "destination": {
                    "properties": {
                        "endpointUrl": "[parameters('endpoint')]"
                    },
                    "endpointType": "WebHook"
                },
                "filter": {
                    "includedEventTypes": [
                        "Microsoft.Storage.BlobCreated",
                        "Microsoft.Storage.BlobDeleted"
                    ]
                }
            }
        }
    ]
}
```

Pokyny k vytváření systémových témat a odběrů pomocí šablon Správce prostředků najdete v tématu [Směrování událostí služby Blob Storage do webového koncového bodu pomocí šablony Azure Resource Manager](blob-event-quickstart-template.md) . 

## <a name="create-system-topic-while-creating-an-event-subscription"></a>Vytvořit systémové téma při vytváření odběru události 
Chcete-li vytvořit systémové téma implicitně při vytváření odběru událostí ve zdroji Azure, můžete použít následující šablonu:

``` json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string",
            "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Provide a unique name for the Blob Storage account."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Provide a location for the Blob Storage account that supports Event Grid."
            }
        },
        "eventSubName": {
            "type": "string",
            "defaultValue": "subToStorage",
            "metadata": {
                "description": "Provide a name for the Event Grid subscription."
            }
        },
        "endpoint": {
            "type": "string",
            "metadata": {
                "description": "Provide the URL for the WebHook to receive events. Create your own endpoint for events."
            }
        }
    },
    "resources": [
        {
            "name": "[parameters('storageName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2017-10-01",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "accessTier": "Hot"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
            "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
            "apiVersion": "2018-01-01",
            "dependsOn": [
                "[parameters('storageName')]"
            ],
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "[parameters('endpoint')]"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false,
                    "includedEventTypes": [
                        "All"
                    ]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Další kroky
V části [Systémová témata v Azure Event Grid](system-topics.md) najdete další informace o systémových tématech a typech témat, které podporuje Azure Event Grid. 
