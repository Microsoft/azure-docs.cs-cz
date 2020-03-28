---
title: Konfigurace směrování zpráv pro Azure IoT Hub pomocí Azure PowerShellu
description: Nakonfigurujte směrování zpráv pro Azure IoT Hub pomocí Azure PowerShellu. V závislosti na vlastnostech ve zprávě, směrování k účtu úložiště nebo fronty service bus.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 68338c56419316e561bb072c1a0555e89d3de85b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084442"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Kurz: Konfigurace směrování zpráv služby IoT Hub pomocí Azure PowerShellu

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Stáhnout skript (volitelné)

Pro druhou část tohoto kurzu stáhnete a spustíte aplikaci Visual Studio pro odesílání zpráv do služby IoT Hub. V souboru ke stažení je složka, která obsahuje šablonu a soubor parametrů Azure Resource Manager, stejně jako skripty Azure CLI a PowerShell. 

Pokud chcete zobrazit hotový skript, stáhněte si [ukázky Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Rozbalte soubor master.zip. Skript Azure CLI je v /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ jako **iothub_routing_psh.ps1**.

## <a name="create-your-resources"></a>Vytvořte si zdroje

Začněte vytvořením prostředků pomocí Prostředí PowerShell.

### <a name="use-powershell-to-create-your-base-resources"></a>Vytvoření základních prostředků pomocí PowerShellu

Zkopírujte a vložte skript níže do prostředí Cloud Shell a stiskněte Enter. Spustí skript po jednom řádku. Tato první část skriptu vytvoří základní prostředky pro tento kurz, včetně účtu úložiště, služby IoT Hub, oboru názvů service bus a fronty service bus. Při procházení kurzu zkopírujte každý blok skriptu a vložte jej do prostředí Cloud Shell, abyste jej spusťte.

Existuje několik názvů prostředků, které musí být globálně jedinečné, jako je například název služby IoT Hub a název účtu úložiště. Aby to bylo jednodušší, jsou tyto názvy prostředků připojeny s náhodnou alfanumerickou hodnotou nazvanou *randomValue*. RandomValue je generována jednou v horní části skriptu a připojena k názvům prostředků podle potřeby v celém skriptu. Pokud nechcete, aby byl náhodný, můžete jej nastavit na prázdný řetězec nebo na určitou hodnotu. 

> [!IMPORTANT]
> Proměnné nastavené v počátečním skriptu jsou také používány směrovacím skriptem, proto spusťte všechny skripty ve stejné relaci prostředí Cloud Shell. Pokud otevřete novou relaci pro spuštění skriptu pro nastavení směrování, několik proměnných bude chybět hodnoty. 
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

Nyní, když jsou nastaveny základní prostředky, můžete nakonfigurovat směrování zpráv.

## <a name="set-up-message-routing"></a>Nastavení směrování zpráv

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Chcete-li vytvořit koncový bod směrování, použijte [add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint). Chcete-li vytvořit trasu zasílání zpráv pro koncový bod, použijte [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute).

### <a name="route-to-a-storage-account"></a>Směrovat k účtu úložiště 

Nejprve nastavte koncový bod pro účet úložiště a pak vytvořte trasu zprávy.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Jedná se o proměnné používané skriptem, které musí být nastaveny v rámci relace prostředí Cloud Shell:

**resourceGroup**: Existují dva výskyty tohoto pole – nastavte oba na skupinu prostředků.

**název**: Toto pole je název služby IoT Hub, na který se bude postup vztahovat.

**endpointName**: Toto pole je název identifikující koncový bod. 

**endpointType**: Toto pole je typ koncového bodu. Tato hodnota musí `azurestoragecontainer`být `eventhub` `servicebusqueue`nastavena `servicebustopic`na , , , nebo . Pro vaše účely zde `azurestoragecontainer`nastavte na .

**subscriptionID**: Toto pole je nastaveno na id předplatného pro váš účet Azure.

**storageConnectionString**: Tato hodnota je načtena z účtu úložiště nastaveném v předchozím skriptu. Používá se směrování pro přístup k účtu úložiště.

**containerName**: Toto pole je název kontejneru v účtu úložiště, do kterého budou zapsána data.

**Kódování**: Toto pole `AVRO` `JSON`nastavte na jedno nebo . To určuje formát uložených dat. Výchozí hodnota je AVRO.

**routeName**: Toto pole je název trasy, kterou nastavujete. 

**podmínka**: Toto pole je dotaz používaný k filtrování zpráv odeslaných do tohoto koncového bodu. Podmínka dotazu pro zprávy směrované `level="storage"`do úložiště je .

**povoleno**: Toto `true`pole je výchozí na , což znamená, že po vytvoření by měla být povolena trasa zprávy.

Zkopírujte tento skript a vložte jej do okna Cloud Shell.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

Dalším krokem je vytvoření koncového bodu směrování pro účet úložiště. Můžete také určit kontejner, ve kterém budou uloženy výsledky. Kontejner byl vytvořen při vytvoření účtu úložiště.

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

Dále vytvořte trasu zprávy pro koncový bod úložiště. Trasa zprávy určuje, kam se mají odesílat zprávy splňující specifikaci dotazu.

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

### <a name="route-to-a-service-bus-queue"></a>Trasa do fronty služby Service Bus

Nyní nastavte směrování pro frontu Service Bus. Chcete-li načíst připojovací řetězec pro frontu služby Service Bus, musíte vytvořit autorizační pravidlo, které má definovaná správná práva. Následující skript vytvoří autorizační pravidlo pro `sbauthrule`frontu Služby `Listen Manage Send`service bus volané a nastaví práva na . Po nastavení tohoto autorizačního pravidla jej můžete použít k načtení připojovacího řetězce fronty.

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

Nyní použijte autorizační pravidlo k načtení klíče fronty služby Service Bus. Toto autorizační pravidlo bude použito k načtení připojovacího řetězce později ve skriptu.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Nyní nastavte koncový bod směrování a trasu zprávy pro frontu služby Service Bus. Jedná se o proměnné používané skriptem, které musí být nastaveny v rámci relace prostředí Cloud Shell:

**endpointName**: Toto pole je název identifikující koncový bod. 

**endpointType**: Toto pole je typ koncového bodu. Tato hodnota musí `azurestoragecontainer`být `eventhub` `servicebusqueue`nastavena `servicebustopic`na , , , nebo . Pro vaše účely zde `servicebusqueue`nastavte na .

**routeName**: Toto pole je název trasy, kterou nastavujete. 

**podmínka**: Toto pole je dotaz používaný k filtrování zpráv odeslaných do tohoto koncového bodu. Podmínka dotazu pro zprávy směrované do fronty `level="critical"`služby Service Bus je .

Tady je Azure PowerShell pro směrování zpráv pro frontu Service Bus.

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

Teď, když máte nastavené prostředky a trasy zpráv nakonfigurované, přejdete na další kurz, abyste se dozvěděli, jak odesílat zprávy do služby IoT hub a vidět, že jsou směrovány do různých cílů. 

> [!div class="nextstepaction"]
> [Část 2 – Zobrazení výsledků směrování zpráv](tutorial-routing-view-message-routing-results.md)