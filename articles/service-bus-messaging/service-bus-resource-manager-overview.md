---
title: Vytváření Azure Service Busch prostředků pomocí šablon
description: Použití šablon Azure Resource Manager k automatizaci vytváření prostředků Service Bus
documentationcenter: .net
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: df8a7fde9114f03521f0e57e072f81a867efcf39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89075252"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Vytváření Service Busch prostředků pomocí šablon Azure Resource Manager

Tento článek popisuje, jak vytvořit a nasadit Service Bus prostředky pomocí šablon Azure Resource Manager, PowerShellu a poskytovatele prostředků Service Bus.

Azure Resource Manager šablony vám pomůžou definovat prostředky k nasazení pro řešení a zadat parametry a proměnné, které umožňují zadávat hodnoty pro různá prostředí. Šablona je zapsána ve formátu JSON a skládá se z výrazů, které můžete použít k vytvoření hodnot pro vaše nasazení. Podrobné informace o psaní šablon Azure Resource Manager a diskuzi o formátu šablony najdete v tématu [Struktura a syntaxe šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

> [!NOTE]
> Příklady v tomto článku ukazují, jak pomocí Azure Resource Manager vytvořit obor názvů Service Bus a entitu zasílání zpráv (Queue). Další příklady šablon najdete v [galerii šablon Azure pro rychlý Start][Azure Quickstart Templates gallery] a vyhledejte **Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-bus-resource-manager-templates"></a>Šablony Service Bus Správce prostředků

Tyto šablony Service Bus Azure Resource Manager jsou k dispozici ke stažení a nasazení. Kliknutím na následující odkazy zobrazíte podrobnosti o každém z nich s odkazy na šablony na GitHubu:

* [Vytvoření oboru názvů Service Busu](service-bus-resource-manager-namespace.md)
* [Vytvoření oboru názvů Service Bus s využitím fronty](service-bus-resource-manager-namespace-queue.md)
* [Vytvoření oboru názvů Service Bus s tématem a předplatným](service-bus-resource-manager-namespace-topic.md)
* [Vytvoření oboru názvů Service Bus s použitím fronty a autorizačního pravidla](service-bus-resource-manager-namespace-auth-rule.md)
* [Vytvoření oboru názvů Service Bus s použitím tématu, předplatného a pravidla](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Nasazení s využitím PowerShellu

Následující postup popisuje, jak pomocí PowerShellu nasadit šablonu Azure Resource Manager, která vytvoří obor názvů úrovně Standard Service Bus a frontu v rámci tohoto oboru názvů. Tento příklad je založený na [Vytvoření oboru názvů Service Bus se](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) šablonou Queue. Přibližný pracovní postup je následující:

1. Nainstalujte PowerShell.
2. Vytvořte šablonu a (volitelně) soubor parametrů.
3. V PowerShellu se přihlaste ke svému účtu Azure.
4. Pokud jeden z nich neexistuje, vytvořte novou skupinu prostředků.
5. Otestujte nasazení.
6. V případě potřeby nastavte režim nasazení.
7. Nasazení šablony

Úplné informace o nasazení Azure Resource Manager šablon najdete v tématu [nasazení prostředků pomocí šablon Azure Resource Manager][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>Instalace PowerShellu

Nainstalujte Azure PowerShell podle pokynů v tématu [Začínáme s Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Vytvoření šablony

Naklonujte úložiště nebo zkopírujte šablonu [201-ServiceBus-Create-Queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) z GitHubu:

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

Chcete-li použít nepovinný soubor parametrů, zkopírujte soubor [201-ServiceBus-Create-Queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) . Nahraďte hodnotu `serviceBusNamespaceName` názvem oboru názvů Service Bus, který chcete v tomto nasazení vytvořit, a nahraďte hodnotu `serviceBusQueueName` názvem fronty, kterou chcete vytvořit.

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

Další informace najdete v článku o [parametrech](../azure-resource-manager/templates/parameter-files.md) .

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Přihlaste se k Azure a nastavte předplatné Azure.

Z příkazového řádku PowerShellu spusťte následující příkaz:

```powershell
Connect-AzAccount
```

Zobrazí se výzva, abyste se přihlásili ke svému účtu Azure. Po přihlášení spusťte následující příkaz, který zobrazí vaše dostupná předplatná:

```powershell
Get-AzSubscription
```

Tento příkaz vrátí seznam dostupných předplatných Azure. Spusťte následující příkaz a vyberte odběr aktuální relace. Nahraďte `<YourSubscriptionId>` identifikátorem GUID předplatného Azure, které chcete použít:

```powershell
Set-AzContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Nastavení skupiny prostředků

Pokud nemáte existující skupinu prostředků, vytvořte novou skupinu prostředků pomocí příkazu **New-AzResourceGroup** . Zadejte název skupiny prostředků a umístění, které chcete použít. Například:

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

Ověřte nasazení spuštěním `Test-AzResourceGroupDeployment` rutiny. Při testování nasazení zadejte parametry přesně stejně jako při spuštění nasazení.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Vytvoření nasazení

Pokud chcete vytvořit nové nasazení, spusťte `New-AzResourceGroupDeployment` rutinu a po zobrazení výzvy zadejte potřebné parametry. Parametry zahrnují název vašeho nasazení, název vaší skupiny prostředků a cestu nebo adresu URL k souboru šablony. Pokud není zadán parametr **Mode** , je použita výchozí hodnota pro **přírůstkové** . Další informace najdete v tématu [přírůstková a kompletní nasazení](../azure-resource-manager/templates/deployment-modes.md).

Následující příkaz vás vyzve k zadání tří parametrů v okně PowerShellu:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Chcete-li místo toho zadat soubor parametrů, použijte následující příkaz:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Vložené parametry můžete použít také při spuštění rutiny nasazení. Příkaz vypadá takto:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Chcete-li spustit [kompletní](../azure-resource-manager/templates/deployment-modes.md) nasazení, nastavte parametr **Mode** na hodnotu **Dokončit**:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Ověření nasazení
Pokud se prostředky úspěšně nasazují, v okně PowerShellu se zobrazí souhrn nasazení:

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
Nyní jste viděli základní pracovní postup a příkazy pro nasazení šablony Azure Resource Manager. Podrobnější informace najdete na následujících odkazech:

* [Přehled Azure Resource Manager][Azure Resource Manager overview]
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu][Deploy resources with Azure Resource Manager templates]
* [Tvorba šablon Azure Resource Manageru](../azure-resource-manager/templates/template-syntax.md)
* [Typy prostředků Microsoft. ServiceBus](/azure/templates/microsoft.servicebus/allversions)

[Azure Resource Manager overview]: ../azure-resource-manager/management/overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/templates/deploy-powershell.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
