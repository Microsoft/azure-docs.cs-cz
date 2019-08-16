---
title: Kurz – používání rozšíření zpráv Azure IoT Hub
description: Kurz ukazující použití rozšíření zpráv pro zprávy Azure IoT Hub
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 77d900844705bb86ce4bcfeda31d6ee765cb8d45
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69535010"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments-preview"></a>Kurz: Používání rozšíření zpráv Azure IoT Hub (Preview)

*Rozšiřování zpráv* je schopnost IoT Hub k razítku zpráv s dalšími informacemi, než se zprávy odešlou do určeného koncového bodu. Jedním z důvodů použití rozšíření zpráv je zahrnutí dat, která je možné použít ke zjednodušení zpracování po směru. Například obohacení zpráv telemetrie zařízení pomocí značky s dvojitou silou zařízení může snížit zatížení zákazníky, aby pro tyto informace volalo volání rozhraní API zařízení. Další informace najdete v tématu [Přehled rozšíření zpráv](iot-hub-message-enrichments-overview.md).

V tomto kurzu použijete rozhraní příkazového řádku Azure CLI k nastavení prostředků, včetně dvou koncových bodů odkazujících na dva různé kontejnery úložiště – obohacené a **původní**. Pak použijete [Azure Portal](https://portal.azure.com) ke konfiguraci rozšíření zprávy, aby se použily jenom pro zprávy odeslané na koncový bod s **obohaceným** kontejnerem úložiště. Odesíláte zprávy na IoT Hub, které jsou směrovány do obou kontejnerů úložiště. Budou obohaceny pouze zprávy odeslané do koncového bodu pro obohacený kontejner úložiště.

Tady jsou úkoly, které provedete k dokončení tohoto kurzu:

**Používání rozšíření IoT Hub zpráv**
> [!div class="checklist"]
> * Pomocí Azure CLI vytvořte prostředky – centrum IoT, účet úložiště se dvěma koncovými body a konfiguraci směrování.
> * Ke konfiguraci rozšíření zpráv použijte Azure Portal.
> * Spusťte aplikaci, která simuluje zařízení IoT odesílající zprávy do centra.
> * Podívejte se na výsledky a ověřte, že rozšíření zprávy fungují podle očekávání.

## <a name="prerequisites"></a>Požadavky

* Mít předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Nainstalovat sadu [Visual Studio](https://www.visualstudio.com/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>Načíst vzorový kód

Stáhněte si [simulaci zařízení IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) a rozbalte ho. Toto úložiště obsahuje několik aplikací, včetně toho, který budete používat k posílání zpráv do centra IoT.

Tento soubor ke stažení obsahuje také skript pro vytváření prostředků používaných k testování obohacení zpráv. Skript je v/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Prozatím si můžete prohlédnout skript a použít ho. Skript můžete také zkopírovat přímo z článku.

Až budete připraveni začít s testováním, budete k posílání zpráv do služby IoT Hub používat aplikaci pro simulaci zařízení od tohoto stažení.

## <a name="set-up-and-configure-resources"></a>Nastavení a konfigurace prostředků

Kromě vytvoření nezbytných prostředků skript Azure CLI také nakonfiguruje tyto dvě trasy na koncové body, které jsou samostatné kontejnery úložiště. Další informace o konfiguraci směrování najdete v [kurzu směrování](tutorial-routing.md). Po nastavení prostředků můžete pomocí [Azure Portal](https://portal.azure.com) nakonfigurovat rozšíření zprávy pro každý koncový bod a potom pokračovat v kroku testování.

> [!NOTE]
> Všechny zprávy jsou směrovány do obou koncových bodů, ale budou obohaceny pouze zprávy směrované na koncový bod s nakonfigurovanými rozšířeními zpráv.
>

Můžete použít skript níže nebo otevřít skript ve složce/Resources staženého úložiště. Tady jsou kroky, které skript provede:

* Vytvořit IoT Hub.
* Vytvoření účtu úložiště.
* Vytvořte dva kontejnery v účtu úložiště – jednu pro obohacené zprávy a jednu pro zprávy, které nejsou obohaceny.
* Nastavte směrování pro dva různé účty úložiště.
    * Vytvořte koncový bod pro každý kontejner účtu úložiště.
    * Vytvořte trasu ke každému z koncových bodů kontejneru účtu úložiště.

Existuje několik názvů prostředků, které musí být globálně jedinečné, například IoT Hub název a název účtu úložiště. Aby bylo možné skript snadno spouštět, připojí se názvy prostředků s náhodnou alfanumerický hodnotou s názvem *randomValue*. RandomValue se generuje jednou na začátku skriptu a připojuje se k názvům prostředků podle potřeby v celém skriptu. Pokud nechcete, aby byla náhodná, můžete ji nastavit na prázdný řetězec nebo na konkrétní hodnotu.

Pokud jste to ještě neudělali, otevřete [Cloud Shell okno pro bash.](https://shell.azure.com). Otevřete skript v úložišti Get, pomocí kombinace kláves CTRL + a vyberte vše a potom ho zkopírujte stisknutím kombinace kláves CTRL + C. Alternativně můžete zkopírovat následující skript rozhraní příkazového řádku nebo ho otevřít přímo ve službě cloud Shell. Vložte skript do okna Azure Cloud Shell tak, že kliknete pravým tlačítkem na příkazový řádek a vyberete **Vložit**. Ve skriptu je spuštěn jeden příkaz najednou. Po ukončení běhu skriptu vyberte **ENTER** a ujistěte se, že se spustí poslední příkaz. Následující blok kódu ukazuje skript, který se používá, s komentáři, který vysvětluje, co dělá.

Tady jsou zdroje vytvořené skriptem. **Obohaceno** znamená, že prostředek je pro zprávy s obohacením. **Původní** znamená, že prostředek je pro zprávy, které nejsou obohaceny.

| Name | Value |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| název kontejneru | původně  |
| název kontejneru | Vylepšené  |
| Název zařízení IoT | Contoso-test-zařízení |
| Název centra IoT Hub | ContosoTestHubMsgEn |
| Název účtu úložiště | contosostorage |
| Název koncového bodu 1 | ContosoStorageEndpointOriginal |
| Název koncového bodu 2 | ContosoStorageEndpointEnriched|
| Název trasy 1 | ContosoStorageRouteOriginal |
| Název trasy 2 | ContosoStorageRouteEnriched |

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

V tuto chvíli jsou prostředky všechny nastavené a směrování je nakonfigurované. Můžete zobrazit konfiguraci směrování zpráv na portálu a nastavit rozšíření zpráv pro zprávy, které se budou nacházející do obohaceného kontejneru úložiště.

### <a name="view-routing-and-configure-the-message-enrichments"></a>Zobrazení směrování a konfigurace rozšíření zpráv

1. Vyberte **skupiny prostředků**a pak vyberte skupinu prostředků nastavenou pro tento kurz (**ContosoResources_MsgEn**), a to tak, že vyberete své IoT Hub. Vyhledejte IoT Hub v seznamu a vyberte ji. Vyberte *směrování zpráv** pro Centrum IoT.

   ![Vybrat směrování zpráv](./media/tutorial-message-enrichments/select-iot-hub.png)

   Podokno směrování zpráv obsahuje tři karty – **Směrování**, **vlastní koncové body**a **zprávy obohacení**. Pokud chcete zobrazit konfiguraci nastavenou skriptem, můžete procházet první dvě karty. K přidání rozšíření zpráv použijte třetí kartu. Pojďme vylepšit zprávy na koncový bod pro kontejner úložiště s názvem **obohaceno**. Zadejte název a hodnotu a potom v rozevíracím seznamu vyberte koncový bod **ContosoStorageEndpointEnriched** . Tady je příklad nastavení obohacení, který do zprávy přidá IoT Hub název:

   ![Přidat první obohacení](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Tyto hodnoty přidejte do seznamu pro koncový bod ContosoStorageEndpointEnriched.

   | Name | Value | Koncový bod (rozevírací seznam) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |ID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Pokud vaše zařízení nemá dvojitou hodnotu, hodnota, kterou zde zadáte, bude označena jako řetězec pro hodnotu v rozšířeních zprávy. Chcete-li zobrazit informace o tom, že se zařízení zobrazuje, přejděte do svého centra na portálu, vyberte **zařízení IoT**, vyberte zařízení a potom v horní části stránky vyberte možnost v **zařízení** .
   >
   > Můžete upravit zdvojené informace a přidat značky (například umístění) a nastavit ji na určitou hodnotu, pokud chcete. Další informace najdete v tématu [Principy a použití dvojčat zařízení ve službě IoT Hub](iot-hub-devguide-device-twins.md).

3. Až budete hotovi, podokno by mělo vypadat podobně jako na tomto obrázku:

   ![Tabulka s přidanými všemi obohaceními](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Pokud chcete změny uložit, vyberte **použít** .

## <a name="send-messages-to-the-iot-hub"></a>Odeslat zprávy na IoT Hub

Teď, když jsou pro koncový bod nakonfigurované rozšíření zpráv, spusťte simulaci aplikace simulovaného zařízení, aby se odesílaly zprávy do IoT Hub. Centrum bylo nastaveno s nastavením, které provádí následující akce:

* Zprávy směrované do koncového bodu úložiště ContosoStorageEndpointOriginal nebudou obohaceny a budou uloženy v kontejneru `original`úložiště.

* Zprávy směrované do koncového bodu úložiště ContosoStorageEndpointEnriched budou obohaceny a uloženy v kontejneru `enriched`úložiště.

Aplikace simulovaného zařízení je jedním z aplikací při stažení. Aplikace odesílá zprávy pro každou z různých metod směrování zpráv v [kurzu směrování](tutorial-routing.md); To zahrnuje Azure Storage.

Dvojím kliknutím na soubor řešení (IoT_SimulatedDevice. sln) otevřete kód v aplikaci Visual Studio a pak otevřete Program.cs. Nahraďte `{your hub name}` názvem centra IoT. Formát názvu hostitele služby IoT Hub je **{název vašeho centra}. Azure-Devices.NET**. V tomto kurzu se název hostitele centra **ContosoTestHubMsgEn.Azure-Devices.NET**. Dále nahraďte `{device key}` klíč zařízení, který jste předtím uložili při spuštění skriptu pro vytvoření prostředků.

Pokud nemáte klíč zařízení, můžete ho načíst z portálu. Po přihlášení klikněte na **skupiny prostředků**, vyberte svoji skupinu prostředků a pak vyberte svou IoT Hub. Podívejte se na zařízení **IoT** pro vaše testovací zařízení a vyberte své zařízení. Vyberte ikonu kopírování vedle **primárního klíče** a zkopírujte ji do schránky.

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Spuštění a testování

Spusťte konzolovou aplikaci. Počkejte několik minut. Zprávy, které jsou odesílány, se zobrazí na obrazovce konzoly aplikace.

Aplikace odesílá zprávy typu zařízení-cloud do centra IoT každou sekundu. Zpráva obsahuje serializovaný objekt JSON s ID zařízení, teplotou, vlhkostí a úrovní zprávy, jejíž výchozí hodnota je `normal`. Náhodně přiřadí úroveň `critical` nebo `storage`, což způsobilo přesměrování zprávy do účtu úložiště nebo do výchozího koncového bodu. Zprávy odesílané obohacenému kontejneru v účtu úložiště budou obohaceny.

Po odeslání několika zpráv úložiště si prohlédněte data.

1. Vyberte **skupiny prostředků**, vyhledejte skupinu prostředků (ContosoResourcesMsgEn) a vyberte ji.

2. Vyberte svůj účet úložiště (contosostorage). Pak vyberte **Průzkumník služby Storage (Preview)** v podokně výběr na levé straně.

   ![Výběr Průzkumníka služby Storage](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Vyberte **kontejnery objektů BLOB** pro zobrazení dvou kontejnerů, které se dají použít.

   ![Zobrazit kontejnery v účtu úložiště](./media/tutorial-message-enrichments/show-blob-containers.png)

Zprávy v kontejneru s názvem **obohaceny** mají rozšíření zprávy zahrnuté do zpráv. Zprávy v kontejneru s názvem **originál** budou mít nezpracované zprávy bez rozšíření. Přejděte k podrobnostem v jednom z kontejnerů, dokud se nevrátíte k dolnímu a otevřete poslední soubor zpráv, a pak proveďte stejný pro druhý kontejner, abyste ověřili, že se do zpráv v tomto kontejneru nepřidaly žádné obohacení.

Když se podíváte na zprávy, které byly obohaceny, měli byste se podívat na "můj IoT Hub" s názvem centra a také na umístění a ID zákazníka, například:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

A tady je neobohacená zpráva. Tady se nezobrazuje "" moje IoT Hub "," devicelocation "a" KódZákazníka ", protože tento koncový bod nemá žádné rozšíření.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat všechny prostředky, které jste vytvořili v tomto kurzu, odstraňte skupinu prostředků. Tato akce odstraní všechny prostředky, které skupina obsahuje. V takovém případě odebrání služby IoT Hub, účtu úložiště a samotné skupiny prostředků.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Vyčištění prostředků pomocí Azure CLI

Chcete-li odebrat skupinu prostředků, použijte příkaz [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup`byl nastaven na **ContosoResources** zpět na začátku tohoto kurzu.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nakonfigurovali a otestovali přidání rozšíření zpráv do IoT Hub zpráv pomocí následujících kroků:

**Používání rozšíření IoT Hub zpráv**
> [!div class="checklist"]
> * Pomocí Azure CLI vytvořte prostředky – centrum IoT, účet úložiště se dvěma enendpointsy a konfiguraci směrování.
> * Ke konfiguraci rozšíření zpráv použijte Azure Portal.
> * Spusťte aplikaci, která simuluje odeslání zprávy zařízení IoT do centra.
> * Podívejte se na výsledky a ověřte, že rozšíření zprávy fungují podle očekávání.

Další informace o rozšířeních zpráv najdete v tématu [Přehled rozšíření zpráv](iot-hub-message-enrichments-overview.md).

Další informace o směrování zpráv najdete v těchto článcích:

* [Použití směrování zpráv IoT Hub k posílání zpráv ze zařízení do cloudu do různých koncových bodů](iot-hub-devguide-messages-d2c.md)

* [Kurz: Směrování IoT Hub](tutorial-routing.md)