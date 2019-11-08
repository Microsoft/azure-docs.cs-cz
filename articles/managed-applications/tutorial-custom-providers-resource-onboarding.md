---
title: Připojování zdrojů pomocí vlastních zprostředkovatelů Azure
description: Prostředek k registraci prostřednictvím vlastních zprostředkovatelů vám umožní manipulovat s existujícími prostředky Azure a roztáhnout je.
services: managed-applications
ms.service: managed-applications
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 45086987d5ba3a619028ced798712f2870c6d487
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826790"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Kurz: zprovoznění prostředků pomocí vlastních zprostředkovatelů Azure

V tomto kurzu nasadíte do Azure vlastního poskytovatele prostředků, který rozšiřuje rozhraní Azure Resource Manager API o typ prostředku Microsoft. CustomProviders/Associations. V tomto kurzu se dozvíte, jak můžete roztáhnout stávající prostředky, které se nacházejí mimo skupinu prostředků, ve které se nachází instance vlastního zprostředkovatele. V tomto kurzu je vlastní poskytovatel prostředků napájený pomocí aplikace logiky Azure, ale můžete použít jakýkoli veřejný koncový bod rozhraní API.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete znát tyto informace:

* Možnosti [vlastních zprostředkovatelů Azure](custom-providers-overview.md).
* Základní informace o [připojování prostředků s vlastními poskytovateli](concepts-custom-providers-resourceonboarding.md).

## <a name="get-started-with-resource-onboarding"></a>Začínáme s připojováním zdrojů

V tomto kurzu je potřeba nasadit dva části: vlastní zprostředkovatel a přidružení. Pro usnadnění procesu můžete volitelně použít jedinou šablonu, která nasadí obojí.

Tato šablona bude používat tyto prostředky:

