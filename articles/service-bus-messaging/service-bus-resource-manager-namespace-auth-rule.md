---
title: Vytvoření pravidla autorizace služby Service Bus pomocí šablony Azure Resource Manager | Microsoft Docs
description: Vytvoření pravidla autorizace sběrnice pro obor názvů a fronty pomocí šablony Azure Resource Manageru
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 7f1443a0-5fa8-4d90-8637-1a977ef0b1f0
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/11/2018
ms.author: sethm
ms.openlocfilehash: e29045f43f5b80ffc66d7b10bac39f3d8d03ae3c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Vytvoření pravidla autorizace sběrnice pro obor názvů a fronty pomocí šablony Azure Resource Manager

Tento článek ukazuje, jak používat šablonu Azure Resource Manager, který vytváří [autorizační pravidlo](service-bus-authentication-and-authorization.md#shared-access-signature-authentication) oboru názvů Service Bus a fronty. Článek vysvětluje, jak zadat prostředky, ke kterým nasazených a jak definovat parametry, které jsou zadané, když se spustí nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky.

Další informace o vytváření šablon najdete v tématu [šablon pro tvorbu Azure Resource Manageru][Authoring Azure Resource Manager templates].

Úplnou šablonu, najdete v článku [šablonu pravidla autorizace služby Service Bus] [ Service Bus auth rule template] na Githubu.

> [!NOTE]
> Následující šablony Azure Resource Manager jsou k dispozici ke stažení a nasazení.
> 
> * [Vytvoření oboru názvů Service Bus](service-bus-resource-manager-namespace.md)
> * [Vytvoření oboru názvů Service Bus pomocí fronty](service-bus-resource-manager-namespace-queue.md)
> * [Vytvoření oboru názvů Service Bus s téma a odběr](service-bus-resource-manager-namespace-topic.md)
> * [Vytvoření oboru názvů Service Bus pomocí tématu, předplatné a pravidla](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Vyhledat nejnovější šablony, najdete [šablon Azure rychlý Start] [ Azure Quickstart Templates] galerie a vyhledejte **Service Bus**.
> 
> 

## <a name="what-will-you-deploy"></a>Co budete nasazovat?

S touto šablonou nasazení Service Bus autorizační pravidlo pro obor názvů a entity přenosu zpráv (v tomto případě fronty).

Tato šablona používá [sdíleného přístupového podpisu (SAS)](service-bus-sas.md) pro ověřování. SAS umožňuje aplikacím ke svému ověření u služby Service Bus pomocí přístupový klíč konfigurovány v oboru názvů, nebo u entity zasílání zpráv (fronty nebo téma) ke které jsou přidružené konkrétní práva. Pak můžete tento klíč k vygenerování tokenu SAS, který zase můžou klienti používat ke svému ověření u služby Service Bus.

Pokud chcete nasazení spustit automaticky, klikněte na následující tlačítko:

[![Nasazení do Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Pomocí Azure Resource Manageru definujete parametry pro hodnoty, které chcete zadat při nasazení šablony. Šablona obsahuje oddíl s názvem `Parameters` obsahující všechny hodnoty parametru. Měli byste parametr pro ty hodnoty, které se liší podle prostředí, ve kterém provádíte nasazení nebo na základě projektu, které nasazujete. Nedefinují parametry pro hodnoty, které zůstanou vždy stejná. Každá hodnota parametru se v šabloně použije k definování nasazovaných prostředků.

Šablona definuje následující parametry.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Název oboru názvů Service Bus k vytvoření.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>namespaceAuthorizationRuleName
Název autorizační pravidlo pro obor názvů.

```json
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
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
Vytvoří standardní oboru názvů Service Bus typu **zasílání zpráv**a Service Bus autorizační pravidlo pro obor názvů a entity.

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
Teď, když jste vytvoření a nasazení prostředků pomocí Azure Resource Manager, zjistěte, jak tyto zdroje spravovat pomocí těchto článků:

* [Správa služby Service Bus pomocí prostředí PowerShell](service-bus-powershell-how-to-provision.md)
* [Správa prostředků služby Service Bus pomocí Průzkumníka služby sběrnice](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
* [Service Bus ověřování a autorizace](service-bus-authentication-and-authorization.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus auth rule template]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/
