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
ms.date: 11/06/2018
ms.author: spelluru
ms.openlocfilehash: b0fdfa52d194dfa111c7bacf648c45f6e64fca13
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54430917"
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Vytvoření oboru názvů služby Service Bus pomocí šablony Azure Resource Manageru
V tomto rychlém startu vytvoříte šablonu Azure Resource Manageru, která vytvoří obor názvů služby Service Bus typu **zasílání zpráv** s **standardní** SKU. Tento článek také definuje parametry, které jsou určené pro spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky. Další informace o vytváření šablon najdete v tématu [Tvorba šablon Azure Resource Manageru][Authoring Azure Resource Manager templates]. Úplnou šablonu najdete v článku [šablony obor názvů služby Service Bus] [ Service Bus namespace template] na Githubu.

> [!NOTE]
> Následující šablony Azure Resource Manageru jsou k dispozici ke stažení a nasazení. 
> 
> * [Vytvoření oboru názvů služby Service Bus s frontou](service-bus-resource-manager-namespace-queue.md)
> * [Vytvoření oboru názvů služby Service Bus s tématem a předplatným](service-bus-resource-manager-namespace-topic.md)
> * [Vytvoření oboru názvů služby Service Bus s fronty a autorizační pravidla](service-bus-resource-manager-namespace-auth-rule.md)
> * [Vytvoření oboru názvů služby Service Bus s tématem, předplatné a pravidla](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Vyhledat nejnovější šablony, přejděte [šablony pro rychlý start Azure] [ Azure Quickstart Templates] galerii a vyhledat služby Service Bus.

## <a name="quick-deployment"></a>Rychlé nasazení
Ke spuštění ukázky bez psaní libovolný JSON a spuštění příkazu prostředí PowerShell nebo rozhraní příkazového řádku, výběr na následující tlačítko:

[![Nasazení do Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

Pokud chcete vytvořit a nasadit šablonu ručně, projděte si následující části v tomto článku.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto rychlého startu potřebujete předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Pokud chcete použít **prostředí Azure PowerShell** k nasazení šablony Resource Manageru [instalace Azure Powershellu](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-5.7.0).

Pokud chcete použít **rozhraní příkazového řádku Azure** k nasazení šablony Resource Manageru [instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-the-resource-manager-template-json"></a>Vytvořit kód JSON šablony Resource Manageru 
Vytvořte soubor JSON s názvem **MyServiceBusNamespace.json** s následujícím obsahem: 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusSku": {
            "type": "string",
            "allowedValues": [
                "Basic",
                "Standard",
                "Premium"
            ],
            "defaultValue": "Standard",
            "metadata": {
                "description": "The messaging tier for service Bus namespace"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('serviceBusSku')]"
            }
        }
    ]
}
```

Tato šablona vytvoří standardní obor názvů služby Service Bus. Syntaxi JSON a vlastnostech najdete v tématu [obory názvů](/azure/templates/microsoft.servicebus/namespaces) referenčními informacemi k šablonám.

## <a name="create-the-parameters-json"></a>Vytvoření parametry JSON
Šablona, kterou jste vytvořili v předchozím kroku obsahuje část s názvem `Parameters`. Definujete parametry pro tyto hodnoty, které se liší na základě projektu nasazení nebo v závislosti na cílové prostředí. Tato šablona definuje následující parametry: **serviceBusNamespaceName**, **serviceBusSku**, a **umístění**. Další informace o SKU Service Bus, najdete v článku [skladové položky služby Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) vytvořit.

Vytvořte soubor JSON s názvem **MyServiceBusNamespace-Parameters.json** s následujícím obsahem: 

> [!NOTE] 
> Zadejte název vašeho oboru názvů služby Service Bus. 


```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "value": "<Specify a name for the Service Bus namespace>"
    },
    "serviceBusSku": {
      "value": "Standard"
    },
    "location": {
        "value": "East US"
    }
  }
}
```


## <a name="use-azure-powershell-to-deploy-the-template"></a>Pokud chcete nasadit šablonu pomocí Azure Powershellu

### <a name="sign-in-to-azure"></a>Přihlášení k Azure
1. Spuštění prostředí Azure PowerShell

2. Spuštěním následujícího příkazu se přihlaste k Azure:

   ```azurepowershell
   Login-AzureRmAccount
   ```
3. Pokud máte nastavení aktuálního kontextu předplatného tyto příkazy:

   ```azurepowershell
   Select-AzureRmSubscription -SubscriptionName "<YourSubscriptionName>" 
   ```

### <a name="deploy-resources"></a>Nasazení prostředků
K nasazení prostředků pomocí Azure Powershellu, přejděte do složky, kam jste uložili soubory JSON a spusťte následující příkazy:

> [!IMPORTANT]
> Zadejte název skupiny prostředků Azure jako hodnotu pro $resourceGroupName před spuštěním příkazů. 

1. Deklarujte proměnnou pro název skupiny prostředků a zadejte hodnotu pro něj. 

    ```azurepowershell
    $resourceGroupName = "<Specify a name for the Azure resource group>"
    ```
2. Vytvořte skupinu prostředků Azure.

    ```azurepowershell
    New-AzureRmResourceGroup $resourceGroupName -location 'East US'
    ```
3. Nasazení šablony Resource Manageru. Zadejte názvy samotné nasazení, skupinu prostředků, soubor JSON pro šablonu souboru JSON pro parametry

    ```azurepowershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyServiceBusNamespace.json -TemplateParameterFile MyServiceBusNamespace-Parameters.json
    ```

## <a name="use-azure-cli-to-deploy-the-template"></a>Použití Azure CLI k nasazení šablony

### <a name="sign-in-to-azure"></a>Přihlásit se k Azure

1. Spuštěním následujícího příkazu se přihlaste k Azure:

    ```azurecli
    az login
    ```
2. Nastavte kontext na aktuální předplatné. Nahraďte `MyAzureSub` názvem předplatného Azure, které chcete použít:

    ```azurecli
    az account set --subscription <Name of your Azure subscription>
    ``` 

### <a name="deploy-resources"></a>Nasazení prostředků
Nasazení prostředků pomocí rozhraní příkazového řádku Azure, přejděte do složky, soubory JSON a spusťte následující příkazy:

> [!IMPORTANT]
> Zadejte název pro skupinu prostředků Azure ve skupině az vytvořit příkaz. .

1. Vytvořte skupinu prostředků Azure. 
    ```azurecli
    az group create --name <YourResourceGroupName> --location eastus
    ```

2. Nasazení šablony Resource Manageru. Zadejte název skupiny prostředků, nasazení, soubor JSON pro šablonu, soubor JSON pro parametry.

    ```azurecli
    az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyServiceBusNamespace.json --parameters @MyServiceBusNamespace-Parameters.json
    ```

## <a name="next-steps"></a>Další postup
V tomto článku jste vytvořili obor názvů služby Service Bus. Další rychlé starty, se naučíte vytvořit frontami, tématy nebo předplatnými, zobrazovat a používat: 

- [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
- [Začínáme s tématy Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: service-bus-pricing-billing.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
