---
title: Vytvoření předplatného tématu a pravidla tématu služby Service Bus pomocí šablony Azure
description: Vytvoření oboru názvů Service Bus pomocí tématu, předplatného a pravidla pomocí šablony Azure Resource Manager
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9e0aaf58-0214-4bca-bd00-d29c08f9b1bc
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/27/2019
ms.author: spelluru
ms.openlocfilehash: 6cbaf447dfcf06ae11f2282d7d847978297af8b8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384887"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Vytvoření oboru názvů Service Bus s tématem, předplatným a pravidlem pomocí šablony Azure Resource Manageru

Tento článek ukazuje, jak používat šablonu Azure Resource Manager, která vytváří obor názvů Service Bus s tématem, předplatným a pravidlem (filtrem). Článek vysvětluje, jak určit, které prostředky jsou nasazeny a jak definovat parametry, které jsou určeny při spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky.

Další informace o vytváření šablon najdete v tématu [Tvorba šablon Azure Resource Manageru][Authoring Azure Resource Manager templates].

Další informace o praxi a vzory na azure prostředky konvence pojmenování, najdete [v tématu Doporučené konvence pojmenování pro prostředky Azure][Recommended naming conventions for Azure resources].

Kompletní šablonu najdete v [oboru názvů Service Bus s tématem, předplatným a šablonou pravidla.][Service Bus namespace with topic, subscription, and rule]

> [!NOTE]
> Následující šablony Azure Resource Manager jsou k dispozici ke stažení a nasazení.
> 
> * [Vytvoření oboru názvů service bus s pravidlem fronty a autorizace](service-bus-resource-manager-namespace-auth-rule.md)
> * [Vytvoření oboru názvů service bus s frontou](service-bus-resource-manager-namespace-queue.md)
> * [Vytvoření oboru názvů Service Bus](service-bus-resource-manager-namespace.md)
> * [Vytvoření oboru názvů Service Bus s tématem a odběrem](service-bus-resource-manager-namespace-topic.md)
> 
> Chcete-li vyhledat nejnovější šablony, navštivte galerii [šablon Azure QuickStart][Azure Quickstart Templates] a vyhledejte službu Service Bus.

## <a name="what-do-you-deploy"></a>Co nasadíte?

Pomocí této šablony nasadíte obor názvů Service Bus s tématem, odběrem a pravidlem (filtrem).

[Témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) poskytují formu komunikace 1:N ve vzoru *publikování/odběru.* Při použití témata a odběry, součásti distribuované aplikace nekomunikují přímo mezi sebou, místo toho si vyměňují zprávy prostřednictvím tématu, které funguje jako zprostředkovatel. Odběr tématu se podobá virtuální frontě, která přijímá kopie zpráv, které byly odeslány do tématu. Filtr na odběr umožňuje určit, které zprávy odeslané na téma by se měly zobrazit v rámci konkrétní ho tematické předplatné.

## <a name="what-are-rules-filters"></a>Co jsou pravidla (filtry)?

V mnoha scénářích zprávy, které mají specifické vlastnosti musí být zpracovány různými způsoby. Chcete-li povolit toto vlastní zpracování, můžete nakonfigurovat odběry najít zprávy, které mají specifické vlastnosti a potom provést změny těchto vlastností. Přestože odběry služby Service Bus zobrazit všechny zprávy odeslané do tématu, můžete pouze zkopírovat podmnožinu těchto zpráv do fronty virtuální odběr. To se provádí pomocí filtrů předplatného. Další informace o pravidlech (filtrech) najdete v [tématu Pravidla a akce](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Pokud chcete nasazení spustit automaticky, klikněte na následující tlačítko:

[![Nasazení do Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Pomocí Azure Resource Manager definujte parametry pro hodnoty, které chcete určit při nasazení šablony. Šablona obsahuje část `Parameters`, která obsahuje všechny hodnoty parametrů. Definujte parametr pro tyto hodnoty, které se liší v závislosti na projektu, který nasazujete, nebo na základě prostředí, do kterého nasazujete. Nedefinujte parametry pro hodnoty, které jsou vždy stejné. Každá hodnota parametru se v šabloně použije k definování nasazovaných prostředků.

Šablona definuje následující parametry:

### <a name="servicebusnamespacename"></a>název_oboru serviceBusNameName

Název oboru názvů Service Bus, který chcete vytvořit.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>název serviceBusTopicName

Název tématu vytvořeného v oboru názvů Service Bus.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>název serviceBusSubscriptionName

Název předplatného vytvořeného v oboru názvů Service Bus.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusrulename"></a>serviceBusRuleName

Název pravidla(filtru) vytvořeného v oboru názvů Service Bus.

```json
   "serviceBusRuleName": {
   "type": "string",
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

Vytvoří standardní obor názvů service bus typu **Zasílání zpráv**s tématem a odběrem a pravidly.

```json
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filterType": "SqlFilter",
                        "sqlFilter": {
                            "sqlExpression": "StoreName = 'Store1'",
                            "requiresPreprocessing": "false"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

Syntaxe a vlastnosti JSON naleznete [v tématu obory názvů](/azure/templates/microsoft.servicebus/namespaces), [témata](/azure/templates/microsoft.servicebus/namespaces/topics), [odběry](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions)a [pravidla](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules).

## <a name="commands-to-run-deployment"></a>Příkazy pro spuštění nasazení

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak spravovat tyto prostředky v těchto článcích:

* [Správa Azure Service Bus](service-bus-management-libraries.md)
* [Správa služby Service Bus pomocí PowerShellu](service-bus-manage-with-ps.md)
* [Správa prostředků služby Service Bus pomocí Průzkumníka sběrnice](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Recommended naming conventions for Azure resources]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
