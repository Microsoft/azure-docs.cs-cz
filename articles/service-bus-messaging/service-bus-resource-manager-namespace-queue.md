---
title: Vytvoření oboru názvů služby Azure Service Bus a umístit do fronty pomocí šablony Azure Resource Manageru | Dokumentace Microsoftu
description: Vytvoření oboru názvů služby Service Bus a fronty pomocí šablony Azure Resource Manageru
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/11/2018
ms.author: sethm
ms.openlocfilehash: 47e29050ca78ee116f3c4dee0ecb53a6a71a866b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38232098"
---
# <a name="create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Vytvoření oboru názvů služby Service Bus a fronty pomocí šablony Azure Resource Manageru

Tento článek ukazuje, jak použít šablonu Azure Resource Manageru, která vytvoří obor názvů služby Service Bus a fronty v daném oboru názvů. Tento článek popisuje, jak k určení prostředků, které jsou nasazené a tom, jak definovat parametry, které jsou zadané při spouštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky.

Další informace o vytváření šablon najdete v tématu [šablon pro vytváření Azure Resource Manageru][Authoring Azure Resource Manager templates].

Úplnou šablonu najdete v článku [šablony obor názvů a frontu služby Service Bus] [ Service Bus namespace and queue template] na Githubu.

> [!NOTE]
> Následující šablony Azure Resource Manageru jsou k dispozici ke stažení a nasazení.
> 
> * [Vytvoření oboru názvů služby Service Bus s fronty a autorizační pravidla](service-bus-resource-manager-namespace-auth-rule.md)
> * [Vytvoření oboru názvů služby Service Bus s tématem a předplatným](service-bus-resource-manager-namespace-topic.md)
> * [Vytvoření oboru názvů služby Service Bus](service-bus-resource-manager-namespace.md)
> * [Vytvoření oboru názvů služby Service Bus s tématem, předplatné a pravidla](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Vyhledat nejnovější šablony, najdete v tématu [šablony pro rychlý start Azure] [ Azure Quickstart Templates] galerie a vyhledejte **služby Service Bus**.
> 
> 

## <a name="what-will-you-deploy"></a>Co budete nasazovat?

Pomocí této šablony nasadíte obor názvů Service Bus s frontou.

[Fronty služby Service Bus](service-bus-queues-topics-subscriptions.md#queues) představují první In doručování zpráv první Out (FIFO) na jeden nebo několik konkurenčních spotřebitelů.

Pokud chcete nasazení spustit automaticky, klikněte na následující tlačítko:

[![Nasazení do Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Pomocí Azure Resource Manageru definujete parametry pro hodnoty, které chcete zadat při nasazení šablony. Šablona obsahuje část s názvem `Parameters` , který obsahuje všechny hodnoty parametrů. Byste měli definovat parametr pro tyto hodnoty, které se liší podle projektu, které nasazujete nebo podle prostředí, které nasazujete. Nedefinujte parametry pro hodnoty, které zůstane vždy stejný. Každá hodnota parametru se v šabloně použije k definování nasazovaných prostředků.

Šablona definuje následující parametry.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Název oboru názvů služby Service Bus k vytvoření.

```json
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
Název fronty vytvořené v oboru názvů služby Service Bus.

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
Vytvoří standardní obor názvů služby Service Bus tohoto typu **zasílání zpráv**, s frontou.

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
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]",
            }
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Příkazy pro spuštění nasazení
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Další postup
Teď, když jste vytvořili a nasadili prostředky pomocí Azure Resource Manageru, zjistěte, jak tyto zdroje spravovat pomocí těchto článků:

* [Správa služby Service Bus pomocí Powershellu](service-bus-manage-with-ps.md)
* [Správa prostředků Service Bus pomocí Průzkumníka Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
