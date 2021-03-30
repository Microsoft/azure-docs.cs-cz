---
title: Vytvoření oboru názvů Azure Service Bus pomocí šablony
description: Použití šablony Azure Resource Manager k vytvoření oboru názvů pro zasílání zpráv Service Bus
documentationcenter: .net
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 1b7aafca331170100ce99c084a11c96c97df7781
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88067388"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Vytvoření oboru názvů Service Bus pomocí šablony Azure Resource Manager

Naučte se, jak nasadit šablonu Azure Resource Manager pro vytvoření oboru názvů Service Bus. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky. Další informace o vytváření šablon naleznete v [dokumentaci Azure Resource Manager](../azure-resource-manager/index.yml).

K dispozici jsou také následující šablony pro vytváření Service Bus oborů názvů:

* [Vytvoření oboru názvů Service Bus s využitím fronty](./service-bus-resource-manager-namespace-queue.md)
* [Vytvoření oboru názvů Service Bus s tématem a předplatným](./service-bus-resource-manager-namespace-topic.md)
* [Vytvoření oboru názvů Service Bus s použitím fronty a autorizačního pravidla](./service-bus-resource-manager-namespace-auth-rule.md)
* [Vytvoření oboru názvů Service Bus s použitím tématu, předplatného a pravidla](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="create-a-service-bus-namespace"></a>Vytvoření oboru názvů služby Service Bus

V tomto rychlém startu použijete [existující šablonu správce prostředků](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/):

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Další ukázky šablon najdete v tématu [šablony rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

Vytvoření oboru názvů služby Service Bus nasazením šablony:

1. Vyberte **vyzkoušet** z následujícího bloku kódu a pak podle pokynů se přihlaste ke službě Azure Cloud Shell.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    Název skupiny prostředků je název oboru názvů Service Bus s připojeným **RG** .

2. Vyberte **Kopírovat** a zkopírujte skript prostředí PowerShell.
3. Klikněte pravým tlačítkem na konzolu prostředí a pak vyberte **Vložit**.

Vytvoření centra událostí chvíli trvá.

## <a name="verify-the-deployment"></a>Ověření nasazení

Pokud chcete zobrazit nasazený obor názvů služby Service Bus, můžete buď otevřít skupinu prostředků z Azure Portal, nebo použít následující skript Azure PowerShell. Pokud je Cloud Shell stále otevřený, nemusíte kopírovat/spouštět první a druhý řádek následujícího skriptu.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Azure PowerShell slouží k nasazení šablony v tomto kurzu. Další metody nasazení šablony naleznete zde:

* [Pomocí Azure Portal](../azure-resource-manager/templates/deploy-portal.md).
* [Pomocí Azure CLI](../azure-resource-manager/templates/deploy-cli.md).
* [Pomocí REST API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků. Pokud je Cloud Shell stále otevřený, nemusíte kopírovat/spouštět první a druhý řádek následujícího skriptu.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili obor názvů Service Bus. V ostatních rychlých startech se dozvíte, jak vytvořit fronty, témata a předplatná a používat je:

* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Začínáme s tématy služby Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)