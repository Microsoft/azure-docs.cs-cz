---
title: Vytvořit pravidlo povolení služby Service Bus pomocí šablony Azure Resource Manageru | Dokumentace Microsoftu
description: Vytvořit pravidlo autorizace sběrnice pro obor názvů a fronty pomocí šablony Azure Resource Manageru
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 7f1443a0-5fa8-4d90-8637-1a977ef0b1f0
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 09/11/2018
ms.author: spelluru
ms.openlocfilehash: 1250851386b9423b66bdbfd03292de56fd38850d
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410098"
---
# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Vytvořit pravidlo autorizace sběrnice pro obor názvů a frontu pomocí šablony Azure Resource Manageru

Tento článek ukazuje, jak použít šablonu Azure Resource Manageru, která vytvoří [autorizační pravidlo](service-bus-authentication-and-authorization.md#shared-access-signature-authentication) pro obor názvů služby Service Bus a fronty. Tento článek popisuje, jak k určení prostředků, které jsou nasazené a tom, jak definovat parametry, které jsou zadané při spouštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky.

Další informace o vytváření šablon najdete v tématu [šablon pro vytváření Azure Resource Manageru][Authoring Azure Resource Manager templates].

Úplnou šablonu najdete v článku [šablony pravidla autorizace Service Bus] [ Service Bus auth rule template] na Githubu.

> [!NOTE]
> Následující šablony Azure Resource Manageru jsou k dispozici ke stažení a nasazení.
> 
> * [Vytvoření oboru názvů služby Service Bus](service-bus-resource-manager-namespace.md)
> * [Vytvoření oboru názvů služby Service Bus s frontou](service-bus-resource-manager-namespace-queue.md)
> * [Vytvoření oboru názvů služby Service Bus s tématem a předplatným](service-bus-resource-manager-namespace-topic.md)
> * [Vytvoření oboru názvů služby Service Bus s tématem, předplatné a pravidla](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Vyhledat nejnovější šablony, najdete v tématu [šablony pro rychlý start Azure] [ Azure Quickstart Templates] galerie a vyhledejte **služby Service Bus**.
> 
> 

## <a name="what-will-you-deploy"></a>Co budete nasazovat?

Pomocí této šablony nasadíte služby Service Bus autorizačního pravidla pro obor názvů a entity pro zasílání zpráv (v tomto případě fronty).

Tato šablona používá [sdíleného přístupového podpisu (SAS)](service-bus-sas.md) pro ověřování. SAS umožňuje aplikacím k ověření pomocí přístupového klíče nakonfigurovaná v oboru názvů nebo u entity zasílání zpráv (fronty nebo tématu) pomocí kterého se konkrétní práva přidružené služby Service Bus. Potom můžete tento klíč k vygenerování tokenu SAS, který můžou Klienti pak použít k ověření do služby Service Bus.

Pokud chcete nasazení spustit automaticky, klikněte na následující tlačítko:

[![Nasazení do Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Pomocí Azure Resource Manageru definujete parametry pro hodnoty, které chcete zadat při nasazení šablony. Šablona obsahuje část s názvem `Parameters` , který obsahuje všechny hodnoty parametrů. Byste měli definovat parametr pro tyto hodnoty, které se liší podle projektu, které nasazujete nebo podle prostředí, které nasazujete. Nedefinujte parametry pro hodnoty, které zůstane vždy stejný. Každá hodnota parametru se v šabloně použije k definování nasazovaných prostředků.

Šablona definuje následující parametry.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Název oboru názvů služby Service Bus k vytvoření.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>namespaceAuthorizationRuleName
Název autorizačního pravidla oboru názvů.

```json
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
Název fronty v oboru názvů služby Service Bus.

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
Vytvoří standardní obor názvů služby Service Bus tohoto typu **zasílání zpráv**a pravidla autorizace Service Bus pro obor názvů a entity.

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

## <a name="commands-to-run-deployment"></a>Příkazy pro spuštění nasazení
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Další postup
Teď, když jste vytvořili a nasadili prostředky pomocí Azure Resource Manageru, zjistěte, jak tyto zdroje spravovat pomocí těchto článků:

* [Správa služby Service Bus pomocí PowerShellu](service-bus-powershell-how-to-provision.md)
* [Správa prostředků Service Bus pomocí Průzkumníka Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
* [Ověřování a autorizace Service Bus](service-bus-authentication-and-authorization.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus auth rule template]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/
