---
title: Konfigurace směrování zpráv pro Azure IoT Hub pomocí rozhraní příkazového příkazu Azure
description: Konfigurace směrování zpráv pro Azure IoT Hub pomocí rozhraní příkazového příkazu Azure a portálu Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 38a40d628b883c0e7ada824d47d3fdf3d29caf93
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084386"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>Kurz: Ke konfiguraci směrování zpráv služby IoT Hub pomocí rozhraní příkazového příkazu Azure a portálu Azure

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>Použití azure cli k vytvoření základních prostředků

Tento kurz používá rozhraní příkazového příkazového nastavení Azure k vytvoření základních prostředků a pak pomocí [portálu Azure](https://portal.azure.com) zobrazí teze, jak nakonfigurovat směrování zpráv a nastavit virtuální zařízení pro testování.

Zkopírujte a vložte skript níže do prostředí Cloud Shell a stiskněte Enter. Spustí skript po jednom řádku. Tím se vytvoří základní prostředky pro tento kurz, včetně účtu úložiště, služby IoT Hub, oboru názvů service bus a fronty service bus.

Existuje několik názvů prostředků, které musí být globálně jedinečné, jako je například název služby IoT Hub a název účtu úložiště. Aby to bylo jednodušší, jsou tyto názvy prostředků připojeny s náhodnou alfanumerickou hodnotou nazvanou *randomValue*. RandomValue je generována jednou v horní části skriptu a připojena k názvům prostředků podle potřeby v celém skriptu. Pokud nechcete, aby byl náhodný, můžete jej nastavit na prázdný řetězec nebo na určitou hodnotu.

> [!TIP]
> Tip o ladění: tento skript používá symbol pokračování (zpětné lomítko), `\`aby byl skript čitelnější. Pokud máte problém se spuštěním skriptu, ujistěte `bash` se, že vaše relace prostředí Cloud Je spuštěna a že po žádném z zpětných lomítka nejsou žádné mezery.
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

Teď, když jsou nastaveny základní prostředky, můžete nakonfigurovat směrování zpráv na [webu Azure Portal](https://portal.azure.com).

## <a name="set-up-message-routing"></a>Nastavení směrování zpráv

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>Směrovat k účtu úložiště

Nyní nastavte směrování pro účet úložiště. Přejděte do podokna Směrování zpráv a přidejte trasu. Při přidávání trasy pro ni definujte nový koncový bod. Po nastavení tohoto směrování se automaticky zapisují zprávy, kde je vlastnost **level** nastavena na **úložiště.** 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **Skupiny prostředků**a vyberte skupinu prostředků. Tento kurz používá **ContosoResources**.

2. V seznamu prostředků vyberte centrum IoT Hub. Tento kurz používá **ContosoTestHub**.

3. Vyberte **položku Směrování zpráv**. V podokně **Směrování zpráv** vyberte +**Přidat**. V podokně **Přidat trasu** vyberte +**Přidat** vedle pole Koncový bod, abyste zobrazili podporované koncové body, jak jsou zobrazeny na následujícím obrázku:

   ![Zahájení přidávání koncového bodu pro trasu](./media/tutorial-routing/message-routing-add-a-route-w-storage-ep.png)

4. Vyberte **Úložiště objektů blob**. Zobrazí se podokno **Přidat koncový bod úložiště.**

   ![Přidání koncového bodu](./media/tutorial-routing/message-routing-add-storage-ep.png)

5. Zadejte název koncového bodu. Tento kurz používá **ContosoStorageEndpoint**.

6. Vyberte **Vybrat kontejner**. Tím přejdete na seznam vašich účtů úložiště. Vyberte účet, který jste nastavili v rámci přípravy. Tento kurz používá **contosostorage**. Zobrazí se seznam kontejnerů v daném účtu úložiště. **Vyberte** kontejner, který jste nastavili v přípravných krocích. Tento kurz používá **contosoresults**. Vrátíte se do **podokna Přidat koncový bod úložiště** a zobrazí se výběry, které jste provedli.

7. Nastavte kódování na AVRO nebo JSON. Pro účely tohoto kurzu použijte u zbývajících polí výchozí hodnoty. Toto pole bude zašedlé, pokud vybraná oblast nepodporuje kódování JSON.,

   > [!NOTE]
   > Pomocí pole **Formát názvu souboru objektu blob** můžete nastavit formát názvu objektu blob. Výchozí formát je `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. Formát musí obsahovat {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH} a {mm} v libovolném pořadí.
   >
   > Například při použití výchozího formátu názvu souboru objektu blob a za předpokladu, že název centra je ContosoTestHub a datum a čas je 30. října 2018 v 10:56, bude název objektu blob vypadat takto: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > Objekty blob se zapisují ve formátu Avro.
   >

8. Vyberte **Vytvořit,** chcete-li vytvořit koncový bod úložiště a přidat ho do trasy. Vrátíte se do podokna **Přidat trasu**.

9. Teď vyplňte zbývající informace o dotazu směrování. Tento dotaz určuje kritéria pro odesílání zpráv do kontejneru úložiště, který jste právě přidali jako koncový bod. Vyplňte pole na obrazovce.

   **Název:** Zadejte název dotazu směrování. Tento kurz používá **ContosoStorageRoute**.

   **Koncový bod:** Zobrazí se koncový bod, který jste právě nastavili.

   **Zdroj dat:** Vyberte z rozevíracího seznamu **Telemetrické zprávy zařízení**.

   **Povolit trasu**: Ujistěte `enabled`se, že je toto pole nastaveno na .
   
   **Dotaz směrování:** Jako řetězec dotazu zadejte `level="storage"`.

   ![Vytvoření směrovacího dotazu pro účet úložiště](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   Vyberte **Uložit**. Po dokončení se vrátíte do podokna Směrování zpráv, kde se zobrazí váš nový dotaz směrování pro úložiště. Zavřete podokno Trasy a vraťte se na stránku Skupina prostředků.

### <a name="route-to-a-service-bus-queue"></a>Trasa do fronty služby Service Bus

Nyní nastavte směrování pro frontu Service Bus. Přejděte do podokna Směrování zpráv a přidejte trasu. Při přidávání trasy pro ni definujte nový koncový bod. Po nastavení této trasy zprávy, kde je vlastnost **level** nastavena na **kritickou,** jsou zapsány do fronty Service Bus, která aktivuje aplikaci logiky, která pak odešle e-mail s informacemi.

1. Na stránce Skupina prostředků vyberte centrum IoT a pak vyberte **Směrování zpráv**.

2. V podokně **Směrování zpráv** vyberte +**Přidat**.

3. V podokně **Přidat trasu** vyberte +**Přidat** vedle pole Koncový bod. Vyberte **Fronta služby Service Bus**. Zobrazí se podokno **Přidat koncový bod služby Service Bus**.

   ![Přidání koncového bodu sběrnice](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Vyplňte jednotlivá pole:

   **Název koncového bodu:** Zadejte název koncového bodu. Tento kurz používá **ContosoSBQueueEndpoint**.
   
   **Obor názvů služby Service Bus**: Pomocí rozevíracího seznamu vyberte obor názvů sběrnice, který jste nastavili v krocích přípravy. Tento kurz používá **ContosoSBNamespace**.

   **Fronta služby Service Bus**: Pomocí rozevíracího seznamu vyberte frontu služby Service Bus. Tento kurz používá **contososbqueue**.

5. Výběrem **možnosti Vytvořit** přidáte koncový bod fronty služby Service Bus. Vrátíte se do podokna **Přidat trasu**.

6. Teď vyplníte zbývající informace o dotazu směrování. Tento dotaz určuje kritéria pro odesílání zpráv do fronty služby Service Bus, kterou jste právě přidali jako koncový bod. Vyplňte pole na obrazovce. 

   **Název:** Zadejte název dotazu směrování. Tento kurz používá **ContosoSBQueueRoute**. 

   **Koncový bod:** Zobrazí se koncový bod, který jste právě nastavili.

   **Zdroj dat:** Vyberte z rozevíracího seznamu **Telemetrické zprávy zařízení**.

   **Dotaz směrování:** Jako řetězec dotazu zadejte `level="critical"`. 

   ![Vytvoření směrovacího dotazu pro frontu služby Service Bus](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Vyberte **Uložit**. Jakmile se vrátíte do podokna Trasy, zobrazí se obě nové trasy, jak je vidět tady.

   ![Trasy, které jste právě nastavili](./media/tutorial-routing/message-routing-show-both-routes.png)

8. Vlastní koncové body, které nastavíte, uvidíte tak, že vyberete kartu **Vlastní koncové body.**

   ![Vlastní koncový bod, který jste právě nastavili](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Zavřete podokno Směrování zpráv a vraťte se do podokna Skupina prostředků.

## <a name="create-a-simulated-device"></a>Vytvoření simulovaného zařízení

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>Další kroky

Teď, když máte nastavené prostředky a trasy zpráv nakonfigurované, přejdete na další kurz, abyste se dozvěděli, jak odesílat zprávy do služby IoT hub a vidět, že jsou směrovány do různých cílů. 

> [!div class="nextstepaction"]
> [Část 2 – Zobrazení výsledků směrování zpráv](tutorial-routing-view-message-routing-results.md)