* Microsoft. CustomProviders/resourceProviders
* Microsoft. Logic/Workflows
* Microsoft. CustomProviders/Associations

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "allowedValues": [
                "australiaeast",
                "eastus",
                "westeurope"
            ],
            "metadata": {
                "description": "Location for the resources."
            }
        },
        "logicAppName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the logic app to be created."
            }
        },
        "customResourceProviderName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the custom provider to be created."
            }
        },
        "customResourceProviderId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of an existing custom provider. Provide this to skip deployment of new logic app and custom provider."
            }
        },
        "associationName": {
            "type": "string",
            "defaultValue": "myAssociationResource",
            "metadata": {
                "description": "Name of the custom resource that is being created."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "condition": "[empty(parameters('customResourceProviderId'))]",
            "name": "customProviderInfrastructureTemplate",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "logicAppName": {
                            "type": "string",
                            "defaultValue": "[parameters('logicAppName')]"
                        }
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Logic/workflows",
                            "apiVersion": "2017-07-01",
                            "name": "[parameters('logicAppName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "state": "Enabled",
                                "definition": {
                                    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
                                    "actions": {
                                        "Switch": {
                                            "cases": {
                                                "Case": {
                                                    "actions": {
                                                        "CreateCustomResource": {
                                                            "inputs": {
                                                                "body": {
                                                                    "properties": "@addProperty(triggerBody().Body['properties'], 'myDynamicProperty', 'myDynamicValue')"
                                                                },
                                                                "statusCode": 200
                                                            },
                                                            "kind": "Http",
                                                            "type": "Response"
                                                        }
                                                    },
                                                    "case": "CREATE"
                                                }
                                            },
                                            "default": {
                                                "actions": {
                                                    "DefaultHttpResponse": {
                                                        "inputs": {
                                                            "statusCode": 200
                                                        },
                                                        "kind": "Http",
                                                        "type": "Response"
                                                    }
                                                }
                                            },
                                            "expression": "@triggerBody().operationType",
                                            "type": "Switch"
                                        }
                                    },
                                    "contentVersion": "1.0.0.0",
                                    "outputs": {},
                                    "parameters": {},
                                    "triggers": {
                                        "CustomProviderWebhook": {
                                            "inputs": {
                                                "schema": {}
                                            },
                                            "kind": "Http",
                                            "type": "Request"
                                        }
                                    }
                                }
                            }
                        },
                        {
                            "type": "Microsoft.CustomProviders/resourceProviders",
                            "apiVersion": "2018-09-01-preview",
                            "name": "[parameters('customResourceProviderName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "resourceTypes": [
                                    {
                                        "name": "associations",
                                        "mode": "Secure",
                                        "routingType": "Webhook,Cache,Extension",
                                        "endpoint": "[[listCallbackURL(concat(resourceId('Microsoft.Logic/workflows', parameters('logicAppName')), '/triggers/CustomProviderWebhook'), '2017-07-01').value]"
                                    }
                                ]
                            }
                        }
                    ],
                    "outputs": {
                        "customProviderResourceId": {
                            "type": "string",
                            "value": "[resourceId('Microsoft.CustomProviders/resourceProviders', parameters('customResourceProviderName'))]"
                        }
                    }
                }
            }
        },
        {
            "type": "Microsoft.CustomProviders/associations",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('associationName')]",
            "location": "global",
            "properties": {
                "targetResourceId": "[if(empty(parameters('customResourceProviderId')), reference('customProviderInfrastructureTemplate').outputs.customProviderResourceId.value, parameters('customResourceProviderId'))]",
                "myCustomInputProperty": "myCustomInputValue",
                "myCustomInputObject": {
                    "Property1": "Value1"
                }
            }
        }
    ],
    "outputs": {
        "associationResource": {
            "type": "object",
            "value": "[reference(parameters('associationName'), '2018-09-01-preview', 'Full')]"
        }
    }
}
```

### <a name="deploy-the-custom-provider-infrastructure"></a>Nasazení infrastruktury vlastního zprostředkovatele

První část šablony nasadí infrastrukturu vlastního poskytovatele. Tato infrastruktura definuje účinek prostředku přidružení. Pokud nejste obeznámeni s vlastními poskytovateli, přečtěte si téma [základy vlastních](custom-providers-overview.md)poskytovatelů.

Pojďme nasadit infrastrukturu vlastního poskytovatele. Můžete buď zkopírovat, Uložit a nasadit předchozí šablonu, nebo postupovat podle potřeby a nasadit infrastrukturu pomocí Azure Portal.

1. Přejděte na [portál Azure](https://portal.azure.com).

2. Vyhledejte **šablony** ve **všech službách** nebo pomocí hlavního vyhledávacího pole:

   ![Hledat šablony](media/custom-providers-resource-onboarding/templates.png)

3. V podokně **šablony** vyberte **Přidat** :

   ![Vyberte Přidat](media/custom-providers-resource-onboarding/templatesadd.png)

4. V části **Obecné**zadejte **název** a **Popis** nové šablony:

   ![Název a popis šablony](media/custom-providers-resource-onboarding/templatesdescription.png)

5. Vytvořte šablonu Správce prostředků zkopírováním šablony JSON z části Začínáme s připojováním zdrojů v tomto článku:

   ![Vytvoření šablony Resource Manageru](media/custom-providers-resource-onboarding/templatesarmtemplate.png)

6. Vyberte **Přidat** a vytvořte šablonu. Pokud se nová šablona nezobrazí, vyberte **aktualizovat**.

7. Vyberte nově vytvořenou šablonu a pak vyberte **nasadit**:

   ![Vyberte novou šablonu a pak vyberte nasadit.](media/custom-providers-resource-onboarding/templateselectspecific.png)

8. Zadejte nastavení pro požadovaná pole a pak vyberte předplatné a skupinu prostředků. Pole **ID vlastního poskytovatele prostředků** můžete nechat prázdné.

   | Název nastavení | Povinné? | Popis |
   | ------------ | -------- | ----------- |
   | Umístění | Ano | Umístění prostředků v šabloně. |
   | Název aplikace logiky | Ne | Název aplikace logiky |
   | Název vlastního poskytovatele prostředků | Ne | Název vlastního poskytovatele prostředků |
   | ID vlastního poskytovatele prostředků | Ne | Stávající vlastní poskytovatel prostředků, který podporuje prostředek přidružení. Pokud sem zadáte hodnotu, aplikace logiky a nasazení vlastního poskytovatele se přeskočí. |
   | Název přidružení | Ne | Název prostředku přidružení. |

   Ukázkové parametry:

   ![Zadejte parametry šablony](media/custom-providers-resource-onboarding/templatescustomprovider.png)

9. Přejít na nasazení a počkat na jeho dokončení. Měl by se zobrazit něco podobného jako na následujícím snímku obrazovky. Měl by se zobrazit nový prostředek přidružení jako výstup:

   ![Úspěšné nasazení](media/custom-providers-resource-onboarding/customproviderdeployment.png)

   Tady je skupina prostředků se zvolenými **skrytými typy zobrazit** :

   ![Nasazení vlastního zprostředkovatele](media/custom-providers-resource-onboarding/showhidden.png)

10. Prozkoumejte kartu **historie spuštění** aplikace logiky a zobrazte volání pro vytvoření přidružení:

    ![Historie spuštění aplikace logiky](media/custom-providers-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>Nasazení dalších přidružení

Po nastavení infrastruktury vlastního poskytovatele můžete snadno nasadit více přidružení. Skupina prostředků pro další přidružení nemusí být stejná jako skupina prostředků, ve které jste nasadili infrastrukturu vlastního poskytovatele. Chcete-li vytvořit přidružení, je nutné mít oprávnění Microsoft. CustomProviders/resourceproviders/Write pro zadané ID vlastního poskytovatele prostředků.

1. Ve skupině prostředků v předchozím nasazení přejdete na prostředek **Microsoft. CustomProviders/resourceProviders** vlastního zprostředkovatele. Bude nutné zaškrtnout políčko **Zobrazit skryté typy** :

   ![Přejít k prostředku](media/custom-providers-resource-onboarding/showhidden.png)

2. Zkopírujte vlastnost ID prostředku vlastního zprostředkovatele.

3. Vyhledejte **šablony** ve **všech službách** nebo pomocí hlavního vyhledávacího pole:

   ![Hledat šablony](media/custom-providers-resource-onboarding/templates.png)

4. Vyberte dříve vytvořenou šablonu a pak vyberte **nasadit**:

   ![Vyberte dříve vytvořenou šablonu a pak vyberte nasadit.](media/custom-providers-resource-onboarding/templateselectspecific.png)

5. Zadejte nastavení pro požadovaná pole a pak vyberte předplatné a jinou skupinu prostředků. Pro nastavení **ID vlastního poskytovatele prostředků** zadejte ID prostředku, které jste zkopírovali z vlastního poskytovatele, kterého jste nasadili dříve.

6. Přejít na nasazení a počkat na jeho dokončení. Měl by nyní nasazovat pouze nové prostředky přidružení:

   ![Nový prostředek přidružení](media/custom-providers-resource-onboarding/createdassociationresource.png)

Pokud chcete, můžete se vrátit k **historii spuštění** aplikace logiky a podívat se, že aplikace logiky provedla jiné volání. Aplikaci logiky můžete aktualizovat a rozšířit tak další funkce pro každé vytvořené přidružení.

## <a name="getting-help"></a>Získání nápovědy

Pokud máte dotazy týkající se vlastních zprostředkovatelů Azure, zkuste se na ně zeptat na [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-custom-providers). Podobná otázka již mohla být zodpovězena, před odesláním proto nejprve proveďte kontrolu. Přidejte `azure-custom-providers` značek, abyste získali rychlou odezvu.

