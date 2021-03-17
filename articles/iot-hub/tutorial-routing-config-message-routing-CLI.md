---
title: Kurz – konfigurace směrování zpráv pro Azure IoT Hub pomocí rozhraní příkazového řádku Azure
description: Kurz – konfigurace směrování zpráv pro Azure IoT Hub pomocí rozhraní příkazového řádku Azure V závislosti na vlastnostech ve zprávě můžete směrovat na účet úložiště nebo Service Bus frontu.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a1cce7264e56b16d285685392186a05e2f075439
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199760"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Kurz: použití rozhraní příkazového řádku Azure ke konfiguraci směrování zpráv IoT Hub

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Stažení skriptu (volitelné)

Druhá část tohoto kurzu vám umožní stáhnout a spustit aplikaci Visual Studio pro posílání zpráv do IoT Hub. V souboru ke stažení je složka, která obsahuje Azure Resource Manager šablona a soubor parametrů, a také skripty Azure CLI a PowerShell.

Pokud chcete zobrazit dokončený skript, Stáhněte si ukázky pro [Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Rozbalte soubor master.zip. Skript rozhraní příkazového řádku Azure CLI je v/iot-hub/Tutorials/Routing/SimulatedDevice/resources/jako **iothub_routing_cli. azcli**.

## <a name="use-the-azure-cli-to-create-your-resources"></a>Vytvoření prostředků pomocí Azure CLI

Zkopírujte a vložte následující skript do Cloud Shell a stiskněte klávesu ENTER. Skript spustí jeden řádek po druhém. Tato první část skriptu vytvoří základní prostředky pro účely tohoto kurzu, včetně účtu úložiště, IoT Hub, Service Bus oboru názvů a Service Bus fronty. Po Projděte si zbytek kurzu zkopírujte každý blok skriptu a vložte ho do Cloud Shell a spusťte ho.

> [!TIP]
> Tip o ladění: Tento skript používá symbol pokračování (zpětné lomítko `\` ) k tomu, aby byl skript čitelnější. Pokud máte problém s spuštěním skriptu, ujistěte se, že je spuštěná relace Cloud Shell `bash` a že po žádném z zpětných lomítek neexistují žádné mezery.
> 

Existuje několik názvů prostředků, které musí být globálně jedinečné, například IoT Hub název a název účtu úložiště. Aby to bylo snazší, názvy prostředků se připojí s náhodnou alfanumerický hodnotou s názvem *randomValue*. RandomValue se generuje jednou na začátku skriptu a připojuje se k názvům prostředků podle potřeby v celém skriptu. Pokud nechcete, aby byla náhodná, můžete ji nastavit na prázdný řetězec nebo na konkrétní hodnotu. 

> [!IMPORTANT]
> Proměnné nastavené v počátečním skriptu jsou také používány skriptem směrování, takže se spustí všechny skripty ve stejné Cloud Shell relaci. Pokud otevřete novou relaci pro spuštění skriptu pro nastavení směrování, u některých z těchto proměnných dojde k chybějícím hodnotám.
>

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-iot

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, 
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName
```

Teď, když jsou základní prostředky nastavené, můžete nakonfigurovat směrování zpráv.

## <a name="set-up-message-routing"></a>Nastavení směrování zpráv

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Pokud chcete vytvořit koncový bod směrování, použijte příkaz [AZ IoT Hub Routing-Endpoint Create](/cli/azure/iot/hub/routing-endpoint#az-iot-hub-routing-endpoint-create). Chcete-li vytvořit trasu zprávy pro koncový bod, použijte příkaz [AZ IoT Hub Route Create](/cli/azure/iot/hub/route#az-iot-hub-route-create).

### <a name="route-to-a-storage-account"></a>Směrování do účtu úložiště

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Nejdřív nastavte koncový bod pro účet úložiště a pak nastavte trasu. 

Toto jsou proměnné používané skriptem, které musí být nastaveny v rámci relace Cloud Shell:

**storageConnectionString**: Tato hodnota se načte z účtu úložiště, který jste nastavili v předchozím skriptu. Používá ho směrováním zpráv pro přístup k účtu úložiště.

  skupina **prostředků: existují** dva výskyty skupiny prostředků – nastavte je do vaší skupiny prostředků.

**koncový bod subscriptionID**: Toto pole je nastavené na subscriptionID Azure pro koncový bod. 

**endpointType**: Toto pole je typ koncového bodu. Tato hodnota musí být nastavena na `azurestoragecontainer` , `eventhub` , `servicebusqueue` nebo `servicebustopic` . Pro vaše účely ho nastavte na `azurestoragecontainer` .

**iotHubName**: Toto pole je název centra, které provede směrování.

**ContainerName**: Toto pole je název kontejneru v účtu úložiště, do kterého se budou zapisovat data.

**kódování**: Toto pole bude buď `avro` nebo `json` . Označuje formát uložených dat.

**Route**: Toto pole je název trasy, kterou nastavujete. 

**koncový bod**: Toto pole je název identifikující koncový bod. 

**povoleno**: Toto pole je standardně nastaveno na hodnotu `true` , což znamená, že po vytvoření by mělo být povoleno směrování zpráv.

**Podmínka**: Toto pole je dotaz použitý k filtrování zpráv odesílaných do tohoto koncového bodu. Podmínka dotazu pro zprávy směrované do úložiště je `level="storage"` .

Zkopírujte tento skript a vložte ho do okna Cloud Shell a spusťte ho.

```azurecli
##### ROUTING FOR STORAGE ##### 

endpointName="ContosoStorageEndpoint"
endpointType="azurestoragecontainer"
routeName="ContosoStorageRoute"
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)
```

Dalším krokem je vytvoření koncového bodu směrování pro účet úložiště. Zadejte také kontejner, ve kterém budou uloženy výsledky. Kontejner byl vytvořen dříve, když byl vytvořen účet úložiště.

```azurecli
# Create the routing endpoint for storage.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName \
  --resource-group $resourceGroup \
  --encoding avro
```

Dále vytvořte trasu pro koncový bod úložiště. Směrování zpráv určuje, kam se mají odesílat zprávy, které splňují specifikace dotazu. 

```azurecli
# Create the route for the storage endpoint.
az iot hub route create \
  --name $routeName \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
```

### <a name="route-to-a-service-bus-queue"></a>Směrování do fronty Service Bus

Nyní nastavte směrování pro frontu Service Bus. Chcete-li načíst připojovací řetězec pro frontu Service Bus, je nutné vytvořit autorizační pravidlo, které má definováno správné právo. Následující skript vytvoří autorizační pravidlo pro volanou Service Busovou frontu `sbauthrule` a nastaví práva na `Listen Manage Send` . Po definování tohoto autorizačního pravidla ho můžete použít k načtení připojovacího řetězce pro tuto frontu.

```azurecli
# Create the authorization rule for the Service Bus queue.
az servicebus queue authorization-rule create \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --rights Listen Manage Send \
  --subscription $subscriptionID
```

Nyní použijte autorizační pravidlo k načtení připojovacího řetězce do fronty Service Bus.

```azurecli
# Get the Service Bus queue connection string.
# The "-o tsv" ensures it is returned without the default double-quotes.
sbqConnectionString=$(az servicebus queue authorization-rule keys list \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --subscription $subscriptionID \
  --query primaryConnectionString -o tsv)

# Show the Service Bus queue connection string.
echo "service bus queue connection string = " $sbqConnectionString
```

Nyní nastavte koncový bod směrování a zprávu trasy pro Service Bus frontu. Toto jsou proměnné používané skriptem, které musí být nastaveny v rámci relace Cloud Shell:

**koncový bod**: Toto pole je název identifikující koncový bod. 

**endpointType**: Toto pole je typ koncového bodu. Tato hodnota musí být nastavena na `azurestoragecontainer` , `eventhub` , `servicebusqueue` nebo `servicebustopic` . Pro vaše účely ho nastavte na `servicebusqueue` .

**Route**: Toto pole je název trasy, kterou nastavujete. 

**Podmínka**: Toto pole je dotaz použitý k filtrování zpráv odesílaných do tohoto koncového bodu. Podmínka dotazu pro zprávy směrované do fronty Service Bus je `level="critical"` .

Tady je rozhraní příkazového řádku Azure CLI pro koncový bod směrování a zprávu pro frontu Service Bus.

```azurecli
endpointName="ContosoSBQueueEndpoint"
endpointType="ServiceBusQueue"
routeName="ContosoSBQueueRoute"
condition='level="critical"'

# Set up the routing endpoint for the Service Bus queue.
# This uses the Service Bus queue connection string.
az iot hub routing-endpoint create \
  --connection-string $sbqConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --resource-group $resourceGroup 

# Set up the message route for the Service Bus queue endpoint.
az iot hub route create --name $routeName \
  --hub-name $iotHubName \
  --source-type devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
  ```

### <a name="view-message-routing-in-the-portal"></a>Zobrazení směrování zpráv na portálu

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Další kroky

Teď, když máte nastavené prostředky a nakonfigurované trasy zpráv, přejděte k dalšímu kurzu, kde se dozvíte, jak odesílat zprávy do centra IoT a jak je směrovat do různých cílů. 

> [!div class="nextstepaction"]
> [Část 2 – zobrazení výsledků směrování zpráv](tutorial-routing-view-message-routing-results.md)
