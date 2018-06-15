---
title: Vytvořit prostředky Azure Service Bus pomocí šablony Resource Manageru | Microsoft Docs
description: Použití šablon Azure Resource Manageru k automatizaci vytváření prostředků služby Service Bus
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 24f6a207-0fa4-49cf-8a58-963f9e2fd655
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/11/2018
ms.author: sethm
ms.openlocfilehash: c8d84de608ccf3d9a9293c20c07c10a00b73da68
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31598281"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Vytvoření služby Service Bus prostředků pomocí šablony Azure Resource Manager

Tento článek popisuje postup vytvoření a nasazení prostředků služby Service Bus pomocí šablony Azure Resource Manager, prostředí PowerShell a zprostředkovatele prostředků služby Service Bus.

Šablony Azure Resource Manageru můžete definovat, které prostředky pro řešení nasadit a určit parametry a proměnné, které vám umožní zadat hodnoty pro různá prostředí. Šablona je napsaný ve formátu JSON a se skládá z výrazů, které můžete použít k vytvoření hodnot pro vaše nasazení. Podrobné informace o vytváření šablon Azure Resource Manageru a diskuzi o formátu šablony najdete v tématu [syntaxi šablon Azure Resource Manager a struktura](../azure-resource-manager/resource-group-authoring-templates.md).

> [!NOTE]
> V příkladech v tomto článku ukazují, jak pomocí Správce prostředků Azure k vytvoření oboru názvů Service Bus a entity zasílání zpráv (fronty). Další příklady šablony najdete v článku [galerii šablon Azure rychlý Start] [ Azure Quickstart Templates gallery] a vyhledejte **Service Bus**.
>
>

## <a name="service-bus-resource-manager-templates"></a>Šablony služby sběrnice Resource Manager

Tyto šablony správce prostředků Azure Service Bus jsou k dispozici ke stažení a nasazení. Kliknutím na následující odkazy podrobnosti o každém z nich, s odkazy na šablony na Githubu:

* [Vytvoření oboru názvů Service Bus](service-bus-resource-manager-namespace.md)
* [Vytvoření oboru názvů Service Bus pomocí fronty](service-bus-resource-manager-namespace-queue.md)
* [Vytvoření oboru názvů Service Bus s téma a odběr](service-bus-resource-manager-namespace-topic.md)
* [Vytvoření oboru názvů Service Bus pomocí fronty a autorizační pravidla](service-bus-resource-manager-namespace-auth-rule.md)
* [Vytvoření oboru názvů Service Bus pomocí tématu, předplatné a pravidla](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Nasazení s využitím PowerShellu

Následující postup popisuje, jak pomocí prostředí PowerShell pro nasazení šablonu Azure Resource Manager, který vytvoří obor názvů sběrnice úrovně Standard a fronty v daném oboru názvů. Tento příklad vychází z [vytvoření oboru názvů Service Bus s frontou](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) šablony. Přibližná pracovní postup je následující:

1. Instalace prostředí PowerShell.
2. Vytvořte šablonu a (volitelně) ze souboru parametrů.
3. V prostředí PowerShell Přihlaste se k účtu Azure.
4. Pokud žádný neexistuje, vytvořte novou skupinu prostředků.
5. Testovací nasazení.
6. V případě potřeby nastavte režim nasazení.
7. Nasazení šablony.

Úplné informace o nasazení šablony Azure Resource Manager najdete v tématu [nasazení prostředků pomocí šablony Azure Resource Manager][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>Instalace PowerShellu

Nainstalovat Azure PowerShell podle pokynů v [Začínáme s Azure Powershellem](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Vytvoření šablony

Klonování úložiště nebo kopírování [201-servicebus vytvořit queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) šablony z Githubu:

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

### <a name="create-a-parameters-file-optional"></a>Vytvořte soubor parametrů (volitelné)

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

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Přihlaste se k Azure a nastavte předplatné Azure

Z řádku prostředí PowerShell spusťte následující příkaz:

```powershell
Connect-AzureRmAccount
```

Zobrazí se výzva k přihlášení k účtu Azure. Po přihlášení, spusťte následující příkaz k zobrazení dostupných předplatných:

```powershell
Get-AzureRMSubscription
```

Tento příkaz vrátí seznam dostupných předplatných Azure. Spuštěním následujícího příkazu vyberte předplatné pro aktuální relaci. Nahraďte `<YourSubscriptionId>` s identifikátorem GUID pro předplatné Azure, kterou chcete použít:

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Nastavit skupinu prostředků

Pokud nemáte existující prostředek skupiny, vytvořte novou skupinu prostředků s ** New-AzureRmResourceGroup ** příkaz. Zadejte název skupiny prostředků a umístění, do kterého chcete použít. Příklad:

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Pokud bylo úspěšné, zobrazí se souhrn novou skupinu prostředků.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Otestování nasazení

Ověření nasazení tak, že spustíte `Test-AzureRmResourceGroupDeployment` rutiny. Při testování nasazení, zadejte parametry přesně stejně jako při provádění nasazení.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Vytvoření nasazení

Chcete-li vytvořit nové nasazení, spusťte `New-AzureRmResourceGroupDeployment` rutiny a zadejte potřebné parametry po zobrazení výzvy. Parametry jsou název pro vaše nasazení, název vaší skupiny prostředků a cesta nebo adresa URL k souboru šablony. Pokud **režimu** není zadán parametr, výchozí hodnota **přírůstkové** se používá. Další informace najdete v tématu [přírůstkové a úplné nasazení](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments).

Následující příkaz vás vyzve k zadání tři parametry v okně prostředí PowerShell:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Místo toho zadat soubor parametrů, použijte následující příkaz:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Vložené parametry můžete použít také při spuštění rutiny nasazení. Příkaz vypadá takto:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Ke spuštění [dokončení](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) nasazení, nastavte **režimu** parametru **Complete**:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Ověření nasazení
Pokud prostředky jsou nasazeny úspěšně, zobrazí se souhrn nasazení v okně prostředí PowerShell:

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
Nyní jste se seznámili základní pracovní postup a příkazy pro nasazení šablonu Azure Resource Manager. Podrobnější informace získáte pomocí následujících odkazů:

* [Přehled Azure Resource Manageru][Azure Resource Manager overview]
* [Nasazení prostředků pomocí šablony Resource Manageru a prostředí Azure PowerShell][Deploy resources with Azure Resource Manager templates]
* [Tvorba šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md)

[Azure Resource Manager overview]: ../azure-resource-manager/resource-group-overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/resource-group-template-deploy.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
