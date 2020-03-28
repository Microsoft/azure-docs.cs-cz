---
title: Výuka – zaškolení zdrojů
description: Přiobování prostředků prostřednictvím vlastních poskytovatelů umožňuje manipulovat a rozšiřovat stávající prostředky Azure.
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 22d1dcd997a4ddb94aba184c5dace4c00509054d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649936"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Kurz: Přizapisování prostředků pomocí vlastních zprostředkovatelů Azure

V tomto kurzu nasadíte do Azure vlastního poskytovatele prostředků, který rozšiřuje rozhraní API Azure Resource Manager s typem prostředků Microsoft.CustomProviders/associations. Kurz ukazuje, jak rozšířit existující prostředky, které jsou mimo skupinu prostředků, kde je umístěna instance vlastního zprostředkovatele. V tomto kurzu je poskytovatel vlastních prostředků napájen pomocí aplikace logiky Azure, ale můžete použít libovolný koncový bod veřejného rozhraní API.

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento výukový program, musíte vědět:

* Možnosti [vlastních zprostředkovatelů Azure](overview.md).
* Základní informace o [připojení zdrojů u vlastních poskytovatelů](concepts-resource-onboarding.md).

## <a name="get-started-with-resource-onboarding"></a>Začínáme s připojením zdrojů

V tomto kurzu existují dva kusy, které je třeba nasadit: vlastní zprostředkovatele a přidružení. Chcete-li proces usnadnit, můžete volitelně použít jednu šablonu, která nasazuje obojí.

Šablona bude používat tyto prostředky:

* Microsoft.CustomProviders/resourceProviders
* Microsoft.Logic/pracovní postupy
* Microsoft.CustomProviders/associations

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

### <a name="deploy-the-custom-provider-infrastructure"></a>Nasazení vlastní infrastruktury zprostředkovatele

První část šablony nasazuje vlastní infrastrukturu zprostředkovatele. Tato infrastruktura definuje účinek prostředku přidružení. Pokud nejste obeznámeni s vlastními poskytovateli, přečtěte [si informace o základech vlastního zprostředkovatele](overview.md).

Pojďme nasadit vlastní infrastrukturu zprostředkovatele. Buď zkopírujte, uložte a nasaďte předchozí šablonu, nebo postupujte podle pokynů a nasaďte infrastrukturu pomocí portálu Azure.

