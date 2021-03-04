---
title: 'Kurz: připojení kompletního řešení'
titleSuffix: Azure Digital Twins
description: Kurz pro sestavení uceleného řešení digitálních vláken Azure, které je založené na datech zařízení
author: baanders
ms.author: baanders
ms.date: 4/15/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: d46a20079919f052ed343c9702ba02ce7f109b5c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "102036128"
---
# <a name="tutorial-build-out-an-end-to-end-solution"></a>Kurz: sestavení kompletního řešení

K nastavení kompletního řešení založeného na živých datech z vašeho prostředí můžete propojit instanci digitálních vláken Azure s dalšími službami Azure za účelem správy zařízení a dat.

V tomto kurzu budete...
> [!div class="checklist"]
> * Nastavení instance digitálních vláken Azure
> * Seznamte se s ukázkovým scénářem sestavení a vytvořením instance předem zapsaných komponent.
> * Použití aplikace [Azure Functions](../azure-functions/functions-overview.md) ke směrování simulované telemetrie ze [IoT Hub](../iot-hub/about-iot-hub.md) zařízení do digitálních vlastností
> * Rozšíří změny přes dodaný **graf** zpracováním digitálních dvojitých oznámení pomocí Azure Functions, koncových bodů a tras.

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Nastavit relaci Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="get-started-with-the-building-scenario"></a>Začínáme se scénářem vytváření

Vzorový projekt použitý v tomto kurzu představuje reálný **scénář stavby**, který obsahuje podlahovou místnost, místnost a termostat zařízení. Tyto součásti budou digitálně zastoupeny v instanci digitálních vláken Azure, která se pak připojí k [IoT Hub](../iot-hub/about-iot-hub.md), [Event Grid](../event-grid/overview.md)a dvěma [funkcím Azure](../azure-functions/functions-overview.md) , aby se usnadnil pohyb dat.

Níže je diagram znázorňující celý scénář. 

Nejprve vytvoříte instanci digitálních vláken Azure (**oddíl A** v diagramu) a pak nastavíte tok dat telemetrie do digitálních vláken (**šipka B**) a pak nastavíte šíření dat pomocí grafu s dvojitou smyčkou (**šipka C**).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Obrázek celého scénáře stavby Znázorňuje tok dat ze zařízení do IoT Hub, prostřednictvím funkce Azure (šipka B) k instanci digitálních vláken Azure (oddíl A), pak prostřednictvím Event Grid na jinou funkci Azure pro zpracování (šipka C).":::

Pokud chcete pracovat v tomto scénáři, budete pracovat s komponentami předem napsané ukázkové aplikace, kterou jste si stáhli dříve.

Tady jsou komponenty implementované ukázkovou aplikací *AdtSampleApp* scénář vytváření:
* Ověřování zařízení 
* Příklady použití [rozhraní .NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) (najdete v *CommandLoop.cs*)
* Rozhraní konzoly pro volání rozhraní API digitálních vláken Azure
* *SampleClientApp* – ukázkové řešení digitálních vláken Azure
* *SampleFunctionsApp* – aplikace Azure Functions, která aktualizuje graf digitálních vláken Azure v důsledku telemetrie z IoT Hub a událostí digitálních vláken Azure

### <a name="instantiate-the-pre-created-twin-graph"></a>Vytvoření instance předem vytvořeného grafu s dvojitou čárkou

