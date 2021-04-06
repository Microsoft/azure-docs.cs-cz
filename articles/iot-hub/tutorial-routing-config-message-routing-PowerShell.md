---
title: Kurz – konfigurace směrování zpráv pro Azure IoT Hub s využitím Azure PowerShell
description: Kurz – konfigurace směrování zpráv pro Azure IoT Hub pomocí Azure PowerShell V závislosti na vlastnostech ve zprávě můžete směrovat na účet úložiště nebo Service Bus frontu.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: be1560bcc03ec7a26f4bc374392c746243cd731a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624068"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Kurz: použití Azure PowerShell ke konfiguraci směrování zpráv IoT Hub

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Stažení skriptu (volitelné)

Druhá část tohoto kurzu vám umožní stáhnout a spustit aplikaci Visual Studio pro posílání zpráv do IoT Hub. V souboru ke stažení je složka, která obsahuje Azure Resource Manager šablona a soubor parametrů, a také skripty Azure CLI a PowerShell. 

Pokud chcete zobrazit dokončený skript, Stáhněte si ukázky pro [Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Rozbalte soubor master.zip. Skript rozhraní příkazového řádku Azure CLI je v/iot-hub/Tutorials/Routing/SimulatedDevice/resources/jako **iothub_routing_psh.ps1**.

## <a name="create-your-resources"></a>Vytvoření prostředků

Začněte tím, že vytvoříte prostředky pomocí PowerShellu.

### <a name="use-powershell-to-create-your-base-resources"></a>Vytvoření základních prostředků pomocí PowerShellu

Zkopírujte a vložte následující skript do Cloud Shell a stiskněte klávesu ENTER. Skript spustí jeden řádek po druhém. Tato první část skriptu vytvoří základní prostředky pro účely tohoto kurzu, včetně účtu úložiště, IoT Hub, Service Bus oboru názvů a Service Bus fronty. Při procházení tohoto kurzu zkopírujte každý blok skriptu a vložte ho do Cloud Shell a spusťte ho.

Existuje několik názvů prostředků, které musí být globálně jedinečné, například IoT Hub název a název účtu úložiště. Aby to bylo snazší, názvy prostředků se připojí s náhodnou alfanumerický hodnotou s názvem *randomValue*. RandomValue se generuje jednou na začátku skriptu a připojuje se k názvům prostředků podle potřeby v celém skriptu. Pokud nechcete, aby byla náhodná, můžete ji nastavit na prázdný řetězec nebo na konkrétní hodnotu. 

> [!IMPORTANT]
> Proměnné nastavené v počátečním skriptu jsou také používány skriptem směrování, takže se spustí všechny skripty ve stejné Cloud Shell relaci. Pokud otevřete novou relaci pro spuštění skriptu pro nastavení směrování, u některých z těchto proměnných dojde k chybějícím hodnotám. 
>

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
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

Teď, když jsou základní prostředky nastavené, můžete nakonfigurovat směrování zpráv.

## <a name="set-up-message-routing"></a>Nastavení směrování zpráv

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Chcete-li vytvořit koncový bod směrování, použijte [příkaz Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint). Pro vytvoření trasy zasílání zpráv pro koncový bod použijte [příkaz Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute).

### <a name="route-to-a-storage-account"></a>Směrování do účtu úložiště 

Nejdřív nastavte koncový bod pro účet úložiště a pak vytvořte trasu zpráv.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Toto jsou proměnné používané skriptem, které musí být nastaveny v rámci relace Cloud Shell:

skupina **prostředků: existují** dva výskyty tohoto pole – nastavení obou z nich pro skupinu prostředků.

**název**: Toto pole je název IoT Hub, na kterou bude směrování platit.

**koncový bod**: Toto pole je název identifikující koncový bod. 

**endpointType**: Toto pole je typ koncového bodu. Tato hodnota musí být nastavena na `azurestoragecontainer` , `eventhub` , `servicebusqueue` nebo `servicebustopic` . Pro vaše účely ho nastavte na `azurestoragecontainer` .

**SubscriptionId**: Toto pole je nastavené na SubscriptionId účtu Azure.

**storageConnectionString**: Tato hodnota se načte z účtu úložiště, který jste nastavili v předchozím skriptu. Používá ho směrováním pro přístup k účtu úložiště.

**ContainerName**: Toto pole je název kontejneru v účtu úložiště, do kterého se budou zapisovat data.

**Encoding**: nastavte toto pole na buď `AVRO` nebo `JSON` . Tím se určí formát uložených dat. Výchozí hodnota je AVRO.

**Route**: Toto pole je název trasy, kterou nastavujete. 

**Podmínka**: Toto pole je dotaz použitý k filtrování zpráv odesílaných do tohoto koncového bodu. Podmínka dotazu pro zprávy směrované do úložiště je `level="storage"` .

**povoleno**: Toto pole je standardně nastaveno na hodnotu `true` , což znamená, že po vytvoření by mělo být povoleno směrování zpráv.

Zkopírujte tento skript a vložte ho do okna Cloud Shell.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

Dalším krokem je vytvoření koncového bodu směrování pro účet úložiště. Zadejte také kontejner, ve kterém budou uloženy výsledky. Kontejner se vytvořil při vytvoření účtu úložiště.

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

Dále vytvořte trasu zpráv pro koncový bod úložiště. Směrování zpráv určuje, kam se mají odesílat zprávy, které splňují specifikace dotazu.

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

### <a name="route-to-a-service-bus-queue"></a>Směrování do fronty Service Bus

Nyní nastavte směrování pro frontu Service Bus. Chcete-li načíst připojovací řetězec pro frontu Service Bus, je nutné vytvořit autorizační pravidlo, které má definováno správné právo. Následující skript vytvoří autorizační pravidlo pro volanou Service Busovou frontu `sbauthrule` a nastaví práva na `Listen Manage Send` . Jakmile je toto autorizační pravidlo nastavené, můžete ho použít k načtení připojovacího řetězce pro tuto frontu.

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

Teď použijte autorizační pravidlo k načtení klíče Service Bus fronty. Toto autorizační pravidlo se použije k načtení připojovacího řetězce později ve skriptu.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Nyní nastavte koncový bod směrování a zprávu trasy pro Service Bus frontu. Toto jsou proměnné používané skriptem, které musí být nastaveny v rámci relace Cloud Shell:

**koncový bod**: Toto pole je název identifikující koncový bod. 

**endpointType**: Toto pole je typ koncového bodu. Tato hodnota musí být nastavena na `azurestoragecontainer` , `eventhub` , `servicebusqueue` nebo `servicebustopic` . Pro vaše účely ho nastavte na `servicebusqueue` .

**Route**: Toto pole je název trasy, kterou nastavujete. 

**Podmínka**: Toto pole je dotaz použitý k filtrování zpráv odesílaných do tohoto koncového bodu. Podmínka dotazu pro zprávy směrované do fronty Service Bus je `level="critical"` .

Tady je Azure PowerShell směrování zpráv pro Service Busovou frontu.

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

### <a name="view-message-routing-in-the-portal"></a>Zobrazení směrování zpráv na portálu

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Další kroky

Teď, když máte nastavené prostředky a nakonfigurované trasy zpráv, přejděte k dalšímu kurzu, kde se dozvíte, jak odesílat zprávy do centra IoT a jak je směrovat do různých cílů. 

> [!div class="nextstepaction"]
> [Část 2 – zobrazení výsledků směrování zpráv](tutorial-routing-view-message-routing-results.md)
