---
title: Nastavení a používání metrik a diagnostických protokolů pomocí centra Azure IoT hub
description: Zjistěte, jak nastavit a používat metriky a diagnostické protokoly pomocí centra Azure IoT hub. To poskytne data k analýze, která vám pomohou diagnostikovat problémy, které může mít vaše centrum.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 3/13/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: d287cfab7adb676d3561486ed34c1062895a4036
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80133640"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>Kurz: Nastavení a použití metrik a diagnostických protokolů pomocí centra IoT hub

Pokud máte řešení IoT Hub spuštěné v produkčním prostředí, chcete nastavit některé metriky a povolit diagnostické protokoly. Pak, pokud dojde k problému, máte data, na která se můžete podívat, která vám pomohou diagnostikovat problém a opravit jej rychleji. V tomto článku uvidíte, jak povolit diagnostické protokoly a jak je zkontrolovat na chyby. Nastavíte také některé metriky pro sledování a upozorníte, že se zapálí, když metriky narazí na určitou hranici. Můžete mít například e-mail odeslaný, když počet odeslaných telemetrických zpráv překročí určitou hranici nebo když se počet použitých zpráv přiblíží kvótě zpráv povolených za den pro službu IoT Hub. 

Příkladem případu použití je čerpací stanice, kde čerpadla jsou zařízení IoT, které odesílají komunikaci s centrem IoT. Platební karty jsou ověřeny a konečná transakce je zapsána do úložiště dat. Pokud se zařízení IoT přestanou připojovat k centru a odesílat zprávy, je mnohem obtížnější opravit, pokud nemáte přehled o tom, co se děje.

Tento kurz používá ukázku Azure z [Směrování služby IoT Hub](tutorial-routing.md) k odesílání zpráv do služby IoT hub.

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
> * Pomocí rozhraní příkazového příkazového příkazového nastavení Azure vytvořte centrum IoT, simulované zařízení a účet úložiště.  
> * Povolte diagnostické protokoly.
> * Povolte metriky.
> * Nastavte upozornění pro tyto metriky. 
> * Stáhněte a spusťte aplikaci, která simuluje zařízení IoT odesílající zprávy do centra. 
> * Spusťte aplikaci, dokud se výstrahy nezačnou spouštět. 
> * Prohlédněte si výsledky metrik a zkontrolujte diagnostické protokoly. 

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

