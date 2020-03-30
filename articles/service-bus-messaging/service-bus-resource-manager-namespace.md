---
title: Vytvoření oboru názvů Azure Service Bus pomocí šablony
description: Vytvoření oboru názvů zasílání zpráv služby Service Bus pomocí šablony Správce prostředků Azure
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
ms.openlocfilehash: 5febdd63ab6f854ca3244f8449f6f715a75e735f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264471"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Vytvoření oboru názvů Service Bus pomocí šablony Azure Resource Manager

Zjistěte, jak nasadit šablonu Azure Resource Manager k vytvoření oboru názvů Service Bus. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky. Další informace o vytváření šablon najdete v [dokumentaci ke Správci prostředků Azure](/azure/azure-resource-manager/).

Následující šablony jsou také k dispozici pro vytváření oborů názvů service bus:

* [Vytvoření oboru názvů service bus s frontou](./service-bus-resource-manager-namespace-queue.md)
* [Vytvoření oboru názvů Service Bus s tématem a odběrem](./service-bus-resource-manager-namespace-topic.md)
* [Vytvoření oboru názvů service bus s pravidlem fronty a autorizace](./service-bus-resource-manager-namespace-auth-rule.md)
* [Vytvoření oboru názvů Service Bus s tématem, předplatným a pravidlem](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="create-a-service-bus-namespace"></a>Vytvoření oboru názvů sběrnice služby Service Bus

V tomto rychlém startu použijete [existující šablonu Správce prostředků](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) ze šablon Azure [QuickStart](https://azure.microsoft.com/resources/templates/):

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Další ukázky šablon najdete v [tématu Šablony rychlého spuštění Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

Vytvoření oboru názvů sběrnice nasazením šablony:

1. Vyberte **Vyzkoušet** z následujícího bloku kódu a postupujte podle pokynů pro přihlášení do prostředí Azure Cloud.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    Název skupiny prostředků je název oboru názvů servisní sběrnice s připojeným **rg.**

2. Vyberte **Kopírovat,** chcete-li zkopírovat skript PowerShellu.
3. Klepněte pravým tlačítkem myši na konzolu prostředí a vyberte příkaz **Vložit**.

Vytvoření centra událostí trvá několik okamžiků.

## <a name="verify-the-deployment"></a>Ověření nasazení

Pokud chcete zobrazit nasazené obor názvů service bus, můžete buď otevřít skupinu prostředků z webu Azure Portal, nebo použít následující skript Azure PowerShellu. Pokud je prostředí Cloud stále otevřené, nemusíte kopírovat/spouštět první a druhý řád následujícího skriptu.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Azure PowerShell se používá k nasazení šablony v tomto kurzu. Další metody nasazení šablony naleznete v tématu:

* [Pomocí portálu Azure](../azure-resource-manager/templates/deploy-portal.md).
* [Pomocí azure cli](../azure-resource-manager/templates/deploy-cli.md).
* [Pomocí rozhraní REST API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků. Pokud je prostředí Cloud stále otevřené, nemusíte kopírovat/spouštět první a druhý řád následujícího skriptu.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili obor názvů Service Bus. Další rychlé starty se dozvíte, jak vytvářet fronty, témata/odběry a jak je používat:

* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Začínáme s tématy služby Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
