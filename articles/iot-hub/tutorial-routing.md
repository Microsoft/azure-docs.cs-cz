---
title: Kurz – konfigurace směrování zpráv pro Azure IoT Hub pomocí rozhraní příkazového řádku Azure
description: Kurz – konfigurace směrování zpráv pro Azure IoT Hub pomocí rozhraní příkazového řádku Azure a Azure Portal
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: d7a10aac82286616273f7283bc3fba74f2c58f35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98625211"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>Kurz: použití rozhraní příkazového řádku Azure a Azure Portal ke konfiguraci směrování zpráv IoT Hub

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>Vytvoření základních prostředků pomocí Azure CLI

V tomto kurzu se k vytvoření základních prostředků používá Azure CLI a pak se pomocí [Azure Portal](https://portal.azure.com) ukáže, jak nakonfigurovat směrování zpráv a nastavit virtuální zařízení pro testování.

Zkopírujte a vložte následující skript do Cloud Shell a stiskněte klávesu ENTER. Skript spustí jeden řádek po druhém. Tím se vytvoří základní prostředky pro tento kurz, včetně účtu úložiště, IoT Hub, oboru názvů Service Bus a Service Bus fronty.

Existuje několik názvů prostředků, které musí být globálně jedinečné, například IoT Hub název a název účtu úložiště. Aby to bylo snazší, názvy prostředků se připojí s náhodnou alfanumerický hodnotou s názvem *randomValue*. RandomValue se generuje jednou na začátku skriptu a připojuje se k názvům prostředků podle potřeby v celém skriptu. Pokud nechcete, aby byla náhodná, můžete ji nastavit na prázdný řetězec nebo na konkrétní hodnotu.

> [!TIP]
> Tip o ladění: Tento skript používá symbol pokračování (zpětné lomítko `\` ) k tomu, aby byl skript čitelnější. Pokud máte problém s spuštěním skriptu, ujistěte se, že je spuštěná relace Cloud Shell `bash` a že po žádném z zpětných lomítek neexistují žádné mezery.
>

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing queries.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults

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

```

Teď, když jsou nastavené základní prostředky, můžete nakonfigurovat směrování zpráv v [Azure Portal](https://portal.azure.com).

## <a name="set-up-message-routing"></a>Nastavení směrování zpráv

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>Směrování do účtu úložiště

Nyní nastavte směrování pro účet úložiště. Přejděte do podokna Směrování zpráv a přidejte trasu. Při přidávání trasy pro ni definujte nový koncový bod. Po nastavení tohoto směrování se zprávy, ve kterých je vlastnost **Level** nastavená na **Storage** , zapisují automaticky do účtu úložiště. 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. V [Azure Portal](https://portal.azure.com)vyberte **skupiny prostředků** a pak vyberte skupinu prostředků. Tento kurz používá **ContosoResources**.

2. V seznamu prostředků vyberte Centrum IoT. Tento kurz používá **ContosoTestHub**.

3. Vyberte **směrování zpráv**. V podokně **směrování zpráv** vyberte +**Přidat**. V podokně **Přidat trasu** vyberte +**přidat koncový bod** vedle pole koncový bod pro zobrazení podporovaných koncových bodů, jak je znázorněno na následujícím obrázku:

   ![Zahájení přidávání koncového bodu pro trasu](./media/tutorial-routing/message-routing-add-a-route-with-storage-endpoint-ver2.png)

4. Vyberte **úložiště**. Zobrazí se podokno **přidat koncový bod úložiště** .

   ![Přidání koncového bodu](./media/tutorial-routing/message-routing-add-storage-endpoint-ver2.png)

5. Zadejte název koncového bodu. V tomto kurzu se používá **ContosoStorageEndpoint**.

6. Vyberte vybrat **kontejner**. Tím přejdete na seznam vašich účtů úložiště. Vyberte účet, který jste nastavili v rámci přípravy. Tento kurz používá **contosostorage**. Zobrazí se seznam kontejnerů v daném účtu úložiště. **Vyberte** kontejner, který jste nastavili v přípravných krocích. Tento kurz používá **contosoresults**. Vrátíte se do podokna **přidat koncový bod úložiště** a zobrazíte provedené výběry.

7. Nastavte kódování na AVRO nebo JSON. Pro účely tohoto kurzu použijte u zbývajících polí výchozí hodnoty. Pokud vybraná oblast nepodporuje kódování JSON, bude toto pole šedé.

   > [!NOTE]
   > Pomocí pole **Formát názvu souboru objektu blob** můžete nastavit formát názvu objektu blob. Výchozí formát je `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. Formát musí obsahovat {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH} a {mm} v libovolném pořadí.
   >
   > Například při použití výchozího formátu názvu souboru objektu blob a za předpokladu, že název centra je ContosoTestHub a datum a čas je 30. října 2018 v 10:56, bude název objektu blob vypadat takto: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > Objekty blob se zapisují ve formátu Avro.
   >

8. Výběrem **vytvořit** vytvořte koncový bod úložiště a přidejte ho do trasy. Vrátíte se do podokna **Přidat trasu**.

9. Teď vyplňte zbývající informace o dotazu směrování. Tento dotaz určuje kritéria pro odesílání zpráv do kontejneru úložiště, který jste právě přidali jako koncový bod. Vyplňte pole na obrazovce.

   **Název:** Zadejte název dotazu směrování. V tomto kurzu se používá **ContosoStorageRoute**.

   **Koncový bod:** Zobrazí se koncový bod, který jste právě nastavili.

   **Zdroj dat:** Vyberte z rozevíracího seznamu **Telemetrické zprávy zařízení**.

   **Povolit trasu**: Ujistěte se, že je toto pole nastavené na `enabled` .
   
   **Dotaz směrování:** Jako řetězec dotazu zadejte `level="storage"`.

   ![Vytvoření dotazu směrování pro účet úložiště](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   Vyberte **Uložit**. Po dokončení se vrátíte do podokna Směrování zpráv, kde se zobrazí váš nový dotaz směrování pro úložiště. Zavřete podokno Trasy a vraťte se na stránku Skupina prostředků.

### <a name="route-to-a-service-bus-queue"></a>Směrování do fronty Service Bus

Nyní nastavte směrování pro frontu Service Bus. Přejděte do podokna Směrování zpráv a přidejte trasu. Při přidávání trasy pro ni definujte nový koncový bod. Po nastavení této trasy se zprávy, ve kterých je vlastnost **Level** nastavená na **kritická** , zapisují do fronty Service Bus, která spustí aplikaci logiky, která pak pošle e-mail s informacemi.

1. Na stránce skupina prostředků vyberte Centrum IoT a pak vyberte **směrování zpráv**.

2. V podokně **směrování zpráv** vyberte +**Přidat**.

3. V podokně **Přidat trasu** vyberte +**Přidat** vedle pole koncový bod. Vyberte **Fronta služby Service Bus**. Zobrazí se podokno **Přidat koncový bod služby Service Bus**.

   ![Přidání koncového bodu služby Service Bus](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Vyplňte jednotlivá pole:

   **Název koncového bodu:** Zadejte název koncového bodu. V tomto kurzu se používá **ContosoSBQueueEndpoint**.
   
   **Service Bus obor názvů**: pomocí rozevíracího seznamu vyberte obor názvů služby Service Bus, který jste nastavili v přípravných krocích. Tento kurz používá **ContosoSBNamespace**.

   **Service Bus fronta**: pomocí rozevíracího seznamu Vyberte frontu Service Bus. Tento kurz používá **contososbqueue**.

5. Výběrem možnosti **vytvořit** přidejte koncový bod fronty Service Bus. Vrátíte se do podokna **Přidat trasu**.

6. Teď vyplníte zbývající informace o dotazu směrování. Tento dotaz určuje kritéria pro odesílání zpráv do fronty služby Service Bus, kterou jste právě přidali jako koncový bod. Vyplňte pole na obrazovce. 

   **Název:** Zadejte název dotazu směrování. V tomto kurzu se používá **ContosoSBQueueRoute**. 

   **Koncový bod:** Zobrazí se koncový bod, který jste právě nastavili.

   **Zdroj dat:** Vyberte z rozevíracího seznamu **Telemetrické zprávy zařízení**.

   **Dotaz směrování:** Jako řetězec dotazu zadejte `level="critical"`. 

   ![Vytvoření dotazu směrování pro frontu Service Bus](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Vyberte **Uložit**. Jakmile se vrátíte do podokna Trasy, zobrazí se obě nové trasy, jak je vidět tady.

   ![Trasy, které jste právě nastavili](./media/tutorial-routing/message-routing-show-both-routes.png)

8. Vlastní koncové body, které nastavíte, můžete zobrazit tak, že vyberete kartu **vlastní koncové body** .

   ![Vlastní koncový bod, který jste právě nastavili](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Zavřete podokno Směrování zpráv a vraťte se do podokna Skupina prostředků.

## <a name="create-a-simulated-device"></a>Vytvoření simulovaného zařízení

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>Další kroky

Teď, když máte nastavené prostředky a nakonfigurované trasy zpráv, přejděte k dalšímu kurzu, kde se dozvíte, jak odesílat zprávy do centra IoT a jak je směrovat do různých cílů. 

> [!div class="nextstepaction"]
> [Část 2 – zobrazení výsledků směrování zpráv](tutorial-routing-view-message-routing-results.md)