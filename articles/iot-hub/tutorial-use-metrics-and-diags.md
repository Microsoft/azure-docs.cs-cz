---
title: Nastavení a použití metrik a diagnostických protokolů pomocí služby Azure IoT hub | Dokumentace Microsoftu
description: Nastavení a použití metrik a diagnostických protokolů pomocí služby Azure IoT hub
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 12/15/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 8bcc72cf151b085c7f65b6c600a49642cd330bac
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54248826"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>Kurz: Nastavení a použití metrik a diagnostických protokolů pomocí služby IoT hub

Pokud máte řešení služby IoT Hub běžících v produkčním prostředí, budete chtít nastavit několik metrik a povolení diagnostických protokolů. Pokud dojde k potížím, máte podívat se na data, která vám pomůže diagnostikovat potíže a opravte rychleji. V tomto článku uvidíte, jak povolit diagnostické protokoly a jak je zkontrolován výskyt chyb. Budete taky nastavit některé metriky pro sledování a výstrah, které se aktivuje při určité hranice přístupů metriky. Můžete například mít e-mailu dostali, když počet telemetrických zpráv odeslaných překračuje hranici konkrétní, nebo když počet zpráv používá blíží kvótu zpráv za den povolených pro službu IoT Hub. 

Případ použití příkladu jsou čerpací stanici čerpadla, že zařízení IoT, které odesílají komunikovat s centrem IoT. Platební karty se ověří a finální transakce je zapsána do úložiště dat. Pokud je na zařízeních IoT zastavit připojení k centru a odesílání zpráv, je mnohem více obtížně opravit, pokud máte žádné přehled o co se děje.

Tento kurz používá Azure ukázku z [IoT Hub směrování](tutorial-routing.md) pro odesílání zpráv do služby IoT hub.

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
> * Pomocí Azure CLI, vytvoření služby IoT hub, Simulovaná zařízení a účet úložiště.  
> * Povolení diagnostických protokolů.
> * Zapnutí metrik.
> * Nastavení upozornění pro tyto metriky. 
> * Stáhněte a spusťte aplikaci, která simuluje zařízení odesílá zprávy do centra IoT. 
> * Tuto aplikaci spusťte, dokud výstrahy začnou vyvolat. 
> * Zobrazení výsledků metrik a diagnostických protokolech. 

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

