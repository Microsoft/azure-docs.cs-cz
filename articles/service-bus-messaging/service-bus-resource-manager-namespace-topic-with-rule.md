---
title: Vytvoření předplatného a pravidla Service Bus tématu pomocí šablony Azure
description: Vytvoření oboru názvů Service Bus s použitím tématu, předplatného a pravidla pomocí šablony Azure Resource Manager
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3f3287dd67f89f678a9875ddce93e2d0d26b2209
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89077620"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Vytvoření oboru názvů Service Bus s použitím tématu, předplatného a pravidla pomocí šablony Azure Resource Manager

Tento článek ukazuje, jak použít šablonu Azure Resource Manager, která vytvoří obor názvů Service Bus s tématem, předplatným a pravidlem (Filter). V tomto článku se dozvíte, jak určit, které prostředky se nasazují a jak definovat parametry, které jsou zadané při spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky.

Další informace o vytváření šablon najdete v tématu [Tvorba šablon Azure Resource Manageru][Authoring Azure Resource Manager templates].

Další informace o postupech a vzorcích pro zásady vytváření názvů prostředků Azure najdete v tématu [Doporučené zásady vytváření názvů pro prostředky Azure][Recommended naming conventions for Azure resources].

Úplnou šablonu najdete v [oboru názvů Service Bus s tématem, předplatným a][Service Bus namespace with topic, subscription, and rule] šablonou pravidel.

> [!NOTE]
> Následující šablony Azure Resource Manager jsou k dispozici ke stažení a nasazení.
> 
> * [Vytvoření oboru názvů Service Bus s použitím fronty a autorizačního pravidla](service-bus-resource-manager-namespace-auth-rule.md)
> * [Vytvoření oboru názvů Service Bus s využitím fronty](service-bus-resource-manager-namespace-queue.md)
> * [Vytvoření oboru názvů Service Busu](service-bus-resource-manager-namespace.md)
> * [Vytvoření oboru názvů Service Bus s tématem a předplatným](service-bus-resource-manager-namespace-topic.md)
> 
> Pokud chcete vyhledat nejnovější šablony, přejděte na galerii [šablon Azure pro rychlý Start][Azure Quickstart Templates] a vyhledejte Service Bus.

## <a name="what-do-you-deploy"></a>Co nasazujete?

Pomocí této šablony nasadíte Service Bus obor názvů s tématem, předplatným a pravidlem (Filter).

[Service Bus témata a předplatná](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) poskytují ve vzoru pro *publikování a odběry* formu komunikace 1: n. Když používáte témata a odběry, komponenty distribuované aplikace spolu nekomunikují přímo, ale vyměňují zprávy přes téma, které funguje jako prostředník. Předplatné na téma se podobá virtuální frontě, která přijímá kopie zpráv odeslaných do tématu. Filtr na základě předplatného umožňuje určit, které zprávy odeslané do tématu se mají zobrazit v rámci konkrétního předplatného tématu.

## <a name="what-are-rules-filters"></a>Jaká jsou pravidla (filtry)?

V mnoha scénářích musí být zprávy, které mají specifické vlastnosti, zpracovávány různými způsoby. Chcete-li povolit toto vlastní zpracování, můžete nakonfigurovat odběry pro hledání zpráv, které mají určité vlastnosti, a následně provést úpravy těchto vlastností. I když Service Bus předplatná uvidí všechny zprávy odeslané do tématu, můžete zkopírovat pouze podmnožinu těchto zpráv do fronty virtuálních předplatných. Provádí se pomocí filtrů předplatného. Další informace o pravidlech (filtrech) najdete v tématu [pravidla a akce](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Pokud chcete nasazení spustit automaticky, klikněte na následující tlačítko:

[![Nasazení do Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

V Azure Resource Manager definujte parametry pro hodnoty, které chcete zadat při nasazení šablony. Šablona obsahuje část `Parameters`, která obsahuje všechny hodnoty parametrů. Definujte parametr pro tyto hodnoty, které se liší v závislosti na projektu, který nasazujete, nebo na základě prostředí, do kterého nasazujete. Nedefinujte parametry pro hodnoty, které jsou vždy stejné. Každá hodnota parametru se v šabloně použije k definování nasazovaných prostředků.

Šablona definuje následující parametry:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

Název oboru názvů Service Bus, který se má vytvořit

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

Název tématu vytvořeného v oboru názvů Service Bus.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

Název předplatného, které jste vytvořili v oboru názvů Service Bus.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusrulename"></a>serviceBusRuleName

Název pravidla (filtru) vytvořeného v oboru názvů Service Bus.

```json
   "serviceBusRuleName": {
   "type": "string",
  }
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

Verze Service Bus rozhraní API šablony.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>Prostředky k nasazení

Vytvoří standardní obor názvů Service Bus typu **zasílání zpráv**, s tématem a předplatnými a pravidly.

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

Informace o syntaxi a vlastnostech JSON najdete v tématu [obory názvů](/azure/templates/microsoft.servicebus/namespaces), [témata](/azure/templates/microsoft.servicebus/namespaces/topics), [odběry](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions)a [pravidla](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules).

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

Naučte se spravovat tyto prostředky zobrazením těchto článků:

* [Správa Azure Service Bus](service-bus-management-libraries.md)
* [Správa služby Service Bus pomocí PowerShellu](service-bus-manage-with-ps.md)
* [Správa prostředků Service Bus pomocí Průzkumníka Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
[Recommended naming conventions for Azure resources]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
