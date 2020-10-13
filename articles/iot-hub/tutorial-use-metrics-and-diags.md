---
title: Nastavení a použití metrik a diagnostických protokolů ve službě Azure IoT Hub
description: Naučte se, jak nastavit a používat metriky a diagnostické protokoly ve službě Azure IoT Hub. Tím získáte data, která se budou analyzovat, aby bylo možné diagnostikovat problémy, které vaše centrum může mít.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 3/13/2019
ms.author: robinsh
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
- devx-track-csharp
ms.openlocfilehash: b24d9e1cbbcf875d7b4bde3981b28d8999ba8d47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89019126"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>Kurz: nastavení a použití metrik a diagnostických protokolů ve službě IoT Hub

Pokud máte řešení IoT Hub spuštěné v produkčním prostředí, chcete nastavit několik metrik a povolit diagnostické protokoly. Pokud dojde k potížím, budete mít k dispozici data, která vám pomůžou s diagnostikou problému a jeho rychlejším řešením. V tomto článku se dozvíte, jak povolit diagnostické protokoly a jak je zjistit, jestli neobsahuje chyby. Nastavili jste také některé metriky, které se budou sledovat, a výstrahy, které se aktivují, když metrika dosáhla určité hranice. Můžete vám například poslat e-mail, když počet odeslaných zpráv telemetrie překročí určitou hranici, nebo když se počet použitých zpráv blíží kvótě povolených zpráv za den pro IoT Hub. 

Příkladem případu použití je plynová stanice, kde jsou pumpa zařízení IoT, která odesílají komunikaci se službou IoT Hub. Platební karty jsou ověřeny a konečná transakce je zapsána do úložiště dat. Pokud zařízení IoT přestanou připojení k centru a posílání zpráv, je mnohem obtížnější opravit, Pokud nevidíte, co se chystá.

