---
title: Nastavení a použití metrik a protokolů ve službě Azure IoT Hub
description: Naučte se, jak nastavit a používat metriky a protokoly ve službě Azure IoT Hub. Tím získáte data, která se budou analyzovat, aby bylo možné diagnostikovat problémy, které vaše centrum může mít.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: robinsh
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
- devx-track-csharp
ms.openlocfilehash: d59e37cdcb6f530b08e980cf75d8834aed332252
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93315196"
---
# <a name="tutorial-set-up-and-use-metrics-and-logs-with-an-iot-hub"></a>Kurz: nastavení a použití metrik a protokolů ve službě IoT Hub

Pomocí Azure Monitor můžete shromažďovat metriky a protokoly pro Centrum IoT, které vám pomůžou monitorovat provoz řešení a řešit problémy, když k nim dojde. V tomto článku se dozvíte, jak vytvářet grafy založené na metrikách, jak vytvářet výstrahy, které aktivují metriky, jak odesílat IoT Hub operace a chyby do protokolů Azure Monitor a jak zjistit chyby v protokolech.

V tomto kurzu se k posílání zpráv do služby IoT Hub používá ukázka Azure z rychlého startu pro [odeslání telemetrie .NET](quickstart-send-telemetry-dotnet.md) . Pro posílání zpráv můžete vždy použít nějaké zařízení nebo jinou ukázku, ale možná budete muset upravit několik kroků odpovídajícím způsobem.

