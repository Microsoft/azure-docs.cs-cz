---
title: Vytvoření služby Service Bus Messaging oboru názvů pomocí šablony Azure Resource Manageru | Dokumentace Microsoftu
description: Vytvoření oboru názvů zasílání zpráv Service Bus pomocí šablony Azure Resource Manageru
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 09/11/2018
ms.author: spelluru
ms.openlocfilehash: 6f3f44394ab11c1b66be3af976dbd1f7d23de96e
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405780"
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Vytvoření oboru názvů služby Service Bus pomocí šablony Azure Resource Manageru

Tento článek popisuje, jak použít šablonu Azure Resource Manageru, která vytvoří obor názvů služby Service Bus typu **zasílání zpráv** pomocí standardní SKU. Tento článek také definuje parametry, které jsou určené pro spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky.

Další informace o vytváření šablon najdete v tématu [Tvorba šablon Azure Resource Manageru][Authoring Azure Resource Manager templates].

Úplnou šablonu najdete v článku [šablony obor názvů služby Service Bus] [ Service Bus namespace template] na Githubu.

> [!NOTE]
> Následující šablony Azure Resource Manageru jsou k dispozici ke stažení a nasazení. 
> 
> * [Vytvoření oboru názvů služby Service Bus s frontou](service-bus-resource-manager-namespace-queue.md)
> * [Vytvoření oboru názvů služby Service Bus s tématem a předplatným](service-bus-resource-manager-namespace-topic.md)
> * [Vytvoření oboru názvů služby Service Bus s fronty a autorizační pravidla](service-bus-resource-manager-namespace-auth-rule.md)
> * [Vytvoření oboru názvů služby Service Bus s tématem, předplatné a pravidla](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Vyhledat nejnovější šablony, přejděte [šablony pro rychlý start Azure] [ Azure Quickstart Templates] galerii a vyhledat služby Service Bus.
> 
> 

## <a name="what-will-you-deploy"></a>Co budete nasazovat?

Pomocí této šablony nasadíte obor názvů služby Service Bus s [Standard nebo Premium](https://azure.microsoft.com/pricing/details/service-bus/) SKU.

Pokud chcete nasazení spustit automaticky, klikněte na následující tlačítko:

[![Nasazení do Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Pomocí Azure Resource Manageru definujete parametry pro hodnoty, které chcete zadat při nasazení šablony. Šablona obsahuje část s názvem `Parameters` , který obsahuje všechny hodnoty parametrů. Parametr byste měli definovat pro hodnoty, které se mění v závislosti na nasazovaném projektu nebo prostředí, do kterého nasazujete. Nedefinujte parametry pro hodnoty, které jsou vždy stejné. Každá hodnota parametru se v šabloně použije k definování nasazovaných prostředků.

Tato šablona definuje následující parametry:

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

### <a name="servicebussku"></a>serviceBusSKU

Název služby Service Bus [SKU](https://azure.microsoft.com/pricing/details/service-bus/) vytvořit.

```json
"serviceBusSku": { 
    "type": "string", 
    "allowedValues": [ 
        "Standard",
        "Premium" 
    ], 
    "defaultValue": "Standard", 
    "metadata": { 
        "description": "The messaging tier for service Bus namespace" 
    } 

```

Šablona definuje hodnoty, které jsou povolené pro tento parametr (Standard nebo Premium). Pokud není zadána žádná hodnota, správce prostředků přiřadí výchozí hodnotu (Standard).

Další informace o cenách služby Service Bus, najdete v části [služby Service Bus, ceny a fakturace][Service Bus pricing and billing].

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

### <a name="service-bus-namespace"></a>Obor názvů služby Service Bus

Vytvoří standardní obor názvů služby Service Bus tohoto typu **zasílání zpráv**.

```json
"resources": [
    {
        "apiVersion": "[parameters('serviceBusApiVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "Standard",
        },
        "properties": {
        }
    }
]
```

## <a name="commands-to-run-deployment"></a>Příkazy pro spuštění nasazení

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json
```

## <a name="next-steps"></a>Další postup
Teď, když jste vytvořili a nasadili prostředky pomocí Azure Resource Manageru, další informace o správě těchto prostředků najdete v těchto článcích:

* [Správa služby Service Bus pomocí PowerShellu](service-bus-manage-with-ps.md)
* [Správa prostředků Service Bus pomocí Průzkumníka Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: service-bus-pricing-billing.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
