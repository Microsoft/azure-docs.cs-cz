---
title: Kurz – použití obohacení zpráv služby Azure IoT Hub
description: Kurz ukazuje, jak používat zprávy obohacení zpráv služby Azure IoT Hub
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: e4906bf9f2aead69c315ddb7b2e3b10489378d87
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259071"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments-preview"></a>Kurz: Pomocí obohacení zpráv služby Azure IoT Hub (preview)

*Zpráva obohacení* je schopnost služby IoT Hub do *razítko* zprávy s doplňujícími informacemi před odesláním zprávy do určeným koncového bodu. Jedním z důvodů použití obohacení zprávy se zahrnou data, která slouží k zjednodušení zpracování příjmu dat. Rozšíření telemetrických zpráv zařízení s značku dvojčete zařízení můžete například snížit zatížení zákazníkům dvojče zařízení pro tyto informace volání rozhraní API. Další informace najdete v tématu [přehled zprávy obohacení](iot-hub-message-enrichments-overview.md).

V tomto kurzu jste pomocí Azure CLI nastavit prostředky, včetně dva koncové body služby, které odkazují na dvě různé úložiště kontejnery – **rozšiřují** a **původní**. Použít [webu Azure portal](https://portal.azure.com) konfigurace obohacení zprávy pouze pro zprávy odeslané do koncového bodu se použije **rozšiřují** kontejneru úložiště. Odešlete zprávy do služby IoT Hub, které jsou směrovány na obou kontejnery úložiště. Pouze zprávy odeslané do koncového bodu pro **rozšiřují** kontejner úložiště se rozšiřují.

Zde jsou úkoly k dokončení tohoto kurzu budete provádět:

**Pomocí služby IoT Hub zpráva obohacení**
> [!div class="checklist"]
> * Pomocí rozhraní příkazového řádku Azure, vytvořte prostředky – služby IoT hub, účet úložiště se dva koncové body služby a konfiguraci směrování.
> * Pomocí webu Azure portal nakonfigurovat obohacení zprávy.
> * Spuštění aplikace, která simuluje zařízení odesílá zprávy do centra IoT.
> * Zobrazit výsledky a ověřte, zda že zpráva obohacení fungují podle očekávání.

## <a name="prerequisites"></a>Požadavky

* Mít předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Nainstalovat sadu [Visual Studio](https://www.visualstudio.com/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>Načíst ukázku kódu

Stáhněte si [simulace zařízení IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) a rozbalte ho. Toto úložiště obsahuje několik aplikací, které v ní, včetně toho, který budete používat k odesílání zpráv do služby IoT hub.

Tento soubor ke stažení obsahuje také skript pro vytváření prostředků používá k testování obohacení zprávy. Skript musí být v /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Teď můžete podívat na skript a použije. Skript můžete také zkopírovat přímo z článku.

Až budete připravení ke spuštění, testování, použije tento soubor ke stažení aplikace simulace zařízení k odeslání zprávy do služby IoT hub.

## <a name="set-up-and-configure-resources"></a>Nastavení a konfigurace prostředků

Kromě vytvoření potřebné prostředky, rozhraní příkazového řádku Azure skript nakonfiguruje také dvě cesty do koncových bodů, které jsou kontejnery, samostatné úložiště. Další informace o konfiguraci směrování, najdete v článku [směrování kurzu](tutorial-routing.md). Po nastavení prostředků použijete [webu Azure portal](https://portal.azure.com) ke konfiguraci obohacení zprávu pro každý koncový bod a poté pokračujte krokem testování.

> [!NOTE]
> Všechny zprávy jsou směrovány na oba koncové body, ale pouze zprávy, že přejdete na koncový bod s nakonfigurovanou zpráva obohacení se rozšiřují.
>

Můžete použít níže uvedený skript, nebo otevřete skript ve složce /resources stažené úložiště. Tady jsou kroky, které skript provede:

* Vytvořit IoT Hub.
* Vytvoření účtu úložiště.
* Vytvoření dvou kontejnerů v účtu úložiště – jeden pro bohatších možností zprávy a jeden pro zprávy, které nejsou rozšiřují.
* Nastavení směrování pro dva různé účty.
    * Vytvořte koncový bod pro každý kontejner účtu úložiště.
    * Vytvořte trasu na všech koncových bodů kontejneru účtu úložiště.

Existuje několik názvy prostředků, které musí být globálně jedinečný, jako je název služby IoT Hub a název účtu úložiště. Chcete-li snadněji při spuštění skriptu, jsou připojeny tyto názvy prostředků náhodných alfanumerických hodnoty nazvané *randomValue*. RandomValue vygeneruje jednou v horní části skriptu a připojí k názvy prostředků podle potřeby v celém skriptu. Pokud nechcete, aby to přijde náhodného, můžete nastavit na prázdný řetězec nebo na určitou hodnotu.

Pokud jste tak již neučinili, otevřete [okna služby Cloud Shell pro Bash.](https://shell.azure.com). Otevřete skript v úložišti rozbaleny, použijte Ctrl A vyberte ji a pak Ctrl-C. Alternativně můžete zkopírujte následující skript rozhraní příkazového řádku nebo jej otevřete přímo ve službě cloud shell. Vložte skript v okně Azure cloud shell tak, že kliknete pravým tlačítkem na příkazovém řádku a vyberete **vložit**. Skript je současně spustit jeden příkaz. Po dokončení skriptu se zastaví, vyberte **Enter** k Ujistěte se, že spuštění posledního příkazu. Následující blok kódu demonstruje skript, který používáte, přičemž komentář s vysvětlením, co dělají.

Tady jsou prostředky vytvořené v rámci skriptu. **Rozšiřují** znamená, že prostředek je pro zprávy s obohacení. **Původní** znamená, že prostředek je pro zprávy, které nejsou rozšiřují.

| Název | Hodnota |
|-----|-----|
| Skupina prostředků | ContosoResourcesMsgEn |
| Název kontejneru | Původní  |
| Název kontejneru | rozšíření  |
| Název zařízení IoT | Contoso-Test zařízení |
| Název centra IoT | ContosoTestHubMsgEn |
| Název účtu úložiště | contosostorage |
| koncový bod název 1 | ContosoStorageEndpointOriginal |
| koncový bod název 2 | ContosoStorageEndpointEnriched|
| trasa název 1 | ContosoStorageRouteOriginal |
| cesta název 2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing rules.
subscriptionID=$(az account show --query id -o tsv)

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
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
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
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One points to container1 in the storage account
#   and includes all messages.
# The other points to container2 in the same storage account
#   and only includes enriched messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for container 1, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for container 2, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are not enriched.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

V tomto okamžiku prostředky jsou nastavené v provozu a směrování je nakonfigurované. Můžete zobrazit konfigurace směrování zpráv na portálu a nastavte obohacení zprávy zprávy přenášené do **rozšiřují** kontejneru úložiště.

### <a name="view-routing-and-configure-the-message-enrichments"></a>Zobrazit směrování a nakonfigurovat obohacení zprávy

1. Přejděte k centru IoT tak, že vyberete **skupiny prostředků**, pak vyberte skupinu prostředků nastavit pro účely tohoto kurzu (**ContosoResources_MsgEn**). V seznamu vyhledejte služby IoT Hub a vyberte ji. Vyberte *směrování zpráv** pro službu Iot Hub.

   ![Vyberte směrování zpráv](./media/tutorial-message-enrichments/select-iot-hub.png)

   Zpráva směrovací podokno má tři karty – **trasy**, **vlastní koncové body**, a **obohacovat zprávy**. Můžete procházet první dvě karty se můžete podívat konfigurace nastavení skriptem. Třetí karta přidat obohacení zprávy. Pojďme obohacovat zprávy, že přejdete na koncový bod pro kontejner úložiště, který volá **rozšiřují**. Zadejte název a hodnotu a pak vyberte koncový bod **ContosoStorageEndpointEnriched** z rozevíracího seznamu. Tady je příklad nastavení rozšíření, která se přidá do zprávy název služby IoT Hub:

   ![Přidat první rozšíření](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Přidejte tyto hodnoty do seznamu ContosoStorageEndpointEnriched koncového bodu.

   | Název | Hodnota | Koncový bod (rozevírací seznam) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | Device location (Umístění zařízení) | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |customerID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Pokud zařízení nemá dvojčete, hodnoty, které tady bude označený jako řetězec pro hodnotu v obohacení zprávy. Pokud chcete zobrazit zařízení, dvojče informace, přejděte k centru na portálu, pak vyberte **zařízení IoT**, vyberte zařízení a pak vyberte **dvojče zařízení** v horní části stránky.
   >
   > Můžete upravit dvojče informace o přidávání značek (jako je například umístění) a nastavte ho na určitou hodnotu, pokud chcete. Další informace najdete v tématu [Principy a použití dvojčat zařízení ve službě IoT Hub](iot-hub-devguide-device-twins.md).

3. Až budete hotovi, do podokna by měl vypadat nějak takto:

   ![Tabulka se přidá všechny obohacení](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Vyberte **použít** a uložte změny.

## <a name="send-messages-to-the-iot-hub"></a>Odesílání zpráv do služby IoT Hub

Teď, když obohacení zprávy jsou nakonfigurovány pro koncový bod, spusťte aplikaci simulovaného zařízení k odesílání zpráv do služby IoT Hub. Centrum je nastavený s pravidly, která provést následující:

* Zprávy, které jsou směrovány na koncový bod úložiště ContosoStorageEndpointOriginal nebude rozšiřují a bude ukládat do kontejneru úložiště `original`.

* Zprávy, které jsou směrovány na koncový bod úložiště ContosoStorageEndpointEnriched se rozšiřují a ukládat do kontejneru úložiště `enriched`.

Aplikace simulovaného zařízení je jedna z aplikací rozzipovaný soubor ke stažení. Aplikace odešle zprávy pro jednotlivé metody směrování jiná zpráva v [směrování kurzu](tutorial-routing.md); jedná se o Azure Storage.

Poklikejte na soubor řešení (IoT_SimulatedDevice.sln) otevřete kód v sadě Visual Studio a pak otevřete soubor Program.cs. Náhradní `{your hub name}` názvem IoT hub. Formát názvu hostitele IoT hub je **{název vašeho centra} .azure devices.net**. Pro účely tohoto kurzu je název hostitele centra **ContosoTestHubMsgEn.azure-devices.net**. V dalším kroku nahraďte `{device key}` klíčem zařízení jste si uložili dříve při spuštění skriptu vytvoříte prostředky.

Pokud nemáte klíč zařízení, můžete ji načíst z portálu. Po přihlášení, přejděte na **skupiny prostředků**, vyberte skupinu prostředků a pak vyberte své Centrum IoT. Podívejte se do části **zařízení IoT** pro vaše zařízení testovací a vyberte své zařízení. Vyberte ikonu kopírování vedle **primární klíč** ho zkopírovat do schránky.

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Spuštění a testování

Spusťte konzolovou aplikaci. Počkejte několik minut. Zprávy, které jsou odesílány jsou zobrazeny v okně konzoly aplikace.

Aplikace odesílá zprávy typu zařízení-cloud do centra IoT každou sekundu. Zpráva obsahuje serializovaný objekt JSON s ID zařízení, teplotou, vlhkostí a úrovní zprávy, jejíž výchozí hodnota je `normal`. Náhodně přiřadí úroveň `critical` nebo `storage`, neporozuměl a zpráva bude směrovat do účtu úložiště nebo výchozí koncový bod. Zprávy odeslané do **rozšiřují** kontejneru v účtu úložiště se rozšiřují.

Po odeslání několik úložiště zpráv, zobrazte data.

1. Vyberte **skupiny prostředků**, najděte vaši skupinu prostředků (ContosoResourcesMsgEn) a vyberte ho.

2. Vyberte svůj účet úložiště (contosostorage). Potom vyberte **Storage Exploreru (preview)** z výběru podokna na levé straně.

   ![Vyberte Průzkumníka služby storage](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Vyberte **kontejnery objektů BLOB** dva kontejnery, které je možné zobrazit.

   ![Zobrazit kontejnery v účtu úložiště](./media/tutorial-message-enrichments/show-blob-containers.png)

Volá se zprávy v kontejneru **rozšiřují** mají obohacení zpráva součástí zprávy. Volá se zprávy v kontejneru **původní** bude mít nezpracované zprávy s žádné obohacení. K podrobnostem jeden z kontejnerů dokud získat do dolní části a otevřete soubor nejnovější zprávy a pak provést totéž pro jiné kontejneru, chcete-li ověřit, že neexistují žádné obohacení přidá do zprávy v tomto kontejneru.

Když se podíváte na zprávy, které byly rozšíření, byste měli vidět "Moje služby IoT Hub" pomocí názvu centra, jakož i umístění a ID zákazníka, následujícím způsobem:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","device location":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

A tady je zpráva o zvětšení. "" Moje služby IoT Hub","Umístění zařízení"a"customerID"nezobrazí, protože tento koncový bod nemá žádné obohacení.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat všechny prostředky, které jste vytvořili v tomto kurzu, odstraňte skupinu prostředků. Tato akce odstraní všechny prostředky, které skupina obsahuje. V takovém případě odstraní samotnou skupinu prostředků, služby IoT hub a účet úložiště.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Pomocí Azure CLI pro vyčištění prostředků

Chcete-li odebrat skupinu prostředků, použijte příkaz [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` byl nastaven na **ContosoResources** zpět na začátku tohoto kurzu.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nakonfigurovali a otestovat přidáním zprávy obohacení zpráv služby IoT Hub pomocí následujících kroků:

**Pomocí služby IoT Hub zpráva obohacení**
> [!div class="checklist"]
> * Pomocí rozhraní příkazového řádku Azure, vytvořte prostředky – služby IoT hub, účet úložiště se dvěma enendpoints a konfigurace směrování.
> * Pomocí webu Azure portal nakonfigurovat obohacení zprávy.
> * Spuštění aplikace, která simuluje odesílání zprávy zařízení IoT k rozbočovači.
> * Zobrazit výsledky a ověřte, zda že zpráva obohacení fungují podle očekávání.

Další informace o obohacení zpráv, najdete v článku [přehled zprávy obohacení](iot-hub-message-enrichments-overview.md).

Další informace o směrování zpráv najdete v těchto článcích:

* [Použití směrování zpráv služby IoT Hub pro odesílání zpráv typu zařízení cloud do různých koncových bodů](iot-hub-devguide-messages-d2c.md)

* [Kurz: Směrování služby IoT Hub](tutorial-routing.md)