Před zahájením tohoto kurzu můžou být užitečné některé znalosti Azure Monitor konceptů. Další informace najdete v tématu [monitorování IoT Hub](monitor-iot-hub.md). Další informace o metrikách a protokolech prostředků vysílaných službou IoT Hub najdete v tématu [monitorování informací o sledování](monitor-iot-hub-reference.md).

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
>
> * Pomocí Azure CLI vytvořte centrum IoT, zaregistrujte simulované zařízení a vytvořte Log Analytics pracovní prostor.  
> * Odešlete IoT Hub připojení a protokoly prostředků telemetrie zařízení do Azure Monitor protokolů v pracovním prostoru Log Analytics.
> * Pomocí Průzkumníka metrik vytvořte graf založený na vybraných metrikách a připněte ho na řídicí panel.
> * Vytvářejte upozornění na metriky, abyste mohli při výskytu důležitých podmínek dostávat oznámení e-mailem.
> * Stáhněte a spusťte aplikaci, která simuluje zařízení IoT odesílající zprávy do služby IoT Hub.
> * Zobrazit výstrahy, když dojde k vašim podmínkám.
> * Zobrazení grafu metrik na řídicím panelu.
> * Zobrazit IoT Hub chyby a operace v protokolech Azure Monitor.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Ve vývojovém počítači potřebujete .NET Core SDK 2,1 nebo vyšší. Sadu .NET Core SDK pro různé platformy si můžete stáhnout z webu [.NET](https://www.microsoft.com/net/download/all).

  Aktuální verzi C# na počítači používaném pro vývoj můžete ověřit pomocí následujícího příkazu:

  ```cmd/sh
  dotnet --version
  ```

* E-mailový účet, který může přijímat poštu.

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto kurzu používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Příprava prostředků

Pro tento kurz potřebujete centrum IoT, Log Analytics pracovní prostor a simulované zařízení IoT. Tyto prostředky můžete vytvořit pomocí Azure CLI nebo Azure PowerShellu. Použijte pro všechny prostředky stejnou skupinu a umístění. Po dokončení tohoto kurzu můžete odebrat vše v jednom kroku odstraněním skupiny prostředků.

Tady jsou požadované kroky.

1. Vytvořte [skupinu prostředků](../azure-resource-manager/management/overview.md).

2. Vytvořte centrum IoT.

3. Vytvořte pracovní prostor Log Analytics.

4. Zaregistrujte identitu zařízení pro simulované zařízení, které odesílá zprávy do služby IoT Hub. Uložte připojovací řetězec zařízení, který se použije ke konfiguraci simulovaného zařízení.

### <a name="set-up-resources-using-azure-cli"></a>Nastavení prostředků pomocí Azure CLI

Zkopírujte tento skript a vložte ho do služby Cloud Shell. Za předpokladu, že jste již přihlášeni, se skript provede jeden řádek po druhém. Spuštění některých příkazů může nějakou dobu trvat. Nové prostředky se vytvoří ve skupině prostředků *ContosoResources*.

Název některých prostředků musí být v rámci Azure jedinečný. Skript vygeneruje náhodnou hodnotu s `$RANDOM` funkcí a uloží ji do proměnné. Pro tyto prostředky skript připojí tuto náhodnou hodnotu k základnímu názvu prostředku, takže název prostředku je jedinečný.

Pro každé předplatné je povolené jenom jedno bezplatné centrum IoT. Pokud už máte ve svém předplatném bezplatné centrum IoT, odstraňte ho před spuštěním skriptu nebo ho upravte tak, aby používal vaše bezplatné centrum IoT nebo IoT Hub, které používají úroveň Standard nebo Basic.

Skript vytiskne název služby IoT Hub, název pracovního prostoru Log Analytics a připojovací řetězec pro zařízení, které registruje. Nezapomeňte si je poznamenat, jak je budete potřebovat později v tomto článku.

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
randomValue=$RANDOM

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier. Partition count must be 2.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --partition-count 2 \
    --sku F1 --location $location

# The Log Analytics workspace name must be globally unique, so add a random number to the end.
workspaceName=contoso-la-workspace$randomValue
echo "Log Analytics workspace name = " $workspaceName


# Create the Log Analytics workspace
az monitor log-analytics workspace create --resource-group $resourceGroup \
    --workspace-name $workspaceName --location $location

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the primary connection string for the device identity, then copy it to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show-connection-string --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>Při vytváření identity zařízení se může zobrazit následující chyba: *nenašly se žádné klíče pro zásady iothubowner IoT Hub ContosoTestHub*. Pokud chcete tuto chybu opravit, aktualizujte rozšíření IoT Azure CLI a pak znovu spusťte poslední dva příkazy ve skriptu. 
>
>Tady je příkaz pro aktualizaci rozšíření. Spusťte tento příkaz v instanci Cloud Shell.
>
>```cli
>az extension update --name azure-iot
>```

## <a name="collect-logs-for-connections-and-device-telemetry"></a>Shromažďovat protokoly pro připojení a telemetrii zařízení

IoT Hub generuje protokoly prostředků pro několik kategorií operací. Chcete-li však zobrazit tyto protokoly, je nutné vytvořit nastavení diagnostiky k odeslání do cíle. Jedním z těchto cílů je Azure Monitor protokolů, které se shromažďují v pracovním prostoru Log Analytics. Protokoly prostředků IoT Hub jsou seskupené do různých kategorií. Můžete vybrat, které kategorie se mají odeslat do Azure Monitor protokoly v nastavení diagnostiky. V tomto článku budeme shromažďovat protokoly o operacích a chybách, ke kterým dochází, a to s využitím připojení a telemetrie zařízení. Úplný seznam kategorií podporovaných pro IoT Hub najdete v tématu [protokoly prostředků IoT Hub](monitor-iot-hub-reference.md#resource-logs).

Chcete-li vytvořit nastavení diagnostiky k odeslání IoT Hubch protokolů prostředků do Azure Monitor protokolů, postupujte takto:

1. Pokud jste tak ještě neučinili v centru na portálu, vyberte **skupiny prostředků** a vyberte skupinu prostředků ContosoResources. Ze seznamu zobrazených prostředků vyberte Centrum IoT.

1. Vyhledejte část **monitorování** v okně IoT Hub. Vyberte **nastavení diagnostiky**. Pak vyberte **Přidat nastavení diagnostiky**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/open-diagnostic-settings.png" alt-text="Snímek obrazovky, který zvýrazní nastavení diagnostiky v části monitorování.":::

1. V podokně **nastavení diagnostiky** zadejte popisný název, jako je například "odesílání připojení a telemetrie do protokolů".

1. V části **Podrobnosti o kategorii** vyberte **připojení** a **telemetrie zařízení**.

1. V části **Podrobnosti o cíli** vyberte **Odeslat do Log Analytics** a pak pomocí nástroje Log Analytics pro výběr pracovního prostoru vyberte pracovní prostor, který jste si poznamenali dříve. Až budete hotovi, nastavení diagnostiky by mělo vypadat podobně jako na následujícím snímku obrazovky:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/add-diagnostic-setting.png" alt-text="Snímek obrazovky s konečným nastavením diagnostického protokolu":::

1. Vyberte **Uložit** a nastavení se uloží. Zavřete podokno **nastavení diagnostiky** . Nové nastavení můžete zobrazit v seznamu nastavení diagnostiky.

## <a name="set-up-metrics"></a>Nastavení metrik

Teď použijeme Průzkumníka metrik k vytvoření grafu, který bude zobrazovat metriky, které chcete sledovat. Tento graf připnete na výchozí řídicí panel v Azure Portal.

1. V levém podokně Centra IoT vyberte v části **monitorování** možnost **metriky** .

1. V horní části obrazovky vyberte **posledních 24 hodin (automaticky)**. V rozevíracím seznamu, který se zobrazí, vyberte **Poslední 4 hodiny** pro **časový rozsah** , nastavte **časové rozlišení** na **1 minutu** a v poli **čas zobrazení** vyberte **místní** . Pokud chcete tato nastavení uložit, vyberte **použít** . Nastavení by teď mělo vystavovat **místní čas: poslední 4 hodiny (1 minuta)**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-select-time-range.png" alt-text="Snímek obrazovky zobrazující nastavení času metrik":::

1. V grafu je k dispozici částečné nastavení metriky vymezené pro Centrum IoT. Hodnoty oboru **Scope** názvů a **metriky** ponechte ve výchozím nastavení. Vyberte nastavení **metriky** a zadejte telemetrie a pak vyberte **zprávy telemetrie odeslané** z rozevíracího seznamu. **Agregace** bude automaticky nastavena na **součet**. Všimněte si, že se také změní název grafu.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-telemetry-messages-sent.png" alt-text="Snímek obrazovky, který ukazuje přidání zpráv telemetrie do grafu":::

1. Teď vyberte **Přidat metriku** a přidejte do grafu další metriku. V části **metrika** vyberte **Celkový počet použitých zpráv**. **Agregace** bude automaticky nastavena na **AVG**. Všimněte si, že se název grafu změnil, aby zahrnoval tuto metriku.

   Teď obrazovka zobrazuje minimalizovanou metriku pro *odeslané zprávy telemetrie* a navíc novou metriku pro *Celkový počet použitých zpráv*.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used.png" alt-text="Snímek obrazovky, který ukazuje přidání celkového počtu zpráv použitých v grafu metriky":::

1. V pravém horním rohu grafu vyberte **Připnout na řídicí panel**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used-pin.png" alt-text="Snímek obrazovky, který zvýrazní tlačítko Připnout na řídicí panel":::

1. V podokně **Připnout na řídicí panel** vyberte **existující** kartu. Vyberte **soukromá** a potom v rozevíracím seznamu řídicího panelu vyberte **řídicí panel** . Nakonec vyberte **připnout** , aby se graf připnout na výchozí řídicí panel v Azure Portal. Pokud graf připnete na řídicí panel, nastavení se při ukončení Průzkumníka metrik neuchovávají.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/pin-to-dashboard.png" alt-text="Snímek obrazovky, který zobrazuje nastavení pro připnutí na řídicí panel":::

## <a name="set-up-metric-alerts"></a>Nastavení upozornění na metriky

Nyní nastavíme výstrahy pro aktivaci na dvou *odeslaných zprávách telemetrie* metrik a *Celkový počet použitých zpráv*.

*Odeslané zprávy telemetrie* jsou vhodnou metrikou ke sledování propustnosti zpráv a k zamezení omezení. U IoT Hub na úrovni Free je limit omezování 100 zpráv za sekundu. U jednoho zařízení nebudeme moct dosáhnout tohoto typu propustnosti, takže místo toho nastavíme výstrahu, která se aktivuje, pokud počet zpráv překročí 1000 v období 5 minut. V produkčním prostředí můžete signál nastavit na důležitější hodnotu podle úrovně, edice a počtu jednotek služby IoT Hub.

*Celkový počet použitých zpráv* sleduje denní počet použitých zpráv. Tato metrika se každý den znovu nastaví v 00:00 UTC. Pokud překročíte denní kvótu po určité prahové hodnotě, IoT Hub už nebude přijímat zprávy. Pro IoT Hub v úrovni Free je denní kvóta zprávy 8000. Nastavíme výstrahu, která se aktivuje, pokud celkový počet zpráv překročí 4000, 50% kvóty. V praxi byste pravděpodobně nastavili toto procento na vyšší hodnotu. Hodnota denní kvóty závisí na úrovni, edici a počtu jednotek služby IoT Hub.

Další informace o limitech kvót a omezení u IoT Hub najdete v tématu [kvóty a omezování](iot-hub-devguide-quotas-throttling.md).

Nastavení výstrah metrik:

1. V Azure Portal můžete přejít do služby IoT Hub.

1. V části **monitorování** vyberte **výstrahy**. Pak vyberte **nové pravidlo výstrahy**.  Otevře se podokno **vytvořit pravidlo výstrahy** .

    :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-pane.png" alt-text="Snímek obrazovky znázorňující podokno vytvořit pravidlo výstrahy":::

    V podokně **vytvořit pravidlo výstrahy** jsou čtyři části:

    * **Obor** už je nastavený na vaše centrum IoT, takže tuto část ponecháme samostatně.
    * **Podmínka** nastaví signál a podmínky, které aktivují výstrahu.
    * **Akce** nakonfigurují, co se stane, když se výstraha aktivuje.
    * **Podrobnosti pravidla výstrahy** umožňují nastavit název a popis výstrahy.

1. Nejdřív nakonfigurujte podmínku, na které se výstraha aktivuje.

    1. V části **Podmínka** vyberte **vybrat podmínku**. V podokně **Konfigurace logiky signálu** do vyhledávacího pole zadejte "telemetrie" a vyberte **odeslané zprávy telemetrie**.

       :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-telemetry-messages-sent.png" alt-text="Snímek obrazovky znázorňující výběr metriky":::

    1. V podokně **Konfigurovat logiku signálu** nastavte nebo potvrďte následující pole v části **Logical Alert** (graf můžete ignorovat):

       **Prahová hodnota** :  *static*.

       **Operátor** : je *větší než*.

       **Typ agregace** : *celkem*.

       **Prahová hodnota** : 1000.

       **Členitost agregace (period)** : *5 minut*.

       **Frekvence hodnocení** : *každou 1 minutu*

        :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-set-conditions.png" alt-text="Snímek obrazovky s nastavením podmínek upozornění":::

       Tato nastavení nastaví signál tak, aby celkový počet zpráv za dobu 5 minut. Tato celková hodnota se vyhodnotí každou minutu a v případě, že celkový počet předchozích 5 minut překračuje 1000 zpráv, výstraha se aktivuje.

       Vyberte **Hotovo** a uložte logiku signálu.

1. Nyní nakonfigurujte akci pro výstrahu.

    1. Zpět v podokně **vytvořit pravidlo výstrahy** vyberte v části **Akce** možnost **Vybrat skupinu akcí**. V podokně **Vyberte skupinu akcí, která se má připojit k tomuto pravidlu výstrahy** vyberte možnost **vytvořit skupinu akcí**.

    1. Na kartě **základy** v podokně **vytvořit skupinu akcí** zadejte název a zobrazovaný název skupiny akcí.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-basics.png" alt-text="Snímek obrazovky znázorňující kartu základy okna vytvořit skupinu akcí":::

    1. Vyberte kartu **oznámení** . Jako **Typ oznámení** vyberte v rozevíracím seznamu **e-mail/SMS zpráva/nabízení/hlas** . Otevře se podokno **zpráva e-mailu/zpráva SMS/oznámení/hlas** .

    1. V podokně **e-mail/zpráva SMS/oznámení/hlas** vyberte e-mail a zadejte svou e-mailovou adresu a pak vyberte **OK**.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/set-email-address.png" alt-text="Snímek obrazovky s nastavením e-mailové adresy":::

    1. Zpět v podokně **oznámení** zadejte název oznámení.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-notification-complete.png" alt-text="Snímek obrazovky znázorňující podokno dokončená oznámení":::

    1. Volitelné Pokud vyberete kartu **Akce** a potom vyberete rozevírací seznam **typ akce** , můžete zobrazit typy akcí, které lze aktivovat pomocí výstrahy. V tomto článku budeme používat jenom oznámení, abyste mohli ignorovat nastavení na této kartě.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/action-types.png" alt-text="Snímek obrazovky znázorňující typy akcí, které jsou k dispozici v podokně akce":::

    1. Vyberte kartu **Kontrola a vytvoření** , ověřte nastavení a vyberte **vytvořit**.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-review-and-create.png" alt-text="Snímek obrazovky znázorňující podokno pro kontrolu a vytváření":::

    1. Zpět v podokně **vytvořit pravidlo výstrahy** si všimněte, že vaše nová skupina akcí byla přidána k akcím pro danou výstrahu.  

1. Nakonec nakonfigurujte Podrobnosti pravidla výstrahy a uložte pravidlo upozornění.

    1. V podokně **vytvořit pravidlo upozornění** v části Podrobnosti pravidla výstrahy zadejte název a popis výstrahy. například "alert Pokud více než 1000 zpráv za 5 minut". Ujistěte se, že je zaškrtnuté políčko **Povolit pravidlo upozornění při vytváření** . Vaše dokončené pravidlo výstrahy bude vypadat podobně jako tento snímek obrazovky.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-final.png" alt-text="Snímek obrazovky znázorňující dokončené podokno vytvořit pravidlo výstrahy.":::

    1. Vyberte **vytvořit pravidlo upozornění** pro uložení nového pravidla.

1. Nyní nastavte další výstrahu pro *Celkový počet použitých zpráv*. Tato metrika je užitečná, pokud chcete odeslat výstrahu, když se počet použitých zpráv blíží denní kvótě pro službu IoT Hub. v takovém případě bude Centrum IoT zahájit odmítnutí zpráv. Postupujte podle kroků, které jste provedli dříve, a následující rozdíly.

    * Pro signál v podokně **Konfigurovat logiku signálu** vyberte **Celkový počet použitých zpráv**.

    * V podokně **Konfigurovat logiku signálu** nastavte nebo potvrďte následující pole (Tento graf můžete ignorovat):

       **Prahová hodnota** :  *static*.

       **Operátor** : je *větší než*.

       **Typ agregace** : *Maximum*.

       **Prahová hodnota** : 4000.

       **Členitost agregace (period)** : *1 minuta*.

       **Frekvence hodnocení** : *každou 1 minutu*

       Tato nastavení nastavují signál, který se aktivuje, když počet zpráv dosáhne 4000. Metrika se vyhodnocuje každou minutu.

    * Když zadáte akci pro pravidlo výstrahy, stačí vybrat skupinu akcí, kterou jste předtím vytvořili.

    * V části Podrobnosti o výstraze vyberte jiný název a popis, než jste předtím.

1. V levém podokně Centra IoT zvolte **výstrahy** v části **monitorování** . Nyní v nabídce v horní části podokna **výstrahy** vyberte možnost **Spravovat pravidla výstrah** . Otevře se podokno **pravidla** . Teď by se vám měly zobrazit vaše dvě výstrahy:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/rules-management.png" alt-text="Snímek obrazovky znázorňující podokno pravidla s novými pravidly upozornění":::

1. Zavřete podokno **pravidla** .

Pomocí těchto nastavení se aktivuje výstraha a dostanete e-mailové oznámení, když se do 5 minut zasílají více než 1000 zpráv a také když celkový počet využitých zpráv překračuje 4000 (50% denní kvóty pro Centrum IoT na bezplatné úrovni).

## <a name="run-the-simulated-device-app"></a>Spuštění aplikace simulovaného zařízení

V části [nastavení prostředků](#set-up-resources) jste zaregistrovali identitu zařízení, která se má použít k simulaci použití zařízení IoT. V této části si stáhnete konzolovou aplikaci .NET, která simuluje zařízení odesílající zprávy typu zařízení-Cloud do IoT Hub, nakonfigurujte ho tak, aby odesílal tyto zprávy do služby IoT Hub, a pak ho spustí. 

> [!IMPORTANT]
>
> Výstrahy můžou trvat až 10 minut, než se IoT Hub plně nakonfigurují a povolí. Počkejte aspoň 10 minut mezi časem konfigurace Poslední výstrahy a spuštěním aplikace simulovaného zařízení.

Stažení řešení pro [Simulaci zařízení IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Tento odkaz stáhne úložiště s několika aplikacemi. ta, kterou hledáte, je ve službě IoT-Hub/starts/simulovaná-Device/.

1. V místním okně terminálu přejděte do kořenové složky řešení. Pak přejděte do složky **iot-hub\Quickstarts\simulated-device**.

1. V libovolném textovém editoru otevřete soubor **SimulatedDevice.cs**.

    1. Nahraďte hodnotu `s_connectionString` proměnné připojovacím řetězcem zařízení, který jste si poznamenali při spuštění skriptu pro nastavení prostředků.

    1. V `SendDeviceToCloudMessagesAsync` metodě změňte `Task.Delay` hodnotu od 1000 na 1, což zkracuje dobu mezi odesíláním zpráv od 1 sekundy do 0,001 sekund. Zkrácením tohoto zpoždění se zvýší počet odeslaných zpráv. (Rychlost zprávy 100 zpráv za sekundu se vám pravděpodobně nezobrazuje.)

        ```csharp
        await Task.Delay(1);
        ```

    1. Uložte změny do **SimulatedDevice.cs**.

1. V okně místního terminálu spusťte následující příkaz, který nainstaluje požadované balíčky pro aplikaci simulovaného zařízení:

    ```cmd/sh
    dotnet restore
    ```

1. Spuštěním následujícího příkazu v okně místního terminálu sestavte a spusťte aplikaci simulovaného zařízení:

    ```cmd/sh
    dotnet run
    ```

    Následující snímek obrazovky ukazuje výstup, zatímco aplikace simulovaného zařízení odesílá telemetrická data do vašeho centra IoT:

    :::image type="content" source="media/tutorial-use-metrics-and-diags/simulated-device-output.png" alt-text="Snímek obrazovky znázorňující výstup simulovaného zařízení":::

Nechte aplikaci spuštěnou aspoň 10-15 minut. V ideálním případě ji nechte běžet až do chvíle, kdy přestane odesílat zprávy (asi 20-30 minut). K tomu dojde, pokud jste překročili kvótu denních zpráv pro službu IoT Hub a přestali byste přijímat další zprávy.

> [!NOTE]
> Pokud necháte aplikaci pro zařízení spuštěnou delší dobu po zastavení odesílání zpráv, může se zobrazit výjimka. Tuto výjimku můžete bezpečně ignorovat a zavřít okno aplikace.

## <a name="view-metrics-chart-on-your-dashboard"></a>Zobrazení grafu metrik na řídicím panelu

1. V levém horním rohu Azure Portal otevřete nabídku portál a pak vyberte možnost **řídicí panel**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/select-dashboard.png" alt-text="Snímek obrazovky s vybraným řídicím panelem":::

1. Najděte si graf, který jste připnuli, a Kliknutím kamkoli na dlaždici mimo data grafu ho rozbalte. Zobrazuje odeslané zprávy telemetrie a celkový počet zpráv použitých v grafu. Poslední čísla se zobrazí v dolní části grafu. Můžete přesunout kurzor v grafu, aby se zobrazily hodnoty metriky pro konkrétní časy. Můžete také změnit hodnotu času a členitost v horní části grafu pro zúžení nebo rozbalení dat do časového období zájmu.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-on-dashboard-last-hour.png" alt-text="Snímek obrazovky znázorňující graf metriky":::

   V tomto scénáři propustnost zpráv simulovaného zařízení není dostatečně velká, takže by IoT Hub omezila jejich zprávy. Ve scénáři, který ve skutečnosti zahrnuje omezení, můžete vidět, že odeslané zprávy telemetrie přesahují limit omezení pro Centrum IoT po omezenou dobu. To se přizpůsobí nárůstu provozu. Podrobnosti najdete v tématu [přenosový tvar](iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="view-the-alerts"></a>Zobrazit výstrahy

Když počet odeslaných zpráv přesáhne limity, které jste nastavili v pravidlech upozornění, začnete získávat e-mailová upozornění.

Pokud chcete zjistit, jestli existují aktivní výstrahy, v levém podokně Centra IoT vyberte **výstrahy** v části **monitorování** . V podokně **výstrahy** se zobrazuje počet výstrah, které se v zadaném časovém rozsahu vyvolaly seřazením podle závažnosti.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-alerts.png" alt-text="Snímek obrazovky zobrazující souhrn výstrah":::

Vyberte řádek pro závažnost závažnost 3. Otevře se podokno **všechna upozornění** a zobrazí se seznam výstrah závažnost 3, které byly aktivovány.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-all-alerts.png" alt-text="Snímek obrazovky obsahující podokno všechny výstrahy":::

Vyberte jednu z výstrah pro zobrazení podrobností o výstrahách.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-individual-alert.png" alt-text="Snímek obrazovky zobrazující podrobnosti výstrahy":::

Ověřte e-maily z Microsoft Azure ve vaší doručené poště. Na řádku předmětu bude popsána výstraha, která byla aktivována. Například *Azure: závažnost: 3 výstraha, pokud více než 1000 zpráv za 5 minut*. Tělo bude vypadat podobně jako na následujícím obrázku:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/alert-mail.png" alt-text="Snímek obrazovky s e-maily ukazující, že se aktivovaly výstrahy":::

## <a name="view-azure-monitor-logs"></a>Zobrazit protokoly Azure Monitor

V části [shromáždit protokoly pro připojení a telemetrie zařízení](#collect-logs-for-connections-and-device-telemetry) jste vytvořili nastavení diagnostiky pro odesílání protokolů prostředků vysílaných službou IoT Hub pro připojení a operace telemetrie zařízení do Azure monitor protokolů. V této části spustíte dotaz Kusto proti Azure Monitor protokolů, abyste mohli sledovat případné chyby, ke kterým došlo.

1. V části **monitorování** v levém podokně Centra IoT v Azure Portal vyberte **protokoly**. Pokud se otevře okno úvodní **dotazy** , zavřete ho.

1. V podokně nový dotaz vyberte kartu **dotazy** a potom rozbalte položku **IoT Hub** . zobrazí se seznam výchozích dotazů.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/new-query-pane.png" alt-text="Snímek obrazovky s IoT Hub výchozími dotazy":::

1. Vyberte dotaz *souhrnu chyb* . Dotaz se zobrazí v podokně Editoru dotazů. V podokně editoru vyberte **Spustit** a sledujte výsledky dotazu. Rozbalením jednoho z řádků zobrazíte podrobnosti.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/logs-errors.png" alt-text="Snímek obrazovky protokolů vrácených dotazem souhrnu chyb":::

   > [!NOTE]
   > Pokud se nezobrazí žádné chyby, zkuste spustit dotaz na *nedávno připojená zařízení* . To by mělo vracet řádek pro simulované zařízení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat všechny prostředky, které jste vytvořili v tomto kurzu, odstraňte skupinu prostředků. Tato akce odstraní všechny prostředky, které skupina obsahuje. V takovém případě odebrání centra IoT, pracovního prostoru Log Analytics a samotné skupiny prostředků. Pokud jste připnuli grafy metriky na řídicí panel, budete je muset odstranit ručně kliknutím na tři tečky v pravém horním rohu každého grafu a výběrem možnosti **Odebrat**. Po odstranění grafů nezapomeňte změny uložit.

Chcete-li odebrat skupinu prostředků, použijte příkaz [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
az group delete --name ContosoResources
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak používat IoT Hub metriky a protokoly prováděním následujících úloh:

> [!div class="checklist"]
>
> * Pomocí Azure CLI vytvořte centrum IoT, zaregistrujte simulované zařízení a vytvořte Log Analytics pracovní prostor.  
> * Odešlete IoT Hub připojení a protokoly prostředků telemetrie zařízení do Azure Monitor protokolů v pracovním prostoru Log Analytics.
> * Pomocí Průzkumníka metrik vytvořte graf založený na vybraných metrikách a připněte ho na řídicí panel.
> * Vytvářejte upozornění na metriky, abyste mohli při výskytu důležitých podmínek dostávat oznámení e-mailem.
> * Stáhněte a spusťte aplikaci, která simuluje zařízení IoT odesílající zprávy do služby IoT Hub.
> * Zobrazit výstrahy, když dojde k vašim podmínkám.
> * Zobrazení grafu metrik na řídicím panelu.
> * Zobrazit IoT Hub chyby a operace v protokolech Azure Monitor.

V dalším kurzu se dozvíte, jak spravovat stav zařízení IoT. 

> [!div class="nextstepaction"]
> [Konfigurace zařízení z back-endové služby](tutorial-device-twins.md)