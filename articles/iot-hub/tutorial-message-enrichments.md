---
title: Kurz – používání rozšíření zpráv Azure IoT Hub
description: Kurz ukazující použití rozšíření zpráv pro zprávy Azure IoT Hub
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 030a69c7eca70c081a1d9392bfa527f3386d7c2b
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150600"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Kurz: použití rozšíření zpráv Azure IoT Hub

*Rozšíření zpráv* popisují schopnost služby Azure IoT Hub vysílat *zprávy s dalšími informacemi, než* se zprávy odešlou do určeného koncového bodu. Jedním z důvodů použití rozšíření zpráv je zahrnutí dat, která je možné použít ke zjednodušení zpracování po směru. Například obohacení zpráv telemetrie zařízení pomocí značky s dvojitou silou zařízení může snížit zatížení zákazníky, aby pro tyto informace volalo volání rozhraní API zařízení. Další informace najdete v tématu [Přehled rozšíření zpráv](iot-hub-message-enrichments-overview.md).

V tomto kurzu vidíte dva způsoby, jak vytvořit a nakonfigurovat prostředky, které jsou potřeba k otestování obohacení zpráv pro Centrum IoT. Mezi prostředky patří jeden účet úložiště se dvěma kontejnery úložiště. Jeden kontejner obsahuje obohacené zprávy a další kontejner obsahuje původní zprávy. Součástí je také služba IoT Hub pro příjem zpráv a jejich směrování do příslušného kontejneru úložiště na základě toho, zda jsou obohaceny.

