---
title: 'Úvodní příručka: Vytvoření centra událostí se skupinou spotřebitelů – Azure Event Hubs'
description: 'Úvodní příručka: Vytvoření oboru názvů Centra událostí pomocí centra událostí a skupiny spotřebitelů pomocí šablon Azure Resource Manager'
services: event-hubs
documentationcenter: .net
author: spelluru
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 02/11/2020
ms.author: spelluru
ms.custom: subject-armqs
ms.openlocfilehash: c2221fe5b5ab38afbdde167e5bcbf6b47ed4f861
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79528077"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>Úvodní příručka: Vytvoření centra událostí pomocí šablony Azure Resource Manageru

Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md). V tomto rychlém startu vytvoříte centrum událostí pomocí [šablony Azure Resource Manager](../azure-resource-manager/management/overview.md). Nasadíte šablonu Azure Resource Manager k vytvoření oboru názvů typu [Event Hubs](event-hubs-what-is-event-hubs.md)s jedním centrem událostí.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Žádné.

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure QuickStart](https://azure.microsoft.com/resources/templates/101-eventhubs-create-namespace-and-eventhub/).

:::code language="json" source="~/quickstart-templates/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json" range="1-61" highlight="32-59":::

Mezi prostředky definované v šabloně patří:

- [**Obory názvů Microsoft.EventHub/Namespaces**](/azure/templates/microsoft.eventhub/namespaces)
- [**Microsoft.EventHub/obory názvů/eventhubs**](/azure/templates/microsoft.eventhub/namespaces/eventhubs)

Další ukázky šablon najdete v [tématu Šablony rychlého spuštění Azure](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Nasazení šablony

Nasazení šablony:

1. Vyberte **Vyzkoušet** z následujícího bloku kódu a postupujte podle pokynů pro přihlášení do Prostředí Azure Cloud Shell.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   Vytvoření centra událostí trvá několik okamžiků.

1. Vyberte **Kopírovat,** chcete-li zkopírovat skript PowerShellu.
1. Klepněte pravým tlačítkem myši na konzolu prostředí a vyberte příkaz **Vložit**.

## <a name="verify-the-deployment"></a>Ověření nasazení

Chcete-li ověřit nasazení, můžete buď otevřít skupinu prostředků z [webu Azure Portal](https://portal.azure.com), nebo použít následující skript Azure PowerShell.  Pokud cloudové prostředí je stále otevřené, nemusíte kopírovat/spouštět první řádek (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků. Pokud cloudové prostředí je stále otevřené, nemusíte kopírovat/spouštět první řádek (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili obor názvů Event Hubs a centrum událostí v oboru názvů. Podrobné pokyny k odesílání událostí do centra událostí (nebo) příjmu událostí najdete v tématu **Kurzy odesílání a přijímání událostí:**

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [Javascript](get-started-java-send-v2.md)
- [Přejít](event-hubs-go-get-started-send.md)
- [C (jenom odesílání)](event-hubs-c-getstarted-send.md)
- [Apache Storm (jenom příjem)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
