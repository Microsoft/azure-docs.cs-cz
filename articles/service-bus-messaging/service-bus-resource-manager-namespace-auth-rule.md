---
title: Vytvoření pravidla autorizace služby Service Bus pomocí šablony Azure
description: Vytvoření pravidla autorizace služby Service Bus pro obor názvů a frontu pomocí šablony Azure Resource Manager
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 7f1443a0-5fa8-4d90-8637-1a977ef0b1f0
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 1bfb2d2d946a85c1d051315fb29a5a63f7a00871
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384921"
---
# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Vytvoření pravidla autorizace služby Service Bus pro obor názvů a frontu pomocí šablony Správce prostředků Azure

Tento článek ukazuje, jak používat šablonu Azure Resource Manager, která vytvoří [pravidlo autorizace](service-bus-authentication-and-authorization.md#shared-access-signature) pro obor názvů service bus a fronty. Článek vysvětluje, jak určit, které prostředky jsou nasazeny a jak definovat parametry, které jsou určeny při spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky.

Další informace o vytváření šablon najdete v [tématu Vytváření šablon Azure Resource Manager .][Authoring Azure Resource Manager templates]

Kompletní šablonu najdete v [šabloně pravidla autorizace service busu][Service Bus auth rule template] na GitHubu.

> [!NOTE]
> Následující šablony Azure Resource Manager jsou k dispozici ke stažení a nasazení.
> 
> * [Vytvoření oboru názvů Service Bus](service-bus-resource-manager-namespace.md)
> * [Vytvoření oboru názvů service bus s frontou](service-bus-resource-manager-namespace-queue.md)
> * [Vytvoření oboru názvů Service Bus s tématem a odběrem](service-bus-resource-manager-namespace-topic.md)
> * [Vytvoření oboru názvů Service Bus s tématem, předplatným a pravidlem](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Chcete-li vyhledat nejnovější šablony, navštivte galerii [šablon Azure QuickStart templates][Azure Quickstart Templates] a vyhledejte **službu Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>Co budete nasazovat?

Pomocí této šablony nasadíte pravidlo autorizace služby Service Bus pro obor názvů a entitu zasílání zpráv (v tomto případě frontu).

Tato šablona používá pro ověřování [sdílený přístupový podpis (SAS).](service-bus-sas.md) SAS umožňuje aplikacím ověřit service bus pomocí přístupového klíče nakonfigurovaného v oboru názvů nebo v entitě zasílání zpráv (fronta nebo téma), ke které jsou přidružena určitá práva. Tento klíč pak můžete použít ke generování tokenu SAS, který mohou klienti zase použít k ověření service bus.

Pokud chcete nasazení spustit automaticky, klikněte na následující tlačítko:

[![Nasazení do Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Pomocí Azure Resource Manageru definujete parametry pro hodnoty, které chcete zadat při nasazení šablony. Šablona obsahuje oddíl `Parameters` s názvem obsahující všechny hodnoty parametrů. Měli byste definovat parametr pro tyto hodnoty, které se budou lišit v závislosti na projektu, který nasazujete, nebo na základě prostředí, do kterého nasazujete. Nedefinujte parametry pro hodnoty, které zůstanou vždy stejné. Každá hodnota parametru se v šabloně použije k definování nasazovaných prostředků.

Šablona definuje následující parametry.

### <a name="servicebusnamespacename"></a>název_oboru serviceBusNameName

Název oboru názvů Service Bus, který chcete vytvořit.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>název oboru AuthorizationRuleName

Název autorizačního pravidla pro obor názvů.

```json
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>název_služby serviceBusQueueName

Název fronty v oboru názvů Service Bus.

```json
"serviceBusQueueName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

Verze rozhraní API služby Service Bus šablony.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>Prostředky k nasazení

Vytvoří standardní obor názvů služby Service Bus typu **Zasílání zpráv**a pravidlo autorizace služby Service Bus pro obor názvů a entitu.

```json
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "Standard",
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

Syntaxe a vlastnosti JSON naleznete [v tématech obory názvů](/azure/templates/microsoft.servicebus/namespaces), [fronty](/azure/templates/microsoft.servicebus/namespaces/queues)a [Autorizační pravidla](/azure/templates/microsoft.servicebus/namespaces/authorizationrules).

## <a name="commands-to-run-deployment"></a>Příkazy pro spuštění nasazení

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili a nasadili prostředky pomocí Správce prostředků Azure, zjistěte, jak spravovat tyto prostředky zobrazením těchto článků:

* [Správa služby Service Bus pomocí PowerShellu](service-bus-powershell-how-to-provision.md)
* [Správa prostředků služby Service Bus pomocí Průzkumníka sběrnice](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
* [Ověřování a autorizace Service Bus](service-bus-authentication-and-authorization.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus auth rule template]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/
