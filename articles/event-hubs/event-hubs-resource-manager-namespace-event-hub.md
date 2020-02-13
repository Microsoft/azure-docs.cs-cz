---
title: 'Rychlý Start: vytvoření centra událostí se skupinou uživatelů – Azure Event Hubs'
description: 'Rychlý Start: vytvoření oboru názvů Event Hubs s centrem událostí a skupinou uživatelů pomocí šablon Azure Resource Manager'
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
ms.openlocfilehash: 88cd29af75239f0ad79eb78b5ff8e106c3b2ee56
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163071"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>Rychlý Start: vytvoření centra událostí pomocí šablony Azure Resource Manager

Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

V tomto rychlém startu vytvoříte centrum událostí pomocí [šablony Azure Resource Manager](../azure-resource-manager/management/overview.md). Nasadíte šablonu Azure Resource Manager pro vytvoření oboru názvů typu [Event Hubs](event-hubs-what-is-event-hubs.md)s jedním centrem událostí. Tento článek ukazuje, jak definovat prostředků, které jsou nasazené a tom, jak definovat parametry, které jsou zadané při spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky. Informace o vytváření šablon najdete v tématu [vytváření šablon Azure Resource Manager][Authoring Azure Resource Manager templates]. Informace o syntaxi a vlastnostech JSON pro použití v šabloně najdete v tématu [typy prostředků Microsoft. EventHub](/azure/templates/microsoft.eventhub/allversions).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

V tomto rychlém startu použijete [existující šablonu pro rychlý Start](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json):

[!code-json[create-azure-event-hub-namespace](~/quickstart-templates/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json)]

Další ukázky šablon najdete v tématu [šablony rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular).

Nasazení šablony:

1. Vyberte **vyzkoušet** z následujícího bloku kódu a pak podle pokynů se přihlaste ke službě Azure Cloud Shell.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   Vytvoření centra událostí chvíli trvá.

1. Vyberte **Kopírovat** a zkopírujte skript prostředí PowerShell.
1. Klikněte pravým tlačítkem na konzolu prostředí a pak vyberte **Vložit**.

## <a name="verify-the-deployment"></a>Ověření nasazení

Chcete-li ověřit nasazení, můžete buď otevřít skupinu prostředků z [Azure Portal](https://portal.azure.com), nebo použít následující skript Azure PowerShell.  Pokud je Cloud Shell stále otevřený, nemusíte kopírovat/spouštět první řádek (pro čtení-hostitel).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků. Pokud je Cloud Shell stále otevřený, nemusíte kopírovat/spouštět první řádek (pro čtení-hostitel).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili obor názvů Event Hubs a centrum událostí v oboru názvů. Podrobné pokyny k odesílání událostí do (nebo) přijímání událostí z centra událostí najdete v tématu kurzy pro **odesílání a příjem** :

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-java-send-v2.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (pouze odeslat)](event-hubs-c-getstarted-send.md)
- [Apache Storm (pouze příjem)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
