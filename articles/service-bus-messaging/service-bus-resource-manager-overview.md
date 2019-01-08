---
title: Vytvořit prostředky služby Azure Service Bus pomocí šablon Resource Manageru | Dokumentace Microsoftu
description: Automatizace vytváření prostředků služby Service Bus pomocí šablon Azure Resource Manageru
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 24f6a207-0fa4-49cf-8a58-963f9e2fd655
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 09/11/2018
ms.author: spelluru
ms.openlocfilehash: e23173b006fcc83f0e4b30b59a65e772b68a612d
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062005"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Vytvořit prostředky služby Service Bus pomocí šablon Azure Resource Manageru

Tento článek popisuje, jak vytvořit a nasadit prostředky služby Service Bus pomocí šablon Azure Resource Manageru, Powershellu a poskytovatele prostředků služby Service Bus.

Šablony Azure Resource Manageru umožňují definovat prostředky pro řešení nasadit a zadejte parametry a proměnné, které vám umožní zadat hodnoty pro různá prostředí. Šablona je zapsán ve formátu JSON a skládá se z výrazů, které můžete použít k vytvoření hodnot pro vaše nasazení. Podrobné informace o vytváření šablon Azure Resource Manageru a diskuzi o formátu šablony najdete v tématu [struktury a syntaxe šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md).

> [!NOTE]
> V příkladech v tomto článku ukazují, jak pomocí Azure Resource Manageru k vytvoření oboru názvů Service Bus a entity pro zasílání zpráv (fronty). Další příklady šablon, přejděte [Galerie šablon rychlý start Azure] [ Azure Quickstart Templates gallery] a vyhledejte **služby Service Bus**.
>
>

## <a name="service-bus-resource-manager-templates"></a>Šablony správce prostředků služby Service Bus

Tyto šablony služby Service Bus Azure Resource Manageru jsou k dispozici ke stažení a nasazení. Klikněte na podrobnosti o každé z nich, s odkazy na šablony na Githubu prostřednictvím následujících odkazů:

* [Vytvoření oboru názvů služby Service Bus](service-bus-resource-manager-namespace.md)
* [Vytvoření oboru názvů služby Service Bus s frontou](service-bus-resource-manager-namespace-queue.md)
* [Vytvoření oboru názvů služby Service Bus s tématem a předplatným](service-bus-resource-manager-namespace-topic.md)
* [Vytvoření oboru názvů služby Service Bus s fronty a autorizační pravidla](service-bus-resource-manager-namespace-auth-rule.md)
* [Vytvoření oboru názvů služby Service Bus s tématem, předplatné a pravidla](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Nasazení s využitím PowerShellu

Následující postup popisuje použití Powershellu k nasazení šablony Azure Resource Manageru, která vytvoří obor názvů služby Service Bus úrovně Standard a fronty v daném oboru názvů. Tento příklad je založen na [vytvoření oboru názvů služby Service Bus s frontou](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) šablony. Přibližná pracovní postup je následující:

1. Instalace Powershellu.
2. Vytvoření šablony a (volitelně) soubor s parametry.
3. V prostředí PowerShell Přihlaste se ke svému účtu Azure.
4. Pokud ještě neexistuje, vytvořte novou skupinu prostředků.
5. Test nasazení.
6. V případě potřeby nastavte režim nasazení.
7. Nasazení šablony.

Kompletní informace o nasazení šablony Azure Resource Manageru najdete v tématu [nasazení prostředků pomocí šablon Azure Resource Manageru][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>Instalace PowerShellu

Nainstalovat Azure PowerShell podle pokynů v [Začínáme s Azure Powershellem](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Vytvoření šablony

Naklonujte úložiště nebo kopírování [201-servicebus vytvořit queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) šablony z Githubu:

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
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    }
  },
  "variables": {
    "defaultSASKeyName": "RootManageSharedAccessKey",
    "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]",
    "sbVersion": "2017-04-01"
  },
  "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('serviceBusNamespaceName')]",
      "type": "Microsoft.ServiceBus/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "type": "Queues",
          "dependsOn": [
            "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "lockDuration": "PT5M",
            "maxSizeInMegabytes": "1024",
            "requiresDuplicateDetection": "false",
            "requiresSession": "false",
            "defaultMessageTimeToLive": "P10675199DT2H48M5.4775807S",
            "deadLetteringOnMessageExpiration": "false",
            "duplicateDetectionHistoryTimeWindow": "PT10M",
            "maxDeliveryCount": "10",
            "autoDeleteOnIdle": "P10675199DT2H48M5.4775807S",
            "enablePartitioning": "false",
            "enableExpress": "false"
          }
        }
      ]
    }
  ],
  "outputs": {
    "NamespaceConnectionString": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
    },
    "SharedAccessPolicyPrimaryKey": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
    }
  }
}
```

### <a name="create-a-parameters-file-optional"></a>Vytvoření souboru parametrů (volitelné)

Chcete-li použít soubor volitelné parametry, zkopírujte [201-servicebus vytvořit queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) souboru. Nahraďte hodnotu `serviceBusNamespaceName` s názvem oboru názvů Service Bus, kterou chcete vytvořit v tomto nasazení a nahraďte hodnotu `serviceBusQueueName` s názvem fronty, kterou chcete vytvořit.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2017-04-01"
        }
    }
}
```

