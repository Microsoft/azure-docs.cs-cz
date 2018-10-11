---
title: Konfigurace směrování zpráv s Azure IoT Hub (.NET) | Microsoft Docs
description: Konfigurace směrování zpráv s Azure IoT Hub
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 09/11/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 575c8a5bec4c7763c75154835830ba350f009e93
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946931"
---
# <a name="tutorial-configure-message-routing-with-iot-hub"></a>Kurz: Konfigurace směrování zpráv s Azure IoT Hub

[Směrování zpráv](iot-hub-devguide-messages-d2c.md) umožňuje odesílání telemetrických dat ze zařízení IoT do předdefinovaných koncových bodů kompatibilních s centrem událostí nebo do vlastních koncových bodů, jako je například úložiště objektů blob, fronta služby Service Bus, téma služby Service Bus a Event Hubs. Při konfiguraci směrování zpráv můžete vytvořit [dotazy směrování](iot-hub-devguide-routing-query-syntax.md) pro přizpůsobení trasy, která odpovídá určité podmínce. Po nastavení se příchozí data automaticky přesměrují na koncové body pomocí služby IoT Hub. 

V tomto kurzu zjistíte, jak nastavit a používat dotazy směrování se službou IoT Hub. Budete směrovat zprávy z IoT zařízení na jednu z několika služeb, včetně úložiště objektů blob a fronty Service Bus. Zprávy do fronty Service Bus budou vyzvednuty aplikací logiky a odeslány e-mailem. Zprávy, které nemají nastavené specifické směrování, se odešlou na výchozí koncový bod a zobrazí se ve vizualizaci Power BI.

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
> * Nastavení základních prostředků pomocí příkazového řádku Azure CLI nebo PowerShell – centrum IoT, účet úložiště, fronta Service Bus a simulované zařízení.
> * Konfigurace koncových bodů a trasy v centru IoT pro účet úložiště a frontu Service Bus.
> * Vytvoření aplikace logiky, která se aktivuje a odešle e-mail, kdykoli se ve frontě Service Bus objeví nová zpráva.
> * Stažení a spuštění aplikaci, která bude simulovat IoT zařízení odesílající zprávy do centra s různými možnosti směrování.
> * Vytvoření vizualizace Power BI pro data odesílaná výchozím koncovým bodem.
> * Zobrazení výsledků...
> * ...ve frontě Service Bus a v e-mailech.
> * ...v účtu úložiště.
> * ...ve vizualizaci Power BI.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

