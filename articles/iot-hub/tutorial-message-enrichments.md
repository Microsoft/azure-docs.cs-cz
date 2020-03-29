---
title: Kurz – použití obohacení zpráv služby Azure IoT Hub
description: Kurz, který ukazuje, jak používat obohacení zpráv pro zprávy služby Azure IoT Hub
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: robinsh
ms.openlocfilehash: c812e00699cd8f8cfbaf32feea1b43866ffb0990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78674356"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Kurz: Použití obohacení zpráv služby Azure IoT Hub

*Obohacení zpráv* popisuje schopnost služby Azure IoT Hub *razítko* zprávy s další informace před zprávy jsou odeslány do určeného koncového bodu. Jedním z důvodů, proč používat obohacení zpráv, je zahrnout data, která lze použít ke zjednodušení následného zpracování. Například obohacení telemetrických zpráv zařízení pomocí značky dvojčete zařízení může snížit zatížení zákazníků, aby tato informace mohla volat rozhraní API dvojčete zařízení. Další informace naleznete v [tématu Přehled obohacení zpráv](iot-hub-message-enrichments-overview.md).

V tomto kurzu uvidíte dva způsoby, jak vytvořit a nakonfigurovat prostředky, které jsou potřebné k testování obohacení zpráv pro službu IoT hub. Prostředky zahrnují jeden účet úložiště se dvěma kontejnery úložiště. Jeden kontejner obsahuje obohacené zprávy a jiný kontejner obsahuje původní zprávy. Součástí je také centrum IoT pro příjem zpráv a jejich směrování do příslušného kontejneru úložiště na základě toho, zda jsou obohacené nebo ne.

