---
title: Připojování zdrojů pomocí vlastních zprostředkovatelů Azure
description: Připojování prostředků prostřednictvím vlastních zprostředkovatelů umožňuje manipulaci s existujícími prostředky Azure a jejich rozšíření.
services: managed-applications
ms.service: managed-applications
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: c722b4dc3219f76f8c7c571af3613996fec9e69c
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608677"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Kurz: zprovoznění prostředků pomocí vlastních zprostředkovatelů Azure

V tomto kurzu nasadíte vlastního poskytovatele prostředků do Azure, který rozšiřuje rozhraní Azure Resource Manager API o typ prostředku Microsoft. CustomProviders/Associations. V této ukázce se dozvíte, jak můžete roztáhnout stávající prostředky, které jsou mimo skupinu prostředků, ve které je instance vlastního zprostředkovatele. V této ukázce je vlastní poskytovatel prostředků napájený pomocí aplikace logiky Azure, ale dá se použít libovolný veřejný koncový bod rozhraní API.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete znát tyto informace:

* Možnosti [vlastního zprostředkovatele Azure](custom-providers-overview.md) .
* [Připojování prostředků s vlastními poskytovateli](concepts-custom-providers-resourceonboarding.md).

## <a name="getting-started-with-resource-onboarding"></a>Začínáme s připojováním zdrojů

Pro tuto ukázku existují dva části, které je třeba nasadit: vlastní zprostředkovatel a přidružení. Pro zjednodušení ukázky existuje jedna šablona, která může volitelně nasadit obojí.

Tato šablona bude používat následující prostředky:

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

První část šablony nasazuje infrastrukturu vlastního poskytovatele. Tato infrastruktura definuje účinek prostředku Associations. Pokud vlastní zprostředkovatelé neznáte, přečtěte si téma [základy vlastních](custom-providers-overview.md)poskytovatelů.

Pojďme nasadit infrastrukturu vlastního poskytovatele, buď si můžete zkopírovat, Uložit a nasadit výše uvedenou šablonu, nebo postupovat společně a nasadit pomocí Azure Portal.

1. Otevřete Azure Portal z https://portal.azure.com.

2. Hledání "šablon" v `All Services` nebo v prostředním panelu hledání. 

![Hledání šablon](media/custom-providers-resource-onboarding/templates.png)

3. Stiskněte tlačítko `+ Add` v levém horním rohu okna šablony.

![Přidat novou šablonu](media/custom-providers-resource-onboarding/templatesadd.png)

4. Vyplňte pole název a popis v části Obecné pro novou šablonu.

![Název a popis šablony](media/custom-providers-resource-onboarding/templatesdescription.png)

5. Do šablony Správce prostředků naplňte zkopírováním šablony JSON z části Začínáme s připojováním zdrojů.

![Vyplnit šablonu Správce prostředků](media/custom-providers-resource-onboarding/templatesarmtemplate.png)

6. Stisknutím tlačítka `Add` vytvořte novou šablonu. Pokud se nová šablona nezobrazuje, stiskněte `Refresh`.

7. Vyberte nově vytvořenou šablonu a stiskněte tlačítko `Deploy`.

![Vyberte nově vytvořenou šablonu a nasaďte ji.](media/custom-providers-resource-onboarding/templateselectspecific.png)

8. Zadejte parametry nastavení pro požadovaná pole a vyberte předplatné a skupinu prostředků. Vlastní ID poskytovatele prostředků může být ponecháno prázdné.

| Název nastavení | Požaduje se | Popis |
| ------------ | -------- | ----------- |
| Umístění | *Ano* | Umístění prostředků v šabloně. |
| Název aplikace logiky | *Ne* | Název aplikace logiky |
| Název vlastního poskytovatele prostředků | *Ne* | Název vlastního poskytovatele prostředků |
| ID vlastního poskytovatele prostředků | *Ne* | Stávající vlastní poskytovatel prostředků, který podporuje prostředek Association. Zadáním této akce přeskočíte nasazení aplikace logiky a vlastního poskytovatele. |
| Název přidružení | *Ne* | Název prostředku přidružení. |

Ukázkové parametry:

![Vstupní parametry šablony](media/custom-providers-resource-onboarding/templatescustomprovider.png)

9. Přejděte do nasazení a počkejte, než se dokončí. Měl by se zobrazit úspěch a výstup nového prostředku Association.

Nasazení bylo úspěšné:

![Nasazení vlastního zprostředkovatele](media/custom-providers-resource-onboarding/customproviderdeployment.png)

Skupina prostředků s "Zobrazit skryté typy":

![Nasazení vlastního zprostředkovatele](media/custom-providers-resource-onboarding/showhidden.png)

10. Prozkoumejte kartu "historie spuštění aplikace logiky" a zobrazte volání pro vytvoření "asociace".

![Historie spuštění aplikace logiky](media/custom-providers-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>Nasazení dalších přidružení

Jakmile máme vytvořenou infrastrukturu vlastního poskytovatele, můžeme snadno nasadit další "asociace". Skupina prostředků pro další "přidružení" nemusí být stejná jako skupina prostředků, do které byla nasazena infrastruktura vlastního zprostředkovatele. Aby bylo možné vytvořit přidružení, jsou pro zadané vlastní ID poskytovatele prostředků potřeba oprávnění Microsoft. CustomProviders/resourceproviders/Write.

1. Ve skupině prostředků v předchozím nasazení přejděte do prostředku Custom Provider "Microsoft. CustomProviders/resourceProviders". Bude nutné vybrat zaškrtávací políčko Zobrazit skryté typy.

![Nasazení vlastního zprostředkovatele](media/custom-providers-resource-onboarding/showhidden.png)

2. Zkopírujte vlastnost ID prostředku vlastního zprostředkovatele.

3. Hledání "šablon" v `All Services` nebo v prostředním panelu hledání. 

![Hledání šablon](media/custom-providers-resource-onboarding/templates.png)

4. Vyberte dříve vytvořenou šablonu a stiskněte tlačítko `Deploy`.

![Vyberte dříve vytvořenou šablonu a nasaďte ji.](media/custom-providers-resource-onboarding/templateselectspecific.png)

5. Zadejte parametry nastavení pro požadovaná pole a vyberte předplatné a jinou skupinu prostředků. U nastavení "ID vlastního poskytovatele prostředků" zadejte zkopírované ID prostředku dříve nasazeného vlastního zprostředkovatele.

6. Přejděte do nasazení a počkejte, než se dokončí. Teď by měl nasadit jenom nový prostředek Associations.

![Vytvořené přidružení](media/custom-providers-resource-onboarding/createdassociationresource.png)

Volitelně můžete přejít zpět na historii spuštění aplikace logiky a podívat se, že aplikace logiky provedla jiné volání. Aplikaci logiky můžete aktualizovat a rozšířit tak další funkce pro každé vytvořené přidružení.

## <a name="looking-for-help"></a>Hledáte nápovědu

Pokud máte dotazy týkající se vlastních zprostředkovatelů Azure, zkuste požádat o [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-custom-providers). Podobná otázka již mohla být požádána o zodpovězení a byla zodpovězena, proto nejprve před odesláním zaregistrujte. Přidejte `azure-custom-providers` značek, abyste získali rychlou odezvu.

