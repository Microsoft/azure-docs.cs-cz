---
title: Konfigurace směrování zpráv pro službu Azure IoT Hub pomocí Azure Powershellu | Dokumentace Microsoftu
description: Konfigurace směrování zpráv pro službu Azure IoT Hub pomocí Azure Powershellu
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 51e9bc85c2ee843aa096674a25a1f634bd08b838
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "66162633"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Kurz: Konfigurace směrování zpráv služby IoT Hub pomocí Azure Powershellu

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Stáhněte si skript (volitelné)

Druhé části tohoto kurzu stáhněte a spusťte aplikaci Visual Studio pro odesílání zpráv do služby IoT Hub. Složka existuje v souboru pro stažení, který obsahuje šablony Azure Resource Manageru a soubor parametrů, jakož i skripty rozhraní příkazového řádku Azure a Powershellu. 

Pokud chcete zobrazit dokončení skriptu, stáhněte si [Azure IoT C# ukázky](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Rozbalte soubor master.zip. Skript Azure CLI je v /iot-hub/Tutorials/Routing/SimulatedDevice/resources/jako **iothub_routing_psh.ps1**.

## <a name="create-your-resources"></a>Vytvoření prostředků

Začněte tím, že vytváření prostředků pomocí Powershellu.

### <a name="use-powershell-to-create-your-base-resources"></a>Použití Powershellu k vytvoření vašich základních prostředků

Existuje několik názvy prostředků, které musí být globálně jedinečný, jako je název služby IoT Hub a název účtu úložiště. Abychom to usnadnili, jsou tyto názvy prostředků připojeny náhodných alfanumerických hodnoty nazvané *randomValue*. RandomValue vygeneruje jednou v horní části skriptu a připojí k názvy prostředků podle potřeby v celém skriptu. Pokud nechcete, aby to přijde náhodného, můžete nastavit na prázdný řetězec nebo na určitou hodnotu. 

> [!IMPORTANT]
> Proměnné nastavené v počáteční skriptu jsou také používány směrování, takže spuštění skriptu všechny skriptu ve stejné relaci služby Cloud Shell. Pokud otevřete novou relaci pro spuštění skriptu pro nastavení směrování několik proměnných se chybějící hodnoty. 
>

Zkopírujte a vložte níže uvedený skript do Cloud Shellu a stiskněte klávesu Enter. Jeden řádek skriptu běží současně. Tento první část tento skript vytvoří základní prostředky pro účely tohoto kurzu, včetně účtu úložiště, IoT Hub, Namespace služby Service Bus a fronty služby Service Bus. Při procházení tohoto kurzu, každý blok skriptu zkopírujte a vložte ho do služby Cloud Shell k jeho spuštění.

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing rules.
$subscriptionID = (Get-AzContext).Subscription.Id

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves the first 6 digits of a random value.
$randomValue = "$(Get-Random)".Substring(0,6)

# Set the values for the resource names that don't have to be globally unique.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
$iotHubName = "ContosoTestHub" + $randomValue
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1 

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random value to the end.
$storageAccountName = "contosostorage" + $randomValue
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
# Retrieve the connection string from the context. 
$storageConnectionString = $storageAccount.Context.ConnectionString
Write-Host "storage connection string = " $storageConnectionString 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageAccount.Context

# The Service Bus namespace must be globally unique,
#   so add a random value to the end.
$serviceBusNamespace = "ContosoSBNamespace" + $randomValue
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random value to the end.
$serviceBusQueueName  = "ContosoSBQueue" + $randomValue
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName  `
    -EnablePartitioning $False 
```

### <a name="create-a-simulated-device"></a>Vytvoření simulovaného zařízení

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

Teď, když se nastaví základní prostředky, můžete nakonfigurovat směrování zpráv.

## <a name="set-up-message-routing"></a>Nastavení směrování zpráv

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Chcete-li vytvořit koncový bod směrování, použijte [přidat AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint). Chcete-li vytvořit trasu zasílání zpráv pro koncový bod, použijte [přidat AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute).

### <a name="route-to-a-storage-account"></a>Směrovat do účtu úložiště 

Nejprve nastavení koncového bodu pro účet úložiště a pak vytvořte směrování zpráv.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Tyto proměnné se nastaví:

**resourceGroup**: Existují dva výskyty tohoto pole – nastavení obou z nich do vaší skupiny prostředků.

**Název**: Toto pole je název služby IoT Hub, na kterou se vztahují směrování.

**endpointName**: Toto pole je název koncového bodu. 

**endpointType**: Toto pole je typ koncového bodu. Tato hodnota musí být nastavená na `azurestoragecontainer`, `eventhub`, `servicebusqueue`, nebo `servicebustopic`. Pro účely tady, nastavte ho na `azurestoragecontainer`.

**subscriptionID**: Toto pole je nastaveno na ID předplatného pro váš účet Azure.

**storageConnectionString**: Tato hodnota je načten z účtu úložiště, nastavte v předchozím scénáři. Pomocí směrování se používá pro přístup k účtu úložiště.

**containerName**: Toto pole je název kontejneru v účtu úložiště, ke kterému se budou zapisovat data.

**Kódování**: Nastavte pole na buď `AVRO` nebo `JSON`. Ta určuje formát uložená data. Výchozí hodnota je AVRO.

**routeName**: Toto pole je název trasy, která nastavujete. 

**Podmínka**: Toto pole je dotaz, který chcete-li filtrovat zprávy odeslané k tomuto koncovému bodu. Podmínka dotazu pro zprávy směruje do úložiště je `level="storage"`.

**Povolené**: Výchozí nastavení tohoto pole `true`, označující, že by měla povoleno směrování zpráv, po vytvoření.

Tento skript zkopírujte a vložte ho do okna služby Cloud Shell.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

Dalším krokem je vytvoření směrování koncového bodu pro účet úložiště. Také zadejte kontejner, ve kterém se uloží výsledky. Kontejner byl vytvořen při vytvoření účtu úložiště.

```powershell
# Create the routing endpoint for storage.
# Specify 'AVRO' or 'JSON' for the encoding of the data.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $storageConnectionString  `
  -ContainerName $containerName `
  -Encoding AVRO
```

Dále vytvořte směrování zpráv pro koncový bod úložiště. Směrování zprávy určuje, kam má odesílat zprávy, které odpovídají specifikaci dotazu.

```powershell
# Create the route for the storage endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="route-to-a-service-bus-queue"></a>Směrovat do fronty služby Service Bus

Nyní nastavte směrování pro frontu Service Bus. Pokud chcete načíst připojovací řetězec pro frontu služby Service Bus, musíte vytvořit autorizační pravidlo, které má správná oprávnění, které jsou definovány. Tento skript vytvoří autorizační pravidlo pro frontu služby Service Bus volá `sbauthrule`a nastaví práva na `Listen Manage Send`. Až nastavíte toto autorizační pravidlo, můžete k načtení připojovacího řetězce pro frontu.

```powershell
##### ROUTING FOR SERVICE BUS QUEUE #####

# Create the authorization rule for the Service Bus queue.
New-AzServiceBusAuthorizationRule `
  -ResourceGroupName $resourceGroup `
  -NamespaceName $serviceBusNamespace `
  -Queue $serviceBusQueueName `
  -Name "sbauthrule" `
  -Rights @("Manage","Listen","Send")
```

Teď pomocí autorizačního pravidla načíst klíč fronty služby Service Bus. Toto autorizační pravidlo se použije k načtení připojovacího řetězce v pozdější části skriptu.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Nyní nastavte směrování koncových bodů a směrování zpráv pro frontu služby Service Bus. Tyto proměnné se nastaví:

**endpointName**: Toto pole je název koncového bodu. 

**endpointType**: Toto pole je typ koncového bodu. Tato hodnota musí být nastavená na `azurestoragecontainer`, `eventhub`, `servicebusqueue`, nebo `servicebustopic`. Pro účely tady, nastavte ho na `servicebusqueue`.

**routeName**: Toto pole je název trasy, která nastavujete. 

**Podmínka**: Toto pole je dotaz, který chcete-li filtrovat zprávy odeslané k tomuto koncovému bodu. Podmínka dotazu pro zprávy směruje do fronty služby Service Bus je `level="critical"`.

Tady je prostředí Azure PowerShell pro směrování zpráv do fronty služby Service Bus.

```powershell
$endpointName = "ContosoSBQueueEndpoint"
$endpointType = "servicebusqueue"
$routeName = "ContosoSBQueueRoute"
$condition = 'level="critical"'

# Add the routing endpoint, using the connection string property from the key.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $sbqkey.PrimaryConnectionString

# Set up the message route for the Service Bus queue endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="view-message-routing-in-the-portal"></a>Zobrazit na portálu pro směrování zpráv

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Další postup

Teď, když máte prostředky nastavit a směrování zpráv nakonfigurována, přejděte k dalšímu kurzu, přečtěte si, jak odesílat zprávy do služby IoT hub a je možné směrovat na různé cíle. 

> [!div class="nextstepaction"]
> [Část 2 – zobrazení výsledků směrování zpráv](tutorial-routing-view-message-routing-results.md)