* První metodou je použití Azure CLI k vytvoření prostředků a konfiguraci směrování zpráv. Pak můžete rozšíření definovat ručně pomocí [Azure Portal](https://portal.azure.com).

* Druhou metodou je použití šablony Azure Resource Manager k vytvoření prostředků i konfigurací pro směrování zpráv *a rozšiřování* zpráv.

Po dokončení konfigurace pro směrování zpráv a rozšiřování zpráv můžete použít aplikaci k posílání zpráv do služby IoT Hub. Centrum je pak směruje do obou kontejnerů úložiště. Budou obohaceny pouze zprávy odeslané do koncového bodu pro **obohacený** kontejner úložiště.

Tady jsou úkoly, které provedete k dokončení tohoto kurzu:

**Použití IoT Hubch rozšíření zpráv**
> [!div class="checklist"]
> * První způsob: vytváření prostředků a konfigurace směrování zpráv pomocí Azure CLI. Ruční konfigurace rozšíření zprávy pomocí [Azure Portal](https://portal.azure.com).
> * Druhá metoda: Vytvořte prostředky a nakonfigurujte směrování zpráv a rozšíření zpráv pomocí šablony Správce prostředků. 
> * Spusťte aplikaci, která simuluje zařízení IoT odesílající zprávy do centra.
> * Zobrazte výsledky a ověřte, že rozšíření zprávy fungují podle očekávání.

## <a name="prerequisites"></a>Předpoklady

* Mít předplatné Azure. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.
* Nainstalujte [Visual Studio](https://www.visualstudio.com/).

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto kurzu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Načtení úložiště ukázek pro IoT C#

Stáhněte si [ukázky IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) z GitHubu a rozbalte je. Toto úložiště obsahuje několik aplikací, skriptů a Správce prostředků šablon. K tomu, aby je bylo možné použít v tomto kurzu, patří následující:

* Pro ruční metodu je k dispozici skript rozhraní příkazového řádku, který se používá k vytvoření prostředků. Tento skript je v/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli. azcli. Tento skript vytvoří prostředky a nakonfiguruje směrování zpráv. Po spuštění tohoto skriptu vytvořte rozšíření zprávy ručně pomocí [Azure Portal](https://portal.azure.com).
* Pro automatizovanou metodu je Azure Resource Manager šablona. Šablona je v/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.jszapnutá. Tato šablona vytvoří prostředky, nakonfiguruje směrování zpráv a potom nakonfiguruje rozšíření zpráv.
* Třetí aplikace, kterou používáte, je aplikace pro simulaci zařízení, kterou používáte k posílání zpráv do služby IoT Hub a testování rozšíření zprávy.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Ruční nastavení a konfigurace pomocí Azure CLI

Kromě vytvoření nezbytných prostředků skript Azure CLI také nakonfiguruje tyto dvě trasy na koncové body, které jsou samostatné kontejnery úložiště. Další informace o tom, jak nakonfigurovat směrování zpráv, najdete v [kurzu směrování](tutorial-routing.md). Po nastavení prostředků použijte [Azure Portal](https://portal.azure.com) ke konfiguraci rozšíření zprávy pro každý koncový bod. Pak pokračujte k testovacímu kroku.

> [!NOTE]
> Všechny zprávy jsou směrovány do obou koncových bodů, ale budou obohaceny pouze zprávy směrované na koncový bod s nakonfigurovanými rozšířeními zpráv.
>

Můžete použít skript, který následuje, nebo můžete skript otevřít ve složce/Resources staženého úložiště. Skript provede následující kroky:

* Vytvořte centrum IoT.
* Vytvoření účtu úložiště
* Vytvořte dva kontejnery v účtu úložiště. Jeden kontejner je pro obohacené zprávy a další kontejner je pro zprávy, které nejsou obohaceny.
* Nastavte směrování pro dva různé účty úložiště:
    * Vytvořte koncový bod pro každý kontejner účtu úložiště.
    * Vytvořte trasu ke každému z koncových bodů kontejneru účtu úložiště.

Existuje několik názvů prostředků, které musí být globálně jedinečné, jako je název služby IoT Hub a název účtu úložiště. Aby bylo možné skript snadno spouštět, připojí se názvy prostředků s náhodnou alfanumerický hodnotou s názvem *randomValue*. Náhodná hodnota je generována jednou na začátku skriptu. Připojí se k názvům prostředků podle potřeby v celém skriptu. Pokud nechcete, aby byla hodnota náhodná, můžete ji nastavit na prázdný řetězec nebo na konkrétní hodnotu.

Pokud jste to ještě neudělali, otevřete [okno Azure Cloud Shell](https://shell.azure.com) a ujistěte se, že je nastavené na bash. Otevřete skript v úložišti pro oddálení, vyberte CTRL + A a vyberte všechny. potom ho zkopírujte kliknutím na CTRL + C. Případně můžete zkopírovat následující skript rozhraní příkazového řádku nebo ho otevřít přímo v Cloud Shell. Vložte skript do okna Cloud Shell tak, že kliknete pravým tlačítkem myši na příkazový řádek a vyberete **Vložit**. Skript spustí jeden příkaz po druhém. Po ukončení běhu skriptu vyberte **ENTER** a ujistěte se, že se spustí poslední příkaz. Následující blok kódu ukazuje skript, který se používá, s komentáři, které vysvětlují, co dělá.

Tady jsou zdroje vytvořené skriptem. *Obohaceno* znamená, že prostředek je pro zprávy s obohacením. *Původní* znamená, že prostředek je pro zprávy, které nejsou obohaceny.

| Název | Hodnota |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| název kontejneru | původně  |
| název kontejneru | Vylepšené  |
| Název zařízení IoT | Contoso-test-zařízení |
| Název IoT Hub | ContosoTestHubMsgEn |
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
az extension add --name azure-iot

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
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

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

# This is the endpoint for the first container, for endpoint messages that are not enriched.
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

# This is the endpoint for the second container, for endpoint messages that are enriched.
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

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

V tomto okamžiku se prostředky nastavují a směrování zpráv je nakonfigurované. Můžete zobrazit konfiguraci směrování zpráv na portálu a nastavit rozšíření zpráv pro zprávy, které se budou nacházející do **obohaceného** kontejneru úložiště.

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Ruční konfigurace rozšíření zpráv pomocí Azure Portal

1. Vyberte **skupiny prostředků**, které chcete přejít do služby IoT Hub. Pak vyberte skupinu prostředků nastavenou pro tento kurz (**ContosoResourcesMsgEn**). V seznamu vyhledejte centrum IoT a vyberte ho. Vyberte **směrování zpráv pro službu** IoT Hub.

   ![Vybrat směrování zpráv](./media/tutorial-message-enrichments/select-iot-hub.png)

   Podokno směrování zpráv obsahuje tři karty označené **směrováním**, **vlastními koncovými body**a **obohacením zpráv**. Projděte si první dvě karty, abyste viděli konfiguraci nastavenou skriptem. K přidání rozšíření zpráv použijte třetí kartu. Pojďme vylepšit zprávy na koncový bod pro kontejner úložiště s názvem **obohaceno**. Zadejte název a hodnotu a v rozevíracím seznamu vyberte koncový bod **ContosoStorageEndpointEnriched** . Tady je příklad, jak nastavit obohacení, který do zprávy přidá název služby IoT Hub:

   ![Přidat první obohacení](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Tyto hodnoty přidejte do seznamu pro koncový bod ContosoStorageEndpointEnriched.

   | Klíč | Hodnota | Koncový bod (rozevírací seznam) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin. Tags. Location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |ID | 6ce345b8-1e4a-411E-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Pokud vaše zařízení nemá dvojitou hodnotu, hodnota, kterou zde zadáte, bude označena jako řetězec pro hodnotu v rozšířeních zpráv. Pokud chcete zobrazit informace o zdvojeném zařízení, přejděte na portálu na portál a vyberte **zařízení IoT**. Vyberte své zařízení a pak v horní části stránky vyberte **vlákna zařízení** .
   >
   > Můžete upravit zdvojené informace a přidat tak značky, jako je umístění, a nastavit je na konkrétní hodnotu. Další informace najdete v tématu [Principy a použití dvojčat zařízení ve službě IoT Hub](iot-hub-devguide-device-twins.md).

3. Až budete hotovi, podokno by mělo vypadat podobně jako na tomto obrázku:

   ![Tabulka s přidanými všemi obohaceními](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Pokud chcete změny uložit, vyberte **použít** . Přejděte k části [zkušebních rozšíření zprávy testu](#test-message-enrichments) .

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Vytvoření a konfigurace pomocí šablony Správce prostředků
Šablonu Správce prostředků můžete použít k vytvoření a konfiguraci prostředků, směrování zpráv a rozšíření zpráv.

1. Přihlaste se k portálu Azure. Výběrem **+ vytvořit prostředek zobrazíte** vyhledávací pole. Zadejte *nasazení šablony*a vyhledejte ho. V podokně výsledků vyberte možnost **template Deployment (nasadit pomocí vlastní šablony)**.

   ![Template deployment v Azure Portal](./media/tutorial-message-enrichments/template-select-deployment.png)

1. V podokně **template Deployment** vyberte **vytvořit** .

1. V podokně **vlastní nasazení** vyberte **v editoru vytvořit vlastní šablonu**.

1. V podokně **Upravit šablonu** vyberte **načíst soubor**. Zobrazí se Průzkumník Windows. Vyhledejte **template_messageenrichments.js** v souboru v souboru úložiště s příponou v **/IoT-Hub/tutorials/Routing/SimulatedDevice/Resources**. 

   ![Vybrat šablonu z místního počítače](./media/tutorial-message-enrichments/template-select.png)

1. Vyberte **otevřít** a načtěte soubor šablony z místního počítače. Načte a zobrazí se v podokně úprav.

   Tato šablona je nastavená tak, aby používala globálně jedinečný název služby IoT Hub a název účtu úložiště, a to přidáním náhodné hodnoty na konec výchozích názvů, takže můžete šablonu použít bez provedení změn.

   Tady jsou zdroje vytvořené pomocí načtení šablony. **Obohaceno** znamená, že prostředek je pro zprávy s obohacením. **Původní** znamená, že prostředek je pro zprávy, které nejsou obohaceny. Jedná se o stejné hodnoty, které se používají ve skriptu rozhraní příkazového řádku Azure CLI.

   | Název | Hodnota |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | název kontejneru | původně  |
   | název kontejneru | Vylepšené  |
   | Název zařízení IoT | Contoso-test-zařízení |
   | Název IoT Hub | ContosoTestHubMsgEn |
   | Název účtu úložiště | contosostorage |
   | Název koncového bodu 1 | ContosoStorageEndpointOriginal |
   | Název koncového bodu 2 | ContosoStorageEndpointEnriched|
   | Název trasy 1 | ContosoStorageRouteOriginal |
   | Název trasy 2 | ContosoStorageRouteEnriched |

1. Vyberte **Uložit**. Zobrazí se podokno **vlastní nasazení** a zobrazí všechny parametry používané šablonou. Jediné pole, které je třeba nastavit, je **Skupina prostředků**. Buď vytvořte nový, nebo ho vyberte v rozevíracím seznamu.

   Toto je horní polovina podokna **vlastní nasazení** . Můžete se podívat, kde vyplníte skupinu prostředků.

   ![Horní polovina podokna vlastního nasazení](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Tady je poslední polovina podokna **vlastní nasazení** . Můžete zobrazit zbývající parametry a podmínky a ujednání. 

   ![Dolní polovina podokna vlastního nasazení](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Zaškrtnutím políčka souhlasíte s podmínkami a ujednáními. Pak vyberte **koupit** , pokud chcete pokračovat v nasazování šablony.

1. Počkejte, až bude šablona plně nasazená. Vyberte ikonu zvonku v horní části obrazovky a podívejte se na průběh. Až se dokončí, přejděte k části [zkušební rozšíření zprávy testu](#test-message-enrichments) .

## <a name="test-message-enrichments"></a>Rozšíření zkušebních zpráv

Chcete-li zobrazit rozšíření zprávy, vyberte **skupiny prostředků**. Pak vyberte skupinu prostředků, kterou používáte pro tento kurz. V seznamu prostředků vyberte Centrum IoT a pak přejít na **zasílání zpráv**. Zobrazí se konfigurace směrování zpráv a nakonfigurovaná rozšíření.

Teď, když jsou pro koncový bod nakonfigurované rozšíření zpráv, spusťte aplikaci simulovaného zařízení, aby se odesílaly zprávy do služby IoT Hub. Centrum se nastavilo s nastaveními, která provádějí následující úlohy:

* Zprávy směrované do koncového bodu úložiště ContosoStorageEndpointOriginal nebudou obohaceny a budou uloženy v kontejneru úložiště `original` .

* Zprávy směrované do koncového bodu úložiště ContosoStorageEndpointEnriched budou obohaceny a uloženy v kontejneru úložiště `enriched` .

Aplikace simulovaného zařízení je jedním z aplikací při stažení. Aplikace odesílá zprávy pro každou z různých metod směrování zpráv v [kurzu směrování](tutorial-routing.md), který zahrnuje Azure Storage.

Dvojím kliknutím na soubor řešení **IoT_SimulatedDevice. sln** otevřete kód v aplikaci Visual Studio a pak otevřete **program.cs**. Nahraďte název služby IoT Hub pro značku `{your hub name}` . Formát názvu hostitele služby IoT Hub je **{název vašeho centra}. Azure-Devices.NET**. V tomto kurzu se název hostitele centra ContosoTestHubMsgEn.azure-devices.net. Potom nahraďte klíč zařízení, který jste předtím uložili, když jste spustili skript, který vytvoří prostředky pro značku `{your device key}` .

Pokud nemáte klíč zařízení, můžete ho načíst z portálu. Po přihlášení klikněte na **skupiny prostředků**, vyberte svoji skupinu prostředků a potom vyberte Centrum IoT. Podívejte se na zařízení **IoT** pro vaše testovací zařízení a vyberte své zařízení. Vyberte ikonu kopírování vedle **primárního klíče** a zkopírujte ji do schránky.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Spuštění a testování

Spusťte konzolovou aplikaci během několika minut. Zprávy, které jsou odesílány, se zobrazí na obrazovce konzoly aplikace.

Aplikace odesílá zprávy typu zařízení-cloud do centra IoT každou sekundu. Zpráva obsahuje serializovaný objekt JSON s ID zařízení, teplotou, vlhkostí a úrovní zprávy, jejíž výchozí hodnota je `normal`. Náhodně přiřadí úroveň `critical` nebo `storage` , což způsobí, že se zpráva směruje do účtu úložiště nebo do výchozího koncového bodu. Zprávy odesílané **obohacenému** kontejneru v účtu úložiště budou obohaceny.

Po odeslání několika zpráv úložiště si prohlédněte data.

1. Vyberte **skupiny prostředků**. Vyhledejte skupinu prostředků **ContosoResourcesMsgEn**a vyberte ji.

2. Vyberte svůj účet úložiště, který je **contosostorage**. V levém podokně vyberte **Průzkumník služby Storage (Preview)** .

   ![Vyberte Průzkumník služby Storage](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Vyberte **kontejnery objektů BLOB** pro zobrazení dvou kontejnerů, které se dají použít.

   ![Zobrazit kontejnery v účtu úložiště](./media/tutorial-message-enrichments/show-blob-containers.png)

Zprávy v kontejneru s názvem **obohaceny** mají rozšíření zprávy zahrnuté do zpráv. Zprávy v kontejneru s názvem **originál** obsahují nezpracované zprávy bez rozšíření. Přejděte k podrobnostem v jednom z kontejnerů, dokud se nedostanete do dolní části, a otevřete nejnovější soubor zpráv. Pak proveďte stejnou akci pro druhý kontejner, abyste ověřili, že do zpráv v tomto kontejneru nejsou přidávány žádné obohacení.

Když se podíváte na zprávy, které byly obohaceny, měli byste vidět "můj IoT Hub" s názvem centra a umístěním a ID zákazníka, například:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Tady je neobohacená zpráva. Všimněte si, že se tady nezobrazí "moje IoT Hub," devicelocation "a" KódZákazníka ", protože tato pole jsou přidaná rozšířeními. Tento koncový bod nemá žádné rozšíření.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat všechny prostředky, které jste vytvořili v tomto kurzu, odstraňte skupinu prostředků. Tato akce odstraní všechny prostředky, které skupina obsahuje. V takovém případě odebrání služby IoT Hub, účtu úložiště a samotné skupiny prostředků.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Vyčištění prostředků pomocí Azure CLI

Chcete-li odebrat skupinu prostředků, použijte příkaz [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete). Odvolání, které `$resourceGroup` bylo nastaveno na **ContosoResourcesMsgEn** na začátku tohoto kurzu.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali a otestovali přidání rozšíření zpráv do IoT Hub zpráv pomocí následujících kroků:

**Použití IoT Hubch rozšíření zpráv**
> [!div class="checklist"]
> * První způsob: vytváření prostředků a konfigurace směrování zpráv pomocí Azure CLI. Ruční konfigurace rozšíření zprávy pomocí [Azure Portal](https://portal.azure.com).
> * Druhá metoda: Vytvořte prostředky a nakonfigurujte směrování zpráv a rozšíření zpráv pomocí šablony Azure Resource Manager.
> * Spusťte aplikaci, která simuluje zařízení IoT odesílající zprávy do centra.
> * Zobrazte výsledky a ověřte, že rozšíření zprávy fungují podle očekávání.

Další informace o rozšířeních zpráv najdete v tématu [Přehled rozšíření zpráv](iot-hub-message-enrichments-overview.md).

Další informace o směrování zpráv najdete v těchto článcích:

* [Použití směrování zpráv IoT Hub k posílání zpráv ze zařízení do cloudu do různých koncových bodů](iot-hub-devguide-messages-d2c.md)
* [Kurz: IoT Hub směrování](tutorial-routing.md)