1. Přejděte na [portál Azure](https://portal.azure.com).

2. Vyhledejte **šablony** ve **všech službách** nebo pomocí hlavního vyhledávacího pole:

   ![Hledání šablon](media/tutorial-resource-onboarding/templates.png)

3. V podokně **Šablony vyberte** **Přidat:**

   ![Vyberte Přidat.](media/tutorial-resource-onboarding/templatesadd.png)

4. V části **Obecné**zadejte **název** a **popis** nové šablony:

   ![Název a popis šablony](media/tutorial-resource-onboarding/templatesdescription.png)

5. Vytvořte šablonu Správce prostředků zkopírováním v šabloně JSON z části Začínáme s registrací zdrojů v tomto článku:

   ![Vytvoření šablony Resource Manageru](media/tutorial-resource-onboarding/templatesarmtemplate.png)

6. Chcete-li vytvořit šablonu, vyberte **Přidat.** Pokud se nová šablona nezobrazí, vyberte **Aktualizovat**.

7. Vyberte nově vytvořenou šablonu a pak vyberte **Nasadit**:

   ![Vyberte novou šablonu a pak vyberte Nasadit](media/tutorial-resource-onboarding/templateselectspecific.png)

8. Zadejte nastavení požadovaných polí a vyberte odběr a skupinu prostředků. Pole **Id vlastního zprostředkovatele prostředků** můžete ponechat prázdné.

   | Název nastavení | Povinné? | Popis |
   | ------------ | -------- | ----------- |
   | Umístění | Ano | Umístění prostředků v šabloně. |
   | Název aplikace logiky | Ne | Název aplikace logiky. |
   | Název vlastního zprostředkovatele prostředků | Ne | Název vlastního zprostředkovatele prostředků. |
   | Id vlastního zprostředkovatele prostředků | Ne | Existující vlastní zprostředkovatel prostředků, který podporuje prostředek přidružení. Pokud zde zadáte hodnotu, aplikace logiky a nasazení vlastního zprostředkovatele budou přeskočeny. |
   | Název přidružení | Ne | Název prostředku přidružení. |

   Parametry vzorku:

   ![Zadat parametry šablony](media/tutorial-resource-onboarding/templatescustomprovider.png)

9. Přejděte na nasazení a počkejte, až bude dokončeno. Měli byste vidět něco jako následující snímek obrazovky. Nový prostředek přidružení byste měli vidět jako výstup:

   ![Úspěšné nasazení](media/tutorial-resource-onboarding/customproviderdeployment.png)

   Tady je skupina prostředků s vybranou možností **Zobrazit skryté typy:**

   ![Vlastní nasazení zprostředkovatele](media/tutorial-resource-onboarding/showhidden.png)

10. Prozkoumejte kartu Historie aplikace **logiky A** podívejte se na volání pro vytvoření přidružení:

    ![Aplikace logiky Spustí historii](media/tutorial-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>Nasazení dalších přidružení

Po nastavení vlastní infrastruktury zprostředkovatele můžete snadno nasadit další přidružení. Skupina prostředků pro další přidružení nemusí být stejná jako skupina prostředků, ve které jste nasadili vlastní infrastrukturu zprostředkovatele. Chcete-li vytvořit přidružení, musíte mít oprávnění Microsoft.CustomProviders/resourceproviders/write na zadané ID vlastního zprostředkovatele prostředků.

1. Přejděte na vlastního zprostředkovatele **Microsoft.CustomProviders/resourceProviders** prostředek ve skupině prostředků předchozího nasazení. Budete muset zaškrtnout políčko **Zobrazit skryté typy:**

   ![Přejít na zdroj](media/tutorial-resource-onboarding/showhidden.png)

2. Zkopírujte vlastnost ID prostředku vlastního zprostředkovatele.

3. Vyhledejte **šablony** ve **všech službách** nebo pomocí hlavního vyhledávacího pole:

   ![Hledání šablon](media/tutorial-resource-onboarding/templates.png)

4. Vyberte dříve vytvořenou šablonu a pak vyberte **Nasadit**:

   ![Vyberte dříve vytvořenou šablonu a pak vyberte Nasadit](media/tutorial-resource-onboarding/templateselectspecific.png)

5. Zadejte nastavení požadovaných polí a vyberte předplatné a jinou skupinu prostředků. Pro nastavení **ID vlastního zprostředkovatele prostředků** zadejte ID prostředku, které jste zkopírovali z vlastního zprostředkovatele, kterého jste nasadili dříve.

6. Přejděte na nasazení a počkejte, až bude dokončeno. Nyní by měl nasadit pouze nový prostředek přidružení:

   ![Nový zdroj přidružení](media/tutorial-resource-onboarding/createdassociationresource.png)

Pokud chcete, můžete se vrátit do **historie spuštění** aplikace logiky a uvidíte, že bylo provedeno další volání do aplikace logiky. Můžete aktualizovat aplikaci logiky rozšířit další funkce pro každé vytvořené přidružení.

## <a name="getting-help"></a>Získání nápovědy

Pokud máte dotazy týkající se vlastních poskytovatelů Azure, zkuste se jich zeptat na [přetečení zásobníku](https://stackoverflow.com/questions/tagged/azure-custom-providers). Podobná otázka již mohla být zodpovězena, takže nejprve zkontrolujte před odesláním. Přidejte `azure-custom-providers` značku, abyste získali rychlou odpověď!