V tomto kurzu se používá ukázka Azure ze [IoT Hub směrování](tutorial-routing.md) k posílání zpráv do služby IoT Hub.

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
> * Pomocí Azure CLI vytvořte centrum IoT, simulované zařízení a účet úložiště.  
> * Povolte diagnostické protokoly.
> * Povolte metriky.
> * Nastavte výstrahy pro tyto metriky. 
> * Stáhněte a spusťte aplikaci, která simuluje zařízení IoT odesílající zprávy do centra. 
> * Spusťte aplikaci, dokud výstrahy nezačnou aktivovat. 
> * Podívejte se na výsledky metrik a zkontrolujte protokoly diagnostiky. 

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Nainstalujte [Visual Studio](https://www.visualstudio.com/). 

- E-mailový účet, který může přijímat poštu.

- Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto kurzu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Příprava prostředků

Pro tento kurz potřebujete centrum IoT, účet úložiště a simulované zařízení IoT. Tyto prostředky můžete vytvořit pomocí Azure CLI nebo Azure PowerShellu. Použijte pro všechny prostředky stejnou skupinu a umístění. Na konci můžete odebrat vše v jednom kroku odstraněním skupiny prostředků.

Jedná se o požadované kroky.

1. Vytvořte [skupinu prostředků](../azure-resource-manager/management/overview.md). 

2. Vytvořte centrum IoT.

3. Vytvořte standardní účet úložiště V1 s replikací Standard_LRS.

4. Vytvořte identitu zařízení pro simulované zařízení, které bude odesílat zprávy do vašeho centra. Uložte klíč pro fázi testování.

### <a name="set-up-resources-using-azure-cli"></a>Nastavení prostředků pomocí Azure CLI

Zkopírujte tento skript a vložte ho do služby Cloud Shell. Za předpokladu, že jste již přihlášeni, se skript provede jeden řádek po druhém. Nové prostředky se vytvoří ve skupině prostředků ContosoResources.

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
>Při vytváření identity zařízení se může zobrazit následující chyba: *nenašly se žádné klíče pro zásady iothubowner IoT Hub ContosoTestHub*. Pokud chcete tuto chybu opravit, aktualizujte rozšíření IoT Azure CLI a pak znovu spusťte poslední dva příkazy ve skriptu. 
>
>Tady je příkaz pro aktualizaci rozšíření. Spusťte to v instanci Cloud Shell.
>
>```cli
>az extension update --name azure-iot
>```

## <a name="enable-the-diagnostic-logs"></a>Povolení diagnostických protokolů 

[Diagnostické protokoly](../azure-monitor/platform/platform-logs-overview.md) jsou ve výchozím nastavení zakázané, když vytváříte nové centrum IoT. V této části povolíte diagnostické protokoly pro vaše centrum.

1. Pokud jste tak ještě neučinili v centru na portálu, klikněte na **skupiny prostředků** a pak klikněte na skupinu prostředků contoso-prostředky. Vyberte centrum ze seznamu zobrazených prostředků. 

2. Vyhledejte část **monitorování** v okně IoT Hub. Klikněte na **Nastavení diagnostiky**. 

   ![Snímek obrazovky zobrazující část nastavení diagnostiky v okně IoT Hub](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. Ujistěte se, že je předplatné a skupina prostředků správné. V části **typ prostředku**zrušte zaškrtnutí políčka **Vybrat vše**a vyhledejte a zaškrtněte **IoT Hub**. (Zaškrtnutím *políčka znovu vybrat vše* , stačí ho ignorovat.) V části **prostředek**vyberte název centra. Vaše obrazovka by měla vypadat jako na tomto obrázku: 

   ![Snímek obrazovky zobrazující část nastavení diagnostiky v okně IoT Hub](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. Nyní klikněte na **zapnout diagnostiku**. Zobrazí se podokno nastavení diagnostiky. Zadejte název nastavení diagnostických protokolů jako "diagnostiky".

5. Ověřte **archiv na účet úložiště**. 

   ![Snímek obrazovky zobrazující nastavení diagnostiky pro archivaci do účtu úložiště](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    Klikněte na **Konfigurovat** , aby se zobrazila obrazovka **Vybrat účet úložiště** , vyberte napravo (*Contosostoragemon*) a kliknutím na **OK** se vraťte do podokna nastavení diagnostiky. 

   ![Snímek obrazovky s nastavením diagnostických protokolů k archivaci na účet úložiště](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. V části **protokol**zaškrtněte políčka **připojení** a **telemetrie zařízení**a nastavte dobu **uchování (dny)** na 7 dní. Obrazovka nastavení diagnostiky by teď měla vypadat jako na tomto obrázku:

   ![Snímek obrazovky s konečným nastavením diagnostického protokolu](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. Kliknutím na **Uložit** nastavení uložte. Zavřete podokno nastavení diagnostiky.

Později, když se podíváte na diagnostické protokoly, budete moci zobrazit protokolování připojení a odpojení pro zařízení. 

## <a name="set-up-metrics"></a>Nastavení metrik 

Teď můžete nastavit některé metriky, které se budou sledovat při posílání zpráv do centra. 

1. V podokně nastavení pro Centrum IoT klikněte na možnost **metriky** v části **monitorování** .

2. V horní části obrazovky klikněte na tlačítko **Poslední 24 hodin (automaticky)**. V rozevíracím seznamu, který se zobrazí, vyberte **Poslední 4 hodiny** pro **časový rozsah**a nastavte **časové rozlišení** na **1 minutu**, místní čas. Kliknutím na **použít** uložte tato nastavení. 

   ![Snímek obrazovky zobrazující nastavení času metrik](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. Ve výchozím nastavení je jedna položka metriky. Ponechte skupinu prostředků nastavenou jako výchozí a obor názvů metriky. V rozevíracím seznamu **metrika** vyberte **odeslané zprávy telemetrie**. Nastavte **agregaci** na **součet**.

   ![Snímek obrazovky znázorňující přidání metriky pro odeslané zprávy telemetrie](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. Nyní kliknutím na **Přidat metriku** přidejte do grafu další metriku. Vyberte skupinu prostředků (**ContosoTestHub**). V části **metrika**vyberte **Celkový počet použitých zpráv**. V případě **agregace**vyberte **AVG**. 

   Teď obrazovka zobrazuje minimalizovanou metriku pro *odeslané zprávy telemetrie*a navíc novou metriku pro *Celkový počet použitých zpráv*.

   ![Snímek obrazovky znázorňující přidání metriky pro odeslané zprávy telemetrie](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   Klikněte na **Připnout na řídicí panel**. Připnout ho na řídicí panel svého Azure Portal, abyste k němu mohli znovu přistupovat. Pokud ho připnete na řídicí panel, nastavení se nezachovají.

## <a name="set-up-alerts"></a>Nastavení výstrah

Přejít na centrum na portálu. Klikněte na **skupiny prostředků**, vyberte *ContosoResources*a pak vyberte IoT Hub *ContosoTestHub*. 

IoT Hub ještě neproběhla migrace na [metriky v Azure monitor](/azure/azure-monitor/platform/data-collection#metrics) . je nutné použít [klasické výstrahy](/azure/azure-monitor/platform/alerts-classic.overview).

1. V části **monitorování**klikněte na **výstrahy** . zobrazí se hlavní obrazovka s výstrahou. 

   ![Snímek obrazovky ukazující, jak najít klasické výstrahy](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. Pokud se chcete dostat k klasickým výstrahám, klikněte na **Zobrazit klasické výstrahy**. 

    ![Snímek obrazovky znázorňující obrazovku s klasickými výstrahami](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    Vyplňte jednotlivá pole: 

    **Předplatné**: nechte toto pole nastavené na vaše aktuální předplatné.

    **Zdroj**: nastavte toto pole na *metriky*.

    **Skupina prostředků**: nastavte toto pole na aktuální skupinu prostředků *ContosoResources*. 

    **Typ prostředku**: nastavte toto pole na IoT Hub. 

    **Prostředek**: vyberte Centrum IoT, *ContosoTestHub*.

3. Kliknutím na **Přidat upozornění metriky (Classic)** nastavte novou výstrahu.

    Vyplňte jednotlivá pole:

    **Název**: zadejte název pravidla upozornění, například *zprávy telemetrie*.

    **Popis**: zadejte popis výstrahy, jako je například *výstraha, když jsou odesílány zprávy telemetrie 1000*. 

    **Zdroj**: nastavte tuto hodnotu na *metriky*.

    U **předplatného**, **skupiny prostředků**a **prostředku** musí být nastavené hodnoty na hodnotu, kterou jste vybrali na obrazovce **Zobrazit klasické výstrahy** . 

    Nastavte **metriku** na *odeslané zprávy telemetrie*.

    ![Snímek obrazovky s nastavením klasické výstrahy pro odeslané zprávy telemetrie](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. Po grafu nastavte následující pole:

   **Podmínka**: nastavte na hodnotu *větší než*.

   **Prahová hodnota**: nastavte na 1000.

   **Period**: nastaveno na *za posledních 5 minut*.

   **Příjemci oznámení e-mailem**: sem zadejte svou e-mailovou adresu. 

   ![Snímek obrazovky zobrazující obrazovku s dolní polovinou výstrah](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   Výstrahu uložíte kliknutím na tlačítko **OK** . 

5. Nyní nastavte další výstrahu pro *Celkový počet použitých zpráv*. Tato metrika je užitečná, pokud chcete odeslat výstrahu, když se počet použitých zpráv blíží kvótě pro službu IoT Hub – abyste věděli, že centrum bude brzy začít odmítat zprávy.

   Na obrazovce **Zobrazit klasické výstrahy** klikněte na **Přidat výstrahu metriky (Classic)** a potom vyplňte tato pole v podokně **Přidat pravidlo** .

   **Název**: zadejte název pravidla upozornění, například *Počet použitých zpráv*.

   **Popis**: zadejte popis výstrahy, jako je například *výstraha, pokud se blíží kvótě*.

   **Zdroj**: nastavte toto pole na *metriky*.

    U **předplatného**, **skupiny prostředků**a **prostředku** musí být nastavené hodnoty na hodnotu, kterou jste vybrali na obrazovce **Zobrazit klasické výstrahy** . 

    Nastavte **metriku** na *Celkový počet použitých zpráv*.

6. V grafu vyplňte následující pole:

   **Podmínka**: nastavte na hodnotu *větší než*.

   **Prahová hodnota**: nastavte na 1000.

   **Period**: nastavte toto pole na *více než posledních 5 minut*. 

   **Příjemci oznámení e-mailem**: sem zadejte svou e-mailovou adresu. 

   Kliknutím na tlačítko **OK** pravidlo uložte. 

5. V podokně výstrahy Classic by se teď měly zobrazit dvě výstrahy: 

   ![Snímek obrazovky znázorňující obrazovku klasických výstrah s novými pravidly upozornění](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. Zavřete podokno výstrahy. 
    
    Pomocí těchto nastavení se zobrazí upozornění, pokud je počet odeslaných zpráv větší než 400 a celkový počet použitých zpráv překračuje číslo.

## <a name="run-simulated-device-app"></a>Spuštění aplikace simulovaného zařízení

Dříve v části nastavení skriptu jste vytvořili zařízení pro simulaci pomocí zařízení IoT. V této části si stáhnete konzolovou aplikaci .NET, která simuluje zařízení odesílající zprávy typu zařízení-cloud do centra IoT.  

Stažení řešení pro [Simulaci zařízení IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Tento odkaz stáhne úložiště s několika aplikacemi. řešení, které hledáte, je ve službě IoT-Hub/kurzy/směrování/.

Dvojím kliknutím na soubor řešení (SimulatedDevice.sln) otevřete kód v sadě Visual Studio a otevřete Program.cs. Nahraďte `{iot hub hostname}` názvem hostitele centra IoT. Formát názvu hostitele centra IoT je **{iot-hub-name}.azure-devices.net**. V tomto kurzu je název hostitele centra **ContosoTestHub.azure-devices.net**. Dále nahraďte `{device key}` klíčem zařízení, který jste si předtím uložili při vytváření simulovaného zařízení. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Spuštění a testování 

V Program.cs změňte `Task.Delay` hodnotu od 1000 na 10, což zkracuje dobu mezi odesíláním zpráv z 1 sekundy na. 01 sekund. Zkrácením tohoto zpoždění se zvýší počet odeslaných zpráv.

```csharp
await Task.Delay(10);
```

Spusťte konzolovou aplikaci. Počkejte pár minut (10-15). Můžete zobrazit zprávy odesílané simulovaným zařízením do centra na obrazovce aplikace v konzole.

### <a name="see-the-metrics-in-the-portal"></a>Zobrazit metriky na portálu

Z řídicího panelu otevřete vaše metriky. Změňte hodnoty času na *posledních 30 minut* s časovým rozlišením *1 minuta*. Zobrazuje odeslané zprávy telemetrie a celkový počet zpráv použitých v grafu s nejnovějšími čísly v dolní části grafu.

   ![Snímek obrazovky znázorňující metriky](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>Zobrazit výstrahy

Vraťte se zpět na výstrahy. Klikněte na **skupiny prostředků**, vyberte *ContosoResources*a pak vyberte centrum *ContosoTestHub*. Na stránce vlastnosti zobrazené pro centrum vyberte **výstrahy**a potom **Zobrazte klasické výstrahy**. 

Když počet odeslaných zpráv překročí limit, začnete získávat e-mailová upozornění. Pokud chcete zjistit, jestli existují aktivní výstrahy, přejděte do svého centra a vyberte **výstrahy**. Zobrazí se upozornění, která jsou aktivní, a pokud se vyskytnou nějaká upozornění. 

   ![Snímek obrazovky znázorňující aktivované výstrahy](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

Klikněte na upozornění pro zprávy telemetrie. Zobrazuje výsledek metriky a graf s výsledky. Také odeslaný e-mail, který vás upozorní na to, že se má vypálení výstrahy líbit, vypadá takto:

   ![Snímek obrazovky s e-maily ukazující, že se aktivovaly výstrahy](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>Zobrazit diagnostické protokoly

Nastavíte, aby byly diagnostické protokoly exportovány do úložiště objektů BLOB. Přejít do skupiny prostředků a vyberte svůj účet úložiště *contosostoragemon*. Vyberte objekty BLOB a pak otevřete kontejner *Insights-logs-připojení*. Přejděte k podrobnostem, dokud se nedostanete k aktuálnímu datu a vyberete poslední soubor. 

   ![Snímek obrazovky s přechodem do kontejneru úložiště pro zobrazení diagnostických protokolů](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

Klikněte na **Stáhnout** a Stáhněte si ho a otevřete ho. Zobrazí se protokoly připojení a odpojení zařízení při odesílání zpráv do centra. Tady je ukázka:

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

Pokud chcete odebrat všechny prostředky, které jste vytvořili v tomto kurzu, odstraňte skupinu prostředků. Tato akce odstraní všechny prostředky, které skupina obsahuje. V takovém případě odebrání služby IoT Hub, účtu úložiště a samotné skupiny prostředků. Pokud jste připnuli metriky na řídicí panel, budete je muset odebrat ručně kliknutím na tři tečky v pravém horním rohu každého a výběrem možnosti **Odebrat**.

Chcete-li odebrat skupinu prostředků, použijte příkaz [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak používat metriky a diagnostické protokoly prováděním následujících úloh:

> [!div class="checklist"]
> * Pomocí Azure CLI vytvořte centrum IoT, simulované zařízení a účet úložiště.  
> * Povolte diagnostické protokoly. 
> * Povolte metriky.
> * Nastavte výstrahy pro tyto metriky. 
> * Stáhněte a spusťte aplikaci, která simuluje zařízení IoT odesílající zprávy do centra. 
> * Spusťte aplikaci, dokud výstrahy nezačnou aktivovat. 
> * Podívejte se na výsledky metrik a zkontrolujte protokoly diagnostiky. 

V dalším kurzu se dozvíte, jak spravovat stav zařízení IoT. 

> [!div class="nextstepaction"]
> [Konfigurace zařízení z back-endové služby](tutorial-device-twins.md)