Nejprve použijete řešení *AdtSampleApp* z ukázkového projektu k sestavení části digitálních vláken Azure v rámci kompletního a koncového scénáře (**oddíl A**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-a.png" alt-text="Výňatek z kompletního vývojového scénáře – část s digitálním výsledkem a, instance digitálního vlákna Azure":::

V okně aplikace Visual Studio, kde je otevřen projekt _**AdtE2ESample**_ , spusťte projekt pomocí tohoto tlačítka na panelu nástrojů:

:::image type="content" source="media/tutorial-end-to-end/start-button-sample.png" alt-text="Tlačítko Start pro Visual Studio (projekt SampleClientApp)":::

Otevře se okno konzoly, provede se ověřování a počká na příkaz. V této konzole spusťte následující příkaz pro vytvoření instance ukázkového řešení digitálních vláken Azure.

> [!IMPORTANT]
> Pokud již máte digitální vazby a vztahy v instanci digitálních vláken Azure, spusťte tento příkaz a nahraďte je dvojitými zprávami a relacemi pro ukázkový scénář.

```cmd/sh
SetupBuildingScenario
```

Výstupem tohoto příkazu je série potvrzujících zpráv, [**protože se vytvářejí**](concepts-twins-graph.md) a připojují ve vaší instanci digitálních vláken Azure: podlahu s názvem *floor1*, místnost s názvem *room21* a snímač teploty s názvem *thermostat67*. Tyto digitální vlákna reprezentují entity, které by existovaly ve skutečném prostředí.

Jsou propojeny prostřednictvím vztahů s následujícím [**dvojitým grafem**](concepts-twins-graph.md). Dvojitý graf představuje prostředí jako celek, včetně toho, jak entity vzájemně spolupracují a vzájemně souvisí.

:::image type="content" source="media/tutorial-end-to-end/building-scenario-graph.png" alt-text="Graf, který znázorňuje, že floor1 obsahuje room21 a room21 obsahuje thermostat67" border="false":::

Můžete ověřit, které vlákna byly vytvořeny spuštěním následujícího příkazu, který se dotazuje připojené instance digitálního vlákna Azure pro všechny digitální vlákna, které obsahuje:

```cmd/sh
Query
```

>[!TIP]
> Tato zjednodušená metoda je k dispozici jako součást projektu _**AdtE2ESample**_ . Mimo kontext tohoto ukázkového kódu se můžete kdykoli dotazovat na všechny vlákna ve vaší instanci pomocí [rozhraní API pro dotazy](/rest/api/digital-twins/dataplane/query) nebo [příkazů rozhraní příkazového řádku](how-to-use-cli.md).
>
> Tady je úplný text dotazu, který vám umožní získat všechny digitální vlákna ve vaší instanci:
> 
> :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

Potom můžete zastavit běh projektu. Nechejte řešení otevřené v aplikaci Visual Studio, i když ho budete dál používat v celém kurzu.

## <a name="set-up-the-sample-function-app"></a>Nastavení ukázkové aplikace Function App

Dalším krokem je nastavení [aplikace Azure Functions](../azure-functions/functions-overview.md) , která se bude používat v celém rámci tohoto kurzu ke zpracování dat. Aplikace Function App, *SampleFunctionsApp*, obsahuje dvě funkce:
* *ProcessHubToDTEvents*: zpracovává příchozí IoT Hub data a podle toho aktualizuje digitální vlákna Azure.
* *ProcessDTRoutedData*: zpracovává data z digitálních vláken a aktualizuje nadřazených vláken v rámci digitálních vláken Azure odpovídajícím způsobem.

V této části publikujete předem napsanou aplikaci funkcí a zajistěte, aby aplikace Function App měla přístup k digitálním událostem Azure pomocí přiřazení Azure Active Directory (Azure AD). Dokončením těchto kroků umožníte zbývající část kurzu používat funkce uvnitř aplikace Function App. 

Zpět v okně aplikace Visual Studio, kde je otevřen projekt _**AdtE2ESample**_ , aplikace Function App se nachází v souboru projektu _**SampleFunctionsApp**_ . Můžete ji zobrazit v podokně *Průzkumník řešení* .

### <a name="update-dependencies"></a>Aktualizovat závislosti

Před publikováním aplikace je vhodné se ujistit, že vaše závislosti jsou aktuální, a přitom se ujistěte, že máte nejnovější verzi všech zahrnutých balíčků.

V podokně *Průzkumník řešení* rozbalte položku _**SampleFunctionsApp** > závislosti_. Klikněte pravým tlačítkem na položku *balíčky* a zvolte možnost *Spravovat balíčky NuGet...*.

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-1.png" alt-text="Visual Studio: Správa balíčků NuGet pro projekt SampleFunctionsApp" border="false":::

Tím se otevře správce balíčků NuGet. Vyberte kartu *aktualizace* a pokud existují balíčky, které se mají aktualizovat, zaškrtněte políčko pro *Výběr všech balíčků*. Pak stiskněte *aktualizovat*.

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-2.png" alt-text="Visual Studio: výběr aktualizace všech balíčků ve Správci balíčků NuGet":::

### <a name="publish-the-app"></a>Publikování aplikace

Zpět v okně aplikace Visual Studio, kde je otevřen projekt _**AdtE2ESample**_ , vyhledejte projekt _**SampleFunctionsApp**_ v podokně *Průzkumník řešení* .

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="assign-permissions-to-the-function-app"></a>Přiřazení oprávnění k aplikaci Function App

Pokud chcete povolit aplikaci Function App přístup k digitálním úlohám Azure, je dalším krokem konfigurace nastavení aplikace, přiřazení této aplikace identitě spravované systémem Azure AD a udělení této identity roli *vlastníka dat v Azure Digital* realiass v instanci digitálních vláken Azure. Tato role se vyžaduje pro libovolného uživatele nebo funkci, která chce v instanci provést mnoho aktivit roviny dat. Další informace o zabezpečení a přiřazování rolí si můžete přečíst v tématu [*Koncepty: zabezpečení pro řešení digitálních vláken Azure*](concepts-security.md).

V Azure Cloud Shell pomocí následujícího příkazu nastavte nastavení aplikace, které vaše aplikace Function App použije k odkazování na instanci digitálních vláken Azure. Zadejte zástupné symboly s podrobnostmi o vašich prostředcích (Nezapomeňte, že adresa URL instance digitálního vlákna Azure představuje název hostitele, kterému předchází *https://*).

```azurecli-interactive
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=<your-Azure-Digital-Twins-instance-URL>"
```

Výstupem je seznam nastavení funkce Azure Functions, která by teď měla obsahovat položku s názvem **ADT_SERVICE_URL**.

Pomocí následujícího příkazu vytvořte identitu spravovanou systémem. Ve výstupu vyhledejte pole **principalId** .

```azurecli-interactive
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Pomocí hodnoty **principalId** z výstupu v následujícím příkazu přiřaďte identitu aplikace funkcí k roli *vlastníka dat digitálních vláken Azure* pro vaši instanci digitálních vláken Azure.

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```

Výsledkem tohoto příkazu jsou informace o přiřazení role, kterou jste vytvořili. Aplikace Function App má teď oprávnění pro přístup k instanci digitálních vláken Azure.

## <a name="process-simulated-telemetry-from-an-iot-hub-device"></a>Zpracovat simulovanou telemetrii z IoT Hubho zařízení

Graf digitálních vláken Azure by měl být řízený telemetrie z reálných zařízení. 

V tomto kroku připojíte simulované zařízení, které je zaregistrované v [IoT Hub](../iot-hub/about-iot-hub.md) k digitálnímu vlákna, které představuje v Azure Digital autovlákna. Když simulované zařízení vysílá telemetrii, budou data směrována prostřednictvím funkce *ProcessHubToDTEvents* Azure, která v digitálním vlákna spustí odpovídající aktualizaci. Tímto způsobem je digitální neaktuálnost v reálném čase díky datům reálného zařízení. V rámci digitálních vláken Azure se proces nasměrování dat událostí z jednoho místa na jiný nazývá [**Směrování událostí**](concepts-route-events.md).

K tomu dochází v této části koncového scénáře (**šipka B**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-b.png" alt-text="Výňatek z celého scénáře sestavování grafického zvýraznění – šipka B, prvky před digitálním výsledkem Azure: zařízení, IoT Hub a první funkce Azure":::

Tady jsou akce, které dokončíte pro nastavení připojení tohoto zařízení:
1. Vytvoření služby IoT Hub, která bude spravovat simulované zařízení
2. Připojte službu IoT Hub ke správné funkci Azure tím, že nastavíte odběr události.
3. Registrace simulovaného zařízení ve službě IoT Hub
4. Spuštění simulovaného zařízení a generování telemetrie
5. Dotazování na živé výsledky Azure pro zobrazení živých výsledků

### <a name="create-an-iot-hub-instance"></a>Vytvoření instance IoT Hubu

Digitální vlákna Azure je navržená tak, aby fungovala vedle [IoT Hub](../iot-hub/about-iot-hub.md), což je služba Azure pro správu zařízení a jejich dat. V tomto kroku nastavíte centrum IoT, které bude spravovat ukázkové zařízení v tomto kurzu.

V Azure Cloud Shell pomocí tohoto příkazu vytvořte novou IoT Hub:

```azurecli-interactive
az iot hub create --name <name-for-your-IoT-hub> -g <your-resource-group> --sku S1
```

Výstupem tohoto příkazu jsou informace o vytvořeném službě IoT Hub.

Uložte **název** , který jste přiřadili do služby IoT Hub. Budete jej později potřebovat.

### <a name="connect-the-iot-hub-to-the-azure-function"></a>Připojení centra IoT ke službě Azure Functions

V dalším kroku připojte službu IoT Hub k funkci *ProcessHubToDTEvents* Azure v aplikaci Function App, kterou jste publikovali dříve, aby se data mohla přesměrovat ze zařízení do IoT Hub prostřednictvím funkce, která aktualizuje digitální vlákna Azure.

Pokud to chcete provést, vytvoříte **odběr událostí** v IoT Hub s funkcí Azure jako koncovým bodem. Tato možnost se přihlásí k odběru událostí, která se děje v IoT Hub.

V [Azure Portal](https://portal.azure.com/)přejděte do svého nově vytvořeného centra IoT tak, že na horním panelu vyhledávání vyhledáte jeho název. V nabídce centra vyberte možnost *události* a vyberte *+ odběr události*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1.png" alt-text="Azure Portal: odběr událostí IoT Hub":::

Tím se zobrazí stránka *vytvořit odběr události* .

:::image type="content" source="media/tutorial-end-to-end/event-subscription-2.png" alt-text="Azure Portal: Vytvoření odběru událostí":::

Vyplňte pole následujícím způsobem (ve výchozím nastavení se nezobrazují pole, která jsou vyplněna):
* *Podrobnosti*  >  odběru události **Název**: zadejte název předplatného události.
* *Podrobnosti o tématu*  >  **Název systémového tématu**: zadejte název, který se má použít pro systémové téma. 
* *typy událostí*  >  **Filtrovat na typy událostí**: z možností nabídky vyberte *telemetrie zařízení* .
* Podrobnosti koncového *bodu*  >  **Typ koncového bodu**: z možností nabídky vyberte *Azure Function* .
* Podrobnosti koncového *bodu*  >  **Koncový bod**: stiskněte odkaz *Vybrat koncový bod* . Otevře se okno *Vybrat funkci Azure* : :::image type="content" source="media/tutorial-end-to-end/event-subscription-3.png" alt-text="Azure Portal odběr událostí: vybrat funkci Azure Functions" border="false":::
    - Vyplňte svoje **předplatné**, **skupinu prostředků**, **aplikaci funkcí** a **funkci** (*ProcessHubToDTEvents*). Některé z těchto možností mohou automaticky být vyplněny po výběru předplatného.
    - **Potvrďte výběr**.

Zpátky na stránce *vytvořit odběr události* klikněte na **vytvořit**.

### <a name="register-the-simulated-device-with-iot-hub"></a>Zaregistrujte simulované zařízení s IoT Hub 

V této části se vytvoří reprezentace zařízení v IoT Hub s ID *thermostat67*. Simulované zařízení se k tomu připojí a to znamená, jak budou události telemetrie ze zařízení přecházet do IoT Hub, kde se přihlašuje předplacená funkce Azure z předchozího kroku, která je připravená na výběr událostí a pokračování v zpracování.

V Azure Cloud Shell vytvořte v IoT Hub zařízení pomocí následujícího příkazu:

```azurecli-interactive
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> -g <your-resource-group>
```

Výstupem jsou informace o vytvořeném zařízení.

### <a name="configure-and-run-the-simulation"></a>Konfigurace a spuštění simulace

Následně nakonfigurujte simulátor zařízení, aby posílal data do instance IoT Hubu.

Začněte získáním *připojovacího řetězce centra IoT* pomocí tohoto příkazu:

```azurecli-interactive
az iot hub connection-string show -n <your-IoT-hub-name>
```

Pak načtěte *připojovací řetězec zařízení* pomocí tohoto příkazu:

```azurecli-interactive
az iot hub device-identity connection-string show --device-id thermostat67 --hub-name <your-IoT-hub-name>
```

Tyto hodnoty připojíte do kódu simulátoru zařízení v místním projektu a připojíte simulátor do tohoto zařízení IoT Hub a IoT Hub.

V novém okně sady Visual Studio otevřete (ze stažené složky řešení) _simulátor zařízení > **DeviceSimulator. sln**_.

>[!NOTE]
> Nyní byste měli mít dvě okna sady Visual Studio, jednu s _**DeviceSimulator. sln**_ a jednu z předchozích verzí _**AdtE2ESample. sln**_.

V podokně *Průzkumník řešení* v tomto novém okně sady Visual Studio vyberte možnost _DeviceSimulator/**AzureIoTHub.cs**_ a otevřete ji v okně pro úpravy. Změňte následující hodnoty připojovacího řetězce na hodnoty, které jste shromáždili výše:

```csharp
iotHubConnectionString = <your-hub-connection-string>
deviceConnectionString = <your-device-connection-string>
```

Soubor uložte.

Nyní chcete-li zobrazit výsledky simulace dat, kterou jste nastavili, spusťte projekt **DeviceSimulator** pomocí tohoto tlačítka na panelu nástrojů:

:::image type="content" source="media/tutorial-end-to-end/start-button-simulator.png" alt-text="Tlačítko Start pro Visual Studio (projekt DeviceSimulator)":::

Otevře se okno konzoly, ve kterém se zobrazí simulované zprávy telemetrie o teplotě. Tyto zprávy jsou odesílány do IoT Hub, kde jsou následně vydány a zpracovány funkcí Azure Functions.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Výstup konzoly simulátoru zařízení, který ukazuje, že se posílá telemetrie na teplotu":::

V této konzole nemusíte nic dalšího dělat, ale při provádění dalších kroků ho nechte spuštěný.

### <a name="see-the-results-in-azure-digital-twins"></a>Podívejte se na výsledky v části digitální vlákna Azure

Funkce *ProcessHubToDTEvents* , kterou jste publikovali dříve, naslouchá datům IoT Hub a volá rozhraní API digitálních vláken Azure s cílem aktualizovat vlastnost *teploty* na *thermostat67* .

Pokud se chcete podívat na data z oblasti digitálních vláken Azure, přejděte do okna aplikace Visual Studio, kde je projekt _**AdtE2ESample**_ otevřený a spusťte projekt.

V okně konzoly projektu, které se otevře, spusťte následující příkaz, který získá teploty hlášené pomocí digitálního vlákna *thermostat67*:

```cmd
ObserveProperties thermostat67 Temperature
```

Měli byste vidět živá aktualizované teploty *z instance digitálních vláken Azure* , které jsou protokolovány do konzoly každé dvě sekundy.

>[!NOTE]
> Může trvat několik sekund, než se data ze zařízení šíří do vlákna. Prvních několika čtení teploty se může zobrazit jako 0 předtím, než se data začnou dorazit.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry.png" alt-text="Výstup konzoly zobrazující protokol teplotních zpráv z digitálního vlákna thermostat67":::

Jakmile ověříte, že toto ověření funguje úspěšně, můžete zastavit spouštění obou projektů. Nechejte okna sady Visual Studio otevřené, jak je budete dál používat ve zbývající části kurzu.

## <a name="propagate-azure-digital-twins-events-through-the-graph"></a>Šíření událostí digitálních vláken Azure pomocí grafu

V tomto kurzu jste se seznámili s tím, jak se digitální vlákna Azure dají aktualizovat z externích dat zařízení. V dalším kroku uvidíte, jak se můžou změny jedné digitální funkce šířit prostřednictvím grafu digitálních vláken Azure – jinými slovy, jak aktualizovat vlákna z interních dat služby.

K tomu použijete funkci *ProcessDTRoutedData* Azure k aktualizaci vlákna v *místnosti* , když se aktualizuje připojený *termostat* . K tomu dochází v této části koncového scénáře (**šipka C**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-c.png" alt-text="Výňatek z celého scénáře sestavování grafického zvýraznění – šipka C, prvky po digitálních událostech Azure: Event Grid a druhá funkce Azure":::

Tady jsou akce, které dokončíte pro nastavení tohoto toku dat:
1. Vytvoření koncového bodu Event Grid v digitálních událostech Azure, které připojují instanci k Event Grid
2. Nastavení trasy v rámci digitálních vláken Azure pro posílání dvojitých událostí změny vlastností do koncového bodu
3. Nasaďte aplikaci Azure Functions, která na koncovém bodu naslouchá (prostřednictvím [Event Grid](../event-grid/overview.md)), a odpovídajícím způsobem aktualizuje další vlákna.
4. Spuštění simulovaného zařízení a dotazování digitálních vláken Azure na výsledky zobrazení živých výsledků

### <a name="set-up-endpoint"></a>Nastavení koncového bodu

[Event Grid](../event-grid/overview.md) je služba Azure, která pomáhá směrovat a doručovat události přicházející ze služeb Azure na jiné místo v rámci Azure. Můžete vytvořit [téma Event gridu](../event-grid/concepts.md) , ve kterém se budou shromažďovat určité události ze zdroje, a potom můžou předplatitelé naslouchat na tématu, aby mohli přijímat události při jejich absolvování.

V této části vytvoříte téma Event Grid a potom v rámci digitálních vláken Azure, který odkazuje (odesílá události) do tohoto tématu, vytvoříte koncový bod. 

V Azure Cloud Shell spusťte následující příkaz, který vytvoří téma Event gridu:

```azurecli-interactive
az eventgrid topic create -g <your-resource-group> --name <name-for-your-event-grid-topic> -l <region>
```

> [!TIP]
> Pokud chcete vypsat seznam názvů oblastí Azure, které se dají předávat do příkazů v Azure CLI, spusťte tento příkaz:
> ```azurecli-interactive
> az account list-locations -o table
> ```

Výstupem tohoto příkazu jsou informace o tématu, které jste vytvořili.

Dále vytvořte koncový bod Event Grid v digitálních událostech Azure, které budou vaši instanci připojovat k tématu Event gridu. Použijte následující příkaz, podle potřeby vyplňte pole zástupné symboly:

```azurecli-interactive
az dt endpoint create eventgrid --dt-name <your-Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

Výstupem tohoto příkazu jsou informace o koncovém bodu, který jste vytvořili.

Spuštěním následujícího příkazu můžete také ověřit, jestli se vytvoření koncového bodu dokončilo, a to tak, že spustíte následující příkaz, který se dotazuje na instanci digitálního vlákna Azure pro tento

```azurecli-interactive
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

Vyhledejte `provisioningState` pole ve výstupu a zkontrolujte, zda je hodnota "úspěch". Může taky vyslovit "zřizování", což znamená, že se koncový bod pořád vytváří. V takovém případě počkejte několik sekund a spusťte příkaz znovu a ověřte, zda byl úspěšně dokončen.

:::image type="content" source="media/tutorial-end-to-end/output-endpoints.png" alt-text="Výsledek dotazu koncového bodu zobrazující koncový bod s provisioningStateem úspěšného":::

Uložte názvy, které jste zadali do svého **tématu Event gridu** , a Event Grid **koncový bod** v části digitální vlákna Azure. Později je budete používat.

### <a name="set-up-route"></a>Nastavení trasy

Dále vytvořte trasu digitálních vláken Azure, která odešle události do Event Gridho koncového bodu, který jste právě vytvořili.

```azurecli-interactive
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

Výstupem z tohoto příkazu jsou některé informace o trasách, které jste vytvořili.

>[!NOTE]
>Aby bylo možné nastavit trasu událostí, která je používá, je nutné zajistit zřízení koncových bodů (z předchozího kroku). Pokud se vytvoření trasy nepovede, protože koncové body nejsou připravené, počkejte pár minut a pak to zkuste znovu.

#### <a name="connect-the-function-to-event-grid"></a>Připojení funkce k Event Grid

Dále přihlaste *ProcessDTRoutedData* funkci Azure do tématu Event gridu, které jste vytvořili dříve, aby data telemetrie mohla být z *thermostat67* vyplněna prostřednictvím tématu Event gridu do funkce, která se vrátí do digitálních vláken Azure a odpovídajícím způsobem aktualizuje *room21* .

Uděláte to tak, že vytvoříte **předplatné Event Grid** , které odesílá data z **tématu Event gridu** , které jste vytvořili dříve, do služby Azure Functions v *ProcessDTRoutedData* .

V [Azure Portal](https://portal.azure.com/)přejděte na téma Event gridu tak, že na horním panelu vyhledávání vyhledáte jeho název. Vyberte *+ Odběr události*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1b.png" alt-text="Azure Portal: odběr událostí Event Grid":::

Postup vytvoření odběru událostí je podobný jako při přihlášení k odběru první funkce Azure, která IoT Hub dříve v tomto kurzu. Tentokrát nemusíte zadávat *telemetrii zařízení* jako typ události, která se má naslouchat, a Vy se připojíte k jiné funkci Azure Functions.

Na stránce *vytvořit odběr události* vyplňte pole následujícím způsobem (pole vyplněná ve výchozím nastavení nejsou zmíněná):
* *Podrobnosti*  >  odběru události **Název**: zadejte název předplatného události.
* Podrobnosti koncového *bodu*  >  **Typ koncového bodu**: z možností nabídky vyberte *Azure Function* .
* Podrobnosti koncového *bodu*  >  **Koncový bod**: stiskněte odkaz *Vybrat koncový bod* . Otevře se okno *Vybrat Azure Function* :
    - Vyplňte svoje **předplatné**, **skupinu prostředků**, **aplikaci funkcí** a **funkci** (*ProcessDTRoutedData*). Některé z těchto možností mohou automaticky být vyplněny po výběru předplatného.
    - **Potvrďte výběr**.

Zpátky na stránce *vytvořit odběr události* klikněte na **vytvořit**.

### <a name="run-the-simulation-and-see-the-results"></a>Spustit simulaci a zobrazit výsledky

Nyní můžete spustit simulátor zařízení, aby se aktivoval nový tok událostí, který jste nastavili. V okně aplikace Visual Studio, kde je otevřený projekt _**DeviceSimulator**_ , a spusťte projekt.

Podobně jako v případě, že jste dříve spustili simulátor zařízení, okno konzoly otevře a zobrazí simulované zprávy telemetrie o teplotě. Tyto události procházejí tokem, který jste nastavili dříve, abyste aktualizovali *thermostat67u* , a pak projdete tokem, který jste v poslední době nastavili, aby se shodovala shoda s *room21* .

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Výstup konzoly simulátoru zařízení, který ukazuje, že se posílá telemetrie na teplotu":::

V této konzole nemusíte nic dalšího dělat, ale při provádění dalších kroků ho nechte spuštěný.

Pokud se chcete podívat na data z oblasti digitálních vláken Azure, přejděte do okna aplikace Visual Studio, kde je projekt _**AdtE2ESample**_ otevřený, a spusťte projekt.

V okně konzoly projektu, které se otevře, spusťte následující příkaz, který získá teploty hlášené digitálním  *thermostat67em* a digitálním *room21em*.

```cmd
ObserveProperties thermostat67 Temperature room21 Temperature
```

Měli byste vidět živá aktualizované teploty *z instance digitálních vláken Azure* , které jsou protokolovány do konzoly každé dvě sekundy. Všimněte si, že teplota pro *room21* se aktualizuje tak, aby odpovídala aktualizacím *thermostat67*.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry-b.png" alt-text="Výstup konzoly zobrazující protokol teplotních zpráv, z termostatu a místnosti":::

Jakmile ověříte, že toto ověření funguje úspěšně, můžete zastavit spouštění obou projektů. Okna sady Visual Studio můžete také zavřít, protože kurz je nyní dokončen.

## <a name="review"></a>Opakování

Tady je přehled scénáře, který jste vytvořili v tomto kurzu.

1. Instance digitálních vláken Azure představuje v následujícím diagramu podlahu, místnost a termostat (reprezentované **částí a** v diagramu níže).
2. Telemetrii simulovaného zařízení se odesílá do IoT Hub, kde *ProcessHubToDTEvents* funkce Azure naslouchá událostem telemetrie. Funkce *ProcessHubToDTEvents* Azure používá informace v těchto událostech k nastavení vlastnosti *teploty* v *thermostat67* (**šipka B** v diagramu).
3. Události změny vlastností v digitálních událostech Azure jsou směrované do tématu Event gridu, kde *ProcessDTRoutedData* funkce Azure naslouchá událostem. Funkce *ProcessDTRoutedData* Azure používá informace v těchto událostech k nastavení vlastnosti *teploty* v *room21* (**šipka C** v diagramu).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Obrázek celého scénáře stavby Znázorňuje tok dat ze zařízení do IoT Hub, prostřednictvím funkce Azure (šipka B) k instanci digitálních vláken Azure (oddíl A), pak prostřednictvím Event Grid na jinou funkci Azure pro zpracování (šipka C).":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu můžete zvolit prostředky, které chcete odebrat, v závislosti na tom, co byste chtěli udělat dál.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

* **Pokud chcete dál používat instanci digitálních vláken Azure, kterou jste nastavili v tomto článku, ale vymažete některé nebo všechny jeho modely, vlákna a vztahy**, můžete k odstranění prvků, které chcete odebrat, použít příkazy [AZ dt](/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true) CLI v [Azure Cloud Shell](https://shell.azure.com) okně.

    Tato možnost neodebere žádný z dalších prostředků Azure vytvořených v tomto kurzu (IoT Hub, Azure Functions aplikace atd.). Můžete je odstranit jednotlivě pomocí [příkazů DT](/cli/azure/reference-index?view=azure-cli-latest&preserve-view=true) vhodných pro každý typ prostředku.

Je také možné, že budete chtít odstranit složku projektu z místního počítače.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili kompletní scénář, který zobrazuje digitální vlákna Azure, která se řídí daty živého zařízení.

Pak začněte hledat v dokumentaci konceptu, kde najdete další informace o prvcích, se kterými jste pracovali v tomto kurzu:

> [!div class="nextstepaction"]
> [*Koncepty: vlastní modely*](concepts-models.md)