- Nainstalovat sadu [Visual Studio](https://www.visualstudio.com/). 

- E-mailový účet, schopná přijmout e-mailu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Příprava prostředků

Pro účely tohoto kurzu budete potřebovat službu IoT hub, účet úložiště a Simulovaná zařízení IoT. Tyto prostředky můžete vytvořit pomocí Azure CLI nebo Azure PowerShellu. Použijte pro všechny prostředky stejnou skupinu a umístění. Na konci můžete odebrat vše v jednom kroku odstraněním skupiny prostředků.

Jedná se o požadované kroky.

1. Vytvořte [skupinu prostředků](../azure-resource-manager/resource-group-overview.md). 

2. Vytvoření služby IoT hub.

3. Vytvořte standardní účet úložiště V1 s replikací Standard_LRS.

4. Vytvořte identitu zařízení pro simulované zařízení, které bude odesílat zprávy do vašeho centra. Uložte klíč pro fázi testování.

### <a name="set-up-resources-using-azure-cli"></a>Nastavení prostředků pomocí Azure CLI

Zkopírujte tento skript a vložte ho do služby Cloud Shell. Za předpokladu, že jste již přihlášeni, se skript provede jeden řádek po druhém. Ve skupině prostředků ContosoResources se vytvoří nové prostředky.

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
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku F1 --location $location

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostoragemon$RANDOM
echo "Storage account name = " $storageAccountName

# Create the storage account.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName 

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>Při vytvoření identity zařízení, může se zobrazit následující chyba: *Pro zásady iothubowner ContosoTestHub centra IoT se nenašly žádné klíče*. Pokud chcete tuto chybu opravit, aktualizujte rozšíření IoT Azure CLI a potom spusťte poslední dva příkazy ve skriptu znovu. 
>
>Tady je příkaz k aktualizaci rozšíření. Spusťte v službě Cloud Shell.
>
>```cli
>az extension update --name azure-cli-iot-ext
>```

## <a name="enable-the-diagnostic-logs"></a>Povolení diagnostických protokolů 

[Diagnostické protokoly](../azure-monitor/platform/diagnostic-logs-overview.md) jsou ve výchozím nastavení zakázán, když vytvoříte novou službu IoT hub. V této části Povolení diagnostických protokolů pro vaše centrum.

1. Nejprve, pokud ještě nejste na centru na portálu, klikněte na tlačítko **skupiny prostředků** a klikněte na skupinu prostředků společnosti Contoso na prostředky. Vyberte ze seznamu prostředků zobrazí Centrum. 

2. Hledat **monitorování** části v okně služby IoT Hub. Klikněte na **Nastavení diagnostiky**. 

   ![Snímek obrazovky zobrazující část nastavení diagnostiky v okně služby IoT Hub.](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. Ujistěte se, že jsou správné předplatné a skupinu prostředků. V části **typ prostředku**, zrušte zaškrtnutí políčka **Vybrat vše**, vyhledání a kontrola **služby IoT Hub**. (Umístí na značku zaškrtnutí vedle *Vybrat vše* znovu, jednoduše ignorovat.) V části **prostředků**, vyberte název centra. Vaše obrazovka by měla vypadat jako na tomto obrázku: 

   ![Snímek obrazovky zobrazující část nastavení diagnostiky v okně služby IoT Hub.](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. Nyní klikejte na příkaz **zapnout diagnostiku**. Zobrazí se podokno nastavení diagnostiky. Zadejte název nastavení diagnostické protokoly jako "diagnostiku hub".

5. Zkontrolujte **archivovat do účtu úložiště**. 

   ![Snímek obrazovky zobrazující nastavení diagnostiky pro archivaci do účtu úložiště.](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    Klikněte na tlačítko **konfigurovat** zobrazíte **vyberte účet úložiště** vyberte ten správný (*contosostoragemon*) a klikněte na tlačítko **OK** do Vraťte se do podokna nastavení diagnostiky. 

   ![Snímek obrazovky zobrazující nastavení diagnostické protokoly pro archivaci do účtu úložiště.](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. V části **protokolu**, zkontrolujte **připojení** a **Telemetrii zařízení**a nastavte **uchování (dny)** do 7 dní pro každou. Vaše nastavení diagnostiky. obrazovka by měla vypadat jako na tomto obrázku:

   ![Snímek obrazovky zobrazující nastavení konečné diagnostického protokolu.](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. Kliknutím na **Uložit** nastavení uložte. Zavřete podokno nastavení diagnostiky.

Později když se podíváte na diagnostické protokoly, budete mít najdete v okně připojení a odpojení protokolování zařízení pro položku. 

## <a name="set-up-metrics"></a>Nastavení metriky 

Nyní nastavte některé metriky a sledujte pro odeslání zprávy do centra. 

1. V podokně nastavení pro službu IoT hub, klikněte na **metriky** možnost **monitorování** oddílu.

2. V horní části obrazovky klikněte na tlačítko **posledních 24 hodin (automaticky)**. V rozevíracím seznamu, který se zobrazí, vyberte **poslední 4 hodiny** pro **časový rozsah**a nastavte **časové intervaly** k **1 minuta**, místního času. Klikněte na tlačítko **použít** nastavení uložte. 

   ![Snímek obrazovky zobrazující nastavení času metriky.](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. Ve výchozím nastavení je jedna položka metriky. Opustit skupinu prostředků jako výchozí a metriky oboru názvů. V **metrika** rozevíracího seznamu vyberte **telemetrické zprávy odesílané**. Nastavte **agregace** k **součet**.

   ![Snímek obrazovky přidání metriky pro telemetrické zprávy odesílané.](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. Nyní klikejte na příkaz **přidat metriku** do grafu přidat další metriku. Vyberte skupinu prostředků (**ContosoTestHub**). V části **metrika**vyberte **celkový počet zpráv používá**. Pro **agregace**vyberte **Avg**. 

   Nyní na obrazovce zobrazí minimalizované metriku *telemetrické zprávy odesílané*, plus nové metriky pro *celkový počet zpráv používá*.

   ![Snímek obrazovky přidání metriky pro telemetrické zprávy odesílané.](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   Klikněte na tlačítko **připnout na řídicí panel**. To se ji připnout na řídicí panel webu Azure Portal tak budete mít znovu přístup. Pokud není ji připnout na řídicí panel, vaše nastavení se nezachovají.

## <a name="set-up-alerts"></a>Nastavení výstrah

Přejděte k centru na portálu. Klikněte na tlačítko **skupiny prostředků**vyberte *ContosoResources*, vyberte centrum IoT *ContosoTestHub*. 

Nebyla migrována do služby IoT Hub [metriky ve službě Azure Monitor](/azure/azure-monitor/platform/data-collection#metrics) ještě; je nutné použít [klasických upozornění](/azure/azure-monitor/platform/alerts-classic.overview).

1. V části **monitorování**, klikněte na tlačítko **výstrahy** to ukazuje na hlavní obrazovce oznámení. 

   ![Snímek obrazovky ukazující, jak najít upozornění classic.](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. Odsud se dostat do klasických upozornění, klikněte na tlačítko **zobrazit upozornění classic**. 

    ![Snímek obrazovky upozornění classic.](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    Vyplňte jednotlivá pole: 

    **Předplatné**: Ponechte toto pole nastavte na aktuálním předplatném.

    **Zdroj**: Nastavte pole na *metriky*.

    **Skupina prostředků**: Nastavte pole na aktuální skupinu prostředků, *ContosoResources*. 

    **Typ prostředku**: Nastavte toto pole do služby IoT Hub. 

    **Prostředek**: Vyberte své Centrum IoT *ContosoTestHub*.

3. Klikněte na tlačítko **přidat upozornění metriky (klasické)** nastavit nová výstraha.

    Vyplňte jednotlivá pole:

    **Název**: Zadejte název pro pravidlo výstrahy, jako třeba *telemetrických zpráv*.

    **Popis**: Zadejte popis výstrahy, jako třeba *pošle upozornění, když existuje 1000 telemetrické zprávy odesílané*. 

    **Zdroj**: Nastavte na *metriky*.

    **Předplatné**, **skupiny prostředků**, a **prostředků** musí být nastavena na hodnoty, které jste vybrali na **zobrazit upozornění classic** obrazovky. 

    Nastavte **metrika** k *telemetrické zprávy odesílané*.

    ![Snímek obrazovky zobrazující nastavení klasického upozornění pro telemetrické zprávy odesílané.](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. Po grafu nastavte následující pole:

   **Podmínka**: Nastavte na *větší než*.

   **Prahová hodnota**: Nastavena na hodnotu 1000.

   **Období**: Nastavte na *za posledních 5 minut*.

   **Příjemce e-mailová oznámení**: Sem zadejte e-mailovou adresu. 

   ![Snímek obrazovky s dolní polovinu obrazovky výstrahy.](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   Klikněte na tlačítko **OK** uložit výstrahu. 

5. Teď nastavte další výstrahu pro *celkový počet zpráv používá*. Tato metrika je užitečné, pokud chcete odeslat výstrahu, když počet zpráv, které používají, které se blíží kvótu pro službu IoT hub – s oznámením centra brzy začne odmítnutí zpráv.

   Na **zobrazit upozornění classic** obrazovce, klikněte na tlačítko **přidat upozornění metriky (klasické)**, potom vyplňte tato pole na **přidat pravidlo** podokně.

   **Název**: Zadejte název pro pravidlo výstrahy, jako třeba *číslo sady zpráv – používá*.

   **Popis**: Zadejte popis výstrahy, jako třeba *pošle upozornění, když přibližujete kvóty*.

   **Zdroj**: Nastavte pole na *metriky*.

    **Předplatné**, **skupiny prostředků**, a **prostředků** musí být nastavena na hodnoty, které jste vybrali na **zobrazit upozornění classic** obrazovky. 

    Nastavte **metrika** k *celkový počet zpráv používá*.

6. Pod grafem vyplňte následující pole:

   **Podmínka**: Nastavte na *větší než*.

   **Prahová hodnota**: Nastavena na hodnotu 1000.

   **Období**: Nastavte pole na *za posledních 5 minut*. 

   **Příjemce e-mailová oznámení**: Sem zadejte e-mailovou adresu. 

   Klikněte na tlačítko **OK** uložíte pravidlo. 

5. Teď byste měli vidět dvě výstrahy v podokně upozornění classic: 

   ![Snímek obrazovky znázorňující klasických upozornění obrazovka s novou pravidla upozornění.](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. Zavřete podokno oznámení. 
    
    S těmito nastaveními zobrazí se upozornění při odeslání počet zpráv, které je větší než 400 a pokud překročí celkový počet zpráv používá číslo.

## <a name="run-simulated-device-app"></a>Spuštění aplikace simulovaného zařízení

Dříve v části nastavení skriptu jste vytvořili zařízení pro simulaci pomocí zařízení IoT. V této části si stáhnete konzolovou aplikaci .NET, která simuluje zařízení odesílající zprávy typu zařízení-cloud do centra IoT.  

Stažení řešení pro [Simulaci zařízení IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Tento odkaz stáhne úložiště s několika aplikacemi v. řešení, které hledáte, je v iot-hub/kurzy/směrování/SimulatedDevice /.

Dvojím kliknutím na soubor řešení (SimulatedDevice.sln) otevřete kód v sadě Visual Studio a otevřete Program.cs. Nahraďte `{iot hub hostname}` názvem hostitele centra IoT. Formát názvu hostitele centra IoT je **{iot-hub-name}.azure-devices.net**. V tomto kurzu je název hostitele centra **ContosoTestHub.azure-devices.net**. Dále nahraďte `{device key}` klíčem zařízení, který jste si předtím uložili při vytváření simulovaného zařízení. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Spuštění a testování 

V souboru Program.cs změnit `Task.Delay` od 1 do 10 000, což snižuje množství času mezi odesíláním zpráv od 1 sekundy na.01 sekund. Zkrácení toto zpoždění zvyšuje počet odeslaných zpráv.

```csharp
await Task.Delay(10);
```

Spusťte konzolovou aplikaci. Počkejte několik minut (10 – 15). Zobrazí se zprávy odesílané ze simulovaného zařízení k centru na obrazovce aplikace konzoly.

### <a name="see-the-metrics-in-the-portal"></a>Zobrazení metrik na portálu

Otevřete metriky z řídicího panelu. Změnit časové hodnoty, abyste *posledních 30 minut* se časové intervaly z *1 minuta*. Zobrazuje telemetrické zprávy odeslané a celkový počet zpráv používaných v grafu s poslední číslice v dolní části grafu. 

   ![Snímek obrazovky zobrazující metriky.](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>Zobrazit výstrahy

Přejděte zpět na výstrahy. Klikněte na tlačítko **skupiny prostředků**vyberte *ContosoResources*, pak vyberte centrum *ContosoTestHub*. Na stránce Vlastnosti zobrazí rozbočovače, vyberte **výstrahy**, pak **zobrazit upozornění classic**. 

Počet zpráv odeslaných překročí limit, spustíte, získávání e-mailová oznámení. Pokud chcete zobrazit, pokud jsou všechny aktivní výstrahy, přejděte na rozbočovače a vyberte **výstrahy**. Zobrazí se výstrahy, které jsou aktivní, a pokud jsou všechna upozornění. 

   ![Snímek obrazovky zobrazující výstrahy mají aktivována.](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

Klikněte na výstrahu pro telemetrické zprávy. Zobrazuje metriky výsledek a graf s výsledky. E-mail odeslaný s upozorněním na aktivaci výstrahy navíc vypadá jako na tomto obrázku:

   ![Snímek obrazovky ukazující, že máte vyvolané výstrahy e-mailu.](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>Zobrazit diagnostické protokoly

Nastavení diagnostické protokoly nelze exportovat do úložiště objektů blob. Přejděte do vaší skupiny prostředků a vyberte svůj účet úložiště *contosostoragemon*. Vyberte objekty BLOB a pak otevřete kontejner *přehledy. protokoly připojení*. Procházet hierarchii, dokud se nedostanete na aktuální datum a vyberte nejnovější soubor. 

   ![Snímek obrazovky procházení k podrobnostem kontejner úložiště, který chcete zobrazit diagnostické protokoly.](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

Klikněte na tlačítko **Stáhnout** stáhněte a otevřete ho. Zobrazí protokoly zařízení, připojení a odpojení, protože odesílá zprávy do centra. Ukázka tady:

``` json
{ 
  "time": "2018-12-17T18:11:25Z", 
  "resourceId": 
    "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
  "operationName": "deviceConnect", 
  "category": "Connections", 
  "level": "Information", 
  "properties": 
      {"deviceId":"Contoso-Test-Device",
       "protocol":"Mqtt",
       "authType":null,
       "maskedIpAddress":"73.162.215.XXX",
       "statusCode":null
       }, 
  "location": "westus"
}
{ 
   "time": "2018-12-17T18:19:25Z", 
   "resourceId": 
     "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
    "operationName": "deviceDisconnect", 
    "category": "Connections", 
    "level": "Error", 
    "resultType": "404104", 
    "resultDescription": "DeviceConnectionClosedRemotely", 
    "properties": 
        {"deviceId":"Contoso-Test-Device",
         "protocol":"Mqtt",
         "authType":null,
         "maskedIpAddress":"73.162.215.XXX",
         "statusCode":"404"
         }, 
    "location": "westus"
}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Chcete-li odebrat všechny prostředky, které jste vytvořili v tomto kurzu, odstraňte skupinu prostředků. Tato akce odstraní všechny prostředky, které skupina obsahuje. V takovém případě odstraní samotnou skupinu prostředků, služby IoT hub a účet úložiště. Pokud jste připnuli k řídicímu panelu metrik, budete muset odebrat ručně tak, že kliknete na tři tečky v pravém horním rohu každé a vyberete **odebrat**.

Chcete-li odebrat skupinu prostředků, použijte příkaz [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak používat diagnostické protokoly a metriky provedením následujících úloh:

> [!div class="checklist"]
> * Pomocí Azure CLI, vytvoření služby IoT hub, Simulovaná zařízení a účet úložiště.  
> * Povolení diagnostických protokolů. 
> * Zapnutí metrik.
> * Nastavení upozornění pro tyto metriky. 
> * Stáhněte a spusťte aplikaci, která simuluje zařízení odesílá zprávy do centra IoT. 
> * Tuto aplikaci spusťte, dokud výstrahy začnou vyvolat. 
> * Zobrazení výsledků metrik a diagnostických protokolech. 

V dalším kurzu se dozvíte, jak spravovat stav zařízení IoT. 

> [!div class="nextstepaction"]
[Konfigurace zařízení z back-endové služby](tutorial-device-twins.md)