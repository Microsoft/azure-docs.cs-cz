---
title: Konfigurace směrování zpráv pro Azure IoT Hubu pomocí rozhraní příkazového řádku Azure a webu Azure portal | Dokumentace Microsoftu
description: Konfigurace směrování zpráv pro Azure IoT Hubu pomocí rozhraní příkazového řádku Azure a webu Azure portal
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: d81b01992bd3bdd49a48a873281d1be1e795497a
ms.sourcegitcommit: 9e8dfa1169a55c3c8af93a6c5f4e0dace4de48b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2019
ms.locfileid: "65556021"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>Kurz: Konfigurace směrování zpráv služby IoT Hub pomocí Azure CLI a webu Azure portal

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>Chcete-li vytvořit základní prostředky pomocí Azure CLI

Tento kurz používá Azure CLI k vytvoření základní prostředky a pak pomocí [webu Azure portal](https://portal.azure.com) zobrazíte postup konfigurace směrování zpráv a nastavit virtuální zařízení pro účely testování.

Existuje několik názvy prostředků, které musí být globálně jedinečný, jako je název služby IoT Hub a název účtu úložiště. Abychom to usnadnili, jsou tyto názvy prostředků připojeny náhodných alfanumerických hodnoty nazvané *randomValue*. RandomValue vygeneruje jednou v horní části skriptu a připojí k názvy prostředků podle potřeby v celém skriptu. Pokud nechcete, aby to přijde náhodného, můžete nastavit na prázdný řetězec nebo na určitou hodnotu.

Zkopírujte a vložte níže uvedený skript do Cloud Shellu a stiskněte klávesu Enter. Jeden řádek skriptu běží současně. Tím se vytvoří základní prostředky pro účely tohoto kurzu, včetně účtu úložiště, IoT Hub, Namespace služby Service Bus a fronty služby Service Bus.

Poznámka k ladění: Tento skript používá symbol pokračování (zpětné lomítko `\`) aby skript lépe čitelný. Pokud máte potíže při spuštění skriptu, ujistěte se, že nejsou žádné mezery po všech zpětná lomítka.

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing rules.
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

Teď, když se nastaví základní prostředky, můžete nakonfigurovat směrování zpráv v [webu Azure portal](https://portal.azure.com).

## <a name="set-up-message-routing"></a>Nastavení směrování zpráv

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>Směrovat do účtu úložiště

Nyní nastavte směrování pro účet úložiště. Přejděte do podokna Směrování zpráv a přidejte trasu. Při přidávání trasy pro ni definujte nový koncový bod. Po tomto směrování nastavení, zprávy, kde **úroveň** je nastavena na **úložiště** se zapisují na účet úložiště automaticky. 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. V [webu Azure portal](https://portal.azure.com)vyberte **skupiny prostředků**, pak vyberte skupinu prostředků. Tento kurz používá **ContosoResources**.

2. Vyberte v seznamu prostředků centra IoT. Tento kurz používá **ContosoTestHub**.

3. Vyberte **směrování zpráv**. V **směrování zpráv** vyberte +**přidat**. Na **přidejte trasu** vyberte +**přidat** vedle pole koncový bod a zobrazit podporovaných koncových bodů, jako je zobrazena na následujícím obrázku:

   ![Začátek přidávání koncový bod trasy](./media/tutorial-routing/message-routing-add-a-route-w-storage-ep.png)

4. Vyberte **Úložiště objektů blob**. Zobrazí **přidat koncový bod úložiště** podokně.

   ![Přidání koncového bodu](./media/tutorial-routing/message-routing-add-storage-ep.png)

5. Zadejte název koncového bodu. Tento kurz používá **ContosoStorageEndpoint**.

6. Vyberte **vyberte kontejner**. Tím přejdete na seznam vašich účtů úložiště. Vyberte účet, který jste nastavili v rámci přípravy. Tento kurz používá **contosostorage**. Zobrazí se seznam kontejnerů v daném účtu úložiště. **Vyberte** kontejneru jste vytvořili v postupu přípravy. Tento kurz používá **contosoresults**. Se vrátit k **přidat koncový bod úložiště** podokně uvidíme voleb vybraných.

7. Nastavte kódování AVRO nebo JSON. Pro účely tohoto kurzu použijte u zbývajících polí výchozí hodnoty. Toto pole bude zapnutá Pokud vybrané oblasti nepodporuje kódování JSON.,

   > [!NOTE]
   > Pomocí pole **Formát názvu souboru objektu blob** můžete nastavit formát názvu objektu blob. Výchozí formát je `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. Formát musí obsahovat {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH} a {mm} v libovolném pořadí.
   >
   > Například při použití výchozího formátu názvu souboru objektu blob a za předpokladu, že název centra je ContosoTestHub a datum a čas je 30. října 2018 v 10:56, bude název objektu blob vypadat takto: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > Objekty blob se zapisují ve formátu Avro.
   >

8. Vyberte **vytvořit** vytvořit koncový bod úložiště a přidat trasy. Vrátíte se do podokna **Přidat trasu**.

9. Teď vyplňte zbývající informace o dotazu směrování. Tento dotaz určuje kritéria pro odesílání zpráv do kontejneru úložiště, který jste právě přidali jako koncový bod. Vyplňte pole na obrazovce.

   **Název**: Zadejte název pro směrování dotazů. Tento kurz používá **ContosoStorageRoute**.

   **Koncový bod**: To ukazuje na koncový bod, který jste právě nainstalovali.

   **Zdroj dat**: Vyberte **zařízení Telemetrických zpráv** z rozevíracího seznamu.

   **Povolit trasy**: Ujistěte se, toto pole je nastaveno na `enabled`.
   
   **Směrování dotazů**: Zadejte `level="storage"` jako řetězec dotazu.

   ![Vytvoření směrování dotazu pro účet úložiště](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   Vyberte **Uložit**. Po dokončení se vrátíte do podokna Směrování zpráv, kde se zobrazí váš nový dotaz směrování pro úložiště. Zavřete podokno Trasy a vraťte se na stránku Skupina prostředků.

### <a name="route-to-a-service-bus-queue"></a>Směrovat do fronty služby Service Bus

Nyní nastavte směrování pro frontu Service Bus. Přejděte do podokna Směrování zpráv a přidejte trasu. Při přidávání trasy pro ni definujte nový koncový bod. Až tato trasa nastavená, zprávy, kde **úroveň** je nastavena na **kritické** se zapisují do fronty služby Service Bus, která aktivuje aplikace logiky, který odešle e-mail s informacemi.

1. Na stránce skupiny prostředků, vyberte službu IoT hub a pak vyberte **směrování zpráv**.

2. V **směrování zpráv** vyberte +**přidat**.

3. Na **přidejte trasu** podokně, vyberte +**přidat** vedle pole pro koncový bod. Vyberte **Fronta služby Service Bus**. Zobrazí se podokno **Přidat koncový bod služby Service Bus**.

   ![Přidání koncového bodu služby Service bus](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Vyplňte jednotlivá pole:

   **Název koncového bodu**: Zadejte název koncového bodu. Tento kurz používá **ContosoSBQueueEndpoint**.
   
   **Služba Service Bus Namespace**: Pomocí rozevíracího seznamu vyberte obor názvů služby Service bus, které jste vytvořili v postupu přípravy. Tento kurz používá **ContosoSBNamespace**.

   **Fronty služby Service Bus**: Pomocí rozevíracího seznamu vyberte frontu služby Service Bus. Tento kurz používá **contososbqueue**.

5. Vyberte **vytvořit** k přidání koncového bodu fronty služby Service Bus. Vrátíte se do podokna **Přidat trasu**.

6. Teď vyplníte zbývající informace o dotazu směrování. Tento dotaz určuje kritéria pro odesílání zpráv do fronty služby Service Bus, kterou jste právě přidali jako koncový bod. Vyplňte pole na obrazovce. 

   **Název**: Zadejte název pro směrování dotazů. Tento kurz používá **ContosoSBQueueRoute**. 

   **Koncový bod**: To ukazuje na koncový bod, který jste právě nainstalovali.

   **Zdroj dat**: Vyberte **zařízení Telemetrických zpráv** z rozevíracího seznamu.

   **Směrování dotazů**: Zadejte `level="critical"` jako řetězec dotazu. 

   ![Vytvoření směrování dotazu pro frontu služby Service Bus](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Vyberte **Uložit**. Jakmile se vrátíte do podokna Trasy, zobrazí se obě nové trasy, jak je vidět tady.

   ![Trasy, které jste právě nainstalovali](./media/tutorial-routing/message-routing-show-both-routes.png)

8. Zobrazí se vlastní koncové body nastavit tak, že vyberete **vlastní koncové body** kartu.

   ![Vlastní koncový bod, který jste právě nainstalovali](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Zavřete podokno Směrování zpráv a vraťte se do podokna Skupina prostředků.

## <a name="create-a-simulated-device"></a>Vytvoření simulovaného zařízení

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>Další postup

Teď, když máte prostředky nastavit a směrování zpráv nakonfigurována, přejděte k dalšímu kurzu, přečtěte si, jak odesílat zprávy do služby IoT hub a je možné směrovat na různé cíle. 

> [!div class="nextstepaction"]
> [Část 2 – zobrazení výsledků směrování zpráv](tutorial-routing-view-message-routing-results.md)
