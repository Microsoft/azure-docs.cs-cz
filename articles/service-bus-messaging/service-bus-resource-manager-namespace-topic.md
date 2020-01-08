---
title: 'Rychlý Start: Vytvoření odběru tématu Azure Service Bus oboru názvů pomocí šablony Azure Resource Manager'
description: 'Rychlý Start: vytvoření oboru názvů Service Bus s použitím tématu a předplatného pomocí šablony Azure Resource Manager'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d3d55200-5c60-4b5f-822d-59974cafff0e
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 661edf94fd0c505968fc78a367ceda6a93afe401
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426880"
---
# <a name="quickstart-create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Rychlý Start: vytvoření oboru názvů Service Bus s použitím tématu a předplatného pomocí šablony Azure Resource Manager

Tento článek ukazuje, jak použít šablonu Azure Resource Manager, která vytvoří obor názvů Service Bus a téma a předplatné v rámci tohoto oboru názvů. V tomto článku se dozvíte, jak určit, které prostředky se nasazují a jak definovat parametry, které jsou zadané při spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky.

Další informace o vytváření šablon najdete v tématu [vytváření šablon Azure Resource Manager][Authoring Azure Resource Manager templates].

Úplnou šablonu najdete v tématu [obor názvů Service Bus se šablonou pro téma a odběr][Service Bus namespace with topic and subscription] .

> [!NOTE]
> Následující šablony Azure Resource Manager jsou k dispozici ke stažení a nasazení.
> 
> * [Vytvoření oboru názvů Service Bus](service-bus-resource-manager-namespace.md)
> * [Vytvoření oboru názvů Service Bus s využitím fronty](service-bus-resource-manager-namespace-queue.md)
> * [Vytvoření oboru názvů Service Bus s použitím fronty a autorizačního pravidla](service-bus-resource-manager-namespace-auth-rule.md)
> * [Vytvoření oboru názvů Service Bus s použitím tématu, předplatného a pravidla](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Pokud chcete vyhledat nejnovější šablony, přejděte na galerii [šablon Azure pro rychlý Start][Azure Quickstart Templates] a vyhledejte **Service Bus**.
> 
> 

## <a name="what-do-you-deploy"></a>Co nasazujete?

Pomocí této šablony nasadíte Service Bus obor názvů s tématem a předplatným.

[Service Bus témata a předplatná](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) poskytují ve vzoru pro *publikování a odběry* formu komunikace 1: n.

Pokud chcete nasazení spustit automaticky, klikněte na následující tlačítko:

[![Nasazení do Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Pomocí Azure Resource Manageru definujete parametry pro hodnoty, které chcete zadat při nasazení šablony. Šablona obsahuje oddíl s názvem `Parameters`, který obsahuje všechny hodnoty parametrů. Definujte parametr pro tyto hodnoty, které se liší v závislosti na projektu, který nasazujete, nebo na základě prostředí, do kterého nasazujete. Nedefinujte parametry pro hodnoty, které jsou vždy stejné. Každá hodnota parametru se v šabloně použije k definování nasazovaných prostředků.

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
Vytvoří obor názvů Standard Service Bus typu **zasílání zpráv**s tématem a odběrem.

```json
"resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
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
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

Informace o syntaxi a vlastnostech JSON najdete v tématu [obory názvů](/azure/templates/microsoft.servicebus/namespaces), [témata](/azure/templates/microsoft.servicebus/namespaces/topics)a [předplatná](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions).

## <a name="commands-to-run-deployment"></a>Příkazy pro spuštění nasazení
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>Další kroky
Teď, když jste vytvořili a nasadili prostředky pomocí Azure Resource Manager, Naučte se spravovat tyto prostředky zobrazením těchto článků:

* [Správa služby Service Bus pomocí PowerShellu](service-bus-manage-with-ps.md)
* [Správa prostředků Service Bus pomocí Průzkumníka Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus namespace with topic and subscription]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/