* První metodou je použití rozhraní příkazového příkazu Azure k vytvoření prostředků a konfiguraci směrování zpráv. Pak definujete obohacení ručně pomocí [portálu Azure](https://portal.azure.com).

* Druhou metodou je použití šablony Azure Resource Manager k vytvoření prostředků *i* konfigurací pro směrování zpráv a obohacení zpráv.

Po dokončení konfigurace směrování zpráv a obohacení zpráv pomocí aplikace k odesílání zpráv do služby IoT hub. Rozbočovač je pak směruje do obou kontejnerů úložiště. Jsou obohaceny pouze zprávy odeslané do koncového bodu pro **obohacený** kontejner úložiště.

Zde jsou úkoly, které provádíte k dokončení tohoto kurzu:

**Použití obohacení zpráv služby IoT Hub**
> [!div class="checklist"]
> * První metoda: Vytvořte prostředky a nakonfigurujte směrování zpráv pomocí rozhraní příkazového příkazu Azure. Nakonfigurujte obohacení zpráv ručně pomocí [portálu Azure](https://portal.azure.com).
> * Druhá metoda: Vytvořte prostředky a nakonfigurujte směrování zpráv a obohacení zpráv pomocí šablony Správce prostředků. 
> * Spusťte aplikaci, která simuluje zařízení IoT odesílající zprávy do centra.
> * Zobrazit výsledky a ověřte, že obohacení zprávy fungují podle očekávání.

## <a name="prerequisites"></a>Požadavky

* Mít předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.
* Nainstalujte [visual studio](https://www.visualstudio.com/).

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto kurzu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Načtení úložiště ukázek IoT C#

Stáhněte si [ukázky IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) z GitHubu a rozbalte je. Toto úložiště obsahuje několik šablon aplikací, skriptů a správce prostředků. Ty, které mají být použity pro tento kurz jsou následující:

* Pro ruční metodu je skript cli, který se používá k vytvoření prostředků. Tento skript je v /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Tento skript vytvoří prostředky a nakonfiguruje směrování zpráv. Po spuštění tohoto skriptu vytvořte obohacení zpráv ručně pomocí [portálu Azure](https://portal.azure.com).
* Pro automatizovanou metodu je šablona Azure Resource Manager. Šablona je v /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.json. Tato šablona vytvoří prostředky, nakonfiguruje směrování zpráv a potom nakonfiguruje obohacení zpráv.
* Třetí aplikace, kterou používáte, je aplikace Simulace zařízení, kterou používáte k odesílání zpráv do služby IoT hub a testování obohacení zpráv.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Ruční nastavení a konfigurace pomocí rozhraní příkazového příkazu Azure

Kromě vytváření potřebných prostředků skript Azure CLI také konfiguruje dvě trasy do koncových bodů, které jsou samostatné kontejnery úložiště. Další informace o konfiguraci směrování zpráv naleznete v [kurzu směrování](tutorial-routing.md). Po nastavení prostředků použijte [portál Azure](https://portal.azure.com) ke konfiguraci obohacení zpráv pro každý koncový bod. Pak pokračujte na testovací krok.

> [!NOTE]
> Všechny zprávy jsou směrovány do obou koncových bodů, ale budou obohaceny pouze zprávy, které přejdou do koncového bodu s nakonfigurovaným obohacením zpráv.
>

Můžete použít skript, který následuje, nebo můžete otevřít skript ve složce /resources staženého úložiště. Skript provede následující kroky:

* Vytvořte centrum IoT.
* Vytvoření účtu úložiště
* Vytvořte dva kontejnery v účtu úložiště. Jeden kontejner je pro obohacené zprávy a jiný kontejner je pro zprávy, které nejsou obohacené.
* Nastavte směrování pro dva různé účty úložiště:
    * Vytvořte koncový bod pro každý kontejner účtu úložiště.
    * Vytvořte trasu ke každému koncovému bodu kontejneru účtu úložiště.

Existuje několik názvů prostředků, které musí být globálně jedinečné, jako je například název centra IoT a název účtu úložiště. Chcete-li usnadnit spuštění skriptu, jsou tyto názvy prostředků připojeny s náhodnou alfanumerickou hodnotou nazvanou *randomValue*. Náhodná hodnota je generována jednou v horní části skriptu. Je připojen k názvům prostředků podle potřeby v celém skriptu. Pokud nechcete, aby hodnota byla náhodná, můžete ji nastavit na prázdný řetězec nebo na určitou hodnotu.

Pokud jste tak ještě neučinili, otevřete okno Azure [Cloud Shell](https://shell.azure.com) a ujistěte se, že je nastavena na Bash. Otevřete skript v rozvráceném úložišti, vyberte Ctrl+A, chcete-li vybrat vše, a pak ho zkopírujte stisknutím kombinace kláves Ctrl+C. Případně můžete zkopírovat následující skript příkazového příkazu nebo jej otevřít přímo v prostředí Cloud Shell. Vložte skript do okna Prostředí cloudu tak, že kliknete pravým tlačítkem myši na příkazový řádek a vyberete **Vložit**. Skript spouští jeden příkaz najednou. Po zastavení skriptu vyberte **Enter,** abyste se ujistili, že spouští poslední příkaz. Následující blok kódu zobrazuje použitý skript s komentáři, které vysvětlují, co dělá.

Zde jsou zdroje vytvořené skriptem. *Obohacené* znamená, že prostředek je pro zprávy s obohacení. *Původní* znamená, že prostředek je pro zprávy, které nejsou obohaceny.

| Name (Název) | Hodnota |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| název kontejneru | Původní  |
| název kontejneru | Obohacený  |
| Název zařízení IoT | Zařízení contoso-Test |
| Název ioT hubu | ContosoTestHubMsgEn |
| název účtu úložiště | contosostorage |
| název koncového bodu 1 | ContosoStorageEndpointOriginal |
| název koncového bodu 2 | ContosoStorageEndpointObohaceno|
| název trasy 1 | ContosoStorageRoutePůvodní |
| název trasy 2 | ContosoStorageRouteObohaceno |

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

V tomto okamžiku jsou všechny prostředky nastaveny a směrování zpráv je nakonfigurováno. Můžete zobrazit konfiguraci směrování zpráv na portálu a nastavit obohacení zpráv pro zprávy, které přejdou do **obohaceného** kontejneru úložiště.

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Ruční konfigurace obohacení zpráv pomocí portálu Azure

1. Přejděte do centra IoT výběrem **skupin prostředků**. Pak vyberte skupinu prostředků nastavenou pro tento kurz (**ContosoResourcesMsgEn**). Najděte centrum IoT v seznamu a vyberte ho. Vyberte **Směrování zpráv** pro centrum IoT.

   ![Vybrat směrování zpráv](./media/tutorial-message-enrichments/select-iot-hub.png)

   Podokno směrování zpráv obsahuje tři karty označené **Trasy**, **Vlastní koncové body**a **Obohatit zprávy**. Projděte si první dvě karty a podívejte se na konfiguraci nastavenou skriptem. Pomocí třetí karty přidejte obohacení zpráv. Obohaťme zprávy, které se přepyšují do koncového bodu pro kontejner úložiště s názvem **enriched**. Vyplňte název a hodnotu a pak vyberte koncový bod **ContosoStorageEndpointEnriched** z rozevíracího seznamu. Tady je příklad, jak nastavit obohacení, které přidá název centra IoT do zprávy:

   ![Přidat první obohacení](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Přidejte tyto hodnoty do seznamu pro koncový bod ContosoStorageEndpointEnriched.

   | Klíč | Hodnota | Koncový bod (rozevírací seznam) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | Umístění zařízení | $twin.tags.umístění | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |Kódzákazníka | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Pokud vaše zařízení nemá dvojče, hodnota, kterou sem vložíte, bude označena jako řetězec pro hodnotu v obohacení zprávy. Informace o dvojčeti zařízení zobrazíte tak, že přejdete do svého centra na portálu a vyberte **zařízení IoT**. Vyberte zařízení a v horní části stránky vyberte **Dvojče zařízení.**
   >
   > Můžete upravit informace o dvojčeti a přidat značky, například umístění, a nastavit je na určitou hodnotu. Další informace najdete v tématu [Principy a použití dvojčat zařízení ve službě IoT Hub](iot-hub-devguide-device-twins.md).

3. Po dokončení by mělo podokno vypadat podobně jako tento obrázek:

   ![Tabulka se všemi přidanými obohaceními](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Chcete-li změny uložit, vyberte **použít.** Přejděte do části [Test message enrichments.](#test-message-enrichments)

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Vytvoření a konfigurace pomocí šablony Správce prostředků
Šablonu Správce prostředků můžete použít k vytvoření a konfiguraci prostředků, směrování zpráv a obohacení zpráv.

1. Přihlaste se k portálu Azure. Výběrem **možnosti + Vytvoření zdroje** vyvoláte vyhledávací pole. Zadejte *nasazení šablony*a vyhledejte ji. V podokně výsledků vyberte **nasazení šablony (nasazení pomocí vlastní šablony).**

   ![Nasazení šablony na webu Azure Portal](./media/tutorial-message-enrichments/template-select-deployment.png)

1. V podokně **nasazení šablony** vyberte **Vytvořit.**

1. V podokně **Vlastní nasazení** vyberte vytvořit **vlastní šablonu v editoru**.

1. V podokně **předlohy Úpravy** vyberte **Načíst soubor**. Zobrazí se Průzkumník Windows. Vyhledejte soubor **template_messageenrichments.json** v rozbaleném souboru úložiště v **části /iot-hub/Tutorials/Routing/SimulatedDevice/resources**. 

   ![Vybrat šablonu z místního počítače](./media/tutorial-message-enrichments/template-select.png)

1. Výběrem **možnosti Otevřít** načtete soubor šablony z místního počítače. Načte se a zobrazí se v podokně úprav.

   Tato šablona je nastavena tak, aby používala globálně jedinečný název centra IoT a název účtu úložiště přidáním náhodné hodnoty na konec výchozích názvů, takže můžete šablonu použít bez jakýchkoli změn.

   Zde jsou prostředky vytvořené načtením šablony. **Obohacené** znamená, že prostředek je pro zprávy s obohacení. **Původní** znamená, že prostředek je pro zprávy, které nejsou obohaceny. Jedná se o stejné hodnoty, které se používají ve skriptu azure cli.

   | Name (Název) | Hodnota |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | název kontejneru | Původní  |
   | název kontejneru | Obohacený  |
   | Název zařízení IoT | Zařízení contoso-Test |
   | Název ioT hubu | ContosoTestHubMsgEn |
   | název účtu úložiště | contosostorage |
   | název koncového bodu 1 | ContosoStorageEndpointOriginal |
   | název koncového bodu 2 | ContosoStorageEndpointObohaceno|
   | název trasy 1 | ContosoStorageRoutePůvodní |
   | název trasy 2 | ContosoStorageRouteObohaceno |

1. Vyberte **Uložit**. Zobrazí se podokno **Vlastní nasazení** a zobrazí všechny parametry používané šablonou. Jediné pole, které je třeba **nastavit,** je skupina prostředků . Buď vytvořte nový, nebo vyberte jeden z rozevíracího seznamu.

   Tady je horní polovina **podokna vlastního nasazení.** Můžete vidět, kde vyplníte skupinu prostředků.

   ![Horní polovina podokna vlastního nasazení](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Tady je spodní polovina **podokna vlastního nasazení.** Můžete vidět zbývající parametry a podmínky. 

   ![Dolní polovina podokna vlastního nasazení](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Zaškrtnutím políčka souhlasíte s podmínkami. Pak vyberte **Koupit,** chcete-li pokračovat v nasazení šablony.

1. Počkejte, až bude šablona plně nasazena. Vyberte ikonu zvonku v horní části obrazovky a zkontrolujte průběh. Po dokončení pokračujte v části [Obohacení testovací zprávy.](#test-message-enrichments)

## <a name="test-message-enrichments"></a>Obohacení zkušební zprávy

Chcete-li zobrazit obohacení zprávy, vyberte **položku Skupiny prostředků**. Pak vyberte skupinu prostředků, kterou používáte pro tento kurz. Ze seznamu prostředků vyberte centrum IoT a přejděte na **Zprávy**. Zobrazí se konfigurace směrování zpráv a nakonfigurované obohacení.

Teď, když jsou obohacení zpráv nakonfigurovány pro koncový bod, spusťte aplikaci Simulované zařízení a odesílejte zprávy do centra IoT Hub. Centrum bylo nastaveno s nastavením, které provádí následující úkoly:

* Zprávy směrované do koncového bodu úložiště ContosoStorageEndpointOriginal nebudou obohaceny a `original`budou uloženy v kontejneru úložiště .

* Zprávy směrované do koncového bodu úložiště ContosoStorageEndpointEnriched budou obohaceny a uloženy v kontejneru `enriched`úložiště .

Aplikace Simulované zařízení je jednou z aplikací v rozbaleném stahování. Aplikace odesílá zprávy pro každou z různých metod směrování zpráv v [kurzu směrování](tutorial-routing.md), který zahrnuje Azure Storage.

Poklepáním na soubor řešení **IoT_SimulatedDevice.sln** otevřete kód v sadě Visual Studio a otevřete **Program.cs**. Nahraďte značku `{your hub name}`názvem centra IoT . Formát názvu hostitele centra IoT hub je **{název centra}.azure-devices.net**. V tomto kurzu je název hostitele centra ContosoTestHubMsgEn.azure-devices.net. Dále nahraďte klíč zařízení, který jste uložili dříve při `{your device key}`spuštění skriptu, a vytvořte prostředky pro značku .

Pokud klíč zařízení nemáte, můžete ho načíst z portálu. Po přihlášení přejděte do **skupin prostředků**, vyberte skupinu prostředků a vyberte centrum IoT hub. Testovací zařízení nahlédněte do části **Zařízení IoT** a vyberte zařízení. Vyberte ikonu kopírování vedle **primárního klíče,** chcete-li ji zkopírovat do schránky.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Spuštění a testování

Spusťte aplikaci konzoly na několik minut. Zprávy, které jsou odesílány jsou zobrazeny na obrazovce konzoly aplikace.

Aplikace odesílá zprávy typu zařízení-cloud do centra IoT každou sekundu. Zpráva obsahuje serializovaný objekt JSON s ID zařízení, teplotou, vlhkostí a úrovní zprávy, jejíž výchozí hodnota je `normal`. Náhodně přiřadí úroveň `critical` nebo `storage`, která způsobí, že zpráva má být směrována do účtu úložiště nebo do výchozího koncového bodu. Zprávy odeslané do **obohaceného** kontejneru v účtu úložiště budou obohaceny.

Po odeslání několika zpráv úložiště zobrazte data.

1. Vyberte **Skupiny prostředků**. Najděte skupinu prostředků **ContosoResourcesMsgEn**a vyberte ji.

2. Vyberte účet úložiště, který je **contosostorage**. Pak v levém podokně vyberte **Průzkumník a úložiště (náhled).**

   ![Vybrat Průzkumníka úložiště](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Vyberte **KONTEJNERY BLOB** zobrazíte dva kontejnery, které lze použít.

   ![Podívejte se na kontejnery v účtu úložiště](./media/tutorial-message-enrichments/show-blob-containers.png)

Zprávy v kontejneru s názvem **enriched** mají obohacení zprávy zahrnuty ve zprávách. Zprávy v kontejneru s názvem **original** mají nezpracovaná zprávy bez obohacení. Přejděte k podrobnostem do jednoho z kontejnerů, dokud se nedostanete na dno a neotevřete nejnovější soubor zpráv. Potom proveďte totéž pro ostatní kontejneru k ověření, že neexistují žádné obohacení přidány do zpráv v tomto kontejneru.

Když se podíváte na zprávy, které byly obohacené, měli byste vidět "moje Služby IoT Hub" s názvem centra a umístěním a ID zákazníka, například takto:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Tady je nezdokonalená zpráva. Všimněte si, že "moje IoT Hub", "devicelocation" a "customerID" se zde nezobrazují, protože tato pole jsou přidána obohacením. Tento koncový bod nemá žádné obohacení.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat všechny prostředky, které jste vytvořili v tomto kurzu, odstraňte skupinu prostředků. Tato akce odstraní všechny prostředky, které skupina obsahuje. V takovém případě odebere službu IoT hub, účet úložiště a samotnou skupinu prostředků.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Použití azure cli k vyčištění prostředků

Chcete-li odebrat skupinu prostředků, použijte příkaz [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). Připomeňme, že `$resourceGroup` byla nastavena na **ContosoResourcesMsgEn** na začátku tohoto kurzu.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali a otestovali přidání obohacení zpráv do zpráv služby IoT Hub pomocí následujících kroků:

**Použití obohacení zpráv služby IoT Hub**
> [!div class="checklist"]
> * První metoda: Vytvořte prostředky a nakonfigurujte směrování zpráv pomocí rozhraní příkazového příkazu Azure. Nakonfigurujte obohacení zpráv ručně pomocí [portálu Azure](https://portal.azure.com).
> * Druhá metoda: Vytvořte prostředky a nakonfigurujte směrování zpráv a obohacení zpráv pomocí šablony Azure Resource Manager.
> * Spusťte aplikaci, která simuluje zařízení IoT odesílající zprávy do centra.
> * Zobrazit výsledky a ověřte, že obohacení zprávy fungují podle očekávání.

Další informace o obohacení zpráv naleznete v [tématu Přehled obohacení zpráv](iot-hub-message-enrichments-overview.md).

Další informace o směrování zpráv naleznete v těchto článcích:

* [Směrování zpráv služby IoT Hub slouží k odesílání zpráv mezi zařízeními a cloudy do různých koncových bodů](iot-hub-devguide-messages-d2c.md)
* [Kurz: Směrování služby IoT Hub](tutorial-routing.md)
