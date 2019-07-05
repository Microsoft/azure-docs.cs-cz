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
ms.date: 06/21/2019
ms.author: spelluru
ms.openlocfilehash: 4162775153a48dc8ea28e06f7c99f9927b9c602a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444758"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Vytvoření oboru názvů služby Service Bus s použitím šablony Azure Resource Manageru

Zjistěte, jak nasadit šablonu Azure Resource Manageru k vytvoření oboru názvů služby Service Bus. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky. Další informace o vytváření šablon najdete v tématu [dokumentace ke službě Azure Resource Manageru](/azure/azure-resource-manager/).

Následující šablony jsou také k dispozici pro vytváření obory názvů služby Service Bus:

* [Vytvoření oboru názvů služby Service Bus s frontou](./service-bus-resource-manager-namespace-queue.md)
* [Vytvoření oboru názvů služby Service Bus s tématem a předplatným](./service-bus-resource-manager-namespace-topic.md)
* [Vytvoření oboru názvů služby Service Bus s fronty a autorizační pravidla](./service-bus-resource-manager-namespace-auth-rule.md)
* [Vytvoření oboru názvů služby Service Bus s tématem, předplatné a pravidla](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-a-service-bus-namespace"></a>Vytvořit obor názvů služby Service bus

V tomto rychlém startu použijete [existující šablonu Resource Manageru](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) z [šablony pro rychlý start Azure](https://azure.microsoft.com/resources/templates/):

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Další ukázkové šablony najdete v tématu [šablony pro rychlý start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

Vytvoření oboru názvů služby Service bus nasazením šablony:

1. Vyberte **vyzkoušet** z následující blok kódu a pak postupujte podle pokynů pro přihlášení ke službě Azure Cloud shell.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    Název skupiny prostředků je název oboru názvů služby Service bus s **rg** připojí.

2. Vyberte **kopírování** zkopírujte skript prostředí PowerShell.
3. Klikněte pravým tlačítkem na konzolu prostředí a pak vyberte **vložit**.

Trvá několik minut pro vytvoření centra událostí.

## <a name="verify-the-deployment"></a>Ověření nasazení

Obor názvů nasazené služby Service bus najdete můžete otevřít skupinu prostředků na webu Azure Portal nebo pomocí následujícího skriptu prostředí Azure PowerShell. Pokud službě Cloud shell je stále otevřen, není nutné kopírovat nebo spuštění prvního a druhého řádky následující skript.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Prostředí Azure PowerShell slouží k nasazení šablony v tomto kurzu. Další způsoby nasazení šablony najdete v tématu:

* [Pomocí webu Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md).
* [Pomocí Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md).
* [Pomocí rozhraní REST API](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků. Pokud službě Cloud shell je stále otevřen, není nutné kopírovat nebo spuštění prvního a druhého řádky následující skript.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili obor názvů služby Service Bus. Další rychlé starty, se naučíte vytvořit frontami, tématy nebo předplatnými, zobrazovat a používat:

* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Začínáme s tématy Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
