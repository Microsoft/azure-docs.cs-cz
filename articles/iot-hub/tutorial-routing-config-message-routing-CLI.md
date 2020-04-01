---
title: Konfigurace směrování zpráv pro Azure IoT Hub pomocí rozhraní příkazového příkazu Azure
description: Nakonfigurujte směrování zpráv pro Azure IoT Hub pomocí rozhraní příkazového příkazového příkazu k Azure. V závislosti na vlastnostech ve zprávě, směrování k účtu úložiště nebo fronty service bus.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 056dac7977115f97892d8dbfde0710e00237804e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78674349"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Kurz: Ke konfiguraci směrování zpráv služby IoT Hub pomocí rozhraní příkazového příkazu Azure

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Stáhnout skript (volitelné)

Pro druhou část tohoto kurzu stáhnete a spustíte aplikaci Visual Studio pro odesílání zpráv do služby IoT Hub. V souboru ke stažení je složka, která obsahuje šablonu a soubor parametrů Azure Resource Manager, stejně jako skripty Azure CLI a PowerShell.

Pokud chcete zobrazit hotový skript, stáhněte si [ukázky Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Rozbalte soubor master.zip. Skript Azure CLI je v /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ jako **iothub_routing_cli.azcli**.

## <a name="use-the-azure-cli-to-create-your-resources"></a>Vytvoření prostředků pomocí azure cli

Zkopírujte a vložte skript níže do prostředí Cloud Shell a stiskněte Enter. Spustí skript po jednom řádku. Tato první část skriptu vytvoří základní prostředky pro tento kurz, včetně účtu úložiště, služby IoT Hub, oboru názvů service bus a fronty service bus. Při procházení zbytku kurzu zkopírujte každý blok skriptu a vložte jej do prostředí Cloud Shell, abyste jej spusťte.

> [!TIP]
> Tip o ladění: tento skript používá symbol pokračování (zpětné lomítko), `\`aby byl skript čitelnější. Pokud máte problém se spuštěním skriptu, ujistěte `bash` se, že vaše relace prostředí Cloud Je spuštěna a že po žádném z zpětných lomítka nejsou žádné mezery.
> 

Existuje několik názvů prostředků, které musí být globálně jedinečné, jako je například název služby IoT Hub a název účtu úložiště. Aby to bylo jednodušší, jsou tyto názvy prostředků připojeny s náhodnou alfanumerickou hodnotou nazvanou *randomValue*. RandomValue je generována jednou v horní části skriptu a připojena k názvům prostředků podle potřeby v celém skriptu. Pokud nechcete, aby byl náhodný, můžete jej nastavit na prázdný řetězec nebo na určitou hodnotu. 

> [!IMPORTANT]
> Proměnné nastavené v počátečním skriptu jsou také používány směrovacím skriptem, proto spusťte všechny skripty ve stejné relaci prostředí Cloud Shell. Pokud otevřete novou relaci pro spuštění skriptu pro nastavení směrování, několik proměnných bude chybět hodnoty.
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

Nyní, když jsou nastaveny základní prostředky, můžete nakonfigurovat směrování zpráv.

## <a name="set-up-message-routing"></a>Nastavení směrování zpráv

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Chcete-li vytvořit koncový bod směrování, použijte [vytvoření směrovacího bodu az iot hub](/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest#az-iot-hub-routing-endpoint-create). Chcete-li vytvořit trasu zprávy pro koncový bod, použijte [vytvoření trasy az iot hub](/cli/azure/iot/hub/route?view=azure-cli-latest#az-iot-hub-route-create).

### <a name="route-to-a-storage-account"></a>Směrovat k účtu úložiště

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Nejprve nastavte koncový bod pro účet úložiště a nastavte trasu. 

Jedná se o proměnné používané skriptem, které musí být nastaveny v rámci relace prostředí Cloud Shell:

**storageConnectionString**: Tato hodnota je načtena z účtu úložiště nastaveném v předchozím skriptu. Používá se směrování zpráv pro přístup k účtu úložiště.

  **resourceGroup**: Existují dva výskyty skupiny prostředků – nastavte je na skupinu prostředků.

**Id předplatného koncového bodu**: Toto pole je nastaveno na ID předplatného Azure pro koncový bod. 

**endpointType**: Toto pole je typ koncového bodu. Tato hodnota musí `azurestoragecontainer`být `eventhub` `servicebusqueue`nastavena `servicebustopic`na , , , nebo . Pro vaše účely zde `azurestoragecontainer`nastavte na .

**iotHubName**: Toto pole je název rozbočovače, který bude dělat směrování.

**containerName**: Toto pole je název kontejneru v účtu úložiště, do kterého budou zapsána data.

**kódování**: Toto pole `avro` `json`bude buď nebo . To označuje formát uložených dat.

**routeName**: Toto pole je název trasy, kterou nastavujete. 

**endpointName**: Toto pole je název identifikující koncový bod. 

**povoleno**: Toto `true`pole je výchozí na , což znamená, že po vytvoření by měla být povolena trasa zprávy.

**podmínka**: Toto pole je dotaz používaný k filtrování zpráv odeslaných do tohoto koncového bodu. Podmínka dotazu pro zprávy směrované `level="storage"`do úložiště je .

Zkopírujte tento skript a vložte jej do okna Cloud Shell a spusťte jej.

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

Dalším krokem je vytvoření koncového bodu směrování pro účet úložiště. Můžete také určit kontejner, ve kterém budou uloženy výsledky. Kontejner byl vytvořen dříve při vytvoření účtu úložiště.

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

Dále vytvořte trasu pro koncový bod úložiště. Trasa zprávy určuje, kam se mají odesílat zprávy splňující specifikaci dotazu. 

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

### <a name="route-to-a-service-bus-queue"></a>Trasa do fronty služby Service Bus

Nyní nastavte směrování pro frontu Service Bus. Chcete-li načíst připojovací řetězec pro frontu služby Service Bus, musíte vytvořit autorizační pravidlo, které má definovaná správná práva. Následující skript vytvoří autorizační pravidlo pro `sbauthrule`frontu Služby `Listen Manage Send`service bus volané a nastaví práva na . Jakmile je toto autorizační pravidlo definováno, můžete jej použít k načtení připojovacího řetězce pro frontu.

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

Nyní použijte autorizační pravidlo k načtení připojovacího řetězce do fronty služby Service Bus.

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

Nyní nastavte koncový bod směrování a trasu zprávy pro frontu služby Service Bus. Jedná se o proměnné používané skriptem, které musí být nastaveny v rámci relace prostředí Cloud Shell:

**endpointName**: Toto pole je název identifikující koncový bod. 

**endpointType**: Toto pole je typ koncového bodu. Tato hodnota musí `azurestoragecontainer`být `eventhub` `servicebusqueue`nastavena `servicebustopic`na , , , nebo . Pro vaše účely zde `servicebusqueue`nastavte na .

**routeName**: Toto pole je název trasy, kterou nastavujete. 

**podmínka**: Toto pole je dotaz používaný k filtrování zpráv odeslaných do tohoto koncového bodu. Podmínka dotazu pro zprávy směrované do fronty `level="critical"`služby Service Bus je .

Tady je Azure CLI pro koncový bod směrování a trasu zprávy pro frontu service bus.

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

Teď, když máte nastavené prostředky a trasy zpráv nakonfigurované, přejdete na další kurz, abyste se dozvěděli, jak odesílat zprávy do služby IoT hub a vidět, že jsou směrovány do různých cílů. 

> [!div class="nextstepaction"]
> [Část 2 – Zobrazení výsledků směrování zpráv](tutorial-routing-view-message-routing-results.md)
