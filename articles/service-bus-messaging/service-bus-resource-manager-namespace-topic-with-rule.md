---
title: Vytvoření odběru tématu služby Azure Service Bus a pravidla pomocí šablony Azure Resource Manageru | Dokumentace Microsoftu
description: Vytvoření oboru názvů služby Service Bus s tématem, předplatné a pravidla pomocí šablony Azure Resource Manageru
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
ms.date: 09/11/2018
ms.author: spelluru
ms.openlocfilehash: 7774d67d02b2b0f0080cbff2ffc3606b99450f26
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47404957"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Vytvoření oboru názvů služby Service Bus s tématem, předplatné a pravidla pomocí šablony Azure Resource Manageru

Tento článek ukazuje, jak použít šablonu Azure Resource Manageru, která vytvoří obor názvů služby Service Bus s tématem, předplatné a pravidlo (filtr). Tento článek popisuje, jak k určení prostředků, které jsou nasazené a tom, jak definovat parametry, které jsou zadané při spouštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky.

Další informace o vytváření šablon najdete v tématu [Tvorba šablon Azure Resource Manageru][Authoring Azure Resource Manager templates].

Další informace o postupy a vzory na zásady vytváření názvů prostředků Azure najdete v tématu [doporučené zásady vytváření názvů pro prostředky Azure][Recommended naming conventions for Azure resources].

Úplnou šablonu najdete v článku [obor názvů služby Service Bus s tématu, předplatným a pravidlem] [ Service Bus namespace with topic, subscription, and rule] šablony.

> [!NOTE]
> Následující šablony Azure Resource Manageru jsou k dispozici ke stažení a nasazení.
> 
> * [Vytvoření oboru názvů služby Service Bus s fronty a autorizační pravidla](service-bus-resource-manager-namespace-auth-rule.md)
> * [Vytvoření oboru názvů služby Service Bus s frontou](service-bus-resource-manager-namespace-queue.md)
> * [Vytvoření oboru názvů služby Service Bus](service-bus-resource-manager-namespace.md)
> * [Vytvoření oboru názvů služby Service Bus s tématem a předplatným](service-bus-resource-manager-namespace-topic.md)
> 
> Vyhledat nejnovější šablony, přejděte [šablony pro rychlý start Azure] [ Azure Quickstart Templates] galerii a vyhledat služby Service Bus.
> 
> 

## <a name="what-do-you-deploy"></a>Co nasadit?

Pomocí této šablony nasadíte obor názvů Service Bus s tématem, předplatné a pravidlo (filtr).

[Témata a odběry Service Bus](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) zadat jeden mnoho forma komunikace, *publikování/odběr* vzor. Když používáte témata a odběry, komponenty distribuované aplikace nekomunikují navzájem přímo, místo toho si vyměňují zprávy přes téma, které funguje jako prostředník. Předplatné tématu se podobá virtuální frontě, která obdrží kopii zprávy, které byly odeslány do tématu. Filtr na předplatné umožňují vám určit, které zprávy odeslané do tématu by se zobrazit v konkrétním odběru tématu.

## <a name="what-are-rules-filters"></a>Co jsou pravidla (filtry)?

V mnoha případech je nutné zpracovat zprávy, které mají určité charakteristiky různými způsoby. Pokud chcete povolit vlastní zpracování, můžete nakonfigurovat odběry najít zprávy, které mají specifické vlastnosti a pak proveďte změny k těmto vlastnostem. I když se předplatné služby Service Bus zobrazit všechny zprávy odeslané do tématu, kopírovat můžete pouze podmnožinu těchto zpráv do fronty virtuální odběru. To se provádí pomocí filtry předplatných. Další informace o pravidlech (filtry) najdete v tématu [pravidla a akce](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Pokud chcete nasazení spustit automaticky, klikněte na následující tlačítko:

[![Nasazení do Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

S Azure Resource Manageru, definujte parametry pro hodnoty, které chcete zadat při nasazení šablony. Šablona obsahuje část `Parameters`, která obsahuje všechny hodnoty parametrů. Definování parametru pro tyto hodnoty, které se liší podle projektu, které nasazujete nebo podle prostředí, které nasazujete. Nedefinujte parametry pro hodnoty, které jsou vždy stejné. Každá hodnota parametru se v šabloně použije k definování nasazovaných prostředků.

Šablona definuje následující parametry:

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
### <a name="servicebusrulename"></a>serviceBusRuleName
Název rule(filter) vytvořené v oboru názvů služby Service Bus.

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
Vytvoří standardní obor názvů služby Service Bus tohoto typu **zasílání zpráv**s téma a odběr a pravidla.

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

## <a name="commands-to-run-deployment"></a>Příkazy pro spuštění nasazení
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Další postup
Teď, když jste vytvořili a nasadili prostředky pomocí Azure Resource Manageru, zjistěte, jak tyto zdroje spravovat pomocí těchto článků:

* [Správa služby Azure Service Bus](service-bus-management-libraries.md)
* [Správa služby Service Bus pomocí PowerShellu](service-bus-manage-with-ps.md)
* [Správa prostředků Service Bus pomocí Průzkumníka Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Recommended naming conventions for Azure resources]: ../guidance/guidance-naming-conventions.md
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md

