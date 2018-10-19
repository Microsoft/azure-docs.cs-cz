---
title: Vytvoření služby Azure Event Hubs oboru názvů a příjemce skupiny pomocí šablony | Dokumentace Microsoftu
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
ms.date: 10/16/2018
ms.author: shvija
ms.openlocfilehash: e3a7b2a7ad866fe6c70c638dc5203b9a31c6b5b3
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426630"
---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Vytvořte obor názvů služby Event Hubs s centrem a příjemce skupiny událostí pomocí šablony Azure Resource Manageru
Azure Event Hubs je vysoce škálovatelná platforma pro streamování dat a služba pro ingestování, která je schopná přijmout a zpracovat miliony událostí za sekundu. Tento rychlý start ukazuje, jak vytvořit Centrum událostí pomocí šablony Azure Resource Manageru.

Použijete šablony Azure Resource Manageru k vytvoření oboru názvů typu [Event Hubs](event-hubs-what-is-event-hubs.md)s jedním centrem událostí a jedné skupiny uživatelů. Tento článek ukazuje, jak definovat prostředků, které jsou nasazené a tom, jak definovat parametry, které jsou zadané při spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky. Informace o vytváření šablon najdete v tématu [šablon pro vytváření Azure Resource Manageru][Authoring Azure Resource Manager templates].

Úplnou šablonu najdete v článku [šablony skupiny centra a příjemce události] [ Event Hub and consumer group template] na Githubu.

