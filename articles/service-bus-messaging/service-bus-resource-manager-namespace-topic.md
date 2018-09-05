---
title: Vytvoření odběru tématu obor názvů služby Azure Service Bus pomocí šablony Azure Resource Manageru | Dokumentace Microsoftu
description: Vytvoření oboru názvů služby Service Bus s tématem a předplatným pomocí šablony Azure Resource Manageru
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d3d55200-5c60-4b5f-822d-59974cafff0e
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: b5512186913eb59be2b89ce8b8bb9fb881f59cd8
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43699817"
---
# <a name="create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Vytvoření oboru názvů služby Service Bus s tématem a předplatným pomocí šablony Azure Resource Manageru

Tento článek ukazuje, jak použít šablonu Azure Resource Manageru, která vytvoří obor názvů služby Service Bus a téma a odběr v daném oboru názvů. Tento článek popisuje, jak k určení prostředků, které jsou nasazené a tom, jak definovat parametry, které jsou zadané při spouštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky.

Další informace o vytváření šablon najdete v tématu [šablon pro vytváření Azure Resource Manageru][Authoring Azure Resource Manager templates].

Úplnou šablonu najdete v článku [obor názvů služby Service Bus s tématem a předplatným] [ Service Bus namespace with topic and subscription] šablony.

> [!NOTE]
> Následující šablony Azure Resource Manageru jsou k dispozici ke stažení a nasazení.
> 
> * [Vytvoření oboru názvů služby Service Bus](service-bus-resource-manager-namespace.md)
> * [Vytvoření oboru názvů služby Service Bus s frontou](service-bus-resource-manager-namespace-queue.md)
> * [Vytvoření oboru názvů služby Service Bus s fronty a autorizační pravidla](service-bus-resource-manager-namespace-auth-rule.md)
> * [Vytvoření oboru názvů služby Service Bus s tématem, předplatné a pravidla](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Vyhledat nejnovější šablony, najdete v tématu [šablony pro rychlý start Azure] [ Azure Quickstart Templates] galerie a vyhledejte **služby Service Bus**.
> 
> 

## <a name="what-will-you-deploy"></a>Co budete nasazovat?

Pomocí této šablony nasadíte obor názvů Service Bus s tématem a předplatným.

[Témata a odběry Service Bus](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) zadat jeden mnoho forma komunikace, *publikování/odběr* vzor.

Pokud chcete nasazení spustit automaticky, klikněte na následující tlačítko:

[![Nasazení do Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Pomocí Azure Resource Manageru definujete parametry pro hodnoty, které chcete zadat při nasazení šablony. Šablona obsahuje část s názvem `Parameters` , který obsahuje všechny hodnoty parametrů. Parametr byste měli definovat pro hodnoty, které se mění v závislosti na nasazovaném projektu nebo prostředí, do kterého nasazujete. Nedefinujte parametry pro hodnoty, které jsou vždy stejné. Každá hodnota parametru se v šabloně použije k definování nasazovaných prostředků.

Šablona definuje následující parametry.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Název oboru názvů služby Service Bus k vytvoření.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
Název tématu vytvořili v oboru názvů služby Service Bus.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
Název předplatného vytvořené v oboru názvů služby Service Bus.

```json
"serviceBusSubscriptionName": {
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
Vytvoří standardní obor názvů služby Service Bus tohoto typu **zasílání zpráv**, s tématem a předplatným.

```json
"resources ": [{
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

## <a name="next-steps"></a>Další postup
Teď, když jste vytvořili a nasadili prostředky pomocí Azure Resource Manageru, zjistěte, jak tyto zdroje spravovat pomocí těchto článků:

* [Správa služby Service Bus pomocí Powershellu](service-bus-manage-with-ps.md)
* [Správa prostředků Service Bus pomocí Průzkumníka Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus namespace with topic and subscription]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/