- Nainstalovat sadu [Visual Studio](https://www.visualstudio.com/). 

- Účet Power BI pro analýzu streamu výchozího koncového bodu. ([Vyzkoušejte službu Power BI zdarma](https://app.powerbi.com/signupredirect?pbi_source=web).)

- Účet Office 365 pro odesílání e-mailových oznámení. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Příprava prostředků

V tomto kurzu budete potřebovat IoT centrum, účet úložiště a frontu Service Bus. Tyto prostředky můžete vytvořit pomocí Azure CLI nebo Azure PowerShellu. Použijte pro všechny prostředky stejnou skupinu a umístění. Na konci můžete odebrat vše v jednom kroku odstraněním skupiny prostředků.

V následujících částech najdete podrobnější popis požadovaných kroků. Postupujte podle pokynů pro rozhraní příkazového řádku *nebo* PowerShell.

1. Vytvořte [skupinu prostředků](../azure-resource-manager/resource-group-overview.md). 

2. Vytvořte centrum IoT ve vrstvě S1. Přidejte do centra IoT skupinu uživatelů. Skupinu uživatelů používá služba Azure Stream Analytics při získávání dat.

3. Vytvořte standardní účet úložiště V1 s replikací Standard_LRS.

4. Vytvořte oboru názvů a frontu Service Bus. 

5. Vytvořte identitu zařízení pro simulované zařízení, které bude odesílat zprávy do vašeho centra. Uložte klíč pro fázi testování.

### <a name="set-up-your-resources-using-azure-cli"></a>Nastavení prostředků pomocí Azure CLI

Zkopírujte tento skript a vložte ho do služby Cloud Shell. Za předpokladu, že jste již přihlášeni, se skript provede jeden řádek po druhém. 

K proměnným, které musí být globálně jedinečné, je zřetězená hodnota `$RANDOM`. Při spuštění skriptu a nastavení proměnných se vygeneruje náhodný číselný řetězec a zřetězí se ke konci pevného řetězce. Tím se zajistí jeho jedinečnost.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostorage$RANDOM
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
echo "$storageAccountKey"

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off 

# The Service Bus namespace must be globally unique, so add a random number to the end.
sbNameSpace=ContosoSBNamespace$RANDOM
echo "Service Bus namespace = " $sbNameSpace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNameSpace \
    --location $location
    
# The Service Bus queue name must be globally unique, so add a random number to the end.
sbQueueName=ContosoSBQueue$RANDOM
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNameSpace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

### <a name="set-up-your-resources-using-azure-powershell"></a>Nastavení prostředků pomocí Azure PowerShellu

Zkopírujte tento skript a vložte ho do služby Cloud Shell. Za předpokladu, že jste již přihlášeni, se skript provede jeden řádek po druhém.

K proměnným, které musí být globálně jedinečné, je zřetězená hodnota `$(Get-Random)`. Při spuštění skriptu a nastavení proměnných se vygeneruje náhodný číselný řetězec a zřetězí se ke konci pevného řetězce. Tím se zajistí jeho jedinečnost.

```azurepowershell-interactive
# Log into Azure account.
Login-AzureRMAccount

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"
$iotDeviceName = "Contoso-Test-Device"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, so add a random number to the end.
$iotHubName = "ContosoTestHub$(Get-Random)"
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzureRmIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzureRmIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random number to the end.
$storageAccountName = "contosostorage$(Get-Random)"
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
$storageContext = $storageAccount.Context 

# Create the container in the storage account.
New-AzureStorageContainer -Name $containerName `
    -Context $storageContext

# The Service Bus namespace must be globally unique,
#   so add a random number to the end.
$serviceBusNamespace = "ContosoSBNamespace$(Get-Random)"
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzureRmServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random number to the end.
$serviceBusQueueName  = "ContosoSBQueue$(Get-Random)"
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzureRmServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName 

```

V dalším kroku vytvořte identitu zařízení a uložte její klíč pro pozdější použití. Tuto identitu zařízení používá simulační aplikace k odesílání zpráv do centra IoT. Tato funkce není v prostředí PowerShell dostupná, ale můžete zařízení vytvořit na webu [Azure Portal](https://portal.azure.com).

1. Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se ke svému účtu Azure.

2. Klikněte na **Skupinu prostředků** a vyberte skupinu prostředků. Tento kurz používá **ContosoResources**.

3. V seznamu prostředků klikněte na své centrum IoT. Tento kurz používá **ContosoTestHub**. Vyberte v podokně Hub volbu **Zařízení IoT**.

4. Klikněte na tlačítko **+ Přidat**. V podokně Přidat zařízení zadejte ID zařízení. Tento kurz používá **Contoso-Test-Device**. Ponechte klíče prázdné a zaškrtněte **Automaticky vygenerovat klíče**. Zkontrolujte, že je povoleno **Připojit zařízení k centru IoT**. Klikněte na **Uložit**.

   ![Snímek obrazovky přidání zařízení.](./media/tutorial-routing/add-device.png)

5. Když teď máte zařízení vytvořené, klikněte na ně a podívejte se na vygenerované klíče. Klikněte na ikonu zkopírování u primárního klíče a uložte si ho pro testovací fázi tohoto kurzu, například do Poznámkového bloku.

   ![Snímek obrazovky podrobností o zařízení včetně klíčů.](./media/tutorial-routing/device-details.png)

## <a name="set-up-message-routing"></a>Nastavení směrování zpráv

Potřebujete směrovat zprávy do různých prostředků na základě vlastností, které ke zprávě připojilo simulované zařízení. Zprávy, které nejsou směrovány podle vlastních pravidel, se posílají na výchozí koncový bod (zprávy/události). 

|hodnota |Výsledek|
|------|------|
|level="storage" |Zapsat do úložiště Azure Storage.|
|level="critical" |Zapsat do fronty Service Bus. Aplikace logiky načte zprávu z fronty a pomocí Office 365 ji odešle e-mailem.|
|default |Zobrazte tato data pomocí Power BI.|

### <a name="routing-to-a-storage-account"></a>Směrování do účtu úložiště 

Nyní nastavte směrování pro účet úložiště. Přejděte do podokna Směrování zpráv a přidejte trasu. Při přidávání trasy pro ni definujte nový koncový bod. Po tomto nastavení se zprávy s vlastností **level** nastavenou na **storage** budou do účtu úložiště zapisovat automaticky.

1. Na webu [Azure Portal](https://portal.azure.com) klikněte na **Skupiny prostředků** a vyberte vaši skupinu prostředků. Tento kurz používá **ContosoResources**. 

2. V seznamu prostředků klikněte na centrum IoT. Tento kurz používá **ContosoTestHub**. 

3. Klikněte na **Směrování zpráv**. V podokně **Směrování zpráv** klikněte na **+ Přidat**. V podokně **Přidat trasu** klikněte na **+ Přidat** vedle pole Koncový bod, jak je znázorněno na následujícím obrázku:

   ![Snímek obrazovky ukazující, jak začít přidávat koncový bod do trasy](./media/tutorial-routing/message-routing-add-a-route-w-storage-ep.png)

4. Vyberte **Úložiště objektů blob**. Zobrazí se podokno **Přidat koncový bod úložiště**. 

   ![Snímek obrazovky zobrazující přidání koncového bodu.](./media/tutorial-routing/message-routing-add-storage-ep.png)

5. Zadejte název koncového bodu. Tento kurz používá **StorageContainer**.

6. Klikněte na **Vybrat kontejner**. Tím přejdete na seznam vašich účtů úložiště. Vyberte účet, který jste nastavili v rámci přípravy. Tento kurz používá **contosostorage**. Zobrazí se seznam kontejnerů v daném účtu úložiště. Vyberte kontejner, který jste nastavili v rámci přípravy. Tento kurz používá **contosoresults**. Klikněte na **Vybrat**. Vrátíte se do podokna **Přidat koncový bod**. 

7. Pro zbývající pole použijte výchozí hodnoty. Kliknutím na **Vytvořit** vytvořte koncový bod úložiště a přidejte ho do trasy. Vrátíte se do podokna **Přidat trasu**.

8.  Teď vyplňte zbývající informace o dotazu směrování. Tento dotaz určuje kritéria pro odesílání zpráv do kontejneru úložiště, který jste právě přidali jako koncový bod. Vyplňte pole na obrazovce. 

   **Název:** Zadejte název dotazu směrování. Tento kurz používá **StorageRoute**.

   **Koncový bod:** Zobrazí se koncový bod, který jste právě nastavili. 
   
   **Zdroj dat:** Vyberte z rozevíracího seznamu **Telemetrické zprávy zařízení**.

   **Povolit trasu:** Ujistěte se, že je tato možnost povolená.
   
   **Dotaz směrování:** Jako řetězec dotazu zadejte `level="storage"`. 

   ![Snímek obrazovky ukazující vytváření dotazu směrování pro účet úložiště](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  
   
   Klikněte na **Uložit**. Po dokončení se vrátíte do podokna Směrování zpráv, kde se zobrazí váš nový dotaz směrování pro úložiště. Zavřete podokno Trasy a vraťte se na stránku Skupina prostředků.

### <a name="routing-to-a-service-bus-queue"></a>Směrování do fronty Service Bus 

Nyní nastavte směrování pro frontu Service Bus. Přejděte do podokna Směrování zpráv a přidejte trasu. Při přidávání trasy pro ni definujte nový koncový bod. Po tomto nastavení se zprávy s vlastností **level** nastavenou na **critical** budou zapisovat do fronty Service Bus, která aktivuje aplikaci logiky, která pak odešle e-mail s informacemi. 

1. Na stránce Skupina prostředků klikněte na své centrum IoT a pak na **Směrování zpráv**. 

2. V podokně **Směrování zpráv** klikněte na **+ Přidat**. 

3. V podokně **Přidat trasu** klikněte na **+ Přidat** vedle pole Koncový bod. Vyberte **Fronta služby Service Bus**. Zobrazí se podokno **Přidat koncový bod služby Service Bus**. 

   ![Snímek obrazovky ukazující přidání koncového bodu služby Service Bus](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Vyplňte jednotlivá pole:

   **Název koncového bodu:** Zadejte název koncového bodu. Tento kurz používá **CriticalQueue**.
   
   **Obor názvů služby Service Bus:** Kliknutím na toto pole zobrazíte rozevírací seznam. Vyberte obor názvů služby Service Bus, který jste nastavili v rámci přípravy. Tento kurz používá **ContosoSBNamespace**.

   **Fronta služby Service Bus:** Kliknutím na toto pole zobrazíte rozevírací seznam. V rozevíracím seznamu vyberte frontu služby Service Bus. Tento kurz používá **contososbqueue**.

5. Kliknutím na **Vytvořit** přidejte koncový bod fronty služby Service Bus. Vrátíte se do podokna **Přidat trasu**. 

6.  Teď vyplníte zbývající informace o dotazu směrování. Tento dotaz určuje kritéria pro odesílání zpráv do fronty služby Service Bus, kterou jste právě přidali jako koncový bod. Vyplňte pole na obrazovce. 

   **Název:** Zadejte název dotazu směrování. Tento kurz používá **SBQueueRoute**. 

   **Koncový bod:** Zobrazí se koncový bod, který jste právě nastavili.

   **Zdroj dat:** Vyberte z rozevíracího seznamu **Telemetrické zprávy zařízení**.

   **Dotaz směrování:** Jako řetězec dotazu zadejte `level="critical"`. 

   ![Snímek obrazovky ukazující vytváření dotazu směrování pro frontu služby Service Bus](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Klikněte na **Uložit**. Jakmile se vrátíte do podokna Trasy, zobrazí se obě nové trasy, jak je vidět tady.

   ![Snímek obrazovky s nově vytvořenými trasami.](./media/tutorial-routing/message-routing-show-both-routes.png)

8. Vlastní koncové body, které jste nastavili, můžete zobrazit kliknutím na kartu **Vlastní koncové body**.

   ![Snímek obrazovky ukazující právě nastavené vlastní koncové body](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Zavřete podokno Směrování zpráv a vraťte se do podokna Skupina prostředků.

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky  

Fronta Service Bus se použije pro příjem zpráv označených jako kritické. Dále vytvořte aplikaci logiky pro monitorování fronty Service Bus, která odešle e-mail, kdykoli se ve frontě objeví nová zpráva. 

1. Na webu [Azure Portal](https://portal.azure.com) klikněte na **+ Vytvořit prostředek**. Do vyhledávacího pole zadejte **aplikace logiky** a stiskněte Enter. Ve výsledcích hledání vyberte Aplikaci logiky a pokračujte kliknutím na **Vytvořit** do podokna **Vytvořit aplikaci logiky**. Vyplňte jednotlivá pole. 

   **Název:** V tomto poli je název aplikace logiky. Tento kurz používá **ContosoLogicApp**. 

   **Předplatné**: Vyberte své předplatné Azure.

   **Skupina prostředků:** Klikněte na **Použít existující** a vyberte vaši skupinu prostředků. Tento kurz používá **ContosoResources**. 

   **Umístění:** Použijte vaše umístění. Tento kurz používá **Západ USA**. 

   **Log Analytics:** Tento přepínač by měl být vypnutý. 

   ![Snímek obrazovky znázorňující vytvoření aplikace logiky.](./media/tutorial-routing/create-logic-app.png)

   Klikněte na možnost **Vytvořit**.

1. Teď přejděte do aplikace logiky. Nejjednodušší způsob, jak se dostat do aplikace logiky, je kliknout na **Skupiny prostředků**, vybrat vaši skupinu prostředků (tento kurz používá **ContosoResources**) a vybrat ze seznamu prostředků aplikaci logiky. Zobrazí se stránka návrháře aplikace logiky (možná se budete muset kvůli zobrazení celé stránky posunout doprava). Na stránce návrháře aplikace logiky přejděte dolů až k dlaždici **Prázdná aplikace logiky +** a klikněte na ni. 

1. Zobrazí se seznam konektorů. Vyberte **Service Bus**. 

   ![Snímek obrazovky zobrazující seznam konektorů.](./media/tutorial-routing/logic-app-connectors.png)

1. Zobrazí se seznam aktivačních událostí. Vyberte **Service Bus – Při doručení zprávy do fronty (automatické dokončení)**. 

   ![Snímek obrazovky se seznamem aktivačních událostí pro Service Bus.](./media/tutorial-routing/logic-app-triggers.png)

1. Na další obrazovce zadejte Název připojení. Tento kurz používá **ContosoConnection**. 

   ![Snímek obrazovky vytváření připojení pro frontu Service Bus.](./media/tutorial-routing/logic-app-define-connection.png)

   Klikněte na obor názvů služby Service Bus. Tento kurz používá **ContosoSBNamespace**. Když vyberete obor názvů, portál se dotáže oboru názvů služby Service Bus a načte klíče. Vyberte **RootManageSharedAccessKey** a klikněte na **Vytvořit**. 
   
   ![Snímek obrazovky zobrazující dokončení nastavení připojení.](./media/tutorial-routing/logic-app-finish-connection.png)

1. Na další obrazovce vyberte z rozevíracího seznamu název fronty (tento kurz používá **contososbqueue**). Pro zbývající pole můžete použít výchozí hodnoty. 

   ![Snímek obrazovky s možnostmi fronty.](./media/tutorial-routing/logic-app-queue-options.png)

1. Nyní nastavte akci odeslání e-mailu při přijetí nové zprávy do fronty. V Návrháři aplikace logiky klikněte na **+ Nový krok** a pak na **Přidat akci**. V podokně **Vybrat akci** vyhledejte a klikněte na **Office 365 Outlook**. Na obrazovce aktivačních událostí vyberte **Office 365 Outlook – Odeslat e-mail**.  

   ![Snímek obrazovky s možnostmi Office 365.](./media/tutorial-routing/logic-app-select-outlook.png)

1. Pak se přihlaste k účtu Office 365, aby se vytvořilo připojení. Zadejte e-mailové adresy příjemců e-mailu. Také zadejte předmět a zprávu, kterou chcete mít v těle zprávy. Pro testování zadejte jako adresu příjemce vlastní e-mail.

   Kliknutím na tlačítko **Přidat dynamický obsah** zobrazíte obsah, který můžete do zprávy zahrnout. Vyberte **Obsah** – příchozí zpráva bude zahrnuta do e-mailu. 

   ![Snímek obrazovky znázorňující možnosti e-mailu v aplikaci logiky.](./media/tutorial-routing/logic-app-send-email.png)

1. Klikněte na **Uložit**. Pak návrháře aplikace logiky zavřete.

## <a name="set-up-azure-stream-analytics"></a>Nastavení služby Azure Stream Analytics

Pokud chcete zobrazit data ve vizualizaci Power BI, nejprve vytvořte úlohu Stream Analytics pro načítání dat. Mějte na paměti, že pouze zprávy, jejichž vlastnost **level** je **normal**, se odesílají výchozímu koncovému bodu a budou úlohou Stream Analytics vizualizované v PowerBI.

### <a name="create-the-stream-analytics-job"></a>Vytvoření úlohy služby Stream Analytics

1. Na webu [Azure Portal](https://portal.azure.com) klikněte na **Vytvořit prostředek** > **Internet věcí** > **Úloha Stream Analytics**.

1. Zadejte o úloze následující informace.

   **Název úlohy:** Název, který chcete úloze dát. Název musí být globálně jedinečný. Tento kurz používá **contosoJob**.

   **Skupina prostředků:** Použijte stejnou skupinu prostředků jako pro centrum IoT. Tento kurz používá **ContosoResources**. 

   **Umístění:** Použijte stejné umístění, které používáte v instalačním skriptu. Tento kurz používá **Západ USA**. 

   ![Snímek obrazovky předvádějící vytvoření úlohy Stream Analytics.](./media/tutorial-routing/stream-analytics-create-job.png)

1. Vytvořte úlohu kliknutím na **Vytvořit**. Zpět do úlohy se vrátíte kliknutím na **Skupiny prostředků**. Tento kurz používá **ContosoResources**. Vyberte skupinu prostředků a potom v seznamu prostředků klikněte na úlohu Stream Analytics. 

### <a name="add-an-input-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. V části **Topologie úlohy** klikněte na **Vstupy**.

1. V podokně **Vstupy** klikněte na **Přidat vstup streamu** a vyberte IoT Hub. Na další obrazovce vyplňte následující pole:

   **Alias pro vstup:** Tento kurz používá **contosoinputs**.

   **Předplatné:** Vyberte vaše předplatné.

   **IoT Hub:** Vyberte IoT Hub. Tento kurz používá **ContosoTestHub**.

   **Koncový bod:** vyberte **Zasílání zpráv**. (Pokud vyberete monitorování operací, získáte namísto procházejících dat telemetrická data o centru IoT.) 

   **Název zásad sdíleného přístupu:** Vyberte **iothubowner**. Portál vyplní Klíč zásad sdíleného přístupu za vás.

   **Skupina prostředků:** Vyberte skupinu prostředků, kterou jste vytvořili dříve. Tento kurz používá **contosoconsumers**.
   
   Pro zbývající pole můžete použít výchozí hodnoty. 

   ![Snímek obrazovky předvádějící nastavení vstupů úlohy Stream Analytics.](./media/tutorial-routing/stream-analytics-job-inputs.png)

1. Klikněte na **Uložit**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. V části **Topologie úlohy** klikněte na **Výstupy**.

1. V podokně **Výstupy** klikněte na **Přidat** a potom vyberte **Power BI**. Na další obrazovce vyplňte následující pole:

   **Alias pro výstup:** Jedinečný alias pro výstup. Tento kurz používá **contosooutputs**. 

   **Název datové sady:** Název datové sady, kterou chcete v Power BI použít. Tento kurz používá **contosodataset**. 

   **Název tabulky:** Název tabulky, kterou chcete v Power BI použít. Tento kurz používá **contosotable**.

   Pro zbývající pole můžete použít výchozí hodnoty.

1. Klikněte na **Autorizovat** a přihlaste se do účtu Power BI.

   ![Snímek obrazovky předvádějící nastavení výstupů úlohy Stream Analytics.](./media/tutorial-routing/stream-analytics-job-outputs.png)

1. Klikněte na **Uložit**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurace dotazu pro úlohu Stream Analytics

1. V části **Topologie úlohy** klikněte na **Dotaz**.

1. Nahraďte `[YourInputAlias]` názvem aliasu pro vstup úlohy. Tento kurz používá **contosoinputs**.

1. Nahraďte `[YourOutputAlias]` názvem aliasu pro výstup. Tento kurz používá **contosooutputs**.

   ![Snímek obrazovky předvádějící nastavení dotazu pro úlohu Stream Analytics.](./media/tutorial-routing/stream-analytics-job-query.png)

1. Klikněte na **Uložit**.

1. Zavřete podokno dotazu. Tím se vrátíte k zobrazení prostředků ve Skupině prostředků. Klikněte na úlohu Stream Analytics. V tomto kurzu má název **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

V úloze Stream Analytics klikněte na **Spustit** > **Nyní** > **Spustit**. Jakmile se úloha úspěšně spustí, stav úlohy se změní ze **Zastaveno** na **Spuštěno**.

K vytvoření sestavy Power BI potřebujete data, takže Power BI je potřeba po vytvoření zařízení a spuštění aplikace simulace zařízení nastavit.

## <a name="run-simulated-device-app"></a>Spuštění aplikace simulovaného zařízení

Dříve v části nastavení skriptu jste vytvořili zařízení pro simulaci pomocí zařízení IoT. V této části si stáhnete konzolovou aplikaci .NET, která simuluje zařízení odesílající zprávy typu zařízení-cloud do centra IoT. Tato aplikace odesílá zprávy pro každou z různých metod směrování. 

Stažení řešení pro [Simulaci zařízení IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Tím stáhnete úložiště s několika aplikacemi. Řešení, které hledáte, je ve složce iot-hub/Tutorials/Routing/SimulatedDevice/.

Dvojím kliknutím na soubor řešení (SimulatedDevice.sln) otevřete kód v sadě Visual Studio a otevřete Program.cs. Nahraďte `{iot hub hostname}` názvem hostitele centra IoT. Formát názvu hostitele centra IoT je **{iot-hub-name}.azure-devices.net**. V tomto kurzu je název hostitele centra **ContosoTestHub.azure-devices.net**. Dále nahraďte `{device key}` klíčem zařízení, který jste si předtím uložili při vytváření simulovaného zařízení. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Spuštění a testování 

Spusťte konzolovou aplikaci. Počkejte několik minut. Na obrazovce konzoly aplikace uvidíte odesílané zprávy.

Aplikace odesílá zprávy typu zařízení-cloud do centra IoT každou sekundu. Zpráva obsahuje serializovaný objekt JSON s ID zařízení, teplotou, vlhkostí a úrovní zprávy, jejíž výchozí hodnota je `normal`. Úrovni zprávy se náhodně přiřazuje `critical` nebo `storage`, což způsobí směrování zprávy do účtu úložiště nebo do fronty Service Bus (což aktivuje aplikaci logiky, která odešle e-mail). Zprávy s výchozí hodnotou `normal` se zobrazí v sestavě BI, kterou připravíte v dalším kroku.

Pokud je všechno správně nastavené, v tomto okamžiku byste měli získat následující výsledky:

1. Začnete dostávat e-maily o kritických zprávách. 

   ![Snímek obrazovky s výslednými e-maily.](./media/tutorial-routing/results-in-email.png)

   To znamená následující:

   * Směrování do fronty Service Bus pracuje správně.
   * Aplikace logiky přebírající zprávy z fronty Service Bus pracuje správně.
   * Konektor aplikace logiky do Outlooku pracuje správně. 

1. Na webu [Azure Portal](https://portal.azure.com) klikněte na **Skupiny prostředků** a vyberte vaši skupinu prostředků. Tento kurz používá **ContosoResources**. Vyberte účet úložiště, klikněte na tlačítko **Objekty Blob** a pak vyberte kontejner. Tento kurz používá **contosoresults**. Měli byste vidět složku, ve které můžete procházet adresáře, dokud neuvidíte jeden nebo několik souborů. Otevřete jeden z těchto souborů; obsahují položky směrované do účtu úložiště. 

   ![Snímek obrazovky s výslednými soubory v úložišti.](./media/tutorial-routing/results-in-storage.png)

To znamená následující:

   * Směrování do účtu úložiště pracuje správně.

Nyní se stále běžící aplikací vytvořte vizualizaci Power BI pro zprávy přicházející přes výchozí směrování. 

## <a name="set-up-the-power-bi-visualizations"></a>Nastavení vizualizací Power BI

1. Přihlaste se ke svému účtu [Power BI](https://powerbi.microsoft.com/).

1. Přejděte na **Pracovní prostory** a vyberte pracovní prostor, který jste nastavili při vytváření výstupu pro úlohu služby Stream Analytics. Tento kurz používá **My Workspace**. 

1. Klikněte na **Datové sady**.

   Měli byste vidět datovou sadu určenou při vytváření výstupu pro úlohu služby Stream Analytics. Tento kurz používá **contosodataset**. (Zobrazení datové sady může na začátku 5 až 10 minut trvat.)

1. V části **AKCE** kliknutím na první ikonu vytvořte sestavu.

   ![Snímek obrazovky pracovního prostoru Power BI se zvýrazněnou položkou Akce a ikonou sestavy.](./media/tutorial-routing/power-bi-actions.png)

1. Vytvořte spojnicový graf zobrazující v reálném čase vývoj teploty.

   a. Na stránce pro vytvoření sestavy přidejte graf kliknutím na ikonu spojnicového grafu.

   ![Snímek obrazovky s vizualizacemi a poli.](./media/tutorial-routing/power-bi-visualizations-and-fields.png)

   b. V podokně **Pole** rozbalte tabulku, kterou jste určili při vytváření výstupu pro úlohu služby Stream Analytics. Tento kurz používá **contosotable**.

   c. Přetáhněte **EventEnqueuedUtcTime** na **Osu** v podokně **Vizualizace**.

   d. Přetáhněte položku **temperature** na **Hodnoty**.

   Vytvoří spojnicový graf. Na ose x bude datum a čas v časovém pásmu UTC. Na ose y bude hodnota snímače teploty.

1. Vytvořte jiný spojnicový graf zobrazující v reálném čase vývoj vlhkosti. Při vytváření druhého grafu postupujte stejně a přetáhněte **EventEnqueuedUtcTime** na osu x a **humidity** na osu y.

   ![Snímek obrazovky předvádějící konečnou sestavu Power BI se dvěma grafy.](./media/tutorial-routing/power-bi-report.png)

1. Kliknutím na **Uložit** sestavu uložte.

Nyní byste měli vidět příchozí data v obou grafech. To znamená následující:

   * Směrování do výchozího koncového bodu pracuje správně.
   * Úloha Azure Stream Analytics správně streamuje.
   * Vizualizace Power BI je nastavená správně.

Obnovením grafů zobrazíte nejnovější data. Stačí kliknout na tlačítko Aktualizovat nahoře v okně Power BI. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud chcete odebrat všechny prostředky, které jste vytvořili, odstraňte skupinu prostředků. Tato akce odstraní všechny prostředky, které skupina obsahuje. V tomto případě se odebere centrum IoT, obor názvů a fronta Service Bus, aplikace logiky, účet úložiště i samotná skupina prostředků. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Vyčištění prostředků ve vizualizaci Power BI

Přihlaste se ke svému účtu [Power BI](https://powerbi.microsoft.com/). Přejděte do svého pracovního prostoru. Tento kurz používá **My Workspace**. Vizualizaci Power BI odeberete tak, že přejdete do datových sad a kliknutím na ikonu koše datovou sadu odstraníte. Tento kurz používá **contosodataset**. Pokud datovou sadu odeberete, odebere se také sestava.

### <a name="clean-up-resources-using-azure-cli"></a>Vyčištění prostředků pomocí Azure CLI

Chcete-li odebrat skupinu prostředků, použijte příkaz [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```
### <a name="clean-up-resources-using-powershell"></a>Vyčištění prostředků pomocí PowerShell

K odebrání skupiny prostředků použijte příkaz [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/remove-azurermresourcegroup). Proměnná $resourceGroup byla nastavena na **ContosoIoTRG1** na začátku tohoto kurzu.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $resourceGroup
```


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak používat směrování zpráv služby IoT Hub na různé cíle provedením následujících úloh.  

> [!div class="checklist"]
> * Nastavení základních prostředků pomocí příkazového řádku Azure CLI nebo PowerShell – centrum IoT, účet úložiště, fronta Service Bus a simulované zařízení.
> * Konfigurace koncových bodů a trasy v centru IoT pro účet úložiště a frontu Service Bus.
> * Vytvoření aplikace logiky, která se aktivuje a odešle e-mail, kdykoli se ve frontě Service Bus objeví nová zpráva.
> * Stažení a spuštění aplikaci, která bude simulovat IoT zařízení odesílající zprávy do centra s různými možnosti směrování.
> * Vytvoření vizualizace Power BI pro data odesílaná výchozím koncovým bodem.
> * Zobrazení výsledků...
> * ...ve frontě Service Bus a v e-mailech.
> * ...v účtu úložiště.
> * ...ve vizualizaci Power BI.

V dalším kurzu se dozvíte, jak spravovat stav zařízení IoT. 

> [!div class="nextstepaction"]
[Konfigurace zařízení z back-endové služby](tutorial-device-twins.md)

 <!--  [Manage the state of a device](./tutorial-manage-state.md) -->