- Nainstalujte [visual studio](https://www.visualstudio.com/). 

- E-mailový účet schopný přijímat poštu.

- Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto kurzu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Příprava prostředků

Pro účely tohoto kurzu potřebujete službu IoT hub, účet úložiště a simulované zařízení IoT. Tyto prostředky můžete vytvořit pomocí Azure CLI nebo Azure PowerShellu. Použijte pro všechny prostředky stejnou skupinu a umístění. Na konci můžete odebrat vše v jednom kroku odstraněním skupiny prostředků.

Jedná se o požadované kroky.

1. Vytvořte [skupinu prostředků](../azure-resource-manager/management/overview.md). 

2. Vytvořte centrum IoT.

3. Vytvořte standardní účet úložiště V1 s replikací Standard_LRS.

4. Vytvořte identitu zařízení pro simulované zařízení, které bude odesílat zprávy do vašeho centra. Uložte klíč pro fázi testování.

### <a name="set-up-resources-using-azure-cli"></a>Nastavení prostředků pomocí azure cli

Zkopírujte tento skript a vložte ho do služby Cloud Shell. Za předpokladu, že jste již přihlášeni, se skript provede jeden řádek po druhém. Nové prostředky jsou vytvořeny ve skupině prostředků ContosoResources.

K proměnným, které musí být globálně jedinečné, je zřetězená hodnota `$RANDOM`. Při spuštění skriptu a nastavení proměnných se vygeneruje náhodný číselný řetězec a zřetězí se ke konci pevného řetězce. Tím se zajistí jeho jedinečnost.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-iot

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
>Při vytváření identity zařízení se může stát následující chyba: *Nebyly nalezeny žádné klíče pro vlastníka zásad iothub contosotesthub*. Chcete-li tuto chybu opravit, aktualizujte rozšíření Azure CLI IoT Extension a pak znovu spusťte poslední dva příkazy ve skriptu. 
>
>Zde je příkaz k aktualizaci rozšíření. Spusťte to v instanci Cloud Shell.
>
>```cli
>az extension update --name azure-iot
>```

## <a name="enable-the-diagnostic-logs"></a>Povolení diagnostických protokolů 

[Diagnostické protokoly](../azure-monitor/platform/platform-logs-overview.md) jsou ve výchozím nastavení zakázány při vytváření nového centra IoT Hub. V této části povolte diagnostické protokoly pro vaše centrum.

1. Pokud ještě nejste ve svém centru na portálu, klikněte na **Skupiny prostředků** a klikněte na skupinu prostředků Contoso-Resources. Vyberte centrum ze zobrazeného seznamu zdrojů. 

2. Vyhledejte část **Monitorování** v noži centra IoT Hub. Klikněte na **Nastavení diagnostiky**. 

   ![Snímek obrazovky zobrazující část nastavení diagnostiky v okně služby IoT Hub](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. Ujistěte se, že odběr a skupina prostředků jsou správné. V **části Typ prostředku**zrušte zaškrtnutí **políčka Vybrat vše**a vyhledejte a zkontrolujte **ioT Hub**. (Zaškrtávačka se umístí vedle položky *Vybrat vše* znovu, prostě to ignorujte.) V části **Resource**vyberte název centra. Obrazovka by měla vypadat takto: 

   ![Snímek obrazovky zobrazující část nastavení diagnostiky v okně služby IoT Hub](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. Nyní klepněte na tlačítko **Zapnout diagnostiku**. Zobrazí se podokno Nastavení diagnostiky. Zadejte název nastavení diagnostických protokolů jako "diags-hub".

5. Zkontrolujte **archivovat účet úložiště**. 

   ![Snímek obrazovky zobrazující nastavení diagnostiky pro archivaci na účet úložiště](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    Kliknutím na **Konfigurovat** zobrazíte obrazovku **Vybrat účet úložiště,** vyberte tu správnou (*contosostoragemon*) a klepnutím na tlačítko **OK** se vraťte do podokna Nastavení diagnostiky. 

   ![Snímek obrazovky zobrazující nastavení diagnostických protokolů pro archivaci na účet úložiště](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. V **části LOG**zkontrolujte **připojení** a **telemetrii zařízení**a nastavte uchovávání **(dny)** na 7 dní pro každou z nich. Obrazovka Nastavení diagnostiky by nyní měla vypadat takto:

   ![Snímek obrazovky s konečným nastavením diagnostického protokolu](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. Kliknutím na **Uložit** nastavení uložte. Zavřete podokno Nastavení diagnostiky.

Později, když se podíváte na diagnostické protokoly, budete moci zobrazit protokolování připojení a odpojení pro zařízení. 

## <a name="set-up-metrics"></a>Nastavení metrik 

Teď nastavte některé metriky, které se mají dívat na odeslání zpráv do centra. 

1. V podokně nastavení centra IoT klikněte na možnost **Metriky** v části **Monitorování.**

2. V horní části obrazovky klikněte na **Posledních 24 hodin (Automaticky).** V rozevíracím souboru, který se zobrazí, vyberte **možnost Poslední 4 hodiny** pro časový **rozsah**a nastavte **rozlišovací schopnost času** na **1 minutu**, místní čas. Chcete-li tato nastavení uložit, klepněte na **tlačítko Použít.** 

   ![Snímek obrazovky s nastavením času metriky](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. Ve výchozím nastavení existuje jedna položka metriky. Ponechte skupinu prostředků jako výchozí a obor názvů metriky. V rozevíracím seznamu **Metrika** vyberte **Odeslané telemetrické zprávy**. Nastavit **agregaci** na **Součet**.

   ![Snímek obrazovky s přidáním metriky pro odeslané telemetrické zprávy](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. Teď klikněte na **Přidat metriku** a přidejte do grafu další metriku. Vyberte skupinu prostředků (**ContosoTestHub**). V části **Metrika**vyberte **Celkový počet použitých zpráv**. V **případě agregace**vyberte **možnost Avg**. 

   Nyní se na obrazovce zobrazuje minimalizovaná metrika *odeslaných zpráv telemetrie*a nová metrika *celkového počtu použitých zpráv*.

   ![Snímek obrazovky s přidáním metriky pro odeslané telemetrické zprávy](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   Klikněte **na Připnout na řídicí panel**. Připne ho na řídicí panel vašeho portálu Azure, abyste k němu měli znovu přístup. Pokud jej nepřipnete na řídicí panel, nastavení se nezachová.

## <a name="set-up-alerts"></a>Nastavení výstrah

Přejděte do centra na portálu. Klikněte na **Skupiny prostředků**, vyberte *ContosoResources*a vyberte IoT Hub *ContosoTestHub*. 

Služba IoT Hub ještě nebyla migrována na [metriky ve službě Azure Monitor.](/azure/azure-monitor/platform/data-collection#metrics) musíte použít [klasické výstrahy](/azure/azure-monitor/platform/alerts-classic.overview).

1. V části **Sledování**klikněte na **upozornění:** Zobrazí se hlavní výstražná obrazovka. 

   ![Snímek obrazovky znázorňující, jak najít klasické výstrahy](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. Pokud se chcete dostat k klasickým výstrahám, klikněte na **Zobrazit klasická upozornění**. 

    ![Snímek obrazovky s klasickou obrazovkou upozornění](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    Vyplňte jednotlivá pole: 

    **Předplatné**: Toto pole ponechte nastavené na aktuální předplatné.

    **Zdroj**: Toto pole nastavte na *metriky*.

    **Skupina prostředků**: Toto pole nastavte na aktuální skupinu prostředků *ContosoResources*. 

    **Typ zdroje**: Nastavte toto pole na IoT Hub. 

    **Prostředek**: Vyberte centrum IoT hub, *ContosoTestHub*.

3. Kliknutím na **Přidat upozornění na metriku (klasické)** nastavte novou výstrahu.

    Vyplňte jednotlivá pole:

    **Název**: Zadejte název pravidla výstrahy, například *telemetrické zprávy*.

    **Popis**: Zadejte popis výstrahy, například *výstrahu, pokud je odesláno 1000 telemetrických zpráv*. 

    **Zdroj:** Nastavte tuto položku na *metriky*.

    **Odběr**, **skupina prostředků**a **prostředek** by měly být nastaveny na hodnoty, které jste vybrali na obrazovce Zobrazit **klasické výstrahy.** 

    Nastavit **metriku** na *odeslané zprávy telemetrie*.

    ![Snímek obrazovky zobrazující nastavení klasické výstrahy pro odeslané telemetrické zprávy](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. Za grafem nastavte následující pole:

   **Podmínka**: Nastaveno na *větší než*.

   **Práh**: Nastaveno na 1000.

   **Období**: Nastavit na *Za posledních 5 minut*.

   **Příjemci e-mailu**s oznámením : Sem zadejte svou e-mailovou adresu. 

   ![Snímek obrazovky zobrazující spodní polovinu obrazovky upozornění](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   Klepnutím na **tlačítko OK** výstrahu uložte. 

5. Nyní nastavte další výstrahu pro *celkový počet použitých zpráv*. Tato metrika je užitečná, pokud chcete odeslat výstrahu, když se počet použitých zpráv blíží kvótě pro centrum IoT – abyste věděli, že centrum brzy začne zprávy odmítat.

   Na obrazovce **Zobrazit klasická upozornění** klikněte na **Přidat upozornění na metriku (klasické)** a v podokně **Přidat pravidla** vyplňte tato pole.

   **Název**: Zadejte název pravidla výstrahy, například *počet použitých zpráv*.

   **Popis**: Zadejte popis výstrahy, například *upozornění při blížíní se kvótě*.

   **Zdroj**: Toto pole nastavte na *metriky*.

    **Odběr**, **skupina prostředků**a **prostředek** by měly být nastaveny na hodnoty, které jste vybrali na obrazovce Zobrazit **klasické výstrahy.** 

    Nastavte **metriku** na *celkový počet použitých zpráv*.

6. Pod grafem vyplňte následující pole:

   **Podmínka**: Nastaveno na *větší než*.

   **Práh**: Nastaveno na 1000.

   **Období**: Toto pole nastavte *na Za posledních 5 minut*. 

   **Příjemci e-mailu**s oznámením : Sem zadejte svou e-mailovou adresu. 

   Kliknutím na tlačítko **OK** pravidlo uložte. 

5. Nyní byste měli vidět dvě výstrahy v klasickém podokně výstrah: 

   ![Snímek obrazovky zobrazující klasickou obrazovku upozornění s novými pravidly upozornění](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. Zavřete podokno výstrah. 
    
    S těmito nastaveními se zobrazí výstraha, pokud je počet odeslaných zpráv větší než 400 a celkový počet použitých zpráv překročí číslo.

## <a name="run-simulated-device-app"></a>Spuštění aplikace simulovaného zařízení

Dříve v části nastavení skriptu jste vytvořili zařízení pro simulaci pomocí zařízení IoT. V této části si stáhnete konzolovou aplikaci .NET, která simuluje zařízení odesílající zprávy typu zařízení-cloud do centra IoT.  

Stažení řešení pro [Simulaci zařízení IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Tento odkaz stáhne repo s několika aplikacemi v něm; řešení, které hledáte, je v iot-hub/Tutorials/Routing/.

Dvojím kliknutím na soubor řešení (SimulatedDevice.sln) otevřete kód v sadě Visual Studio a otevřete Program.cs. Nahraďte `{iot hub hostname}` názvem hostitele centra IoT. Formát názvu hostitele centra IoT je **{iot-hub-name}.azure-devices.net**. V tomto kurzu je název hostitele centra **ContosoTestHub.azure-devices.net**. Dále nahraďte `{device key}` klíčem zařízení, který jste si předtím uložili při vytváření simulovaného zařízení. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Spuštění a testování 

V Program.cs změňte hodnotu `Task.Delay` z 1000 na 10, což zkracuje dobu mezi odesíláním zpráv z 1 sekundy na 0,01 sekundy. Zkrácení tohoto zpoždění zvyšuje počet odeslaných zpráv.

```csharp
await Task.Delay(10);
```

Spusťte konzolovou aplikaci. Počkejte několik minut (10-15). Můžete vidět zprávy odesílané ze simulovaného zařízení do rozbočovače na obrazovce konzoly aplikace.

### <a name="see-the-metrics-in-the-portal"></a>Podívejte se na metriky na portálu

Otevřete metriky z řídicího panelu. Změňte hodnoty času na *Posledních 30 minut* s časovou granularitou *1 minutu*. Zobrazuje odeslané telemetrické zprávy a celkový počet zpráv použitých v grafu s nejnovějšími čísly v dolní části grafu.

   ![Snímek obrazovky s metrikami](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>Podívejte se na upozornění

Vraťte se k upozorněním. Klepněte na **položku Skupiny prostředků**, vyberte *možnost ContosoResources*a vyberte rozbočovač *ContosoTestHub*. Na stránce vlastností zobrazené pro centrum vyberte **Výstrahy**a **potom zobrazit klasické výstrahy**. 

Pokud počet odeslaných zpráv překročí limit, začnete dostávat e-mailová upozornění. Pokud chcete zjistit, jestli nejsou nějaké aktivní výstrahy, přejděte do centra a vyberte **Výstrahy**. Zobrazí výstrahy, které jsou aktivní, a pokud existují nějaká upozornění. 

   ![Snímek obrazovky s výstrahami byly aktivovány.](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

Klikněte na výstrahu pro telemetrické zprávy. Zobrazuje výsledek metriky a graf s výsledky. E-mail odeslaný s upozorněním na výstražné spouštění také vypadá takto:

   ![Snímek obrazovky s e-mailem zobrazujícím výstrahy byly aktivovány.](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>Podívejte se na diagnostické protokoly

Můžete nastavit diagnostické protokoly, které mají být exportovány do úložiště objektů blob. Přejděte do skupiny prostředků a vyberte *contosostoragemon*účtu úložiště . Vyberte objekty BLOB a otevřete *kontejner insights-logs-connections*. Přejděte k podrobnostem, dokud se nedostanete k aktuálnímu datu a nevyberete nejnovější soubor. 

   ![Snímek obrazovky s přechodem na podrobnosti o kontejneru úložiště zobrazíte diagnostické protokoly.](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

Kliknutím na **Stáhnout** jej stáhněte a otevřete. Zobrazí se protokoly zařízení připojení a odpojení při odesílání zpráv do rozbočovače. Zde ukázka:

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

Chcete-li odebrat všechny prostředky, které jste vytvořili v tomto kurzu, odstraňte skupinu prostředků. Tato akce odstraní všechny prostředky, které skupina obsahuje. V takovém případě odebere službu IoT hub, účet úložiště a samotnou skupinu prostředků. Pokud jste připnuli metriky na řídicí panel, budete je muset odstranit ručně kliknutím na tři tečky v pravém horním rohu každého panelu a výběrem **možnosti Odebrat**.

Chcete-li odebrat skupinu prostředků, použijte příkaz [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili používat metriky a diagnostické protokoly provedením následujících úloh:

> [!div class="checklist"]
> * Pomocí rozhraní příkazového příkazového příkazového nastavení Azure vytvořte centrum IoT, simulované zařízení a účet úložiště.  
> * Povolte diagnostické protokoly. 
> * Povolte metriky.
> * Nastavte upozornění pro tyto metriky. 
> * Stáhněte a spusťte aplikaci, která simuluje zařízení IoT odesílající zprávy do centra. 
> * Spusťte aplikaci, dokud se výstrahy nezačnou spouštět. 
> * Prohlédněte si výsledky metrik a zkontrolujte diagnostické protokoly. 

V dalším kurzu se dozvíte, jak spravovat stav zařízení IoT. 

> [!div class="nextstepaction"]
> [Konfigurace zařízení z back-endové služby](tutorial-device-twins.md)
