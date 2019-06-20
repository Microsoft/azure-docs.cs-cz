---
title: Vytvoření centra událostí se skupina uživatelů – Azure Event Hubs | Dokumentace Microsoftu
description: Vytvořte obor názvů služby Event Hubs pomocí centra událostí a příjemce skupiny pomocí šablon Azure Resource Manageru
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/14/2019
ms.author: shvija
ms.openlocfilehash: 007e016672f8548956b37b961805183a504d6bf0
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154079"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>Rychlý start: Vytvoření centra událostí pomocí šablony Azure Resource Manageru

Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

V tomto rychlém startu vytvoříte Centrum událostí s využitím [šablony Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md). Nasazení šablony Azure Resource Manageru k vytvoření oboru názvů typu [Event Hubs](event-hubs-what-is-event-hubs.md), s jedním centrem událostí. Tento článek ukazuje, jak definovat prostředků, které jsou nasazené a tom, jak definovat parametry, které jsou zadané při spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky. Informace o vytváření šablon najdete v tématu [šablon pro vytváření Azure Resource Manageru][Authoring Azure Resource Manager templates]. Syntaxi JSON a vlastnosti, které chcete použít v šabloně najdete v tématu [typy prostředků Microsoft.EventHub](/azure/templates/microsoft.eventhub/allversions).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

V tomto rychlém startu použijete [existující šablonu Resource Manageru](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json). Další ukázkové šablony najdete v tématu [šablony pro rychlý start Azure](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular).

Pokud chcete nasadit šablonu:

1. Vyberte **vyzkoušet** z následující blok kódu a pak postupujte podle pokynů pro přihlášení ke službě Azure Cloud shell.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   Trvá několik minut pro vytvoření centra událostí.

1. Vyberte **kopírování** zkopírujte skript prostředí PowerShell.
1. Klikněte pravým tlačítkem na konzolu prostředí a pak vyberte **vložit**.

## <a name="verify-the-deployment"></a>Ověření nasazení

Pokud chcete ověřit nasazení, můžete buď otevřít skupinu prostředků z [webu Azure portal](https://portal.azure.com), nebo pomocí následujícího skriptu prostředí Azure PowerShell.  Pokud je stále otevřen službě Cloud shell, není nutné kopírovat nebo spuštění prvního řádku (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků. Pokud je stále otevřen službě Cloud shell, není nutné kopírovat nebo spuštění prvního řádku (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili obor názvů služby Event Hubs a centra událostí v oboru názvů. Podrobné pokyny týkající se odesílání událostí do (nebo) přijímat události z centra událostí, najdete v článku **odesílat a přijímat události** kurzy:

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (pouze pro odesílání)](event-hubs-c-getstarted-send.md)
- [Apache Storm (pouze reecive)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
