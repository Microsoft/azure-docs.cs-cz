---
title: Konfigurace směrování zpráv pro Azure IoT Hubu pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Konfigurace směrování zpráv pro službu Azure IoT Hub pomocí Azure CLI
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 53a0645c42255d5f741d31c411ca70e1e009febc
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661577"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Kurz: Konfigurace směrování zpráv služby IoT Hub pomocí Azure CLI

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Stáhněte si skript (volitelné)

Druhé části tohoto kurzu stáhněte a spusťte aplikaci Visual Studio pro odesílání zpráv do služby IoT Hub. Složka existuje v souboru pro stažení, který obsahuje šablony Azure Resource Manageru a soubor parametrů, jakož i skripty rozhraní příkazového řádku Azure a Powershellu.

Pokud chcete zobrazit dokončení skriptu, stáhněte si [Azure IoT C# ukázky](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Rozbalte soubor master.zip. Skript Azure CLI je v /iot-hub/Tutorials/Routing/SimulatedDevice/resources/jako **iothub_routing_cli.azcli**.

## <a name="use-the-azure-cli-to-create-your-resources"></a>Vytvoříte své prostředky pomocí Azure CLI

Existuje několik názvy prostředků, které musí být globálně jedinečný, jako je název služby IoT Hub a název účtu úložiště. Abychom to usnadnili, jsou tyto názvy prostředků připojeny náhodných alfanumerických hodnoty nazvané *randomValue*. RandomValue vygeneruje jednou v horní části skriptu a připojí k názvy prostředků podle potřeby v celém skriptu. Pokud nechcete, aby to přijde náhodného, můžete nastavit na prázdný řetězec nebo na určitou hodnotu. 

> [!IMPORTANT]
> Proměnné nastavené v počáteční skriptu jsou také používány směrování, takže spuštění skriptu všechny skriptu ve stejné relaci služby Cloud Shell. Pokud otevřete novou relaci pro spuštění skriptu pro nastavení směrování několik proměnných se chybějící hodnoty.
>

Zkopírujte a vložte níže uvedený skript do Cloud Shellu a stiskněte klávesu Enter. Jeden řádek skriptu běží současně. Tento první část tento skript vytvoří základní prostředky pro účely tohoto kurzu, včetně účtu úložiště, IoT Hub, Namespace služby Service Bus a fronty služby Service Bus. Při procházení zbývající část tohoto kurzu, každý blok skriptu zkopírujte a vložte ho do služby Cloud Shell k jeho spuštění.

> [!TIP]
> Tip o ladění: Tento skript používá symbol pokračování (zpětné lomítko `\`) aby skript lépe čitelný. Pokud máte potíže při spuštění skriptu, ujistěte se, že nejsou žádné mezery po všech zpětná lomítka.
> 

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing rules.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

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

Teď, když se nastaví základní prostředky, můžete nakonfigurovat směrování zpráv.

## <a name="set-up-message-routing"></a>Nastavení směrování zpráv

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Chcete-li vytvořit koncový bod směrování, použijte [az iot hub směrování – koncový bod vytvořit](/cli/azure/iot/hub/routing-endpoint.md?view=azure-cli-latest#az-iot-hub-routing-endpoint-create). Chcete-li vytvořit směrování zpráv pro koncový bod, použijte [az iot hub postupu vytvoření](/cli/azure/iot/hub/route?view=azure-cli-latest#az-iot-hub-route-create).

### <a name="route-to-a-storage-account"></a>Směrovat do účtu úložiště

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Nejprve nastavení koncového bodu pro účet úložiště a pak nastavte trasy. 

Tyto proměnné se nastaví:

**storageConnectionString**: Tato hodnota je načten z účtu úložiště, nastavte v předchozím scénáři. Používá se ve zprávě směrování pro přístup k účtu úložiště.

  **resourceGroup**: Existují dva výskyty skupiny prostředků – nastavení do vaší skupiny prostředků.

**koncový bod subscriptionID**: Toto pole je nastaveno na ID předplatného Azure pro koncový bod. 

**endpointType**: Toto pole je typ koncového bodu. Tato hodnota musí být nastavená na `azurestoragecontainer`, `eventhub`, `servicebusqueue`, nebo `servicebustopic`. Pro účely tady, nastavte ho na `azurestoragecontainer`.

**iotHubName**: Toto pole je název rozbočovače, která bude provádět směrování.

**containerName**: Toto pole je název kontejneru v účtu úložiště, ke kterému se budou zapisovat data.

**Kódování**: Toto pole může mít hodnotu `avro` nebo `json`. To označuje formátu uložená data.

**routeName**: Toto pole je název trasy, která nastavujete. 

**endpointName**: Toto pole je název koncového bodu. 

**Povolené**: Výchozí nastavení tohoto pole `true`, označující, že by měla povoleno směrování zpráv, po vytvoření.

**Podmínka**: Toto pole je dotaz, který chcete-li filtrovat zprávy odeslané k tomuto koncovému bodu. Podmínka dotazu pro zprávy směruje do úložiště je `level="storage"`.

Tento skript zkopírujte a vložte ho do okna služby Cloud Shell a spusťte jej.

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

Dalším krokem je vytvoření směrování koncového bodu pro účet úložiště. Také zadejte kontejner, ve kterém se uloží výsledky. Kontejner byl vytvořen dříve při vytváření účtu úložiště.

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

Dále vytvořte trasu pro koncový bod úložiště. Směrování zprávy určuje, kam má odesílat zprávy, které odpovídají specifikaci dotazu. 

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

### <a name="route-to-a-service-bus-queue"></a>Směrovat do fronty služby Service Bus

Nyní nastavte směrování pro frontu Service Bus. Pokud chcete načíst připojovací řetězec pro frontu služby Service Bus, musíte vytvořit autorizační pravidlo, které má správná oprávnění, které jsou definovány. Tento skript vytvoří autorizační pravidlo pro frontu služby Service Bus volá `sbauthrule`a nastaví práva na `Listen Manage Send`. Jakmile je definován toto autorizační pravidlo, můžete k načtení připojovacího řetězce pro frontu.

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

Autorizační pravidlo je teď možné použijte k načtení připojovacího řetězce do fronty služby Service Bus.

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

Nyní nastavte směrování koncových bodů a směrování zpráv pro frontu služby Service Bus. Tyto proměnné se nastaví:

**endpointName**: Toto pole je název koncového bodu. 

**endpointType**: Toto pole je typ koncového bodu. Tato hodnota musí být nastavená na `azurestoragecontainer`, `eventhub`, `servicebusqueue`, nebo `servicebustopic`. Pro účely tady, nastavte ho na `servicebusqueue`.

**routeName**: Toto pole je název trasy, která nastavujete. 

**Podmínka**: Toto pole je dotaz, který chcete-li filtrovat zprávy odeslané k tomuto koncovému bodu. Podmínka dotazu pro zprávy směruje do fronty služby Service Bus je `level="critical"`.

Tady je rozhraní příkazového řádku Azure pro koncový bod služby Směrování a směrování zpráv pro frontu služby Service Bus.

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

### <a name="view-message-routing-in-the-portal"></a>Zobrazit na portálu pro směrování zpráv

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Další postup

Teď, když máte prostředky nastavit a směrování zpráv nakonfigurována, přejděte k dalšímu kurzu, přečtěte si, jak odesílat zprávy do služby IoT hub a je možné směrovat na různé cíle. 

> [!div class="nextstepaction"]
> [Část 2 – zobrazení výsledků směrování zpráv](tutorial-routing-view-message-routing-results.md)