> [!NOTE]
> Nejnovější šablony můžete zkontrolovat tak, že přejdete do galerie [Šablony Azure pro rychlý start][Azure Quickstart Templates] a vyhledáte Event Hubs.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto rychlého startu potřebujete předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet] []. než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud používáte **prostředí Azure PowerShell** k nasazení šablony Resource Manageru místně, musíte spustit nejnovější verzi prostředí PowerShell k dokončení tohoto rychlého startu. Pokud PowerShell potřebujete nainstalovat nebo upgradovat, přečtěte si téma [Instalace a konfigurace Azure PowerShellu](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

Pokud se rozhodnete nainstalovat a používat **rozhraní příkazového řádku Azure** k nasazení šablony Resource Manageru místně, tento kurz vyžaduje použití Azure CLI verze 2.0.4 nebo novější. Spuštěním příkazu `az --version` zkontrolujte svou verzi. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="what-will-you-deploy"></a>Co budete nasazovat?

Pomocí této šablony nasadíte obor názvů služby Event Hubs pomocí centra událostí a skupiny příjemců.

Pokud chcete nasazení spustit automaticky, klikněte na následující tlačítko:

[![Nasazení do Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Pomocí Azure Resource Manageru definujete parametry pro hodnoty, které chcete zadat při nasazení šablony. Šablona obsahuje část `Parameters`, která obsahuje všechny hodnoty parametrů. Byste měli definovat parametr pro hodnoty, které se budou lišit podle projektu, které nasazujete nebo podle prostředí, do kterého nasazujete. Nedefinujte parametry pro hodnoty, které jsou vždy stejné. Každá hodnota parametru v šabloně definuje prostředky, které jsou nasazené.

Šablona definuje následující parametry:

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

Název oboru názvů Event Hubs, který se má vytvořit.

```json
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName

Název centra událostí vytvořeného v oboru názvů Event Hubs.

```json
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName

Název skupiny uživatelů vytvořené pro Centrum událostí.

```json
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion

Verze rozhraní API šablony.

```json
"apiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Prostředky k nasazení

Vytvoří obor názvů tohoto typu **EventHubs**s centra událostí a skupiny příjemců:

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="commands-to-run-deployment"></a>Příkazy pro spuštění nasazení

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```azurepowershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```
### <a name="create-a-storage-account-for-event-processor-host"></a>Vytvoření účtu úložiště pro agenta Event Processor Host

Event Processor Host zjednodušuje příjem událostí ze služby Event Hubs tím, že spravuje kontrolní body a paralelní přijímače. K vytváření kontrolních bodů vyžaduje Event Processor Host účet úložiště. Spuštěním následujících příkazů vytvoříte účet úložiště a získáte jeho klíče:

```azurepowershell-interactive
# Create a standard general purpose storage account 
New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name storage_account_name -Location eastus -SkuName Standard_LRS 
e
# Retrieve the storage account key for accessing it
Get-AzureRmStorageAccountKey -ResourceGroupName myResourceGroup -Name storage_account_name
```

Připojovací řetězec je potřeba pro připojení k centru událostí a zpracování událostí. Připojovací řetězec získáte spuštěním:

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Name RootManageSharedAccessKey
```


## <a name="azure-cli"></a>Azure CLI

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

### <a name="create-a-storage-account-for-event-processor-host"></a>Vytvoření účtu úložiště pro agenta Event Processor Host
```azurecli-interactive
# Create a general purpose standard storage account for Event Processor Host
az storage account create --name storageAccountName --resource-group myResourceGroup --location eastus2 --sku Standard_RAGRS --encryption blob

# List the storage account access keys
az storage account keys list --resource-group myResourceGroup --account-name storageAccountName

# Get namespace connection string
az eventhubs namespace authorization-rule keys list --resource-group myResourceGroup --namespace-name namespaceName --name RootManageSharedAccessKey
```

Pro pozdější použití si zkopírujte připojovací řetězec a vložte ho do dočasného umístění, například do Poznámkového bloku.

## <a name="stream-into-event-hubs"></a>Streamování do služby Event Hubs

Teď můžete začít streamovat do služby Event Hubs. Ukázky si můžete stáhnout nebo naklonovat přes Git z [úložiště Event Hubs](https://github.com/Azure/azure-event-hubs).

### <a name="ingest-events"></a>Ingestování událostí

Pokud chcete začít streamovat události, stáhněte si z GitHubu ukázku [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) nebo pomocí následujícího příkazu naklonujte [úložiště Event Hubs na GitHubu](https://github.com/Azure/azure-event-hubs):

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Přejděte do složky \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleSender a načtěte soubor SampleSender.sln do sady Visual Studio.

Dále do projektu přidejte balíček NuGet [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/).

V souboru Program.cs nahraďte následující zástupné hodnoty názvem vašeho centra událostí a připojovacím řetězcem:

```C#
private const string EhConnectionString = "Event Hubs connection string";
private const string EhEntityPath = "Event Hub name";

```

Teď ukázku sestavte a spusťte. Uvidíte, jak se události ingestují do vašeho centra událostí:

![][3]

### <a name="receive-and-process-events"></a>Příjem a zpracování událostí

Teď si stáhněte ukázku přijímače Event Processor Host, který bude přijímat právě odeslané zprávy. Stáhněte si z GitHubu ukázku [SampleEphReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) nebo pomocí následujícího příkazu naklonujte [úložiště Event Hubs na GitHubu](https://github.com/Azure/azure-event-hubs):

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Přejděte do složky \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleEphReceiver a načtěte soubor řešení SampleEphReceiver.sln do sady Visual Studio.

Dále do projektu přidejte balíčky NuGet [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) and [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/).

V souboru Program.cs nahraďte následující konstanty odpovídajícími hodnotami:

```C#
private const string EventHubConnectionString = "Event Hubs connection string";
private const string EventHubName = "Event Hub name";
private const string StorageContainerName = "Storage account container name";
private const string StorageAccountName = "Storage account name";
private const string StorageAccountKey = "Storage account key";
```

Teď ukázku sestavte a spusťte. Uvidíte, jak vaše ukázková aplikace přijímá události:

![][4]

Na webu Azure Portal můžete pro daný obor názvů služby Event Hubs zobrazit rychlost zpracovávání událostí, jak je znázorněno níže:

![][5]


## <a name="next-steps"></a>Další postup

V tomto článku se obor názvů služby Event Hubs a ukázkové aplikace používá k odesílání a příjmu událostí z centra událostí. Podrobné pokyny týkající se odesílání událostí do (nebo) přijímat události z centra událostí najdete v následujících kurzech: 

1. **Odesílání událostí do centra událostí**: [.NET Standard](event-hubs-dotnet-standard-getstarted-send.md), [rozhraní .NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [Node.js ](event-hubs-node-get-started-send.md), [Přejít](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
2. **Příjem událostí z centra událostí**: [.NET Standard](event-hubs-dotnet-standard-getstarted-receive-eph.md), [rozhraní .NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [ Node.js](event-hubs-node-get-started-receive.md), [Přejít](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)

[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