Další informace najdete v tématu [parametry](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) článku.

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Připojte se k Azure a nastavte předplatné Azure

Z příkazového řádku Powershellu spusťte následující příkaz:

```powershell
Connect-AzureRmAccount
```

Zobrazí se výzva k přihlášení k účtu Azure. Po přihlášení, spusťte následující příkaz, chcete-li zobrazit dostupná předplatná:

```powershell
Get-AzureRMSubscription
```

Tento příkaz vrátí seznam hodnot dostupná předplatná Azure. Spuštěním následujícího příkazu vyberte předplatné pro aktuální relaci. Nahraďte `<YourSubscriptionId>` s identifikátorem GUID předplatného Azure, kterou chcete použít:

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Nastavit skupinu prostředků

Pokud nemáte existující prostředek skupiny, vytvořte novou skupinu prostředků s ** New-AzureRmResourceGroup ** příkazu. Zadejte název skupiny prostředků a umístění, které chcete použít. Příklad:

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

V případě úspěchu, zobrazí se souhrn novou skupinu prostředků.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Otestování nasazení

Ověřit nasazení spuštěním `Test-AzureRmResourceGroupDeployment` rutiny. Při testování nasazení, zadejte parametry stejným způsobem jako při spuštění nasazení.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Vytvoření nasazení

Pokud chcete vytvořit nové nasazení, spusťte `New-AzureRmResourceGroupDeployment` rutiny a zadejte potřebné parametry po zobrazení výzvy. Parametry jsou název pro nasazení, název vaší skupiny prostředků a cesta nebo adresa URL k souboru šablony. Pokud **režimu** parametr není zadán, výchozí hodnota **přírůstkové** se používá. Další informace najdete v tématu [přírůstkové a úplné nasazení](../azure-resource-manager/deployment-modes.md).

Následující příkaz vás vyzve k zadání tři parametry v okně Powershellu:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Chcete-li místo toho zadejte soubor parametrů, použijte následující příkaz:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Můžete také použít vložených parametrů při spuštění rutiny nasazení. Příkaz vypadá takto:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Ke spuštění [kompletní](../azure-resource-manager/deployment-modes.md) nasazení, nastavte **režimu** parametr **Complete**:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Ověření nasazení
Pokud se prostředky nasadí úspěšně, zobrazí se souhrn nasazení v okně Powershellu:

```powershell
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2017 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2017-04-01

```

## <a name="next-steps"></a>Další postup
Nyní jste viděli základní pracovní postup a příkazy pro nasazení šablony Azure Resource Manageru. Podrobnější informace naleznete pod těmito odkazy:

* [Přehled Azure Resource Manageru][Azure Resource Manager overview]
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure Powershellu][Deploy resources with Azure Resource Manager templates]
* [Tvorba šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md)
* [Typy prostředků pro Microsoft.ServiceBus](/azure/templates/microsoft.servicebus/allversions)

[Azure Resource Manager overview]: ../azure-resource-manager/resource-group-overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/resource-group-template-deploy.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
