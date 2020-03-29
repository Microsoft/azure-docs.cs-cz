---
title: Vytvoření prostředků Azure Service Bus pomocí šablon
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
ms.openlocfilehash: 9bc784ee57b9bde393408cbefa9a197aebc59b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264454"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Vytváření prostředků služby Service Bus pomocí šablon Azure Resource Manager

Tento článek popisuje, jak vytvořit a nasadit prostředky service bus pomocí šablon Azure Resource Manager, PowerShellu a poskytovatele prostředků service bus.

Šablony Azure Resource Manageru vám pomůžou definovat prostředky, které se mají nasadit pro řešení, a určit parametry a proměnné, které umožňují vstupní hodnoty pro různá prostředí. Šablona je napsána v JSON a skládá se z výrazů, které můžete použít k vytvoření hodnot pro vaše nasazení. Podrobné informace o psaní šablon Azure Resource Manageru a diskusi o formátu šablony najdete v [tématu struktura a syntaxe šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

> [!NOTE]
> Příklady v tomto článku ukazují, jak pomocí Správce prostředků Azure vytvořit obor názvů Service Bus a entitu zasílání zpráv (frontu). Další příklady šablon najdete v [galerii šablon Azure QuickStart][Azure Quickstart Templates gallery] a vyhledejte **službu Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-bus-resource-manager-templates"></a>Šablony Správce prostředků služby Service Bus

Tyto šablony Service Bus Azure Resource Manager jsou k dispozici ke stažení a nasazení. Kliknutím na následující odkazy zobrazíte podrobnosti o jednotlivých textech s odkazy na šablony na GitHubu:

* [Vytvoření oboru názvů Service Bus](service-bus-resource-manager-namespace.md)
* [Vytvoření oboru názvů service bus s frontou](service-bus-resource-manager-namespace-queue.md)
* [Vytvoření oboru názvů Service Bus s tématem a odběrem](service-bus-resource-manager-namespace-topic.md)
* [Vytvoření oboru názvů service bus s pravidlem fronty a autorizace](service-bus-resource-manager-namespace-auth-rule.md)
* [Vytvoření oboru názvů Service Bus s tématem, předplatným a pravidlem](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Nasazení s využitím PowerShellu

Následující postup popisuje, jak pomocí prostředí PowerShell nasadit šablonu Azure Resource Manager, která vytvoří obor názvů Service Bus úrovně Standard tier a frontu v tomto oboru názvů. Tento příklad je založen na [šabloně názvů Vytvořit servisní sběrnici se šablonou fronty.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) Přibližný pracovní postup je následující:

1. Nainstalujte PowerShell.
2. Vytvořte šablonu a (volitelně) soubor parametrů.
3. V PowerShellu se přihlaste ke svému účtu Azure.
4. Vytvořte novou skupinu prostředků, pokud neexistuje.
5. Otestujte nasazení.
6. V případě potřeby nastavte režim nasazení.
7. Nasaďte šablonu.

Úplné informace o nasazení šablon Azure Resource Manageru najdete v tématu [Nasazení prostředků pomocí šablon Azure Resource Manageru][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>Instalace PowerShellu

Nainstalujte Azure PowerShell podle pokynů v [začínáme s Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Vytvoření šablony

Klonujte úložiště nebo zkopírujte šablonu [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) z GitHubu:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
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

Chcete-li použít soubor volitelných parametrů, zkopírujte soubor [201-servicebus-create-queue.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) Nahraďte `serviceBusNamespaceName` hodnotu názvem oboru názvů Service Bus, který chcete vytvořit v `serviceBusQueueName` tomto nasazení, a nahraďte hodnotu názvem fronty, kterou chcete vytvořit.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

Další informace naleznete v článku [Parametry.](../azure-resource-manager/templates/parameter-files.md)

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Přihlášení do Azure a nastavení předplatného Azure

Z výzvy prostředí PowerShell spusťte následující příkaz:

```powershell
Connect-AzAccount
```

Budete vyzváni k přihlášení k účtu Azure. Po přihlášení zobrazte dostupná předplatná následujícím příkazem:

```powershell
Get-AzSubscription
```

Tento příkaz vrátí seznam dostupných předplatných Azure. Vyberte předplatné pro aktuální relaci spuštěním následujícího příkazu. Nahraďte `<YourSubscriptionId>` identifikátorem GUID pro předplatné Azure, které chcete použít:

```powershell
Set-AzContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Nastavení skupiny prostředků

Pokud nemáte existující skupinu prostředků, vytvořte novou skupinu prostředků pomocí příkazu **New-AzResourceGroup.** Zadejte název skupiny prostředků a umístění, které chcete použít. Například:

```powershell
New-AzResourceGroup -Name MyDemoRG -Location "West US"
```

V případě úspěchu se zobrazí souhrn nové skupiny prostředků.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Otestování nasazení

Ověřte nasazení `Test-AzResourceGroupDeployment` spuštěním rutiny. Při testování nasazení zadejte parametry přesně tak, jak byste při provádění nasazení.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Vytvoření nasazení

Chcete-li vytvořit nové `New-AzResourceGroupDeployment` nasazení, spusťte rutinu a po zobrazení výzvy zadejte potřebné parametry. Parametry zahrnují název vašeho nasazení, název skupiny prostředků a cestu nebo adresu URL k souboru šablony. Pokud není zadán parametr **Mode,** použije se výchozí hodnota **Přírůstková.** Další informace naleznete [v tématu Přírůstková a úplná nasazení](../azure-resource-manager/templates/deployment-modes.md).

Následující příkaz zobrazí výzvu pro tři parametry v okně Prostředí PowerShell:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Chcete-li místo toho určit soubor parametrů, použijte následující příkaz:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Při spuštění rutiny nasazení můžete také použít vsazené parametry. Příkaz vypadá takto:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Chcete-li spustit [úplné](../azure-resource-manager/templates/deployment-modes.md) nasazení, nastavte parametr **Režim** na **Hodnotu Dokončeno**:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Ověření nasazení
Pokud jsou prostředky nasazeny úspěšně, zobrazí se v okně Prostředí PowerShell souhrn nasazení:

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

## <a name="next-steps"></a>Další kroky
Teď jste viděli základní pracovní postup a příkazy pro nasazení šablony Azure Resource Manager. Podrobnější informace naleznete na následujících odkazech:

* [Přehled Správce prostředků Azure][Azure Resource Manager overview]
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu][Deploy resources with Azure Resource Manager templates]
* [Tvorba šablon Azure Resource Manageru](../azure-resource-manager/templates/template-syntax.md)
* [Typy prostředků Microsoft.ServiceBus](/azure/templates/microsoft.servicebus/allversions)

[Azure Resource Manager overview]: ../azure-resource-manager/management/overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/templates/deploy-powershell.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
